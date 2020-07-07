---
ID: 17685
post_title: >
  Backup and Synchronize VSCode Settings
  with a GitHub Gist
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2019/03/21/backup-and-synchronize-vscode-settings-with-a-github-gist/
published: true
post_date: 2019-03-21 07:30:11
---
Earlier this year, I watched a recording of the January 2019 <a href="https://azpowershell.wordpress.com/" target="_blank" rel="noopener noreferrer">Arizona PowerShell User Group</a> meeting where <a href="https://twitter.com/TechTrainerTim" target="_blank" rel="noopener noreferrer">Tim Warner</a> presented a session on "<a href="https://www.youtube.com/watch?v=TJfWgcag6Q4&amp;t" target="_blank" rel="noopener noreferrer">Easing your transition from the PowerShell ISE to Visual Studio Code</a>". One of the things Tim demonstrated was a VSCode extension named "<a href="https://marketplace.visualstudio.com/items?itemName=Shan.code-settings-sync" target="_blank" rel="noopener noreferrer">Settings Sync</a>" where you can synchronize your VSCode settings to and from a GitHub Gist. While it seems to be designed to synchronize your settings between multiple computers, I recently found out that it can also be a lifesaver even if you only use it on one computer.

In VSCode, press F1 or Ctrl + Shift + P to open the command palette. Then click the square extensions icon on the left (or Ctrl + Shift + X), search for the "settings sync" extension, and click install.

<a href="https://mikefrobbins.com/wp-content/uploads/2019/03/settings-sync1a.png"><img class="alignnone size-full wp-image-17688" src="https://mikefrobbins.com/wp-content/uploads/2019/03/settings-sync1a.png" alt="" width="859" height="228" /></a>

Open the command palette again (F1 is easier and fewer keys to type than Ctrl + Shift + P). Search for commands that begin with "sync" and click "Sync: Update / Upload Settings" (or Shift + Alt + U).

<a href="https://mikefrobbins.com/wp-content/uploads/2019/03/settings-sync2a.png"><img class="alignnone size-full wp-image-17689" src="https://mikefrobbins.com/wp-content/uploads/2019/03/settings-sync2a.png" alt="" width="859" height="223" /></a>

This will open the access tokens page of your GitHub account. Create a new access token.

<a href="https://mikefrobbins.com/wp-content/uploads/2019/03/settings-sync3a.png"><img class="alignnone size-full wp-image-17690" src="https://mikefrobbins.com/wp-content/uploads/2019/03/settings-sync3a.png" alt="" width="903" height="245" /></a>

Give it a meaningful description and only grant it the ability to create Gists.

<a href="https://mikefrobbins.com/wp-content/uploads/2019/03/settings-sync4a.png"><img class="alignnone size-full wp-image-17691" src="https://mikefrobbins.com/wp-content/uploads/2019/03/settings-sync4a.png" alt="" width="753" height="816" /></a>

Copy the access token as this will be your only chance to see it.

<a href="https://mikefrobbins.com/wp-content/uploads/2019/03/settings-sync5a.png"><img class="alignnone size-full wp-image-17692" src="https://mikefrobbins.com/wp-content/uploads/2019/03/settings-sync5a.png" alt="" width="757" height="225" /></a>

Go back to VSCode. You'll notice the Settings Sync extension is waiting for an access token. Paste the access token and press enter.

<a href="https://mikefrobbins.com/wp-content/uploads/2019/03/settings-sync6a.png"><img class="alignnone size-full wp-image-17693" src="https://mikefrobbins.com/wp-content/uploads/2019/03/settings-sync6a.png" alt="" width="616" height="98" /></a>

You'll see what was uploaded in the output window of VSCode.

<a href="https://mikefrobbins.com/wp-content/uploads/2019/03/settings-sync7a.png"><img class="alignnone size-full wp-image-17694" src="https://mikefrobbins.com/wp-content/uploads/2019/03/settings-sync7a.png" alt="" width="671" height="372" /></a>

Use the "Sync: Download Settings" (or Shift + Alt + D) option on your other computers.

<a href="https://mikefrobbins.com/wp-content/uploads/2019/03/settings-sync8a.png"><img class="alignnone size-full wp-image-17695" src="https://mikefrobbins.com/wp-content/uploads/2019/03/settings-sync8a.png" alt="" width="614" height="209" /></a>

There are a few other options I prefer to set in "Sync: Advanced Options".

<a href="https://mikefrobbins.com/wp-content/uploads/2019/03/settings-sync9a.png"><img class="alignnone size-full wp-image-17696" src="https://mikefrobbins.com/wp-content/uploads/2019/03/settings-sync9a.png" alt="" width="620" height="208" /></a>

"Sync: Toggle Auto-Upload On Settings Change", "Sync: Toggle Auto-Download on Startup", and "Sync: Toggle Show Summary Page on Upload / Download".

<a href="https://mikefrobbins.com/wp-content/uploads/2019/03/settings-sync10a.png"><img class="alignnone size-full wp-image-17697" src="https://mikefrobbins.com/wp-content/uploads/2019/03/settings-sync10a.png" alt="" width="615" height="341" /></a>

All of these settings can be found within the settings section in VSCode. The ID for the Gist that was created can also be found there.

<a href="https://mikefrobbins.com/wp-content/uploads/2019/03/settings-sync11a.png"><img class="alignnone size-full wp-image-17698" src="https://mikefrobbins.com/wp-content/uploads/2019/03/settings-sync11a.png" alt="" width="768" height="794" /></a>Recently, I reloaded my primary work computer and while I thought I had everything important backed up, the one thing I was missing was my VSCode settings. Thankfully, I had installed and configured the "Sync Settings" extension, so recovering my settings was as simple as reinstalling that extension and configuring it to download my settings from the private Gist it previously created.

For security reasons, both the GitHub access token and Gist shown in this blog article have already been deleted.

µ