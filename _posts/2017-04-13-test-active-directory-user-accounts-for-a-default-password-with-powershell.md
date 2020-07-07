---
ID: 15156
post_title: >
  Test Active Directory User Accounts for
  a Default Password with PowerShell
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2017/04/13/test-active-directory-user-accounts-for-a-default-password-with-powershell/
published: true
post_date: 2017-04-13 07:30:05
---
How do you control password resets in your environment? I've worked for numerous companies where their forgotten password reset process was all over the board. Hopefully you have a process in place that allows you to sleep at night. Even with the best policies and procedures in place, what happens when someone on your help desk staff resets a users password to some default password and forgets to set the account so the password has to be changed at next logon? Is the user still using that default password weeks later?

I decided to write a PowerShell script to test user accounts for just that exact scenario.
<pre class="lang:ps decode:true " title="Test-MrADUserPassword">#Requires -Version 3.0 -Modules ActiveDirectory
function Test-MrADUserPassword {

&lt;#
.SYNOPSIS
    Test-MrADUserPassword is a function for testing an Active Directory user account for a specific password.
 
.DESCRIPTION
    Test-MrADUserPassword is an advanced function for testing one or more Active Directory user accounts for a
    specific password.
 
.PARAMETER UserName
    The username for the Active Directory user account.

.PARAMETER Password
    The password to test for.

.PARAMETER ComputerName
    A server or computer name that has PowerShell remoting enabled.

.PARAMETER InputObject
    Accepts the output of Get-ADUser.

.EXAMPLE
     Test-MrADUserPassword -UserName alan0 -Password Password1 -ComputerName Server01

.EXAMPLE
     'alan0'. 'andrew1', 'frank2' | Test-MrADUserPassword -Password Password1 -ComputerName Server01

.EXAMPLE
     Get-ADUser -Filter * -SearchBase 'OU=AdventureWorks Users,OU=Users,OU=Test,DC=mikefrobbins,DC=com' |
     Test-MrPassword -Password Password1 -ComputerName Server01

.INPUTS
    String, Microsoft.ActiveDirectory.Management.ADUser
 
.OUTPUTS
    PSCustomObject
 
.NOTES
    Author:  Mike F Robbins
    Website: http://mikefrobbins.com
    Twitter: @mikefrobbins
#&gt;

    [CmdletBinding(DefaultParameterSetName='Parameter Set UserName')]
    param (
        [Parameter(Mandatory,
                   ValueFromPipeline,
                   ValueFromPipelineByPropertyName,
                   ParameterSetName='Parameter Set UserName')]
        [Alias('SamAccountName')]
        [string[]]$UserName,

        [Parameter(Mandatory)]
        [string]$Password,

        [Parameter(Mandatory)]
        [string]$ComputerName,

        [Parameter(ValueFromPipeline,
                   ParameterSetName='Parameter Set InputObject')]
        [Microsoft.ActiveDirectory.Management.ADUser]$InputObject

    )
    
    BEGIN {
        $Pass = ConvertTo-SecureString $Password -AsPlainText -Force

        $Params = @{
            ComputerName = $ComputerName
            ScriptBlock = {Get-Random | Out-Null}
            ErrorAction = 'SilentlyContinue'
            ErrorVariable  = 'Results'
        }
    }

    PROCESS {
        if ($PSBoundParameters.UserName) {
            Write-Verbose -Message 'Input received via the "UserName" parameter set.'
            $Users = $UserName
        }
        elseif ($PSBoundParameters.InputObject) {
            Write-Verbose -Message 'Input received via the "InputObject" parameter set.'
            $Users = $InputObject
        }

        foreach ($User in $Users) {    
            
            if (-not($Users.SamAccountName)) {
                Write-Verbose -Message "Querying Active Directory for UserName $($User)"
                $User = Get-ADUser -Identity $User
            }
    
            $Params.Credential = (New-Object System.Management.Automation.PSCredential ($($User.UserPrincipalName), $Pass))

            Invoke-Command @Params

            [pscustomobject]@{
                UserName = $User.SamAccountName
                PasswordCorrect =
                    switch ($Results.FullyQualifiedErrorId -replace ',.*$') {
                        LogonFailure {$false; break}
                        AccessDenied {$true; break}
                        default {$true}
                    } 
            }    
    
        }
    
    }      

}</pre>
Test one or more Active Directory user accounts for a password of "Password1":
<pre class="lang:ps decode:true">Test-MrADUserPassword -UserName alan0, david1 -Password Password1 -ComputerName web01</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2017/04/test-password1a.png"><img class="alignnone size-full wp-image-15158" src="http://mikefrobbins.com/wp-content/uploads/2017/04/test-password1a.png" alt="" width="859" height="142" /></a>

Same test except using pipeline input of strings for the usernames:
<pre class="lang:ps decode:true">'alan0', 'david1' | Test-MrADUserPassword -Password Password1 -ComputerName web01</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2017/04/test-password2a.png"><img class="alignnone size-full wp-image-15159" src="http://mikefrobbins.com/wp-content/uploads/2017/04/test-password2a.png" alt="" width="859" height="143" /></a>

Test all of the user accounts in a specific OU (Organizational Unit) in Active Directory:
<pre class="lang:ps decode:true">Get-ADUser -Filter * -SearchBase 'OU=AdventureWorks Users,OU=Users,OU=Test,DC=mikefrobbins,DC=com' |
Test-MrPassword -Password Password1 -ComputerName web01 |
Sort-Object -Property PasswordCorrect</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2017/04/test-password3a.png"><img class="alignnone size-full wp-image-15160" src="http://mikefrobbins.com/wp-content/uploads/2017/04/test-password3a.png" alt="" width="859" height="320" /></a>

Same as the previous example except only return the accounts where the password matched.
<pre class="lang:ps decode:true">Get-ADUser -Filter * -SearchBase 'OU=AdventureWorks Users,OU=Users,OU=Test,DC=mikefrobbins,DC=com' |
Test-MrPassword -Password Password1 -ComputerName web01 |
Where-Object PasswordCorrect -eq $true</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2017/04/test-password4a.png"><img class="alignnone size-full wp-image-15161" src="http://mikefrobbins.com/wp-content/uploads/2017/04/test-password4a.png" alt="" width="858" height="166" /></a>

Be sure to test this and to get permission from someone in your chain of command before running it in a production environment. Be careful when using this function because it does count as a failed login for the user account if the password doesn't match. It will show up on your audit login failures report if you're performing any type of auditing for login failures. You could also end up locking out the user account if you run this enough times to meet the account lockout threshold set in your domain or in the fine grained password policies if they're enabled in your environment.

The function shown in this blog article can be downloaded from <a href="https://github.com/mikefrobbins/ActiveDirectory" target="_blank">my ActiveDirectory repository on GitHub</a>.

<hr />

Update: While at the  PowerShell + DevOps Global Summit this week, I was discussing this function with a group of attendees and I discovered that there's a better way to accomplish this task. I'll post a follow-up blog article next week.

µ