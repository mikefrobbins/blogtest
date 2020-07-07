---
ID: 14363
post_title: >
  Learning Existing and Setting Up New
  Keyboard Shortcuts in Visual Studio Code
  for the PowerShell Enthusiast
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2016/08/25/learning-existing-and-setting-up-new-keyboard-shortcuts-in-visual-studio-code-for-the-powershell-enthusiast/
published: true
post_date: 2016-08-25 07:30:13
---
I've recently made myself start using Visual Studio Code for writing some of my PowerShell code and I thought I would share a few of the tips that I've learned. If you haven't read my blog article titled "<a href="http://mikefrobbins.com/2016/08/11/use-the-powershell-console-from-within-visual-studio-code/" target="_blank"><em>Use the PowerShell Console from within Visual Studio Code</em></a>", I definitely recommend taking a look at it as today's blog article assumes that you've already made those modifications to your Visual Studio Code environment.

When you first open Visual Studio Code, it defaults to plain text (I haven't found a way to change this default):

<a href="http://mikefrobbins.com/wp-content/uploads/2016/08/vscode-defaults1a.png"><img class="alignnone size-full wp-image-14365" src="http://mikefrobbins.com/wp-content/uploads/2016/08/vscode-defaults1a.png" alt="vscode-defaults1a" width="859" height="527" /></a>

This means that when you start typing in the name of a PowerShell cmdlet such as <em>Get-Service</em> and press tab for tabbed expansion or intellisense as shown in the previous example, it has no idea what you want other than an actual tab.

Of course saving whatever you're working on as a PS1 file solves this problem, but honestly who saves their PowerShell code before writing anything at all? The trick is to press F1 to show the available Visual Studio Code commands. Start typing language, select "<em>Change Language Mode</em>":

<a href="http://mikefrobbins.com/wp-content/uploads/2016/08/vscode-defaults2a.png"><img class="alignnone size-full wp-image-14366" src="http://mikefrobbins.com/wp-content/uploads/2016/08/vscode-defaults2a.png" alt="vscode-defaults2a" width="859" height="156" /></a>

As shown in the previous example, you could also use the "Ctrl+K M" (Ctrl+K then M) hotkey to access "<em>Change Language Mode</em>" or simply click on "<em>Plain Text</em>" in the bottom right corner.

Then start typing PowerShell and select PowerShell:

<a href="http://mikefrobbins.com/wp-content/uploads/2016/08/vscode-defaults3a.png"><img class="alignnone size-full wp-image-14367" src="http://mikefrobbins.com/wp-content/uploads/2016/08/vscode-defaults3a.png" alt="vscode-defaults3a" width="859" height="151" /></a>

Now your unsaved script knows that you're writing PowerShell and all of the features such as tabbed expansion and intelllisense work as expected.

<a href="http://mikefrobbins.com/wp-content/uploads/2016/08/vscode-defaults4a.png"><img class="alignnone size-full wp-image-14369" src="http://mikefrobbins.com/wp-content/uploads/2016/08/vscode-defaults4a.png" alt="vscode-defaults4a" width="859" height="527" /></a>

I've typed in a couple of lines of code that I want to run. Press F1 and type "run" to see what commands are available:

<a href="http://mikefrobbins.com/wp-content/uploads/2016/08/vscode-defaults5a.png"><img class="alignnone size-full wp-image-14372" src="http://mikefrobbins.com/wp-content/uploads/2016/08/vscode-defaults5a.png" alt="vscode-defaults5a" width="859" height="258" /></a>

I'll select the first one "<em>PowerShell: Run selection</em>". The F8 hotkey could also be used to run the selected code:

<a href="http://mikefrobbins.com/wp-content/uploads/2016/08/vscode-defaults6a.png"><img class="alignnone size-full wp-image-14373" src="http://mikefrobbins.com/wp-content/uploads/2016/08/vscode-defaults6a.png" alt="vscode-defaults6a" width="859" height="527" /></a>

Some code will run fine, but as shown in the previous example <em>Get-Credential</em> isn't yet supported.

If you remember in the previous blog article that I referenced, I configured PowerShell.exe as my default integrated terminal for Visual Studio Code. This time I'll try selecting the last option "<em>Terminal: Run Selected Text in Active Terminal</em>" to run the selected code in the default integrated terminal:

<a href="http://mikefrobbins.com/wp-content/uploads/2016/08/vscode-defaults7a.png"><img class="alignnone size-full wp-image-14375" src="http://mikefrobbins.com/wp-content/uploads/2016/08/vscode-defaults7a.png" alt="vscode-defaults7a" width="859" height="258" /></a>

That worked with the exception of not being able to connect to the specified machine which doesn't exist so the error this time was expected:

<a href="http://mikefrobbins.com/wp-content/uploads/2016/08/vscode-defaults8a.png"><img class="alignnone size-full wp-image-14377" src="http://mikefrobbins.com/wp-content/uploads/2016/08/vscode-defaults8a.png" alt="vscode-defaults8a" width="859" height="527" /></a>

Let's define a hotkey for running the selected code in the default integrated terminal. Select File &gt; Preferences &gt; Keyboard Shortcuts:

<a href="http://mikefrobbins.com/wp-content/uploads/2016/08/vscode-defaults9a.png"><img class="alignnone size-full wp-image-14378" src="http://mikefrobbins.com/wp-content/uploads/2016/08/vscode-defaults9a.png" alt="vscode-defaults9a" width="859" height="453" /></a>

I'll add the necessary code to make Ctrl+Shift+T the hotkey for "<em>Terminal: Run Selected Text in Active Terminal</em>":
<pre class="lang:ps decode:true ">{
    "key":"ctrl+shift+t",
    "command":"workbench.action.terminal.runSelectedText",
    "when": "editorHasSelection &amp;&amp; editorLangId == 'powershell'"
}</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2016/08/vscode-defaults10a.png"><img class="alignnone size-full wp-image-14379" src="http://mikefrobbins.com/wp-content/uploads/2016/08/vscode-defaults10a.png" alt="vscode-defaults10a" width="859" height="254" /></a>

Add the code listed in the previous example to the keybindings.json file. Use the Ctrl+S hotkey combination to save the file.

Notice that I was very specific about when that hotkey combination is valid by specifying "<em>when</em>" in the previous code example. This is because that particular combination is already used in Visual Studio Code and I don't want to override it in all scenarios, only specific ones. Here's where it's already defined:

<a href="http://mikefrobbins.com/wp-content/uploads/2016/08/vscode-defaults14a.png"><img class="alignnone size-full wp-image-14389" src="http://mikefrobbins.com/wp-content/uploads/2016/08/vscode-defaults14a.png" alt="vscode-defaults14a" width="859" height="198" /></a>

Now the Ctrl+Shift+T hotkey combination can be used to run the selected code in the default integrated terminal but only when there is a selection in the editor and the language is PowerShell based on how the hotkey is defined:

<a href="http://mikefrobbins.com/wp-content/uploads/2016/08/vscode-defaults11a.png"><img class="alignnone size-full wp-image-14381" src="http://mikefrobbins.com/wp-content/uploads/2016/08/vscode-defaults11a.png" alt="vscode-defaults11a" width="859" height="257" /></a>

It would be nice to setup a hotkey to run both Ctrl+I to select the current line and this command I've added Ctrl+Shift+T to at the same time similar to the way the PowerShell ISE runs the current line if nothing is selected, but I don't think it's currently possible to define a hotkey combination to run two commands.

Interested in discovering what other hotkeys are defined by default in Visual Studio Code?

Open the default keyboard shortcuts again (File &gt; Preferences &gt; Keyboard Shortcuts). Press Ctrl+Shift+O. Start typing in a shortcut to see what it's mapped to:

<a href="http://mikefrobbins.com/wp-content/uploads/2016/08/vscode-defaults12c.png"><img class="alignnone size-full wp-image-14393" src="http://mikefrobbins.com/wp-content/uploads/2016/08/vscode-defaults12c.png" alt="vscode-defaults12c" width="859" height="527" /></a>

One last hotkey shortcut tip is that if you have a folder open in Visual Studio Code, press Ctrl+P which opens a menu at the top that can be used to list recently open files and any file in the open folder. Type in the name of a file to filter the list down and then select an item to open that particular file:

<a href="http://mikefrobbins.com/wp-content/uploads/2016/08/vscode-defaults13a.png"><img class="alignnone size-full wp-image-14385" src="http://mikefrobbins.com/wp-content/uploads/2016/08/vscode-defaults13a.png" alt="vscode-defaults13a" width="859" height="527" /></a>

For more information about keyboard shortcuts in Visual Studio Code, see <a href="https://code.visualstudio.com/docs/customization/keybindings" target="_blank">the online key bindings documentation</a>.

µ