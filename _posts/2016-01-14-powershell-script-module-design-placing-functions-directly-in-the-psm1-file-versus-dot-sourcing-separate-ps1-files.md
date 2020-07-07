---
ID: 12976
post_title: '#PowerShell Script Module Design: Placing functions directly in the PSM1 file versus dot-sourcing separate PS1 files'
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2016/01/14/powershell-script-module-design-placing-functions-directly-in-the-psm1-file-versus-dot-sourcing-separate-ps1-files/
published: true
post_date: 2016-01-14 07:30:22
---
So you've transitioned from writing PowerShell one-liners and scripts to creating reusable tools with <a href="http://mikefrobbins.com/2015/06/19/walkthrough-an-example-of-how-i-write-powershell-functions/" target="_blank">functions</a> and <a href="http://mikefrobbins.com/2013/07/04/how-to-create-powershell-script-modules-and-module-manifests/" target="_blank">script modules</a>. You may have started off by simply placing your functions in a PS1 file and dot-sourcing it. That leaves a lot to be desired though since it's a manual process and even if you've added some code to your profile to accomplish that task, the experience still isn't as good as it could be.

Many of the short comings can be alleviated by simply placing your functions into a PSM1 script module file, creating a PSD1 module manifest file, and placing those files into a folder with the same base name as those files underneath one of the modules folders located in your <em>$env:PSModulePath</em>.
<pre class="lang:ps decode:true ">$env:PSModulePath -split ';'</pre>
<img class="alignnone size-full wp-image-12983" src="http://mikefrobbins.com/wp-content/uploads/2016/01/module-design1a.png" alt="module-design1a" width="859" height="107" />

The first path shown in the previous example is the current user module path. This will be different from the user who is currently logged into the computer if you've specified an alternate user when running an elevated PowerShell session. The second path is the all users path for modules which was added in PowerShell version 4. The third path (under Windows\System32) is a location where only Microsoft should place modules (per the PowerShell team).

The module manifest contains metadata about your script module and as a best practice you should always create a <a href="http://mikefrobbins.com/2013/07/04/how-to-create-powershell-script-modules-and-module-manifests/" target="_blank">module manifest</a>. If you plan to use <em><a href="http://mikefrobbins.com/2015/04/23/powershellget-the-big-easy-way-to-discover-install-and-update-powershell-modules/" target="_blank">PowerShellGet</a></em> to upload modules to the <a href="http://www.powershellgallery.com/" target="_blank">PowerShell Gallery</a> or to an internal private repository, there is certain metadata that is required.

The problem that I've encountered with this solution is when one large PSM1 script module file contains lots of functions it becomes difficult to maintain which has lead me to another solution. Placing your functions into PS1 files might not be such a bad idea after all but instead of manually dot-sourcing them, dot-source them from the PSM1 script module file. If each function is placed in its own PS1 file, that would make the functions a lot easier to maintain and it would also reduce the conflicts that are created when different functions in the same module are modified by different people and they're checked into your source control system.

The PS1 files that contain the functions should be placed into the module folder with the PSM1 and PSD1 files to keep all of the files that are associated with the module grouped together. As I previously mentioned, dot-source them from the PSM1 file to make it work just as if the functions were placed directly in the PSM1 file.

Here's the contents of a PSM1 file where I'm dot-sourcing all of the functions that are contained in the PS1 files which are located in the same folder as the script module itself:
<pre class="lang:ps decode:true ">Get-ChildItem -Path $PSScriptRoot\*.ps1 |
ForEach-Object {
    . $_.FullName
}</pre>
One problem I've run into with this solution is that the functions don't auto-load when attempting to use them and I certainly don't want to go back to the dark ages where modules had to be manually imported.

The problem seems to be that using <em>Export-ModuleMember -Function '*'</em> doesn't work in the PSM1 file when dot-sourcing external functions. Specifying the individual function names themselves instead of an asterisk works without issue though. To be honest with you, there's no reason to use <em>Export-ModuleMember</em> anyway since you should always have a module manifest and the functions to export can be controlled with it.

Specifying <em>FunctionsToExport = '*'</em> in the module manifest doesn't work either and even if it did, it would be slower than specifying the function names individually. So I've specified the functions names as shown in the following snippet of code from one of my module manifest files:
<pre class="lang:ps decode:true "># Functions to export from this module
FunctionsToExport = 'Backup-MrSqlDatabase', 'Connect-MrSqlDatabase', 'Find-MrSqlDatabaseChange', 'Get-MrSqlDatabaseBackupInfo', 'Get-MrSqlDatabaseIndexFragmentation',
                    'Get-MrSqlDatabaseInfo', 'Get-MrSqlInstance', 'Import-MrSqlModule', 'Invoke-MrSqlDataReader'
</pre>
Specifying the individual function names seems to work without issue with the module auto-loading functionality that was introduced with PowerShell version 3.

Have you been down this path before? If so, I'd like your input on this subject. Feel free to leave a comment so others can learn from your experiences.

µ