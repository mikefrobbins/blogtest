---
ID: 9252
post_title: >
  Proving Leap Year is NOT Every Four
  Years with PowerShell
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2014/03/06/proving-leap-year-is-not-every-four-years-with-powershell/
published: true
post_date: 2014-03-06 07:30:53
---
Last week, I saw a couple of tweets from PowerShell MVP <a href="http://twitter.com/JeffHicks" target="_blank">Jeffery Hicks</a> about leap year that caught my attention:

<a href="http://twitter.com/JeffHicks" target="_blank"><img class="alignnone size-full wp-image-9253" alt="030614-1" src="http://mikefrobbins.com/wp-content/uploads/2014/03/030614-1.png" width="590" height="189" /></a>

One of the reasons that I found those tweets so interesting is that I had just heard the night before on of all places, Wheel of Fortune, that leap year wasn't every four years. I'd always been told that leap year was every four years and it has been for my entire life so it was time to investigate further.

I decided to start with one of Jeff's examples and add some code to it to determine which years should be leap years but weren't based on the popular belief that leap year is every four years:
<pre class="lang:ps decode:true">$leapYears = 1600..2250 | where {[datetime]::DaysInMonth($_,2) -eq 29}

foreach ($leapYear in $leapYears) {
    $nextLeapYear = $leapYear + 4

    if ($leapYears -notcontains $nextLeapYear -and $leapYear -ne $leapYears[-1]) {
        Write-Output "$nextLeapYear"
    }
}</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/03/030614-2b.png"><img class="alignnone size-full wp-image-9271" alt="030614-2b" src="http://mikefrobbins.com/wp-content/uploads/2014/03/030614-2b.png" width="872" height="254" /></a>

Now that's interesting. Why aren't those years, shown in the previous example, leap years? I found a <a href="http://en.wikipedia.org/wiki/Leap_year" target="_blank">Wikipedia article about leap year</a> with the answer to that question which I documented in the description section of the comments in the function shown in the following example. That Wikipedia article also provided some pseudo-code which I converted into PowerShell:
<pre class="lang:ps decode:true crayon-selected" title="Get-LeapYear">function Get-LeapYear {

&lt;#
.SYNOPSIS
   Get-LeapYear is used to determine whether or not a specific year is a leap year.
.DESCRIPTION
   Get-LeapYear is a function that is used to determine whether or not the specified
   year(s) are leap years. Contrary to popular belief, leap year does not occur every
   four years. According to Wikipedia, if a year is divisible by 400 then it's a leap
   year, else if the year is divisible by 100 then it's a normal year, else if the year
   is divisible by 4 then it's a leap year, else it's a normal year. Source:
   http://en.wikipedia.org/wiki/Leap_year
.PARAMETER Year
   The year(s) specified in integer form that you would like to determine
   whether or not they are a leap year.
.EXAMPLE
   Get-LeapYear
.EXAMPLE
   Get-LeapYear -Year 2010, 2011, 2012, 2013, 2014, 2015
.EXAMPLE
   1890..1910 | Get-LeapYear
.INPUTS
   Integer
.OUTPUTS
   String
.NOTES
   Author:  Mike F Robbins
   Website: http://mikefrobbins.com
   Twitter: @mikefrobbins
#&gt;

    [CmdletBinding()]
    param (
        [Parameter(ValueFromPipeline=$true)]
        [ValidateRange(1582,9999)]
        [int[]]$Year = (Get-Date).Year
    )

    PROCESS {
        foreach ($y in $Year) {
            if ($y / 400 -is [int]) {
                Write-Output "$y is a leap year"
            }
            elseif ($y / 100 -is [int]) {
                Write-Output "$y is not a leap year"
            }
            elseif ($y / 4 -is [int]) {
                Write-Output "$y is a leap year"
            }
            else {
                Write-Output "$y is not a leap year"
            }
        }
    }
}</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/03/030614-3a.png"><img class="alignnone size-full wp-image-9275" alt="030614-3a" src="http://mikefrobbins.com/wp-content/uploads/2014/03/030614-3a.png" width="881" height="340" /></a>

As you can see 1900 was not a leap year and there were 8 years between leap years from 1896 to 1904. Eight years between leap years won't occur again until years 2096 to 2104 .

µ