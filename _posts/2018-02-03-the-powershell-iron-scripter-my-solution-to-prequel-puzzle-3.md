---
ID: 16328
post_title: 'The PowerShell Iron Scripter: My solution to prequel puzzle 3'
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2018/02/03/the-powershell-iron-scripter-my-solution-to-prequel-puzzle-3/
published: true
post_date: 2018-02-03 11:04:37
---
I've been working through <a href="https://powershell.org/category/announcements/scripting-games/" target="_blank" rel="noopener">the Iron Scripter 2018 prequel puzzles</a> which can be found on <a href="https://powershell.org/" target="_blank" rel="noopener">PowerShell.org's</a> website. In puzzle 3, you're asked to create a "reusable PowerShell artifact". To me, that almost always means a PowerShell function. One requirement is to pull information from the PowerShell.org RSS feed. <a href="https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.utility/invoke-restmethod?view=powershell-5.1" target="_blank" rel="noopener"><em>Invoke-RestMethod</em></a> which was introduced in PowerShell version 3.0 is the easiest way to accomplish that task.

You're also asked to display the returned information in a way that allows the user to select an item from the feed. My first thought was to use <a href="https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.utility/out-gridview?view=powershell-5.1" target="_blank" rel="noopener"><em>Out-GridView</em></a> to build a GUI based menu for selecting the item, but that won't work in PowerShell Core since it doesn't have the same GUI based commands as Windows PowerShell. This means I'll have to build a text based menu in PowerShell. Luckily, I've recently read "<a href="https://leanpub.com/powershell-scripting-toolmaking" target="_blank" rel="noopener">The PowerShell Scripting and Toolmaking Book</a>" by <a href="https://twitter.com/concentrateddon" target="_blank" rel="noopener">Don Jones</a> and <a href="https://twitter.com/JeffHicks" target="_blank" rel="noopener">Jeff Hicks</a> which has some examples of building text based menus, although I designed mine a little differently than theirs. Consuming content from others often sparks your own creativity.

<a href="http://mikefrobbins.com/wp-content/uploads/2018/02/prequel3-ironscripter2a.jpg"><img class="alignnone size-full wp-image-16336" src="http://mikefrobbins.com/wp-content/uploads/2018/02/prequel3-ironscripter2a.jpg" alt="" width="859" height="276" /></a>

The next part was actually the most challenging. Give the user the ability to display the selected item from the RSS feed in either their browser or in plain text.

<a href="http://mikefrobbins.com/wp-content/uploads/2018/02/prequel3-ironscripter3b.jpg"><img class="alignnone size-full wp-image-16339" src="http://mikefrobbins.com/wp-content/uploads/2018/02/prequel3-ironscripter3b.jpg" alt="" width="859" height="135" /></a>

While the browser part was easy enough and the text part was also easy for Windows PowerShell, returning the content of the feed in text for PowerShell Core was more difficult. This was due to the <em>ParsedHtml</em> property not existing for <a href="https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.utility/invoke-webrequest?view=powershell-5.1" target="_blank" rel="noopener"><em>Invoke-WebRequest</em></a> in PowerShell Core.

One of the drawbacks to using <em>Invoke-WebRequest</em> in Windows PowerShell is that it's dependent on Internet Explorer, although that isn't the case in PowerShell Core running on a Windows system.
<pre class="lang:ps decode:true ">Invoke-WebRequest -Uri https://powershell.org/</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2018/02/prequel3-ironscripter1a.jpg"><img class="alignnone size-full wp-image-16332" src="http://mikefrobbins.com/wp-content/uploads/2018/02/prequel3-ironscripter1a.jpg" alt="" width="859" height="188" /></a>

Making it work for any RSS feed was a bonus and mine does work against a couple of different sites I tested it against (PowerShell.org and my mikefrobbins.com blog site). Your results may vary when using it for RSS feeds on other websites.
<pre class="lang:ps decode:true" title="Invoke-MrRssFeedMenu">#Requires -Version 3.0
function Invoke-MrRssFeedMenu {

&lt;#
.SYNOPSIS
    Retrieves information from the specified RSS feed and displays it in a selectable menu.
 
.DESCRIPTION
    Invoke-MrRssFeedMenu is an advanced function that retrieves information from the specified RSS feed, displays it in
    a selectable menu and then prompts the user to return the information about the selected item in a browser or as text.
 
.PARAMETER Uri
    Specifies the Uniform Resource Identifier (URI) of the RSS feed to which the web request is sent. This parameter supports 
    HTTP, HTTPS, FTP, and FILE values. The default is the PowerShell.org RSS feed.
 
.EXAMPLE
     Invoke-MrRssFeedMenu

.EXAMPLE
     Invoke-MrRssFeedMenu -Uri http://mikefrobbins.com/feed/
 
.INPUTS
    None
 
.NOTES
    Author:  Mike F Robbins
    Website: http://mikefrobbins.com
    Twitter: @mikefrobbins
#&gt;

    [CmdletBinding()]
    param (
        [ValidateNotNullOrEmpty()]
        [string]$Uri = 'https://powershell.org/feed/'
    )

    $Results = Invoke-RestMethod -Uri $Uri 

    $MenuItems = for ($i = 1; $i -le $Results.Count; $i++) {
        [pscustomobject]@{
            Number = $i
            Title = $Results[$i-1].title
            PublicationDate = ([datetime]$Results[$i-1].pubDate).ToShortDateString()
            Author = $Results[$i-1].creator.InnerText
        }
    }

$PostMenu = @"
    $($MenuItems | Out-String)
    Enter the number of the article to view
"@

    do {
        Clear-Host
        [int]$Post = Read-Host -Prompt $PostMenu   
    }
    while (
        $Post -notin 1..$Results.Count
    )

    Clear-Host

$OutputMenu = @"
Number DisplayOption
------ ----------------
     1 Default Browser
     2 Text
     3 Quit

Enter the number to display the article in the desired option
"@

    Clear-Host

    do {
        [int]$OutputType = Read-Host -Prompt $OutputMenu
    }
    while (
        $OutputType -notin 1..3
    )

    switch ($OutputType) {
        1 {
            Start-Process -FilePath ($Results[$Post-1]).link
            Clear-Host
            break
          }
        2 {
            Clear-Host
            ($Results[$Post-1]).InnerText.ToString() -replace '\t.*|\n|&lt;[^&gt;]*&gt;|/\s\s+/g|^\s+|\s+$'
            break
          }
        3 {
            break
          }
    }

}</pre>
I use a regular expression to parse the text if PowerShell Core is used. That regular expression could probably use some more tweaking and I'll post an update if I decide to work on it more. The other things that could be added are error handling and verbose output.

<a href="http://mikefrobbins.com/wp-content/uploads/2018/02/prequel3-ironscripter4a.jpg"><img class="alignnone size-full wp-image-16352" src="http://mikefrobbins.com/wp-content/uploads/2018/02/prequel3-ironscripter4a.jpg" alt="" width="859" height="172" /></a>

One of the rules I've broken in the PowerShell function shown in this blog article is that a function should do one thing and do it well. More about that rule can be read about in <a href="https://blogs.technet.microsoft.com/heyscriptingguy/2013/04/07/do-one-thing-and-do-it-well/" target="_blank" rel="noopener">this Hey, Scripting Guy! Blog article</a>. This function could be broken up into multiple functions to better adhere to that rule.

The function shown in this blog article can be downloaded from <a href="https://github.com/mikefrobbins/IronScripter" target="_blank" rel="noopener">my IronScripter repository on GitHub</a>. It can also be installed from <a href="https://www.powershellgallery.com/packages/MrIronScripter/0.3.1" target="_blank" rel="noopener">the PowerShell Gallery</a>.

See <a href="http://mikefrobbins.com/category/powershell/scripting-games/" target="_blank" rel="noopener">the scripting games category on this blog site</a> to view my other Iron Scripter entries along with my previous scripting games entries. Other recommended reading: <a href="https://get-powershellblog.blogspot.com/2018/01/powershell-core-61-web-cmdlets-roadmap.html" target="_blank" rel="noopener">PowerShell Core 6.1 Web Cmdlets Roadmap blog article</a> by Microsoft MVP <a href="https://twitter.com/markekraus" target="_blank" rel="noopener">Mark Kraus</a>.

Learn to write award winning PowerShell functions and script modules from a former winner of the advanced category in the scripting games and a multiyear recipient of both Microsoft’s MVP and SAPIEN Technologies’ MVP award at <a href="https://powershelldevopsglobalsummit2018.sched.com/event/CnMp/writing-award-winning-powershell-functions-and-script-modules" target="_blank" rel="noopener">the PowerShell + DevOps Global Summit 2018</a>.

<hr />

Update - February 4th, 2018
I modified the switch statement to use the same code regardless if Windows PowerShell or PowerShell Core is used.

µ