---
ID: 12799
post_title: >
  Installing Visual Studio Code and the
  PowerShell Extension
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2015/11/26/installing-visual-studio-code-and-the-powershell-extension/
published: true
post_date: 2015-11-26 07:30:04
---
Last week Microsoft released a new version of Visual Studio Code along with an extension for writing PowerShell in it. Visit <a href="https://code.visualstudio.com/" target="_blank">code.visualstudio.com</a> to download Visual Studio Code. There's also an update link on that page if you happen to have a version prior to "0.10.1". The PowerShell extension for Visual Studio Code only works with PowerShell version 5. Either Windows 8.1 with the <a href="http://www.microsoft.com/en-us/download/details.aspx?id=48729" target="_blank">WMF 5 Production Preview</a> installed or Windows 10 is sufficient.

The GUI installation of Visual Studio Code is a clickersize. There's also a long list of command line options to perform an unattended installation:
<pre class="lang:ps decode:true">.\VSCodeSetup.exe /?</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/11/vscode1a.png"><img class="alignnone size-full wp-image-12801" src="http://mikefrobbins.com/wp-content/uploads/2015/11/vscode1a.png" alt="vscode1a" width="859" height="61" /></a>

This <a href="http://www.jrsoftware.org/ishelp/index.php?topic=setupcmdline" target="_blank">website</a> is listed at the bottom of the following image:

<a href="http://mikefrobbins.com/wp-content/uploads/2015/11/vscode2a.png"><img class="alignnone size-full wp-image-12803" src="http://mikefrobbins.com/wp-content/uploads/2015/11/vscode2a.png" alt="vscode2a" width="847" height="908" /></a>

I'll install it from the command line using the "Very Silent" option:
<pre class="lang:ps decode:true">.\VSCodeSetup.exe /VerySilent</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/11/vscode3a.png"><img class="alignnone size-full wp-image-12804" src="http://mikefrobbins.com/wp-content/uploads/2015/11/vscode3a.png" alt="vscode3a" width="859" height="59" /></a>

<a href="https://code.visualstudio.com/" target="_blank"><img class="alignleft size-full wp-image-12806" src="http://mikefrobbins.com/wp-content/uploads/2015/11/vscode4a.png" alt="vscode4a" width="76" height="83" /></a>After the installation finishes, a desktop icon will be created and Visual Studio Code will be launched automatically.

To install the PowerShell extension, from within Visual Studio Code use the key combination Cntl+Shift+P or select View &gt; Command Palette... from the menu options:

<a href="http://mikefrobbins.com/wp-content/uploads/2015/11/vscode5b.png"><img class="alignnone size-full wp-image-12808" src="http://mikefrobbins.com/wp-content/uploads/2015/11/vscode5b.png" alt="vscode5b" width="859" height="310" /></a>

Backspace over the "&gt;" and type in "ext install powershell" and select the first entry:

<a href="http://mikefrobbins.com/wp-content/uploads/2015/11/vscode6a.png"><img class="alignnone size-full wp-image-12809" src="http://mikefrobbins.com/wp-content/uploads/2015/11/vscode6a.png" alt="vscode6a" width="859" height="151" /></a>

That will download the extension and place it in the .vscode\extensions folder under your user profile:

<a href="http://mikefrobbins.com/wp-content/uploads/2015/11/vscode7a.png"><img class="alignnone size-full wp-image-12811" src="http://mikefrobbins.com/wp-content/uploads/2015/11/vscode7a.png" alt="vscode7a" width="859" height="150" /></a>

Note: Internet access is required to install the extension otherwise you'll receive this error:

<a href="http://mikefrobbins.com/wp-content/uploads/2015/11/vscode8a.png"><img class="alignnone size-full wp-image-12813" src="http://mikefrobbins.com/wp-content/uploads/2015/11/vscode8a.png" alt="vscode8a" width="859" height="101" /></a>

The following message is displayed once the installation is complete and you'll need to restart Visual Studio Code:

<a href="http://mikefrobbins.com/wp-content/uploads/2015/11/vscode9a.png"><img class="alignnone size-full wp-image-12814" src="http://mikefrobbins.com/wp-content/uploads/2015/11/vscode9a.png" alt="vscode9a" width="859" height="101" /></a>

The best place to start is by opening up the examples folder in Visual Studio Code. The examples folder is located under your user profile in ".vscode\extensions\ms-vscode.PowerShell\examples":

<a href="http://mikefrobbins.com/wp-content/uploads/2015/11/vscode10c.png"><img class="alignnone size-full wp-image-12833" src="http://mikefrobbins.com/wp-content/uploads/2015/11/vscode10c.png" alt="vscode10c" width="859" height="223" /></a>

Now you'll be able to write PowerShell in Visual Studio Code:

<a href="http://mikefrobbins.com/wp-content/uploads/2015/11/vscode11a.png"><img class="alignnone size-full wp-image-12834" src="http://mikefrobbins.com/wp-content/uploads/2015/11/vscode11a.png" alt="vscode11a" width="859" height="761" /></a>

Be sure to see the Windows PowerShell Team Blog titled "<a href="http://blogs.msdn.com/b/powershell/archive/2015/11/17/announcing-windows-powershell-for-visual-studio-code-and-more.aspx" target="_blank">Announcing PowerShell language support for Visual Studio Code and more!</a>" for the specific PowerShell related features that are available in Visual Studio Code.

µ