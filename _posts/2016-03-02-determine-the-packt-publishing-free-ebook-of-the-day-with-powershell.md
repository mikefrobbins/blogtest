---
ID: 13513
post_title: >
  Determine the Packt Publishing Free
  eBook of the Day with PowerShell
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2016/03/02/determine-the-packt-publishing-free-ebook-of-the-day-with-powershell/
published: true
post_date: 2016-03-02 07:30:19
---
First of all, let me state that I'm in no way affiliated with <a href="https://www.packtpub.com/" target="_blank">Packt Publishing</a> other than being a customer. They offer <a href="https://www.packtpub.com/packt/offers/free-learning" target="_blank">a free eBook each day</a> and that information is freely available on the Internet. Like any information on the web, it's publicly available and you can read it with a web browser or with something like PowerShell.

As shown in the following example, a PowerShell one liner can be used to determine their free eBook of the day:
<pre class="lang:ps decode:true">(Invoke-WebRequest -Uri https://www.packtpub.com/packt/offers/free-learning).ParsedHtml.getElementsByTagName('H2')[0].InnerHTML.Trim()</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2016/03/free-book1a.jpg" rel="attachment wp-att-13514"><img class="alignnone size-full wp-image-13514" src="http://mikefrobbins.com/wp-content/uploads/2016/03/free-book1a.jpg" alt="free-book1a" width="859" height="81" /></a>

This requires PowerShell version 3 or higher and based on the way it's written, it may not work if the format of their webpage is changed since it simply returns the first item on the webpage that uses a H2 html tag.

I've placed that one liner in my all users console host profile so I see what their free eBook of the day is when I open the PowerShell console. I typically only see this information once per day since I normally only open the PowerShell console once a day and leave it open until I shutdown my computer.
<pre class="lang:ps mark:3,11,12 decode:true " title="Microsoft.PowerShell_profile.ps1">$Now = Get-Date
$Date = Get-Date -Month $Now.Month -Day 1
$Book = (Invoke-WebRequest -Uri https://www.packtpub.com/packt/offers/free-learning).ParsedHtml.getElementsByTagName('H2')[0].InnerHTML.Trim()

while ($Date.DayOfWeek -ne 'Tuesday') {$Date = $Date.AddDays(1)}
if ($Date.ToShortDateString() -eq $Now.ToShortDateString()) {
    Update-Module -Force
    Update-Help -ErrorAction SilentlyContinue
}

Write-Host 'The Packt Publishing free learning eBook of the day is: ' -ForegroundColor Cyan -NoNewline
Write-Host "'$Book'" -ForegroundColor Yellow</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2016/03/free-book2a.jpg" rel="attachment wp-att-13515"><img class="alignnone size-full wp-image-13515" src="http://mikefrobbins.com/wp-content/uploads/2016/03/free-book2a.jpg" alt="free-book2a" width="859" height="57" /></a>

As you can see in the previous set of results, The Packt Publishing free learning eBook of the day for today (March 2, 2016) is: '<em><a href="https://www.packtpub.com/networking-and-servers/sql-server-2012-powershell-v3-cookbook" target="_blank">SQL Server 2012 with PowerShell V3 Cookbook</a></em>'. It's a book that I've previously purchased and read that's written by Microsoft MVP Donabel Santos (<a href="http://twitter.com/sqlbelle" target="_blank">sqlbelle</a>) who is also a contributing author of a chapter in the <a href="https://www.manning.com/books/powershell-deep-dives" target="_blank">PowerShell Deep Dives book</a> which I also wrote a chapter in.

If you're not familiar with the different PowerShell profiles such as the one that I referenced that's applied to all users and only the PowerShell console host, see my blog article on "<em><a href="http://mikefrobbins.com/2015/07/02/powershell-profile-the-six-options-and-their-precedence/" target="_blank">PowerShell $Profile: The six options and their precedence</a></em>".

The most recent version of my profile scripts can be found in <a href="https://github.com/mikefrobbins/PowerShell" target="_blank">my PowerShell repository on GitHub</a>.

µ