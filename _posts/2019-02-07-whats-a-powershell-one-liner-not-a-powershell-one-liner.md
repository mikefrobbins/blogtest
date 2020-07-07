---
ID: 17537
post_title: 'What&#8217;s a PowerShell One-Liner &#038; NOT a PowerShell One-Liner?'
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2019/02/07/whats-a-powershell-one-liner-not-a-powershell-one-liner/
published: true
post_date: 2019-02-07 07:30:25
---
Lately, I've seen a few examples of commands that aren't PowerShell one-liners being passed off as such by people in the PowerShell community who "should" know better. If I were interviewing someone who claimed to be a PowerShell expert with more than five years of experience, I would definitely ask these types of questions.

Example 1 - The following command is a PowerShell one-liner? (True or False)
<pre class="lang:ps decode:true">$Process = 'notepad.exe'; Start-Process $Process -PassThru -OutVariable NoteProc; Stop-Process -Id $NoteProc.Id -Force</pre>
Example 2 - The following command is a PowerShell one-liner? (True or False)
<pre class="lang:ps decode:true ">Start-Process notepad.exe -PassThru |
Stop-Process -Force -PassThru</pre>
A PowerShell one-liner is one continuous pipeline and not necessarily a command that’s on one physical line. Not all commands that are on one physical line are PowerShell one-liners.

µ