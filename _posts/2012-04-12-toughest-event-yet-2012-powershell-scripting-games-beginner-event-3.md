---
ID: 3667
post_title: 'Toughest Event Yet &#8211; 2012 PowerShell Scripting Games Beginner Event #3'
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2012/04/12/toughest-event-yet-2012-powershell-scripting-games-beginner-event-3/
published: true
post_date: 2012-04-12 07:30:28
---
The details of the event scenario and the design points for Beginner Event #3 of the 2012 PowerShell Scripting Games can be found on the “<a href="http://blogs.technet.com/b/heyscriptingguy/archive/2012/04/04/2012-scripting-games-beginner-event-3-create-a-file-in-a-folder.aspx" target="_blank">Hey, Scripting Guys! Blog</a>”.

This was the toughest event out of the first three for me. I spent a lot of time researching how to check permissions because part of the first design point stated: <em>"If you do not have permission off the root, create the nested folders where you have permissions"</em>. This was clarified in the comments section later during the day and I was able to move on. Check the comments section of the event scenario's often since there may be additional information posted in them that's very useful.

I also spent a lot of time trying to figure out why try-catch wasn't catching my errors since another one of the design points stated: <em>"Your script should not generate errors (that are ignored)"</em>. After a lot of research, I finally figured out that try-catch only catches terminating errors and I set the ErrorActionPreference to Stop for the entire script to put that problem to rest. I saw a tweet later that day from Jeff Hicks (<a href="http://twitter.com/JeffHicks" target="_blank">@JeffHicks</a>) about a new blog article of his titled "<a href="http://jdhitsolutions.com/blog/2012/04/try-and-catch-me-if-you-can/" target="_blank">Try and Catch Me If You Can</a>" that covers this exact problem.

Here's the script I submitted. I also included comments in my submission. See my script on the PowerShell Code Repository site if your interested in seeing the comments. I try to keep my code as short and simple as possible while still meeting all the requirements.

<a href="http://mikefrobbins.com/wp-content/uploads/2012/04/2012sg-be3-1.png"><img class="alignnone size-full wp-image-3724" title="2012sg-be3-1" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/04/2012sg-be3-1.png" width="640" height="175" /></a>

It starts out by defining two parameters and setting default values that meet the requirements of the scenario. Then it sets the ErrorActionPreference to Stop for the entire script to keep it from having any unhandled errors. Then there's the Try block. I really like using the If statement with the -not so if the first statement is true, the second one doesn't execute and vise-versa. The pathType parameter checks for a container (directory) because if event3 was a file with no extension, it would return true without this option. If the path doesn't exist, the directories are created. The Get-Process line is fairly generic except for the -Force parameter which will overwrite the file even if it is set to read only. Then there's the catch block which will tell you why the command failed if there's an error.
<pre class="lang:ps decode:true " title="Beginner Category Event #3 of the 2012 Scripting Games">&lt;#
.SYNOPSIS
Save-Processes saves the name and id of the locally running processes at
the given point in time when this script is run to a file on the local computer.
.DESCRIPTION
Save-Processes retrieves the currently runnning processes on the local computer
and saves the process name along with the process id to a file on the local
computer. You must have modify rights to create the file specified in the filename
parameter and to the directories specified in the path parameter. The file is
overwritten if it already exists even if the file is set to read only (the -force
option causes a file set to read only to be overwritten regardless). The specified
path is created if it does not already exist.
.PARAMETER path
The drive letter and path to save the results to. Default: 'c:\2012SG\event3'
.PARAMETER filename
The filename to save the results to. Default: 'process3.txt'
.EXAMPLE
Get-DiskInventory -path 'c:\2012SG\event3' -filename 'process3.txt'
#&gt;
param (
  $path = 'c:\2012SG\event3',
  $filename = 'process3.txt'
)
$ErrorActionPreference = 'stop'
 try {
 if (-not(Test-Path $path -pathType container)) {New-Item -ItemType directory -path $path}
 Get-Process | Select-Object name, id | Out-File $path\$filename -Force
 }
 catch
{
   "The script failed due to: $_"
}</pre>
View my entry for this event on the <a href="http://2012sg.poshcode.org/2919" target="_blank">PowerShell Code Repository site</a>.

µ