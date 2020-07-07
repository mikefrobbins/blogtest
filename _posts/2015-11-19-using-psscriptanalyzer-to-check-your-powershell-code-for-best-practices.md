---
ID: 12750
post_title: >
  Using PSScriptAnalyzer to check your
  PowerShell code for best practices
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2015/11/19/using-psscriptanalyzer-to-check-your-powershell-code-for-best-practices/
published: true
post_date: 2015-11-19 07:30:43
---
Are you interested in learning if your PowerShell code follows what the community considers to be best practices? Well, you're in luck because Microsoft has a new open source PowerShell module named PSScriptAnalyzer that does just that. According to <a href="http://github.com/powershell/psscriptanalyzer" target="_blank">the GitHub page for PSScriptAnalyzer</a>, it's a static code checker for PowerShell modules and scripts that checks the quality of PowerShell code by running a set of rules that are based on best practices identified by the PowerShell team and community. In addition to testing your code with the PSScriptAnalyzer built in rules, you can also create your own custom rules if your organization has specific guidelines for writing PowerShell code.

Installing the PSScriptAnalyzer module is easy when installing it from the PowerShell gallery:
<pre class="lang:ps decode:true">Install-Module -Name PSScriptAnalyzer -Repository PSGallery -Force
Get-Module -Name PSScriptAnalyzer -ListAvailable</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/11/scriptanalyzer1a.jpg"><img class="alignnone size-full wp-image-12754" src="http://mikefrobbins.com/wp-content/uploads/2015/11/scriptanalyzer1a.jpg" alt="scriptanalyzer1a" width="859" height="191" /></a>

I used the <em>Get-Module</em> cmdlet after installing the PSScriptAnalyzer module to verify that it was indeed installed.

In the following example, I'll use Script Analyzer to test <a href="http://github.com/mikefrobbins/DSC/tree/master/MrDSC" target="_blank">my MrDSC module</a> that I have on GitHub to determine if I've missed following any of the best practices. I'll exclude the PSProvideDefaultParameterValue rule since <a href="http://github.com/PowerShell/PSScriptAnalyzer/issues/360" target="_blank">there's a bug with it</a> that I've logged on GitHub.
<pre class="lang:ps decode:true">Invoke-ScriptAnalyzer -Path "$env:ProgramFiles\WindowsPowerShell\Modules\MrDSC" -ExcludeRule PSProvideDefaultParameterValue</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/11/scriptanalyzer2a.jpg"><img class="alignnone size-full wp-image-12757" src="http://mikefrobbins.com/wp-content/uploads/2015/11/scriptanalyzer2a.jpg" alt="scriptanalyzer2a" width="859" height="240" /></a>

In the previous example, it did catch several items in my module that I need to revisit. An unused variable that can be removed from my code, a plural noun that should be singular, and the use of a positional parameter.

It's very easy to over look things so I'm glad to see something like PSScriptAnalyzer that I can use to validate that my PowerShell code meets the industry standards for best practices before sharing it online. Speaking of sharing PowerShell code, I typically share my code on GitHub and I will continue to use GitHub as a source control and collaboration tool moving forward, but I also plan to start using the <a href="http://www.powershellgallery.com/" target="_blank">PowerShell Gallery</a> as a method of distribution for the stable versions of my PowerShell modules if for no other reason than for the ease of installation.

A module containing <a href="http://github.com/PowerShell/PSScriptAnalyzer/tree/development/Tests/Engine/CommunityAnalyzerRules" target="_blank">sample rules</a> can be found on GitHub as well. I'll use those rules to check my module also:
<pre class="lang:ps decode:true">Invoke-ScriptAnalyzer -Path "$env:ProgramFiles\WindowsPowerShell\Modules\MrDSC" -CustomizedRulePath C:\tmp\CommunityAnalyzerRules -ExcludeRule PSProvideDefaultParameterValue</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/11/scriptanalyzer3a.jpg"><img class="alignnone size-full wp-image-12763" src="http://mikefrobbins.com/wp-content/uploads/2015/11/scriptanalyzer3a.jpg" alt="scriptanalyzer3a" width="859" height="393" /></a>

Of course you can place anything you want in your own custom rules and the ones that are informational are worth reading through but action on those isn't always necessary.

If you have the <a href="http://www.powertheshell.com/isesteroids/" target="_blank">ISESteroids</a> add-on for the PowerShell ISE (Integrated Scripting Environment), you can also enable real time code analysis using the script analyzer rules:

<a href="http://mikefrobbins.com/wp-content/uploads/2015/11/scriptanalyzer4a.jpg"><img class="alignnone size-full wp-image-12776" src="http://mikefrobbins.com/wp-content/uploads/2015/11/scriptanalyzer4a.jpg" alt="scriptanalyzer4a" width="722" height="202" /></a>

If you don't have ISESteroids, you can still take advantage of the script analyzer rules in the default ISE with the script analyzer add-on:
<pre class="lang:ps decode:true">Install-Module -Name ISEScriptAnalyzerAddOn -Force
Get-Module -Name ISEScriptAnalyzerAddOn -ListAvailable</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/11/scriptanalyzer5a.jpg"><img class="alignnone size-full wp-image-12777" src="http://mikefrobbins.com/wp-content/uploads/2015/11/scriptanalyzer5a.jpg" alt="scriptanalyzer5a" width="859" height="189" /></a>

Once the script analyzer add-on is enabled, the add-on will be displayed on the far right side in the ISE:

<a href="http://mikefrobbins.com/wp-content/uploads/2015/11/scriptanalyzer6a.jpg"><img class="alignnone size-full wp-image-12778" src="http://mikefrobbins.com/wp-content/uploads/2015/11/scriptanalyzer6a.jpg" alt="scriptanalyzer6a" width="551" height="254" /></a>

I also noticed that <a href="http://twitter.com/NanaLakshmanan" target="_blank">Nana Lakshmanan</a> from the PowerShell team has a script analyzer custom rules module on the PowerShell Gallery:
<pre class="lang:ps decode:true ">Find-Module -Name nScriptAnalyzerRules | Format-List -Property *</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/11/scriptanalyzer7a.jpg"><img class="alignnone size-full wp-image-12780" src="http://mikefrobbins.com/wp-content/uploads/2015/11/scriptanalyzer7a.jpg" alt="scriptanalyzer7a" width="859" height="358" /></a>

While I haven't taken a look at that specific module, based on the author it's definitely something worth taking a look at. Also notice that a license URL is specified for the module shown in the previous example. You should always specify a license when sharing your code &lt;period&gt;. I'm not perfect either and that's something I need to revisit for some of the code that I've previously shared. I plan to specify <a href="http://choosealicense.com/licenses/mit/" target="_blank">the MIT license</a> for all of the code that I share online.

Last but not least, <em>Get-Involved</em>. There are script analyzer community meetings which are hosted by Microsoft. The information for those meetings can be found in the announcements section of the <a href="http://github.com/powershell/psscriptanalyzer" target="_blank">PSScriptAnalyzer GitHub page</a>.

µ