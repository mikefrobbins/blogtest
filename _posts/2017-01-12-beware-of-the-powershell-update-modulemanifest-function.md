---
ID: 14903
post_title: >
  Beware of the PowerShell
  Update-ModuleManifest Function
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2017/01/12/beware-of-the-powershell-update-modulemanifest-function/
published: true
post_date: 2017-01-12 07:30:12
---
I recently presented a session for the <a href="http://mspsug.com/" target="_blank">Mississippi PowerShell User Group</a> on <a href="http://mspsug.com/2017/01/09/mspsug-january-2017-virtual-meeting-powershell-non-monolithic-script-module-design/" target="_blank">PowerShell Non-Monolithic Script Module Design</a>. While preparing for that session, I discovered that a problem I had previously experienced with <em>Update-ModuleManifest</em> when trying to update the <em>FunctionsToExport</em> section when <em>FormatsToProcess</em> is specified appeared to be resolved in PowerShell version 5.1 (I'm running build 14393). The details of this bug can be found <a href="https://windowsserver.uservoice.com/forums/301869-powershell/suggestions/12479136--bug-update-modulemanifest-fails-if-typestoproces" target="_blank">here</a>. I also noticed that <a href="https://twitter.com/getch3028" target="_blank">Nicholas Getchell</a> had written about <a href="https://powershell.getchell.org/2016/06/01/update-modulemanifest-back/" target="_blank">this problem being resolved on his blog</a>.

What I discovered is that <em>Update-ModuleManifest</em> does a little too much updating which can cause problems. To demonstrate this problem, I'll create a script module named MyModule containing the following code:
<pre class="lang:ps decode:true " title="MyModule.psm1">Get-ChildItem -Path $PSScriptRoot\*.ps1 -Exclude *.tests.ps1, *profile.ps1 |
ForEach-Object {
    . $_.FullName
}</pre>
The previous code is saved as MyModule.psm1 in the following folder: $env:ProgramFiles\WindowsPowerShell\Modules\MyModule

A function named <em>Get-MrSystemInfo</em> is created:
<pre class="lang:ps decode:true" title="Get-MrSystemInfo.ps1">#Requires -Modules CimCmdlets
function Get-MrSystemInfo {
    Get-CimInstance -ClassName Win32_OperatingSystem |
    Select-Object -ExpandProperty Caption
}</pre>
That function is saved as <em>Get-MrSystemInfo.ps1</em> in the same folder as the PSM1 file.

And finally a module manifest is created:
<pre class="lang:ps decode:true" title="MyModule.psd1">New-ModuleManifest -Path $env:ProgramFiles\WindowsPowerShell\Modules\MyModule\MyModule.psd1 -RootModule MyModule -Author 'Mike F Robbins' -Description 'MyModule' -CompanyName 'mikefrobbins.com'</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2017/01/beware-updatemodulemanifest3a.png"><img class="alignnone size-full wp-image-14908" src="http://mikefrobbins.com/wp-content/uploads/2017/01/beware-updatemodulemanifest3a.png" alt="" width="859" height="68" /></a>

When designing modules with a non-monolithic approach and specifying a requires statement for a module in one of the functions, as shown in the <em>Get-MrSystemInfo</em> example, causes all of the functions that are part of the specified module to show up as being part of your module.

In this scenario, the CimCmdlets are showing up as being part of MyModule:
<pre class="lang:ps decode:true">Get-Command -Module MyModule</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2017/01/beware-updatemodulemanifest1a.png"><img class="alignnone size-full wp-image-14904" src="http://mikefrobbins.com/wp-content/uploads/2017/01/beware-updatemodulemanifest1a.png" alt="" width="859" height="297" /></a>

I've written about this problem in <a href="http://mikefrobbins.com/2016/05/05/dont-use-default-manifest-settings-when-dot-sourcing-functions-in-ps1-files-from-a-powershell-script-module/" target="_blank">a previous blog article</a> and it's easy enough to resolve by removing the '*' from the <em>CmdletsToExport</em> section and replace the '*' in the <em>FunctionsToExport</em> section of the module manifest with your actual function names.

The default settings in the module manifest that was previous created in this blog article:
<pre class="lang:ps decode:true "># Functions to export from this module, for best performance, do not use wildcards and do not delete the entry, use an empty array if there are no functions to export.
FunctionsToExport = '*'

# Cmdlets to export from this module, for best performance, do not use wildcards and do not delete the entry, use an empty array if there are no cmdlets to export.
CmdletsToExport = '*'

# Variables to export from this module
# VariablesToExport = @()

# Aliases to export from this module, for best performance, do not use wildcards and do not delete the entry, use an empty array if there are no aliases to export.
AliasesToExport = '*'</pre>
The problem is that when you run <em>Update-ModuleManifest</em> to add the function name to <em>FunctionsToExport</em>:
<pre class="lang:ps decode:true">Update-ModuleManifest -Path $env:ProgramFiles\WindowsPowerShell\Modules\MyModule\MyModule.psd1 -FunctionsToExpor
t 'Get-MrSystemInfo'</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2017/01/beware-updatemodulemanifest2a.png"><img class="alignnone size-full wp-image-14905" src="http://mikefrobbins.com/wp-content/uploads/2017/01/beware-updatemodulemanifest2a.png" alt="" width="859" height="67" /></a>

It not only changes the '*' in the <em>FunctionsToExport</em> section of the manifest to the function name that was specified, but it also updates <em>CmdletsToExport</em> to contain all of the cmdlets contained in the CimCmdlets module and it removes the '*' from the <em>AliasesToExport</em> section of the manifest.
<pre class="lang:ps decode:true "># Functions to export from this module, for best performance, do not use wildcards and do not delete the entry, use an empty array if there are no functions to export.
FunctionsToExport = 'Get-MrSystemInfo'

# Cmdlets to export from this module, for best performance, do not use wildcards and do not delete the entry, use an empty array if there are no cmdlets to export.
CmdletsToExport = 'New-CimInstance', 'Set-CimInstance', 'Get-CimSession', 
               'Get-CimInstance', 'Get-CimAssociatedInstance', 'Get-CimClass', 
               'New-CimSession', 'Remove-CimInstance', 'Remove-CimSession', 
               'New-CimSessionOption', 'Invoke-CimMethod', 'Export-BinaryMiLog', 
               'Register-CimIndicationEvent', 'Import-BinaryMiLog'

# Variables to export from this module
# VariablesToExport = @()

# Aliases to export from this module, for best performance, do not use wildcards and do not delete the entry, use an empty array if there are no aliases to export.
AliasesToExport = @()</pre>
<em>Update-ModuleManifest</em> had one action to perform and only one action which was to update the <em>FunctionsToExport</em> section in the module manifest as previous specified.

To workaround this problem in this scenario, you can change <em>CmdletsToExport</em> to @() prior to running <em>Update-ModuleManifest</em> and it won't be changed. You could also specify the <em>-CmdletsToExport</em> parameter with the @() value when creating the module manifest to prevent this problem from occurring.

My recommendation is not to use '*' anywhere in your module manifest &lt;period&gt;.

µ