---
ID: 14314
post_title: >
  Use the PowerShell Console from within
  Visual Studio Code
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2016/08/11/use-the-powershell-console-from-within-visual-studio-code/
published: true
post_date: 2016-08-11 07:30:07
---
I recently revisited <a href="http://code.visualstudio.com" target="_blank">Visual Studio Code</a>. I was looking for a markdown editor and remembered seeing a tweet a few weeks ago saying that VS Code could be used to edit markdown. It supports markdown by default, although I would recommend adding a spell check extension to it.

I thought that it would be convenient if I could write my PowerShell code right from within the same interface that I'm writing other things such as markdown. One of the problems that I previously experienced with VS Code is there wasn't a PowerShell console pane like in the ISE (Integrated Scripting Environment). Based on the release notes for VS Code, back in May of 2016, <a href="https://code.visualstudio.com/updates/May_2016" target="_blank">version 1.2.0</a> was released and added support for an <a href="https://code.visualstudio.com/docs/editor/integrated-terminal" target="_blank">Integrated Terminal</a> which uses the cmd.exe command prompt by default on a Windows system. As you'll see in this blog article, it's really easy to change the integrated terminal to use PowerShell.exe instead of cmd.exe.

If you haven't already installed Visual Studio Code and <a href="https://marketplace.visualstudio.com/items?itemName=ms-vscode.PowerShell" target="_blank">the PowerShell extension</a> for it, take a look at my blog article titled "<a href="http://mikefrobbins.com/2015/11/26/installing-visual-studio-code-and-the-powershell-extension/" target="_blank">Installing Visual Studio Code and the PowerShell Extension</a>".

Open VS Code. Select File &gt; Preferences &gt; User Settings:

<a href="http://mikefrobbins.com/wp-content/uploads/2016/08/vscode-terminal1a.png"><img class="alignnone size-full wp-image-14317" src="http://mikefrobbins.com/wp-content/uploads/2016/08/vscode-terminal1a.png" alt="vscode-terminal1a" width="859" height="453" /></a>

There's a setting in the default settings that references cmd.exe as the terminal:

<a href="http://mikefrobbins.com/wp-content/uploads/2016/08/vscode-terminal2a.png"><img class="alignnone size-full wp-image-14318" src="http://mikefrobbins.com/wp-content/uploads/2016/08/vscode-terminal2a.png" alt="vscode-terminal2a" width="859" height="156" /></a>

A settings.json file is also opened when "User Settings" is selected. It allows any of the default settings to be overwritten. Based on the <a href="https://code.visualstudio.com/docs/editor/integrated-terminal" target="_blank">Integrated Terminal</a> documentation page that was previously referenced, place the following entry in the settings.json file to make PowerShell.exe the default integrated terminal program:
<pre class="lang:ps decode:true">// 64-bit PowerShell if available, otherwise 32-bit
"terminal.integrated.shell.windows":"C:\\Windows\\sysnative\\WindowsPowerShell\\v1.0\\powershell.exe"</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2016/08/vscode-terminal3b.png"><img class="alignnone size-full wp-image-14327" src="http://mikefrobbins.com/wp-content/uploads/2016/08/vscode-terminal3b.png" alt="vscode-terminal3b" width="859" height="201" /></a>

Save the settings.json file. Close and reopen VS Code.

Open the Integrated Terminal from View &gt; Integrated Terminal or with the Ctrl + ` keyboard shortcut:

<a href="http://mikefrobbins.com/wp-content/uploads/2016/08/vscode-terminal4a.png"><img class="alignnone size-full wp-image-14321" src="http://mikefrobbins.com/wp-content/uploads/2016/08/vscode-terminal4a.png" alt="vscode-terminal4a" width="859" height="315" /></a>

Now you can run PowerShell code via the PowerShell console right from within Visual Studio Code:

<a href="http://mikefrobbins.com/wp-content/uploads/2016/08/vscode-terminal5b.png"><img class="alignnone size-full wp-image-14326" src="http://mikefrobbins.com/wp-content/uploads/2016/08/vscode-terminal5b.png" alt="vscode-terminal5b" width="859" height="640" /></a>

I also found the following two Visual Studio documentation articles helpful and interesting:

<a href="https://code.visualstudio.com/docs/customization/userandworkspace" target="_blank">User and Workspace Settings</a>
<a href="https://code.visualstudio.com/docs/languages/markdown" target="_blank">Markdown and VS Code</a>

µ