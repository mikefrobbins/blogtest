---
ID: 16858
post_title: >
  Determine the Day of the Week in 11 Days
  from Now with PowerShell
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2018/08/14/determine-the-day-of-the-week-in-11-days-from-now-with-powershell/
published: true
post_date: 2018-08-14 07:30:45
---
A couple of days ago, one of my kids asked me "<em>What day of the week will it be in 11 days from now?</em>". My response was "<em>I'm not sure, but I can tell you how to figure out the answer for yourself</em>". Open up PowerShell, wrap <em>Get-Date</em> in parentheses, place a dot or period afterwards, followed by <em>AddDays, </em> then 11 in another set of parentheses, and finally another dot or period followed by <em>DayOfWeek</em>.
<pre class="lang:ps decode:true ">(Get-Date).AddDays(11).DayOfWeek</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2018/08/dayofweek1a.jpg"><img class="alignnone size-full wp-image-16859" src="https://mikefrobbins.com/wp-content/uploads/2018/08/dayofweek1a.jpg" alt="" width="859" height="69" /></a>

Want to know what the day of the week was 11 days ago instead? Simply use negative 11 (-11) which subtracts that number of days from the current <em>datetime</em> instead of adding it as shown in the previous example.

µ