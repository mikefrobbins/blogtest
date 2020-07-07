---
ID: 10127
post_title: >
  Create Active Directory Users Home
  Folder and Assign Permissions with
  PowerShell
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2014/06/26/create-active-directory-users-home-folder-and-assign-permissions-with-powershell/
published: true
post_date: 2014-06-26 07:30:51
---
The following function is a work in progress, but I thought I would go ahead and share it.

This function requires a module named PowerShellAccessControl that was created by Rohn Edwards which is <a href="http://gallery.technet.microsoft.com/scriptcenter/PowerShellAccessControl-d3be7b83" target="_blank">downloadable</a> from the TechNet Script Repository. The version 3.0 beta revision of his module which is also downloadable on that same page is what was used to test the examples shown in this blog article.
<pre class="lang:ps decode:true crayon-selected" title="Create-MrUserFolder">#Requires -Version 3.0
#Requires -Modules ActiveDirectory, PowerShellAccessControl

function Create-MrUserFolder {

&lt;#
.SYNOPSIS
    Creates an Active Directory user's home folders and assigns modify permission
    to the user and read permission to their manager.
 
.DESCRIPTION
    Create-MrUserFolder is a function that is designed to create an Active Directory
    user's home folder and assign full permissions to administrators and system,
    modify to the user and read access to the user's manager.
 
.PARAMETER UserName
    The Active Directory user account object for the user to create a folder for.

.PARAMETER Path
    The root path location where to create the user folder.
 
.EXAMPLE
     Get-ADUser -Identity MikeFRobbins -Properties Manager |
     Create-MrUserFolder -Path 'S:\Users'
 
.INPUTS
    Microsoft.ActiveDirectory.Management.ADAccount
 
.OUTPUTS
    System.IO.DirectoryInfo
 
.NOTES
    Author:  Mike F Robbins
    Website: http://mikefrobbins.com
    Twitter: @mikefrobbins
#&gt;

    [CmdletBinding()]
    param (
        [ValidateScript({Test-Path -Path $_ -PathType Container})]
        [string]$Path,

        [Parameter(Mandatory, 
                   ValueFromPipeline)]
        [Microsoft.ActiveDirectory.Management.ADAccount[]]$UserName
    )
    
    BEGIN {    
        $DefaultUsers = "$env:COMPUTERNAME\Administrators", 'System'
    }    

    PROCESS {

        foreach ($u in $UserName) {

            $UserPath = Join-Path -Path $Path -ChildPath $u.SamAccountName

            if (-not(Test-Path $UserPath -PathType Container)) {
                New-Item -Path $UserPath -ItemType Directory
            }
            else {
                Write-Warning -Message "'$UserPath' already exists."
            }
             
            $SecurityDescriptor = Get-SecurityDescriptor -Path $UserPath

            foreach ($d in $DefaultUsers) {
                $SecurityDescriptor | Add-AccessControlEntry -Principal $d -FolderRights FullControl -Apply -Force
            }

            $SecurityDescriptor | Add-AccessControlEntry -Principal $($u.UserPrincipalName) -FolderRights Modify -Apply -Force

            if ($($u.Manager)) {
                $SecurityDescriptor | Add-AccessControlEntry -Principal $((Get-ADUser -Identity $u.Manager).UserPrincipalName) -FolderRights ReadAndExecute -Apply -Force
            }

            Disable-AclInheritance -Path $UserPath -Force

        }
    }
}</pre>
The following example demonstrates creating  home folders and assigning permissions to those folders for all of the users in the Northwind organizational unit in my test Active Directory environment:
<pre class="lang:ps decode:true">Invoke-Command -ComputerName DC01 {
    Get-ADUser -Filter * -SearchBase 'OU=Northwind Users,OU=Users,OU=Test,DC=mikefrobbins,DC=com' -Properties Manager |
    Create-MrUserFolder -Path 'S:\Users'
}</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/06/homefolder1.jpg"><img class="alignnone size-full wp-image-10128" src="http://mikefrobbins.com/wp-content/uploads/2014/06/homefolder1.jpg" alt="homefolder1" width="877" height="323" /></a>

Now to see if the permissions are correct, once again using a function from Rohn's module:
<pre class="lang:ps decode:true">Get-ChildItem -Path S:\Users | Get-AccessControlEntry | Format-Table -AutoSize</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/06/homefolder2.jpg"><img class="alignnone size-full wp-image-10132" src="http://mikefrobbins.com/wp-content/uploads/2014/06/homefolder2.jpg" alt="homefolder2" width="877" height="931" /></a>

They look correct based on the previous information and verifying it against who each user's manager is in Active Directory:
<pre class="lang:ps decode:true">Get-ADUser -Filter * -SearchBase 'OU=Northwind Users,OU=Users,OU=Test,DC=mikefrobbins,DC=com' -Properties Manager, Title |
Select-Object -Property Name, Title, @{label='Manager';expression={$_.manager -replace '^CN=|\,.*$'}} |
Sort-Object -Property Manager, Name</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/06/homefolder3.jpg"><img class="alignnone size-full wp-image-10133" src="http://mikefrobbins.com/wp-content/uploads/2014/06/homefolder3.jpg" alt="homefolder3" width="877" height="276" /></a>

Did you notice the regular expression that I used in the previous example to extract the managers name from the distinguished name that is returned by default?

The cool thing is you can actually create an Active Directory user account, create their home directory, and assign the proper permissions all in one command when the <em>PassThru</em> parameter of <em>Get-ADUser</em> is used to pass the user information to my function:
<pre class="lang:ps decode:true">Invoke-Command -ComputerName DC01 {
    New-ADUser -Name 'John Doe' -SamAccountName 'jdoe' -UserPrincipalName 'jdoe@mikefrobbins.com' -PassThru |
    Create-MrUserFolder -Path 'S:\Users'
}</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/06/homefolder4.jpg"><img class="alignnone size-full wp-image-10135" src="http://mikefrobbins.com/wp-content/uploads/2014/06/homefolder4.jpg" alt="homefolder4" width="877" height="230" /></a>

And to validate the permissions on the users folder were set correctly:
<pre class="lang:ps decode:true">Get-AccessControlEntry S:\Users\jdoe</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/06/homefolder5.jpg"><img class="alignnone size-full wp-image-10136" src="http://mikefrobbins.com/wp-content/uploads/2014/06/homefolder5.jpg" alt="homefolder5" width="877" height="217" /></a>

If you enjoyed this blog article and you live within driving distance of Baton Rouge Louisiana, you should consider attending <a href="http://www.sqlsaturday.com/324/eventhome.aspx" target="_blank">SQL Saturday #324</a> on August 2nd. There's an all day PowerShell track and I'll be presenting two of the PowerShell sessions in that track. Rohn Edwards will also be presenting a session, his is on PowerShell and Access Control.

µ