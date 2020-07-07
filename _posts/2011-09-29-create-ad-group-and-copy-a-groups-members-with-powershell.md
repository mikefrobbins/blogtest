---
ID: 2913
post_title: 'Create AD Group and Copy a Group&#8217;s Members with PowerShell'
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2011/09/29/create-ad-group-and-copy-a-groups-members-with-powershell/
published: true
post_date: 2011-09-29 07:30:19
---
This week, I was asked if I could export a list of users who were members of a specific group in Active Directory. My Question: What's this list for? Answer: We're working on a project that requires us to create a new security group in Active Directory and we're going to add all the users on the list to the new group. I determined that this new group really was necessary. My response: I can do even better than providing you guys with a list. I can create the new AD group, output a list of users, and import them into the new group.

I had previously created a couple of PowerShell scripts that would help me get started. One of them created an AD group and the other added a single user to an AD group. I combined my existing scripts and nested the add user to group portion in a for each loop:
<pre class="lang:ps decode:true">'Define variables'
$NewGrpName = "NewADGroup"
$GrpScope = "Global"
$Description = "New AD Group"
$GrpCat = "Security"
$Path = "OU=security,OU=groups,OU=test,DC=mikefrobbins,DC=com"
$ExistingGrpName = "ExistingADGroup"
$Domain = "mikefrobbins.com"

'Create a new AD group using the variables as parameters'
New-ADGroup -Name $NewGrpName  -GroupScope $GrpScope -Description $Description `
-GroupCategory $GrpCat -Path $Path

'Obtain a list of users from an existing AD Group and store them in a variable'
$UserNames = Get-ADGroupMember $ExistingGrpName | Get-ADUser | Select-Object `
-ExpandProperty SamAccountName

'Add each user to the newly created AD group'
ForEach ($UserName in $UserNames) {
$User = [ADSI]("WinNT://$Domain/$UserName")
$Grp = [ADSI]("WinNT://$Domain/$NewGrpName")
$Grp.PSBase.Invoke("Add",$User.PSBase.Path)
}

'Output a list of users that are now members of the new group'
Get-ADGroupMember $NewGrpName | Get-ADUser | Select-Object Name</pre>
PowerShell to the Rescue!

Here's an updated script based on Jeffery Hicks comments. You've gotta love the PowerShell community.
<pre class="lang:ps decode:true">$newGrpName = "NewADGroup"
$grpScope = "Global"
$description = "New AD Group"
$grpCat = "Security"
$path = "OU=security,OU=groups,OU=test,DC=mikefrobbins,DC=com"
$existingGrpName = "ExistingADGroup"

New-ADGroup -name $newGrpName -GroupScope $grpScope -Description $description -GroupCategory `
$grpCat -Path $path -PassThru  | Add-ADGroupMember -Members (Get-ADGroupMember $existingGrpName) `
-PassThru | Get-ADGroupMember | Select Name</pre>
<span style="font-size: small;"><span class="Apple-style-span" style="line-height: 24px; white-space: normal;">µ</span></span>