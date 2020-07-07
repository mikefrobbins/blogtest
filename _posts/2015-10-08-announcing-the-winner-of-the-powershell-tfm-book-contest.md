---
ID: 12526
post_title: >
  Announcing the Winner of the PowerShell
  TFM Book Contest
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2015/10/08/announcing-the-winner-of-the-powershell-tfm-book-contest/
published: true
post_date: 2015-10-08 07:30:51
---
Two weeks ago, I started <a href="http://mikefrobbins.com/2015/09/23/windows-powershell-tfm-book-contest-and-giveaway/" target="_blank">a PowerShell contest</a> which required the participants to convert a string of text to title case. I didn't specifically say title case but I explained that the first letter of each word should be converted to upper case and all remaining letters in each word should be converted to lower case. This was because a search on how to convert to title case with PowerShell gave away a good portion of the answer.

There were a total of 22 entries in the contest and all of them were submitted as a secret gist as stated in the requirements for the contest. A link to each entry can be found in the comments of my "<a href="http://mikefrobbins.com/2015/09/23/windows-powershell-tfm-book-contest-and-giveaway/" target="_blank">Windows PowerShell TFM Book Contest and Giveaway</a>" blog article. Overall, I would rate all of the submissions as good or better although some did a lot more work than was required to accomplish the task.

I went through each entry, evaluated it, and thoroughly tested each one. Based on the results that I found, I chose <a href="https://gist.github.com/jbuenting/2a6806627345b5ae073a" target="_blank">Jeff Buenting's entry</a> as the winner:
<pre class="lang:ps decode:true " title="Jeff Buenting's Contest Entry">Function Format-StringToTitleCase {

&lt;#
    .Description
        Capitalizes the first letter of each word in a string of text and converts the remaining letters in each word to lower case.
        
    .Parameter Text
        Sentence or string of text.

    .Example
        Pipe text to Format-StringtoTitleCase

        'THIS IS,A tesT','ThE wINDOWS PowerShell TFM book CONTEST aNd GiVeAwAy' | Format-StringtoTitleCase

    .Example
        Pass an array os strings to Format-StringtoTitleCase
        
        Format-StringtoTitleCase -Text 'THIS IS,A tesT','ThE wINDOWS PowerShell TFM book CONTEST aNd GiVeAwAy'

    .Notes
        Author: Jeff Buenting
        Date: September 23, 2015

    .Link
        http://mikefrobbins.com/2015/09/23/windows-powershell-tfm-book-contest-and-giveaway/
#&gt;

    [CmdletBinding()]
    Param (
        [Parameter(Mandatory=$True, ValueFromPipeLine=$True)]
        [String[]]$Text
    )

    Process {
        Foreach ( $T in $Text ) {
            Write-Verbose "Processing: $T"

            # ---- Convert text to lowercase and then using the ToTitleCase Method from System.Globalization.Textinfo class (Get-Culture)
            Write-OutPut (Get-Culture).textinfo.totitlecase( $T.ToLower() )
        }
    }
}</pre>
His entry used a function with a <em>Verb-Noun</em> name, an approved verb, singular noun, and a pascal cased name. One thing that was missing from Jeff's entry was a <em>Requires</em> statement to tell me what version was required by his function, but none of the entries were perfect. His entry included comment based help, although it was missing a synopsis which I typically include. It included both pipeline and parameter input along with making the parameter mandatory. I also like that Jeff went the extra mile and included support for multiple strings of text instead of just one string of text. That added a very small amount of unnecessary complexity but overall his entry was simple, clear, and concise.

There were a number of entries that didn't use the <em>Get-Culture</em> cmdlet to accomplish the task which created a lot of unnecessary complexity. Some of those entries also added things like an extra space at the end of the string which showed up in the Pester tests that I ran against each entry:

<a href="http://mikefrobbins.com/wp-content/uploads/2015/10/contest-winner1a.jpg"><img class="alignnone size-full wp-image-12531" src="http://mikefrobbins.com/wp-content/uploads/2015/10/contest-winner1a.jpg" alt="contest-winner1a" width="859" height="382" /></a>

Before I had even thought about using this task for a contest, I had written a simple solution for it. My original solution didn't of course include comment based help among other things, but it is unique and I was actually surprised that no one else submitted a similar solution:
<pre class="lang:ps decode:true" title="ConvertTo-MrTitleCase">filter ConvertTo-MrTitleCase {(Get-Culture).TextInfo.ToTitleCase($_.ToLower())}</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/10/contest-winner2a.jpg"><img class="alignnone size-full wp-image-12538" src="http://mikefrobbins.com/wp-content/uploads/2015/10/contest-winner2a.jpg" alt="contest-winner2a" width="859" height="71" /></a>

I decided to take Jeff's entry and update it with what I thought it was missing along with a few things that are just personal preferences of mine:
<pre class="lang:ps decode:true " title="ConvertTo-MrTitleCase">#Requires -Version 3.0
function ConvertTo-MrTitleCase {

&lt;#
.SYNOPSIS
    Capitalizes the first letter of each word in a string of text and converts the remaining letters in each word to lower case.
    
.DESCRIPTION
    ConvertTo-MrTitleCase is an advanced function that converts each word in one or more strings of text to title case.
        
.PARAMETER Text
    One or more sentences or strings of text.

.EXAMPLE
    'THIS IS,A tesT', 'ThE wINDOWS PowerShell TFM book CONTEST aNd GiVeAwAy' | ConvertTo-MrTitleCase

.EXAMPLE        
    ConvertTo-MrTitleCase -Text 'THIS IS,A tesT', 'ThE wINDOWS PowerShell TFM book CONTEST aNd GiVeAwAy'

.INPUTS
    String
 
.OUTPUTS
    String
#&gt;

    [CmdletBinding()]
    param (
        [Parameter(Mandatory,
                   ValueFromPipeline)]
        [String[]]$Text
    )

    PROCESS {
        foreach ($t in $Text) {
            Write-Verbose -Message "Processing: $t"
            Write-Output (Get-Culture).TextInfo.ToTitleCase($t.ToLower())
        }
    }
}</pre>
Congratulations to Jeff and thanks to everyone who took the time to participate in this contest.

µ