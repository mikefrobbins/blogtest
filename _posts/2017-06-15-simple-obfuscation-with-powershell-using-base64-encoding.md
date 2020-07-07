---
ID: 15323
post_title: >
  Simple Obfuscation with PowerShell using
  Base64 Encoding
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2017/06/15/simple-obfuscation-with-powershell-using-base64-encoding/
published: true
post_date: 2017-06-15 07:30:59
---
I recently received a question from someone wanting to know how I encoded a string of text on my blog site. Back in January of 2013, I competed in <a href="http://mikefrobbins.com/2013/01/24/grand-prize-winner-of-trainsignals-jeff-hicks-powershell-challenge/" target="_blank" rel="noopener noreferrer">Jeff Hicks PowerShell Challenge</a> that was held by TrainSignal. One of the questions had an encoded command which you were to decode. I figured out that the <em>-EncodedCommand</em> parameter of <em>PowerShell.exe</em> could not only be used to run commands that are encoded with Base64, that it could also be used to easily decode a string of text that was encoded with Base64.
<pre class="lang:batch decode:true">powershell.exe /?</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2017/06/encodedcmd1a.jpg"><img class="alignnone size-full wp-image-15324" src="http://mikefrobbins.com/wp-content/uploads/2017/06/encodedcmd1a.jpg" alt="" width="859" height="695" /></a>

The help for <em>PowerShell.exe</em> also shows you how to encode a command with Base64:

<a href="http://mikefrobbins.com/wp-content/uploads/2017/06/encodedcmd2b.jpg"><img class="alignnone size-full wp-image-15326" src="http://mikefrobbins.com/wp-content/uploads/2017/06/encodedcmd2b.jpg" alt="" width="859" height="249" /></a>

Encoding something like the domain name for this blog site is easy enough:
<pre class="lang:ps decode:true">[Convert]::ToBase64String([System.Text.Encoding]::Unicode.GetBytes("'mikefrobbins.com'"))</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2017/06/encodedcmd3a.jpg"><img class="alignnone size-full wp-image-15327" src="http://mikefrobbins.com/wp-content/uploads/2017/06/encodedcmd3a.jpg" alt="" width="859" height="68" /></a>

While it could be decoded within PowerShell:
<pre class="lang:ps decode:true">[System.Text.Encoding]::Unicode.GetString([System.Convert]::FromBase64String('JwBtAGkAawBlAGYAcgBvAGIAYgBpAG4AcwAuAGMAbwBtACcA'))</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2017/06/encodedcmd4a.jpg"><img class="alignnone size-full wp-image-15329" src="http://mikefrobbins.com/wp-content/uploads/2017/06/encodedcmd4a.jpg" alt="" width="859" height="81" /></a>

Adding quotes around the domain name also allows it to be decoded with <em>PowerShell.exe</em> using the <em>-EncodedCommand</em> parameter without having to encode it with a command such as <em>Write-Output</em>:
<pre class="lang:batch decode:true">powershell.exe -encodedCommand JwBtAGkAawBlAGYAcgBvAGIAYgBpAG4AcwAuAGMAbwBtACcA</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2017/06/encodedcmd5a.jpg"><img class="alignnone size-full wp-image-15330" src="http://mikefrobbins.com/wp-content/uploads/2017/06/encodedcmd5a.jpg" alt="" width="859" height="69" /></a>

The code shown in the previous example specifies the <em>-NoProfile</em> parameter but it's not required. I've added it since calling <em>PowerShell.exe</em> with <a href="http://mikefrobbins.com/2016/03/02/determine-the-packt-publishing-free-ebook-of-the-day-with-powershell/" target="_blank" rel="noopener noreferrer">my profile displays the Packt Publishing free eBook of the day</a>.

µ