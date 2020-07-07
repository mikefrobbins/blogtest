---
ID: 15350
post_title: >
  Automatically Customizing the PowerShell
  ISE on Startup
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2017/06/29/automatically-customizing-the-powershell-ise-on-startup/
published: true
post_date: 2017-06-29 07:30:14
---
This past weekend while at SQL Saturday Chattanooga, I was asked if it was possible to have the PowerShell ISE (Integrated Scripting Environment) automatically open with a right/left split pane:

<a href="http://mikefrobbins.com/wp-content/uploads/2017/06/ise-startup1a.jpg"><img class="alignnone size-full wp-image-15351" src="http://mikefrobbins.com/wp-content/uploads/2017/06/ise-startup1a.jpg" alt="" width="858" height="621" /></a>

Instead of the top/bottom default split pane:

<a href="http://mikefrobbins.com/wp-content/uploads/2017/06/ise-startup2a.jpg"><img class="alignnone size-full wp-image-15352" src="http://mikefrobbins.com/wp-content/uploads/2017/06/ise-startup2a.jpg" alt="" width="858" height="621" /></a>

One thing to note is that by default the PowerShell ISE will reopen in whatever configuration you close it in, which could be either of the ones previously shown or the full screen editor.

If you want the ISE to open with a certain configuration no matter what it was when it was closed, you can set it using one of the two profiles that only apply to the ISE. In this scenario, I've chosen to use the all users ISE profile since I log into my computer as a different user than I run PowerShell as and I'm the only person who uses my computer.

Showing the right/left split pane with code is simple enough:
<pre class="lang:ps decode:true ">$psISE.Options.SelectedScriptPaneState = 'Right'</pre>
That code just needs to be added to one of the PowerShell profiles that only applies to the ISE. You can view the locations for the different profiles that apply to the ISE by running the following code from within the ISE:
<pre class="lang:ps decode:true">$profile | Select-Object -Property *</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2017/06/ise-startup3a.jpg"><img class="alignnone size-full wp-image-15354" src="http://mikefrobbins.com/wp-content/uploads/2017/06/ise-startup3a.jpg" alt="" width="859" height="175" /></a>

I wanted to make sure the following code would work properly no matter what host it was run from, so I decided to specify the location itself and store it in a variable.
<pre class="lang:ps decode:true">$AllUsersISEProfile = "$env:windir\System32\WindowsPowerShell\v1.0\Microsoft.PowerShellISE_profile.ps1"

if (-not(Test-Path -Path $AllUsersISEProfile)) {
    New-Item -Path $AllUsersISEProfile -ItemType File
}

Add-Content -Path $AllUsersISEProfile -Value "`$psISE.Options.SelectedScriptPaneState = 'Right'"</pre>
I check to see if the profile already exist and create it if it doesn't. Then add the previously shown code to the profile to make it always open with the right/left split pane configuration.

The value from the following variable could be used instead of the one I created in the previous example if the code to add the entry is being run from the PowerShell ISE:
<pre class="lang:ps decode:true ">$profile.AllUsersCurrentHost</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2017/06/ise-startup4b.jpg"><img class="alignnone size-full wp-image-15356" src="http://mikefrobbins.com/wp-content/uploads/2017/06/ise-startup4b.jpg" alt="" width="859" height="55" /></a>

I could use something like the following code to make sure it's only run from within the ISE and to only add the entry if it doesn't already exist:
<pre class="lang:ps decode:true">if ($Host.Name -eq 'Windows PowerShell ISE Host') {
    if (-not(Test-Path -Path $profile.AllUsersCurrentHost)) {
        New-Item -Path $profile.AllUsersCurrentHost -ItemType File
    }

    if (-not(Get-Content -Path $profile.AllUsersCurrentHost | Select-String -SimpleMatch '$psISE.Options.SelectedScriptPaneState')) {
        Add-Content -Path $profile.AllUsersCurrentHost -Value "`$psISE.Options.SelectedScriptPaneState = 'Right'"
    }
}
else {
    Write-Warning -Message 'This code can only be run from within the PowerShell ISE'
}</pre>
One thing I have in the profile that I personally use is code that sets the zoom to 100% since I often present sessions where I set the zoom level much higher and I want it set back to the default when I close and reopen the ISE.
<pre class="lang:ps decode:true ">$psISE.Options.Zoom = 100</pre>
I use three profiles for applying different settings to specific host and/or all hosts. My profiles can be found in <a href="https://github.com/mikefrobbins/PowerShell" target="_blank" rel="noopener">my PowerShell repository on GitHub</a>.

There are 6 possible profiles within PowerShell by default and a certain precedence for them as demonstrated in a previous blog article I've written: "<a href="http://mikefrobbins.com/2015/07/02/powershell-profile-the-six-options-and-their-precedence/" target="_blank" rel="noopener">PowerShell $Profile: The six options and their precedence</a>".

µ