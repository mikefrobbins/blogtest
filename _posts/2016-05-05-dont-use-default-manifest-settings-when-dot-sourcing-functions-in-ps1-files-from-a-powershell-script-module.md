---
ID: 13910
post_title: 'Don&#8217;t use Default Manifest Settings when Dot-Sourcing Functions in PS1 Files from a PowerShell Script Module'
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2016/05/05/dont-use-default-manifest-settings-when-dot-sourcing-functions-in-ps1-files-from-a-powershell-script-module/
published: true
post_date: 2016-05-05 07:30:44
---
I briefly mentioned and demonstrated something similar to this at the end of one of my sessions at the PowerShell and DevOps Global Summit 2016. Since then, I've tested more which has led to a better solution.

We've all been taught that it's best practice to use a <a href="https://technet.microsoft.com/en-us/library/hh847765.aspx" target="_blank"><em>#Requires</em> statement</a> in our functions that specifies the required PowerShell version along with any module dependencies, but following this best practice has one unexpected side effect when dot-sourcing functions in PS1 files from a PSM1 script module.
<pre class="lang:ps decode:true">Get-ChildItem -Path .\GitHub\AWS\MrAWS\</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2016/04/requires-modules1a.jpg"><img class="alignnone size-full wp-image-13915" src="http://mikefrobbins.com/wp-content/uploads/2016/04/requires-modules1a.jpg" alt="requires-modules1a" width="859" height="225" /></a>

Simple enough, right? My MrAWS module folder contains two functions that are dot-sourced from the PSM1 script module file. The PSM1 file contains the following code:
<pre class="lang:ps decode:true ">Get-ChildItem -Path $PSScriptRoot\*.ps1 -Exclude *.tests.ps1, *profile.ps1 |
ForEach-Object {
    . $_.FullName
}</pre>
When the module is imported, the correct functions show up as being exported:
<pre class="lang:ps decode:true">Get-Module -Name MrAWS
Get-Command -Module MrAWS</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2016/04/requires-modules2a.jpg"><img class="alignnone size-full wp-image-13916" src="http://mikefrobbins.com/wp-content/uploads/2016/04/requires-modules2a.jpg" alt="requires-modules2a" width="859" height="226" /></a>

Add a dependency for the AWSPowerShell module via a Requires statement in one or more of the PS1 files:
<pre class="lang:ps decode:true">#Requires -Modules AWSPowerShell</pre>
Re-import the module and now all of the cmdlets that are part of the AWSPowerShell module show as being part of the MrAWS module:
<pre class="lang:ps decode:true ">Get-Module -Name MrAWS
Get-Command -Module MrAWS</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2016/04/requires-modules3a.jpg"><img class="alignnone size-full wp-image-13917" src="http://mikefrobbins.com/wp-content/uploads/2016/04/requires-modules3a.jpg" alt="requires-modules3a" width="859" height="261" /></a>

Although this isn't technically hurting anything, it's not desirable to have all of them show up as being part of my module. This problem also isn't limited to the modules shown in this blog article as it will occur with any module where the default module manifest settings are used, functions in PS1 files are dot-sourced from the module's PSM1 file, and a Requires statement is specified with module dependencies.

Initially, I determined that instead of setting the module dependency using a Requires statement in the PS1 file, it could be set using the RequiredModules section in the module manifest:
<pre class="lang:ps decode:true "># Modules that must be imported into the global environment prior to importing this module
RequiredModules = 'AWSPowerShell'</pre>
I placed a comment in the PS1 file but that still wasn't the optimum solution because it meant someone would have to modify the PS1 file(s) if they only downloaded a function or two from my GitHub repository instead of the entire module otherwise the dependencies wouldn't be set for them.

What I discovered is that by default an asterisk (*) is set in several sections of a module manifest when it's created with the <em>New-ModuleManifest</em> cmdlet:
<pre class="lang:ps decode:true"># Cmdlets to export from this module
CmdletsToExport = '*'</pre>
My scenario is limited to the <em>CmdletsToExport</em> section, but you may encounter this problem with functions, variables, and/or aliases if any exists in the modules you're specifying with the Requires statement.

I also noticed that if these lines were commented out or removed from the manifest altogether, all of the cmdlets were still exported so the default when not specified seems to export all. The only way I've found to resolve this problem in my scenario while meeting all of my objectives and using best practices such as setting module dependencies with a Requires statement is to simply remove the asterisk so no cmdlets are exported:
<pre class="lang:ps decode:true"># Cmdlets to export from this module
CmdletsToExport = ''</pre>
This allows a Requires statement in the PS1 file to be set with the proper module dependencies while not exporting any of the cmdlets from the specified modules. If you had cmdlets to export or for some reason wanted to export some of the cmdlets from the required modules, you could specify them individually. That may sound difficult to maintain but not if you write a tool similar to my <em>Get-MrFunctionsToExport</em> function as shown in my "<em><a href="http://mikefrobbins.com/2016/04/21/keeping-track-of-powershell-functions-in-script-modules-when-dot-sourcing-ps1-files/" target="_blank">Keeping Track of PowerShell Functions in Script Modules when Dot-Sourcing PS1 Files</a></em>" blog article. I also recommend writing tests to automate the process of making sure your modules only export what's expected. You can find an example in my "<em><a href="http://mikefrobbins.com/2016/04/14/write-dynamic-unit-tests-for-your-powershell-code-with-pester/" target="_blank">Write Dynamic Unit Tests for your PowerShell Code with Pester</a></em>" blog article.

µ