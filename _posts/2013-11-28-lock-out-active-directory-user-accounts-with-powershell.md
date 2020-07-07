---
ID: 8629
post_title: >
  Lock Out Active Directory User Accounts
  with PowerShell
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2013/11/28/lock-out-active-directory-user-accounts-with-powershell/
published: true
post_date: 2013-11-28 07:30:24
---
As I'm sure you're aware, there's no setting where you can simply flip a switch to lock out Active Directory user accounts. So what is one to do if you need some locked out accounts to do testing with?

This script is something I whipped up to accomplish just that because I'm working on another blog where I need some locked out Active Directory user accounts to work with. This script requires the RSAT tools to be installed on the workstation that it is being run from, specifically the Active Directory and Group Policy modules.

First, let me say that some assumptions are being made in this script and this is being blogged so I'll be able to figure out how I accomplished this task the next time I need to do the same thing.

The script first checks to see if a lockout policy is defined in the default domain group policy so that it doesn't try to lock out accounts if no lockout policy exists. Then it iterates through each account in a specified OU in my test Active Directory environment and tries to run the <em>Invoke-Command</em> cmdlet with that account and an invalid password against one of the servers in my test environment until the user account is locked out and then it moves onto the next account:
<pre class="wrap:false lang:ps decode:true">#Requires -Version 3.0
#Requires -Modules ActiveDirectory, GroupPolicy

if ((([xml](Get-GPOReport -Name "Default Domain Policy" -ReportType Xml)).GPO.Computer.ExtensionData.Extension.Account |
            Where-Object name -eq LockoutBadCount).SettingNumber) {

    $Password = ConvertTo-SecureString 'NotMyPassword' -AsPlainText -Force

    Get-ADUser -Filter * -SearchBase "OU=AdventureWorks Users,OU=Users,OU=Test,DC=mikefrobbins,DC=com" -Properties SamAccountName, UserPrincipalName, LockedOut |
        ForEach-Object {

            Do {

                Invoke-Command -ComputerName dc01 {Get-Process
                } -Credential (New-Object System.Management.Automation.PSCredential ($($_.UserPrincipalName), $Password)) -ErrorAction SilentlyContinue

            }
            Until ((Get-ADUser -Identity $_.SamAccountName -Properties LockedOut).LockedOut)

            Write-Output "$($_.SamAccountName) has been locked out"
        }
}</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2013/11/lockout-ad-accounts1.png"><img class="alignnone size-full wp-image-8633" alt="lockout-ad-accounts1" src="http://mikefrobbins.com/wp-content/uploads/2013/11/lockout-ad-accounts1.png" width="877" height="799" /></a>

There are several reasons why an account may never become locked out which would cause the previous script to become stuck in a runaway loop that would never finish.

Because of this I decided to write another version of this script and I've really never had a good reason to use a "For" loop in PowerShell so I decided to work that into it as well. The following script uses the "LockoutBadCount" from the "Default Domain Policy" GPO to know how many times to try the password for each account before it should become locked out, that's assuming Fine-Grained Password Policies aren't being used.
<pre class="wrap:false lang:ps decode:true">#Requires -Version 3.0
#Requires -Modules ActiveDirectory, GroupPolicy

if ($LockoutBadCount = ((([xml](Get-GPOReport -Name "Default Domain Policy" -ReportType Xml)).GPO.Computer.ExtensionData.Extension.Account |
            Where-Object name -eq LockoutBadCount).SettingNumber)) {

    $Password = ConvertTo-SecureString 'NotMyPassword' -AsPlainText -Force

    Get-ADUser -Filter * -SearchBase "OU=AdventureWorks Users,OU=Users,OU=Test,DC=mikefrobbins,DC=com" -Properties SamAccountName, UserPrincipalName, LockedOut |
        ForEach-Object {

            for ($i = 1; $i -le $LockoutBadCount; $i++) { 

                Invoke-Command -ComputerName dc01 {Get-Process
                } -Credential (New-Object System.Management.Automation.PSCredential ($($_.UserPrincipalName), $Password)) -ErrorAction SilentlyContinue            

            }

            Write-Output "$($_.SamAccountName) has been locked out: $((Get-ADUser -Identity $_.SamAccountName -Properties LockedOut).LockedOut)"
        }
}</pre>
You'll notice that Andrew0's account wasn't locked out, that's because it's disabled:

<a href="http://mikefrobbins.com/wp-content/uploads/2013/12/lockout-ad-accounts2.png"><img class="alignnone size-full wp-image-8666" alt="lockout-ad-accounts2" src="http://mikefrobbins.com/wp-content/uploads/2013/12/lockout-ad-accounts2.png" width="570" height="128" /></a>

The "if" statement portion is the really neat part of the previous script to me because it not only makes sure a "LockoutBadCount" value is defined in the "Default Domain Policy" GPO before attempting to run the code contained inside of the "if" block, but it also assigns the returned numeric value of "LockoutBadCount" (if there is one) to the $LockoutBadCount variable which is used in the "For" loop.

In the following example, the user accounts are first unlocked that were previously locked out, then the number of user accounts that are currently locked out is checked (which is zero), then the number of accounts that exists in the "AdventureWorks Users" OU is determined (there are 290 of them). The amount of time that the lockout script takes to lockout all of those 290 user accounts is determined (it takes a total of 46 seconds to lock out all of them). The number of locked out user accounts in the domain is then checked (the end result is 290 locked out user accounts in the domain):

<a href="http://mikefrobbins.com/wp-content/uploads/2013/12/lockout-ad-accounts3.png"><img class="alignnone size-full wp-image-8668" alt="lockout-ad-accounts3" src="http://mikefrobbins.com/wp-content/uploads/2013/12/lockout-ad-accounts3.png" width="877" height="359" /></a>

This is really cool stuff :-) and remember this is for testing purposes only.

<strong>Disclaimer: </strong><em>"All data and information provided on this site is for informational purposes only. Mike F Robbins (mikefrobbins.com) makes no representations as to accuracy, completeness, currentness, suitability, or validity of any information on this site and will not be liable for any errors, omissions, or delays in this information or any losses, injuries, or damages arising from its display or use. All information is provided on an as-is basis."</em>

µ