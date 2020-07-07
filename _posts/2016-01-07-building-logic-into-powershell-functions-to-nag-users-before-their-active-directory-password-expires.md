---
ID: 12937
post_title: >
  Building logic into PowerShell functions
  to nag users before their Active
  Directory password expires
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2016/01/07/building-logic-into-powershell-functions-to-nag-users-before-their-active-directory-password-expires/
published: true
post_date: 2016-01-07 07:30:33
---
This week I'm sharing a couple of PowerShell functions that are a work in progress to nag those users who seem to never want to change their passwords. I can't tell you how many times the help desk staff at one of the companies that I provide support for receives a call from a user who is unable to access email or other resources on the intranet.

The problem? They have run their password down to the point where they arrive in the morning, log into their computer without issue, and during the day while they're working their password expires which cuts them off from Intranet resources such as email and websites that require authentication.

If they happen to call me, I know what they've done and my first question to them is "Are you sure that you still work here? Maybe you were terminated and HR cut off your access to network resources."

An automated password expiration email reminder is already sent out daily, but users seem to ignore it so I want to use PowerShell to send one email per day 14 days out and send one email per hour during business hours when their password is going to expire within 3 days.

The first function simply retrieves the password expiration information in case I simply want a report of what's going to expire (and I don't want two functions with redundant code in them):
<pre class="lang:ps decode:true">#Requires -Modules ActiveDirectory
function Search-MrADAccountPasswordExpiration {

    [CmdletBinding()]
    param (
        [ValidateNotNullOrEmpty()]
        [int]$Days = 14,

        [ValidateNotNullOrEmpty()]
        [int]$MaximumPasswordAge = 90,

        [ValidateNotNullOrEmpty()]
        [string]$SearchBase = 'OU=Test Users,OU=Users,DC=mikefrobbins,DC=com'
    )

    [datetime]$CutoffDate = (Get-Date).AddDays(-($MaximumPasswordAge - $Days))

    Get-ADUser -Filter {
        Enabled -eq $true -and PasswordNeverExpires -eq $false -and PasswordLastSet -lt $CutoffDate
    } -Properties PasswordExpired, PasswordNeverExpires, PasswordLastSet, Mail -SearchBase $SearchBase |
    Where-Object PasswordExpired -eq $false |
    Select-Object -Property Name, SamAccountName, Mail, PasswordLastSet, @{label='PasswordExpiresOn';expression={$($_.PasswordLastSet).AddDays($MaximumPasswordAge)}}

}</pre>
In the previous function, you'll notice that I piped to <em>Where-Object</em> to filter out the accounts where the password was already expired. I couldn't for the life of me get the <em>PasswordExpired</em> property to work properly with the <em>Filter</em> parameter of <em>Get-ADUser</em> so hopefully you don't have a lot of accounts where the password is already expired and the user is still enabled in your environment.

You could also dynamically determine the maximum password age for your Active Directory domain using this function, but it's as slow as Christmas and assumes that you're not using fine grained password policies so I would just as well hard code the value as I did in the previous function as to use this method of determining it:
<pre class="lang:ps decode:true ">#Requires -Modules GroupPolicy
function Get-MrMaximumPasswordAge {

    [CmdletBinding()]
    param(
        [ValidateNotNullOrEmpty()]
        [string]$GPOName = 'Default Domain Policy'
    )

    (([xml](Get-GPOReport -Name $GPOName -ReportType Xml)).GPO.Computer.ExtensionData.Extension.Account |
    Where-Object name -eq MaximumPasswordAge).SettingNumber

}</pre>
This function sends the emails. The switch statement along with the range operator is where the magic happens:
<pre class="lang:ps decode:true ">#Requires -Version 3.0
function Send-MrADPasswordReminder {

    [CmdletBinding()]
    param(
        [Parameter(ValueFromPipeline)]
        [ValidateNotNullOrEmpty()]
        [pscustomobject]$Users,

        [ValidateNotNullOrEmpty()]
        [datetime]$Date = (Get-Date),

        [ValidateRange(0,23)]
        [int]$HourOfDay = 9,

        [ValidateNotNullOrEmpty()]
        [mailaddress]$From = 'passwordreminder@mikefrobbins.com',

        [ValidateNotNullOrEmpty()]
        [string]$SmtpServer = 'mail.mikefrobbins.com',

        [System.Management.Automation.Credential()]$Credential = [System.Management.Automation.PSCredential]::Empty
    )

    BEGIN {

        $Params = @{
            From = $From
            SmtpServer = $SmtpServer
            Subject = 'Password Expiration'
        }

        if($PSBoundParameters.Credential) {
            $Params.Credential = $Credential
        }

    }

    PROCESS {

        foreach ($User in $Users) {

            $Params.To = $User.Mail

            $DaysUntilExpiration = (New-TimeSpan -Start $Date -End $($User.PasswordExpiresOn)).Days

            switch ($DaysUntilExpiration) {

                {$_ -in 4..14} {
                    if ($Date.Hour -eq $HourOfDay) {
                        Send-MailMessage @Params -Body "Your network password expires in $DaysUntilExpiration days. Please change your network password at your earliest possible convenience."
                    }
                    break
                }
                {$_ -in 0..3} {
                    Send-MailMessage @Params -Body "Your network password expires in $DaysUntilExpiration days. Please change your network password immediately."
                    break
                }

            }

        }

    }

}</pre>
So this can be setup as a PowerShell Scheduled Job or a scheduled task to run every hour on the hour during business hours:
<pre class="lang:ps decode:true ">Search-MrADAccountPasswordExpiration | Send-MrADPasswordReminder</pre>
Based on how the script is written, the user will receive one email per day if their password is going to expire in 4 to 14 days. They will receive an email every hour that the scheduled job or task runs if their password is going to expire in 3 days or less. Once their password is expired, they will not receive any further emails.

Here's a pro tip from the trenches: If the user changed their password and daylight saving time has started or ended since then but before their password expires, the password expiration date calculated by adding the domain's maximum password age to the date/time when the user last changed their password will be one hour off.

As previously stated, the functions shown in this blog article are a work in process and have not been thoroughly tested so use them at your own risk.

µ