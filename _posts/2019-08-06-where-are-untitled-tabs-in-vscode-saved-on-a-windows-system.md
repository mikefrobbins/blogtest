---
ID: 18029
post_title: >
  Where are Untitled Tabs in VSCode Saved
  on a Windows System?
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2019/08/06/where-are-untitled-tabs-in-vscode-saved-on-a-windows-system/
published: true
post_date: 2019-08-06 07:30:31
---
Ever wonder how <a href="https://code.visualstudio.com/" target="_blank" rel="noopener noreferrer">VSCode (Visual Studio Code)</a> maintains those untitled tabs between sessions? They're stored as files underneath your user's profile in appdata on Windows based systems as shown in the following example.
<pre class="lang:ps decode:true">Get-ChildItem -Path $env:APPDATA\Code\Backups\*\untitled -Recurse</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2019/08/untitled-tabs-in-vscode1a.jpg"><img class="alignnone size-full wp-image-18030" src="https://mikefrobbins.com/wp-content/uploads/2019/08/untitled-tabs-in-vscode1a.jpg" alt="" width="859" height="282" /></a>

The previous command could be piped to <em>Get-Content</em> to view the contents of all of code in the open untitled tabs of VSCode. You could also use <em>Select-String</em> to find something specific. I can see that three of my open tabs contain 'mikefrobbins'.
<pre class="lang:ps decode:true">Get-ChildItem -Path $env:APPDATA\Code\Backups\*\untitled -Recurse |
Select-String -SimpleMatch 'mikefrobbins' |
Format-Table</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2019/08/untitled-tabs-in-vscode2a.jpg"><img class="alignnone size-full wp-image-18031" src="https://mikefrobbins.com/wp-content/uploads/2019/08/untitled-tabs-in-vscode2a.jpg" alt="" width="859" height="200" /></a>

Concerned about losing information contained in your untitled tabs? You should probably save them, but you also have the option of backing up all of your untitled tabs using the following PowerShell one-liner.
<pre class="lang:ps decode:true ">Get-ChildItem -Path $env:APPDATA\Code\Backups\*\untitled -Recurse |
Compress-Archive -DestinationPath "C:\tmp\vscode-untitled-tabs$(Get-Date -Format FileDate).zip"</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2019/08/untitled-tabs-in-vscode3a.jpg"><img class="alignnone size-full wp-image-18032" src="https://mikefrobbins.com/wp-content/uploads/2019/08/untitled-tabs-in-vscode3a.jpg" alt="" width="859" height="77" /></a>

Thoughts, questions, comments? Please post them as a comment to this blog article.

µ