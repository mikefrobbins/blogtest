---
ID: 9372
post_title: >
  Where and Foreach Methods in PowerShell
  version 4
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2014/04/03/where-and-foreach-methods-in-powershell-version-4/
published: true
post_date: 2014-04-03 07:30:05
---
It's a little over a month until <a href="http://northamerica.msteched.com/" target="_blank">Microsoft TechEd North America 2014</a> which is in Houston this year. I was trying to remember the cool stuff I saw at TechEd last year and Desired State Configuration along with the announcement about PowerShell version 4 were the two big things I remember, but let's not forget about some of the other things such as the new Where and Foreach methods.

I certainly don't claim to be an expert with these, but I'm going to provide a couple of examples for documentation for myself as well as to get you thinking about them:

The Where method:
<pre class="lang:ps decode:true">((Get-Command -Name Get-WMIObject).parameters.values).where{
$_.name -eq 'ComputerName'} |
Select-Object -Property Name, Aliases</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-02_18-10-41.png"><img class="alignnone size-full wp-image-9374" src="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-02_18-10-41.png" alt="2014-04-02_18-10-41" width="877" height="169" /></a>

The Foreach method:
<pre class="lang:ps decode:true">('dc01', 'sql01', 'web01').foreach{
Get-WinEvent -ComputerName $_ -LogName System -MaxEvents 1 |
Select-Object -Property MachineName, Id, LevelDisplayName, Message}</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-02_17-44-58.png"><img class="alignnone size-full wp-image-9373" src="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-02_17-44-58.png" alt="2014-04-02_17-44-58" width="877" height="192" /></a>

My first thought is the syntax seems to be insane, but I remember thinking the same thing years ago when I first saw the curly brace dollar sign underscore and so on: {$_.property}.

By the way, I'll be at TechEd again this year and you probably know where you'll find me (In the PowerShell sessions :-))

µ