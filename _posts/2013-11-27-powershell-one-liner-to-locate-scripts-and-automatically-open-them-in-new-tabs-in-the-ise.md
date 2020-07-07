---
ID: 8650
post_title: >
  PowerShell One-Liner to Locate Scripts
  and Automatically Open them in New Tabs
  in the ISE
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2013/11/27/powershell-one-liner-to-locate-scripts-and-automatically-open-them-in-new-tabs-in-the-ise/
published: true
post_date: 2013-11-27 07:30:02
---
I recently wrote a blog about how to find scripts where you had accomplished some specific task. That blog article was titled "<a href="http://mikefrobbins.com/2013/11/18/how-do-i-find-that-powershell-script-where-i-did-__________/" target="_blank">How do I find that PowerShell Script where I did __________?</a>"

What if you not only want to find those scripts, but also open them automatically in new tabs in the PowerShell ISE? It just so happens that you're in luck:
<pre class="lang:ps decode:true">Get-ChildItem -Path 'C:\Scripts\*.ps1' -Recurse |
Select-String -Pattern "1.." -SimpleMatch |
Select-Object -Property Path -Unique |
ForEach-Object {
    $psISE.CurrentPowerShellTab.Files.Add("$($_.Path)")
} | Out-Null</pre>
I went ahead and piped the results to Out-Null because I didn't want anything returned at the command line. As you can see, there were three scripts that were automatically opened in new tabs in the PowerShell ISE:

<a href="http://mikefrobbins.com/wp-content/uploads/2013/11/ise-tabs1a.png"><img class="alignnone size-full wp-image-8653" alt="ise-tabs1a" src="http://mikefrobbins.com/wp-content/uploads/2013/11/ise-tabs1a.png" width="695" height="331" /></a>

The one-liner shown above was a direct copy and paste from the previously referenced blog article, but you could also expand the "Path" property to make it a little cleaner:
<pre class="lang:ps decode:true">Get-ChildItem -Path 'C:\Scripts\*.ps1' -Recurse |
Select-String -Pattern "1.." -SimpleMatch |
Select-Object -ExpandProperty Path -Unique |
ForEach-Object {
    $psISE.CurrentPowerShellTab.Files.Add("$_")
} | Out-Null</pre>
These scripts could of course return a lot of results, more than you would want to try to open in new tabs in the ISE so you could only open them if say there's no more than a dozen results, otherwise return a warning and the results at the command line:
<pre class="wrap:false lang:ps decode:true">$results = Get-ChildItem -Path 'C:\Scripts\*.ps1' -Recurse |
           Select-String -Pattern "1.." -SimpleMatch |
           Select-Object -ExpandProperty Path -Unique

if ($results.count -le 12) {
    foreach ($result in $results) {
        $psISE.CurrentPowerShellTab.Files.Add("$result")
    }
}
else {
    Write-Warning -Message "There were $($results.count) results which is too many to open in new tabs."
    Write-Output $results
}</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2013/11/ise-tabs1b.png"><img class="alignnone size-full wp-image-8661" alt="ise-tabs1b" src="http://mikefrobbins.com/wp-content/uploads/2013/11/ise-tabs1b.png" width="757" height="379" /></a>

The magic of opening a script in a new tab in the PowerShell ISE is with this command:
<pre class="lang:ps decode:true">$psISE.CurrentPowerShellTab.Files.Add("ScriptName")</pre>
µ