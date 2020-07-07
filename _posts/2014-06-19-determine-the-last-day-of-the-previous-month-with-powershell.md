---
ID: 10058
post_title: >
  Determine the Last Day of the Previous
  Month with PowerShell
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2014/06/19/determine-the-last-day-of-the-previous-month-with-powershell/
published: true
post_date: 2014-06-19 07:30:25
---
I recently had a need to determine the last day of the previous month with PowerShell. Unless I'm overlooking something, PowerShell doesn't natively have a way to determine this.

Luckily, the System.Time class in the .NET Framework has a DaysInMonth method that returns the number of days in a specific month which effectively gives you the last day of the month. The command to determine what I needed was simple enough:
<pre class="lang:ps decode:true">$PriorMonth = (Get-Date).AddMonths(-1)
$LastDay = [System.DateTime]::DaysInMonth($PriorMonth.Year, $PriorMonth.Month)
[datetime]"$($PriorMonth.Month), $LastDay, $($PriorMonth.Year), 23:59:59.999"</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/06/lastdayofmonth0.jpg"><img class="alignnone size-full wp-image-10067" src="http://mikefrobbins.com/wp-content/uploads/2014/06/lastdayofmonth0.jpg" alt="lastdayofmonth0" width="877" height="131" /></a>

I went ahead and created a well documented and reusable function that is more dynamic to use in similar scenarios in the future:
<pre class="lang:ps decode:true" title="Get-MrLastDayOfMonth">function Get-MrLastDayOfMonth {

&lt;#
.SYNOPSIS
    Determines the last day of the specified month(s). 
 
.DESCRIPTION
    Get-MrLastDayOfMonth is a function that returns the last day of one or
    more months that are specified via parameter or pipeline input.
 
.PARAMETER Date
    The date(s) to determine the last day for. The default is the previous month.

.EXAMPLE
    Get-MrLastDayOfMonth
 
.EXAMPLE
     Get-MrLastDayOfMonth -Date (Get-Date).AddMonths(1)
 
.EXAMPLE
     (Get-Date).AddMonths(-3) | Get-MrLastDayOfMonth
 
.EXAMPLE
    6..1 | Foreach-Object {(Get-Date).AddMonths(-$_)} | Get-MrLastDayOfMonth
 
.INPUTS
    DateTime
 
.OUTPUTS
    DateTime
 
.NOTES
    Author:  Mike F Robbins
    Website: http://mikefrobbins.com
    Twitter: @mikefrobbins
#&gt;

    [CmdletBinding()]
    param (
        [Parameter(ValueFromPipeline=$true)]
        [ValidateNotNullOrEmpty()]
        [datetime[]]$Date = (Get-Date).AddMonths(-1)
    )

    PROCESS {
        foreach ($d in $Date) {
            $LastDay = [System.DateTime]::DaysInMonth($d.Year, $d.Month)
            [datetime]"$($d.Month), $LastDay, $($d.Year), 23:59:59.999"
        }
    }

}</pre>
I'll work through the examples of the function so you can see how it can be used. Running it without any parameters causes it to default to finding the last day of the previous month:
<pre class="lang:ps decode:true">. .\Get-MrLastDayOfMonth.ps1
Get-MrLastDayOfMonth</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/06/lastdayofmonth1.jpg"><img class="alignnone size-full wp-image-10060" src="http://mikefrobbins.com/wp-content/uploads/2014/06/lastdayofmonth1.jpg" alt="lastdayofmonth1" width="877" height="120" /></a>

The second example shows providing parameter input to get the last day for next month:
<pre class="lang:ps decode:true">Get-MrLastDayOfMonth -Date (Get-Date).AddMonths(1)</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/06/lastdayofmonth2.jpg"><img class="alignnone size-full wp-image-10061" src="http://mikefrobbins.com/wp-content/uploads/2014/06/lastdayofmonth2.jpg" alt="lastdayofmonth2" width="877" height="109" /></a>

The third example shows determining the last day of the month, three months ago:
<pre class="lang:ps decode:true">(Get-Date).AddMonths(-3) | Get-MrLastDayOfMonth</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/06/lastdayofmonth3.jpg"><img class="alignnone size-full wp-image-10062" src="http://mikefrobbins.com/wp-content/uploads/2014/06/lastdayofmonth3.jpg" alt="lastdayofmonth3" width="877" height="108" /></a>

And the final example shows determining the last day of the month for the previous six months via pipeline input:
<pre class="lang:ps decode:true">6..1 | Foreach-Object {(Get-Date).AddMonths(-$_)} | Get-MrLastDayOfMonth</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/06/lastdayofmonth4.jpg"><img class="alignnone size-full wp-image-10063" src="http://mikefrobbins.com/wp-content/uploads/2014/06/lastdayofmonth4.jpg" alt="lastdayofmonth4" width="877" height="167" /></a>

I actually figured out another way to accomplish this task in PowerShell between the time I wrote this blog and the time I published it, but I thought I would leave the previous content intact as well.

You know the first of a month is always going to be number 1 so you can actually subtract one millisecond from the first to receive the last day of the month:
<pre class="lang:ps decode:true">(Get-Date -Date 6/1/14).AddMilliseconds(-1)</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/06/lastdayofmonth5.jpg"><img class="alignnone size-full wp-image-10073" src="http://mikefrobbins.com/wp-content/uploads/2014/06/lastdayofmonth5.jpg" alt="lastdayofmonth5" width="877" height="109" /></a>

And for a more dynamic version of the previous one-liner, you could do something crazy like this:
<pre class="lang:ps decode:true">(Get-Date | ForEach-Object {Get-Date -Date ([datetime]"$($_.Month) 1, $($_.Year)")}).AddMilliseconds(-1)</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/06/lastdayofmonth6.jpg"><img class="alignnone size-full wp-image-10076" src="http://mikefrobbins.com/wp-content/uploads/2014/06/lastdayofmonth6.jpg" alt="lastdayofmonth6" width="877" height="118" /></a>

I don't normally publish commands with aliases on my blog, but since that previous command is fairly cryptic already, I'll make an exception in this scenario:
<pre class="lang:ps decode:true ">(date|%{date("$($_.Month) 1,$($_.Year)")}).AddMilliseconds(-1)</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/06/lastdayofmonth7.jpg"><img class="alignnone size-full wp-image-10078" src="http://mikefrobbins.com/wp-content/uploads/2014/06/lastdayofmonth7.jpg" alt="lastdayofmonth7" width="877" height="106" /></a>

Whatever you do, please don't use aliases or positional parameters in your scripts!

µ