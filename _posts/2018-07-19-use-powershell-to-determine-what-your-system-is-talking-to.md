---
ID: 16737
post_title: >
  Use PowerShell to Determine What Your
  System is Talking to
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2018/07/19/use-powershell-to-determine-what-your-system-is-talking-to/
published: true
post_date: 2018-07-19 07:30:31
---
Recently, while troubleshooting a problem with a newly installed application, I wanted to see what it was communicating with. My only requirement was that I wanted to use PowerShell if at all possible. I couldn't remember if there was a PowerShell command for accomplishing this task or not, but I remembered seeing something about it in <a href="https://twitter.com/pewa2303" target="_blank" rel="noopener">Patrick Gruenauer's</a> chapter (PowerShell as an Enterprise Network Tool) in <a href="https://leanpub.com/powershell-conference-book" target="_blank" rel="noopener">The PowerShell Conference Book</a>.
<blockquote>Note: This blog article is written using Windows 10 version 1803 and Windows PowerShell version 5.1. Your mileage may vary with different operating systems and/or different versions of PowerShell.</blockquote>
A quick look at his chapter and I found what I was looking for. The <em>Get-NetTCPConnection</em> function. The code in Patrick's chapter that I remembered seeing is similar to the following.
<pre class="lang:ps decode:true ">Get-NetTCPConnection |
Select-Object -Property LocalPort, State, OwningProcess</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2018/07/tcpconnection1a.png"><img class="alignnone size-full wp-image-16738" src="https://mikefrobbins.com/wp-content/uploads/2018/07/tcpconnection1a.png" alt="" width="859" height="328" /></a>

I decided to take it a step further from what I read in Patrick's chapter and grab the actual name of the owning process along with a few other properties.
<pre class="lang:ps decode:true ">Get-NetTCPConnection -State Established |
Select-Object -Property LocalAddress, LocalPort, RemoteAddress, RemotePort, State,
                        @{name='Process';expression={(Get-Process -Id $_.OwningProcess).Name}}, CreationTime |
Format-Table -AutoSize</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2018/07/tcpconnection2a.png"><img class="alignnone size-full wp-image-16739" src="https://mikefrobbins.com/wp-content/uploads/2018/07/tcpconnection2a.png" alt="" width="859" height="257" /></a>

That was too easy. I can see what my test VM that's used in this scenario is communicating with along with the names of the processes and the creation times. You could write a powerful tool using this command as the basis to scan remote servers to make sure they aren't communicating with anything out of the ordinary.

Reading seems to be a dying art in our society, but it's well worth your time to read. To be completely honest, you'll never master PowerShell without reading. After all, it's a text based scripting language with no graphics or pictures to point and click and all of the help for it is text-based which means you have to read. While something you've read may not solve the exact problem you're experiencing, it can spark ideas to create those light-bulb moments. Never underestimate the power of reading. Now, go forth and read!

µ