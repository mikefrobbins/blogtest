---
ID: 6058
post_title: >
  Calculate What Date Thanksgiving is on
  with PowerShell
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2012/11/22/calculate-what-date-thanksgiving-is-on-with-powershell/
published: true
post_date: 2012-11-22 07:30:44
---
A quick search on the Internet turned up this definition for Thanksgiving: "<em>Thanksgiving Day is a time for many people to give thanks for what they have</em>".

Based on that definition, I have a long list of things to be thankful for, however, this is a technology related blog site with an emphasis on PowerShell so I'll focus on being thankful for my PowerShell friends, the PowerShell community, an employer who supports my PowerShell addiction, and a wife &amp; children who put up with me spending countless hours on the computer learning more about PowerShell.

I have many friends throughout the world that I've made through the common pursuit of a better understanding and more knowledge of PowerShell. <a href="http://www.jasonhelmick.com/" target="_blank">Jason Helmick</a> is one of those friends who recently wrote <a href="http://blogs.interfacett.com/how-use-powershell-check-slat-windows-8-hyper-v" target="_blank">a blog on Interface Technical Training's website</a> where he called me a "PowerShell Guru":

<a href="http://blogs.interfacett.com/how-use-powershell-check-slat-windows-8-hyper-v" target="_blank"><img class="alignnone size-full wp-image-6059" title="ps-guru" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/11/ps-guru.png" width="640" height="536" /></a>

I think it's awesome to have someone like Jason who is an IT speaker, author (<a href="http://www.manning.com/helmick/" target="_blank">Learn Windows IIS in a Month of Lunches</a>), columnist, PowerShell guru, and IIS guru to write this which was unsolicited. Thank you Jason...

I'm also thankful for an employer who helps support my PowerShell addiction. They're sending me to the <a href="http://powershell.org/summit/" target="_blank">PowerShell Summit North America 2013</a> next year, they have sent me to <a href="http://northamerica.msteched.com/" target="_blank">Microsoft TechEd North America</a> for the past several years, and have paid for countless PowerShell books. I guess that's one of the reasons I've spent more than the past third of my almost twenty year IT career with the same company.

Now for some Thanksgiving PowerShell fun. I want to know what the date is for Thanksgiving, starting with the current year and for a total of ten years:
<pre class="lang:ps decode:true">((Get-Date).Year..((Get-Date).AddYears(10).Year)) |
ForEach-Object {[datetime]$date = "November 1, $_"
while ($date.DayOfWeek -ne 'Thursday') {$date = $date.AddDays(1)}
Write-Output "In $_, Thanksgiving is on November $($date.AddDays(21).Day)"}</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2012/11/calculate-thanksgiving.png"><img class="alignnone size-full wp-image-6072" title="calculate-thanksgiving" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/11/calculate-thanksgiving.png" width="546" height="224" /></a>

µ