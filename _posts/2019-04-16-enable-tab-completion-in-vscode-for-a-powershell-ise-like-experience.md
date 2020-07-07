---
ID: 17798
post_title: >
  Enable Tab Completion in VSCode for a
  PowerShell ISE like Experience
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2019/04/16/enable-tab-completion-in-vscode-for-a-powershell-ise-like-experience/
published: true
post_date: 2019-04-16 07:30:11
---
I'm using VSCode for all of my PowerShell development at this point. I reloaded my system from scratch on March 13th of this year. Yesterday was the first time I've opened the PowerShell ISE since then and it was only to determine if something worked differently between the two (<a href="https://twitter.com/mikefrobbins/status/1117791063232712709" target="_blank" rel="noopener noreferrer">I tweeted this out yesterday</a>).

One of the common problems I hear about and have experienced myself with <a href="https://code.visualstudio.com/" target="_blank" rel="noopener noreferrer">VSCode (Visual Studio Code)</a> is that tabbed expansion of command and parameter names doesn't work like it does in the ISE (Integrated Scripting Environment). That's because tab completion is disabled by default in VSCode. Enabling it gives you a much more ISE like experience and prevents tabbing away from half completed PowerShell command and parameter names before intellisense kicks in.

https://youtu.be/I5B-gVa5u9s

There's a Microsoft article on "<a href="https://docs.microsoft.com/en-us/powershell/scripting/components/vscode/how-to-replicate-the-ise-experience-in-vscode?view=powershell-6" target="_blank" rel="noopener noreferrer">How to replicate the ISE experience in Visual Studio Code</a>" that provides a more comprehensive list of VSCode options to set for a smoother transition from the PowerShell ISE to VSCode.

µ