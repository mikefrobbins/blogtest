---
ID: 15519
post_title: >
  How to install Visual Studio Code and
  configure it as a replacement for the
  PowerShell ISE
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2017/08/24/how-to-install-visual-studio-code-and-configure-it-as-a-replacement-for-the-powershell-ise/
published: true
post_date: 2017-08-24 07:30:27
---
If you <a href="https://twitter.com/mikefrobbins" target="_blank" rel="noopener">follow me on Twitter,</a> then I'm sure you're aware that I've been using nothing but VS Code (<a href="https://code.visualstudio.com/" target="_blank" rel="noopener">Visual Studio Code</a>) as a replacement for the PowerShell ISE (Integrated Scripting Environment) for the past couple of weeks and while I had tried it in the past, I didn't previously think it was ready for prime time. That's now changed with all of the updates and work that has gone into it. From what I've found, it works fairly well flawlessly so I've created a short and simple <a href="https://youtu.be/1sD-RktG_dM" target="_blank" rel="noopener">video</a> to help others get VS Code installed and configured as a replacement for the PowerShell ISE.

https://youtu.be/1sD-RktG_dM

Let me know what you think and if you'd like to see more videos from me.

<hr />

Update - I decided to add a brief summary of the video based on some of the feedback I received.

Go to <a href="https://code.visualstudio.com/" target="_blank" rel="noopener">code.visualstudio.com</a> and download VSCode (Visual Studio Code) for your operating system. I also recommend downloading the version that matches your operating system's architecture (32 or 64 bit). I take all of the default options during the installation.

Install the <a href="https://marketplace.visualstudio.com/items?itemName=ms-vscode.PowerShell" target="_blank" rel="noopener">PowerShell extension</a> for VSCode. I also like to install the <a href="https://marketplace.visualstudio.com/items?itemName=robertohuertasm.vscode-icons" target="_blank" rel="noopener">vscode-icons extension</a>.

<a href="http://mikefrobbins.com/wp-content/uploads/2017/08/vscode-install1a.jpg"><img class="alignnone size-full wp-image-15546" src="http://mikefrobbins.com/wp-content/uploads/2017/08/vscode-install1a.jpg" alt="" width="859" height="377" /></a>

Once the extensions are installed, reload VSCode by clicking the Reload button:

<a href="http://mikefrobbins.com/wp-content/uploads/2017/08/vscode-install2a.jpg"><img class="alignnone size-full wp-image-15547" src="http://mikefrobbins.com/wp-content/uploads/2017/08/vscode-install2a.jpg" alt="" width="859" height="375" /></a>

Click on the Gear on the bottom left and select Settings:

<a href="http://mikefrobbins.com/wp-content/uploads/2017/08/vscode-install3a.jpg"><img class="alignnone size-full wp-image-15548" src="http://mikefrobbins.com/wp-content/uploads/2017/08/vscode-install3a.jpg" alt="" width="859" height="244" /></a>

Enter the following settings on the right side of the settings screen.
<pre class="lang:ps decode:true ">{
    "workbench.iconTheme": "vscode-icons",
    "workbench.colorTheme": "PowerShell ISE",
    "files.defaultLanguage": "powershell",
    "editor.formatOnType": true,
    "editor.formatOnPaste": true,
    "powershell.integratedConsole.focusConsoleOnExecute": false,
    "window.zoomLevel": 0,
    "editor.mouseWheelZoom": true
}</pre>
For more details, see the <a href="https://youtu.be/1sD-RktG_dM" target="_blank" rel="noopener">video</a>.

µ