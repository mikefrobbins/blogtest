---
ID: 15282
post_title: >
  What is this Module Scope in PowerShell
  that you Speak of?
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2017/06/08/what-is-this-module-scope-in-powershell-that-you-speak-of/
published: true
post_date: 2017-06-08 07:30:02
---
Last week I posted <a href="http://mikefrobbins.com/2017/06/01/powershell-and-the-nimble-storage-rest-api/" target="_blank" rel="noopener noreferrer">a blog article about a PowerShell script module that I had written</a> with a few proof of concept commands to manage a Nimble Storage Area Network using their REST API. That module used a command to connect and authenticate to the storage device which needed to share a token with other commands in the module otherwise authentication would have to be performed for each command. I initially placed the token in a global variable even though I mentioned in the blog article that I’m not a big fan of globally scoping variables unless absolutely necessary (which I thought it was at the time).

I used a module named <a href="https://github.com/Jaykul/Tunable-SSL-Validator" target="_blank" rel="noopener noreferrer">TunableSSLValidator</a> that fellow PowerShell MVP <a href="https://twitter.com/Jaykul" target="_blank" rel="noopener noreferrer">Joel Bennett</a> had created to be able to use <em>Invoke-WebRequest</em> with an untrusted or self-signed certificate. I tweeted that blog article to Joel to show him how I had used his module. He responded with a great tip about scoping variables so they're shareable between commands within the same module without being globally scoped.

<a href="https://twitter.com/Jaykul/status/870380357052387328" target="_blank" rel="noopener noreferrer"><img class="alignnone size-full wp-image-15286" src="http://mikefrobbins.com/wp-content/uploads/2017/06/module-scope1a.jpg" alt="" width="588" height="246" /></a>

I searched through the help topics prior to contacting Joel once I saw his comment. I could only find one reference to module scope which says that modules do not have their own scope. Here's what the a<a href="https://msdn.microsoft.com/en-us/powershell/reference/5.1/microsoft.powershell.core/about/about_scopes" target="_blank" rel="noopener noreferrer">bout_Scopes help topic</a> says about module scope:

<em>"The privacy of a module behaves like a scope, but adding a module to a session does not change the scope. And, <span style="color: #ff0000;">the module does not have its own scope</span>, although the scripts in the module, like all Windows PowerShell scripts, do have their own scope."</em>

After reading that, I contacted Joel since I'm familiar with global, script, and local scope but not module scope. My question to Joel was something like <em>"What is this Module Scope that you Speak of?".</em> Joel replied and said script scope in a module is module scope. A little testing confirmed that scoping variables to script scope within a module makes them shareable between the commands from that module without exposing them globally.

To test variable scoping within a module, the following functions were saved as a script module file named MrVarTest.psm1:
<pre class="lang:ps decode:true " title="MrVarTest.psm1">function Set-MrVar {
    $PsProcess = Get-Process -Name PowerShell
}

function Set-MrVarLocal {
    $Local:PsProcess = Get-Process -Name PowerShell
}

function Set-MrVarScript {
    $Script:PsProcess = Get-Process -Name PowerShell
}

function Set-MrVarGlobal {
    $Global:PsProcess = Get-Process -Name PowerShell
}

function Test-MrVarScoping {
    if ($PsProcess) {
        Write-Output $PsProcess
    }
    else {
        Write-Warning -Message 'Variable $PsProcess not found!'
    }
    
}</pre>
First, the PSM1 file is imported since I didn't place it in a location specified in <em>$env:PsModulePath</em>. Next the variable is set from a function in the module without coercing the scope. It's no surprise that the variable isn't found from another function in the module or from the current scope outside of the module. Then local scope is tested. The same results are produced. The third option uses script scope which allows another function within the same module to access the variable, but not from the current scope outside of the module. Finally, global scope is tested and it's accessible from another function within the module and from the current scope outside of the module. There's no reason for the variable to be accessible from outside of the module and having it accessible in that manner can only lead to trouble.
<pre class="lang:ps decode:true ">#Import the script module since I didn't place it in a location specified in $env:PSModulePath
Import-Module C:\tmp\MrVarTest.psm1

#Set the variable without any scoping coercion from within a function in the module
Set-MrVar

#Check the value of the $PsProcess variable from another function in the same module
Test-MrVarScoping

#Check the value of the $PsProcess variable from the current scope
$PsProcess

#Set the variable to the local scope from within a function in the module
Set-MrVarLocal

#Check the value of the $PsProcess variable from another function in the same module
Test-MrVarScoping

#Check the value of the $PsProcess variable from the current scope
$PsProcess

#Set the variable to the script scope from within a function in the module
Set-MrVarScript

#Check the value of the $PsProcess variable from another function in the same module
Test-MrVarScoping

#Check the value of the $PsProcess variable from the current scope
$PsProcess

#Set the variable to the global scope from within a function in the module
Set-MrVarGlobal

#Check the value of the $PsProcess variable from another function in the same module
Test-MrVarScoping

#Check the value of the $PsProcess variable from the current scope
$PsProcess</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2017/06/module-scope2a.jpg"><img class="alignnone size-full wp-image-15293" src="http://mikefrobbins.com/wp-content/uploads/2017/06/module-scope2a.jpg" alt="" width="859" height="742" /></a>

Best practice is to limit the scope of your variables as much as possible otherwise something else that's totally unrelated could overwrite the value contained within one of them if they're globally scoped.

I also confirmed the variables are shared between commands within the same module using script scope regardless of whether or not you're using one monolithic PSM1 file for your script module or separate PS1 files that are dot-sourced from the PSM1 file (<a href="http://mikefrobbins.com/2017/04/06/video-powershell-non-monolithic-script-module-design/" target="_blank" rel="noopener noreferrer">Non-Monolithic script module design</a>).

Here are some other comments related to the tweet posted earlier in this blog article:

<a href="https://twitter.com/FoxDeploy/status/870411856699158529" target="_blank" rel="noopener noreferrer"><img class="alignnone wp-image-15299 size-full" src="http://mikefrobbins.com/wp-content/uploads/2017/06/module-scope3a.jpg" alt="" width="640" height="112" /></a>

<a href="https://twitter.com/mobileck/status/870406377142980613" target="_blank" rel="noopener noreferrer"><img class="alignnone size-full wp-image-15300" src="http://mikefrobbins.com/wp-content/uploads/2017/06/module-scope4a.jpg" alt="" width="587" height="203" /></a>

The key takeaway from this blog article is as Joel said <em>"script scope in a module is module scope"</em>.

µ