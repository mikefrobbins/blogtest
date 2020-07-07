---
ID: 8339
post_title: >
  PowerShell Function to Determine
  Daylight Saving Time
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2013/10/17/powershell-function-to-determine-daylight-saving-time/
published: true
post_date: 2013-10-17 07:30:50
---
About a year ago, I wrote a PowerShell one-liner to "<a href="http://mikefrobbins.com/2012/11/22/calculate-what-date-thanksgiving-is-on-with-powershell/" target="_blank">Calculate What Date Thanksgiving is on with PowerShell</a>" (in the United States). When looking back at that blog now, my first thought is boy that is some rough code that's hard to read. Instead of re-writing the same thing again, I decided to write something similar applying what I've learned during the past year to it.

That's how I came up with the idea to write a PowerShell function that returns the beginning and ending dates for daylight saving time in the USA for a given year or years:
<pre class="wrap:true lang:ps decode:true crayon-selected" title="Get-DayLightSavingTime">#Requires -Version 3.0
function Get-DayLightSavingTime {

&lt;#
.SYNOPSIS
    Returns the beginning and ending date for daylight saving time.
.DESCRIPTION
    Get-DayLightSavingTime is a function that returns the dates when
    daylight saving time begins and ends for the specified year.
.PARAMETER Year
    The year to return the daylight saving time dates for. The year cannot
    be earlier than 2007 because the dates were in April and October instead
    of March and November prior to that year. The default is the current year.
.EXAMPLE
    Get-DayLightSavingTime
.EXAMPLE
    Get-DayLightSavingTime -Year 2014, 2015
.EXAMPLE
    Get-DayLightSavingTime -Year (2011..2020)
.EXAMPLE
    2014, 2015 | Get-DayLightSavingTime
.EXAMPLE
    2011..2020 | Get-DayLightSavingTime
.INPUTS
    Integer
.OUTPUTS
    PSCustomObject
.NOTES
    Written by Mike F Robbins
    Blog: http://mikefrobbins.com
    Twitter: @mikefrobbins
#&gt;

    [CmdletBinding()]
    param (
        [Parameter(ValueFromPipeline)]
        [ValidateRange(2007,9999)]
        [Int[]]$Year = (Get-Date).Year
    )

    PROCESS {
        foreach ($y in $Year) {

            [datetime]$beginDate = "March 1, $y"

            while ($beginDate.DayOfWeek -ne 'Sunday') {
                $beginDate = $beginDate.AddDays(1)
            }

            [datetime]$endDate = "November 1, $y"

            while ($endDate.DayOfWeek -ne 'Sunday') {
                $endDate = $endDate.AddDays(1)
            }            

            [PSCustomObject]@{
                'Year' = $y
                'BeginDate' = $($beginDate.AddDays(7).AddHours(2))
                'EndDate' = $($endDate.AddHours(2))
            }

        }
    }
}</pre>
I can't say that I've seen someone accept multiple integers before, this is something I normally see with strings but it appears to work without issue.

The really cool thing is that you can use the range operator to pipe in a range of years:

<a href="http://mikefrobbins.com/wp-content/uploads/2013/10/dst1.png"><img class="alignnone size-full wp-image-8340" alt="dst1" src="http://mikefrobbins.com/wp-content/uploads/2013/10/dst1.png" width="877" height="241" /></a>

You can also use the range operator to specify a range of years via parameter intput:

<a href="http://mikefrobbins.com/wp-content/uploads/2013/10/dst2.png"><img class="alignnone size-full wp-image-8341" alt="dst2" src="http://mikefrobbins.com/wp-content/uploads/2013/10/dst2.png" width="877" height="372" /></a>

If you found this interesting and want to learn more about PowerShell, there's a <a href="http://powershellsaturday.com/005" target="_blank">PowerShell Saturday technology event in Atlanta</a> on October 26th. They have an awesome lineup of speakers scheduled to present and as the winner of the advanced category in the 2013 scripting games, I'll be presenting a session on "<a href="http://powershellsaturday.com/005/presentation/powershell-best-practices-and-lessons-learned-from-the-2013-scripting-games/" target="_blank">Best Practices and Lessons Learned from the 2013 Scripting Games</a>".

The function shown in this blog can be downloaded from the TechNet <a href="http://gallery.technet.microsoft.com/scriptcenter/Determine-Daylight-Saving-df400bee" target="_blank">script repository</a>.

µ