---
ID: 17013
post_title: 'PowerShell Script Module Design: Don&#8217;t Use Asterisks (*) in your Module Manifest'
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2018/09/13/powershell-script-module-design-dont-use-asterisks-in-your-module-manifest/
published: true
post_date: 2018-09-13 06:30:38
---
Using asterisks (*) in your module manifest is a bad idea no matter how you look at it. First, your module will be slower because it will have to figure out what to export. More importantly, if you use a "#Requires -Modules" statement in your functions and they're in separate PS1 files, all of the specified module's commands will show as being part of your module.

I'll pick up where I left off in one of my previous blog articles "<a href="https://mikefrobbins.com/2018/08/30/powershell-script-module-design-plaster-template-for-creating-modules/" target="_blank" rel="noopener">PowerShell Script Module Design: Plaster Template for Creating Modules</a>". I've created what I'm calling a development version of a script module using the Plaster template mentioned in that previously referenced blog article.

I still have the same PowerShell session open which has the following information stored in a hash table.
<pre class="lang:ps decode:true">$plasterParams = @{
    TemplatePath      = 'U:\GitHub\Plaster\Template'
    DestinationPath   = 'U:\GitHub'
    Name              = 'MrModuleBuildTools'
    Description       = 'PowerShell Script Module Building Toolkit'
    Version           = '1.0.0'
    Author            = 'Mike F. Robbins'
    CompanyName       = 'mikefrobbins.com'
    Folders           = 'public', 'private'
    Git               = 'Yes'
    GitRepoName       = 'ModuleBuildTools'
    Options           = ('License', 'Readme', 'GitIgnore', 'GitAttributes')
}</pre>
The following example shows where asterisks are defined by default in a PowerShell module manifest that's created with <em>New-ModuleManifest</em>.
<pre class="lang:ps decode:true ">Get-Content -Path "$ModulePath\$($plasterParams.Name).psd1" | Select-String -SimpleMatch '*'</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2018/08/no-asterisks1a.png"><img class="alignnone size-full wp-image-17015" src="https://mikefrobbins.com/wp-content/uploads/2018/08/no-asterisks1a.png" alt="" width="859" height="142" /></a>

First, let's store the path to the script module in a variable to make this process easier.
<pre class="lang:ps decode:true ">if ($plasterParams.Git) {
    $ModulePath = "$($plasterParams.DestinationPath)\$($plasterParams.GitRepoName)\$($plasterParams.Name)"
}
else {
    $ModulePath = "$($plasterParams.DestinationPath)\$($plasterParams.Name)"
}</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2018/08/no-asterisks3a.png"><img class="alignnone size-full wp-image-17018" src="https://mikefrobbins.com/wp-content/uploads/2018/08/no-asterisks3a.png" alt="" width="859" height="121" /></a>

Trying to set the values to an empty string or array results in an error.
<pre class="lang:ps decode:true">Update-ModuleManifest -Path "$ModulePath\$($plasterParams.Name).psd1" -FunctionsToExport '' -AliasesToExport '' -VariablesToExport '' -CmdletsToExport ''
Update-ModuleManifest -Path "$ModulePath\$($plasterParams.Name).psd1" -FunctionsToExport @() -AliasesToExport @() -VariablesToExport @() -CmdletsToExport @()</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2018/08/no-asterisks5a.png"><img class="alignnone size-full wp-image-17020" src="https://mikefrobbins.com/wp-content/uploads/2018/08/no-asterisks5a.png" alt="" width="859" height="299" /></a>

Placing an empty array inside of quotes seems to work fine.
<pre class="lang:ps decode:true ">if (Test-Path -Path $ModulePath -PathType Container) {
    Update-ModuleManifest -Path "$ModulePath\$($plasterParams.Name).psd1" -FunctionsToExport '@()' -AliasesToExport '@()' -VariablesToExport '@()' -CmdletsToExport '@()'
}
else {
    Write-Warning -Message "'$ModulePath' path does not exist or is not valid!"
}</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2018/08/no-asterisks4a.png"><img class="alignnone size-full wp-image-17019" src="https://mikefrobbins.com/wp-content/uploads/2018/08/no-asterisks4a.png" alt="" width="859" height="129" /></a>

All of the ones with asterisks are now gone, but strangely enough the quotes themselves also end up as part of the value, although this seems to achieve the desired result.
<pre class="lang:ps decode:true">Get-Content -Path "$ModulePath\$($plasterParams.Name).psd1" | Select-String -SimpleMatch '*'
Get-Content -Path "$ModulePath\$($plasterParams.Name).psd1" | Select-String -SimpleMatch '@()'</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2018/09/no-asterisks6a.png"><img class="alignnone size-full wp-image-17061" src="https://mikefrobbins.com/wp-content/uploads/2018/09/no-asterisks6a.png" alt="" width="859" height="287" /></a>

I wrote a function a while back (<em>Get-MrFunctionsToExport</em>) that's part of <a href="https://github.com/mikefrobbins/PowerShell" target="_blank" rel="noopener">my MrToolkit module</a> that I'll use to update the <em>FunctionsToExport</em> section of the manifest. That function gets the list of a module's functions based on the file names of the individual PS1 files and it just so happens that I name my files the same as the function within them. There's a better way to do this and I'll be updating that function along with moving it to this new <a href="https://github.com/mikefrobbins/ModuleBuildTools" target="_blank" rel="noopener">MrModuleBuildTools module</a>, but at this point I have a chicken and egg problem (Which one came first?), because I'm building my module build tools that will be used to build my modules.
<pre class="lang:ps decode:true ">Get-MrFunctionsToExport -Path $ModulePath\public\ -Simple</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2018/09/no-asterisks7a.png"><img class="alignnone size-full wp-image-17065" src="https://mikefrobbins.com/wp-content/uploads/2018/09/no-asterisks7a.png" alt="" width="859" height="142" /></a>

Use the function shown in the previous example to update the <em>FunctionsToExport</em> section in the module manifest.
<pre class="lang:ps decode:true">Update-ModuleManifest -Path "$ModulePath\$($plasterParams.Name).psd1" -FunctionsToExport (Get-MrFunctionsToExpor
t -Path $ModulePath\public -Simple)</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2018/09/no-asterisks8a.png"><img class="alignnone size-full wp-image-17067" src="https://mikefrobbins.com/wp-content/uploads/2018/09/no-asterisks8a.png" alt="" width="859" height="69" /></a>

That did indeed update the <em>FunctionsToExport</em> section.
<pre class="lang:ps decode:true ">Get-Content -Path "$ModulePath\$($plasterParams.Name).psd1"</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2018/09/no-asterisks9a.png"><img class="alignnone size-full wp-image-17069" src="https://mikefrobbins.com/wp-content/uploads/2018/09/no-asterisks9a.png" alt="" width="859" height="168" /></a>

Be sure to read the comments listed in the module manifest.
<blockquote>Functions to export from this module, <span style="color: #ff0000;">for best performance, do not use wildcards</span> and do not delete the entry, use an empty array if there are no functions to export.</blockquote>
One thing I've never quite understood about Microsoft products is they tell you not to set something a certain way and then they set it that way by default or they say the optimum setting is "X" and then they set that setting to "Y" by default. Why can't they just set it to the optimum setting by default? and if it shouldn't be set a certain way, then why set it that way by default?

I also have a function to test to make sure the functions listed in <em>FunctionsToExport</em> match the functions that I want exported (which will also be updated and moved).
<pre class="lang:ps decode:true">Test-MrFunctionsToExport -ManifestPath $ModulePath\$($plasterParams.Name).psd1</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2018/09/no-asterisks10a.png"><img class="alignnone size-full wp-image-17066" src="https://mikefrobbins.com/wp-content/uploads/2018/09/no-asterisks10a.png" alt="" width="859" height="135" /></a>

<a href="https://mikefrobbins.com/2017/01/12/beware-of-the-powershell-update-modulemanifest-function/" target="_blank" rel="noopener">Beware of <em>Update-ModuleManifest</em></a> because it does things you don't ask it to. In other words, always test anytime you make updates to the manifest because it updates things which can break your modules. It's like a mechanic who tightens one bolt and loosens three more or maybe like playing Russian roulette since it only breaks things sometimes depending on what else you're updating.

The functions listed in <em>FunctionsToExport</em> should only be the ones that you want made publicly available. Don't add private functions to <em>FunctionsToExport</em>. If you're using separate PS1 files and dot-sourcing them from the PSM1 file, then you do need to dot-source both the public and private functions. Here's what my PSM1 template file looks like that's part of my Plaster template.
<pre class="lang:ps decode:true">#Dot source all functions in all ps1 files located in the module's public and private folder
Get-ChildItem -Path $PSScriptRoot\public\*.ps1, $PSScriptRoot\private\*.ps1 -Exclude *.tests.ps1, *profile.ps1 |
ForEach-Object {
    . $_.FullName
}</pre>
I exclude tests and profiles just in case any of them happen to be saved in one of the folders. Tests shouldn't be in either one as they'll live in their own folder. Once upon a time, I had a problem where my profile was reloading because of re-importing a module which almost drove me crazy until I realized what was happening.

Leave your thoughts, comments, and/or questions as a comment to this blog article.

µ