---
ID: 10472
post_title: >
  My Solution to the Impractical One-liner
  Challenge
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2014/09/25/my-solution-to-the-impractical-one-liner-challenge/
published: true
post_date: 2014-09-25 07:30:05
---
I recently participated in an "<a href="http://foxdeploy.com/2014/09/19/impractical-one-liner-challenge/" target="_blank">Impractical One-liner Challenge</a>" that <a href="http://twitter.com/SRed13" target="_blank">Stephen Owen</a> posted on his blog site where he challenged his readers to come up with a PowerShell one-liner that would allow the person running it to select a single OU (Organizational Unit) from a list of all the OU's in Active Directory and then move a list of computer accounts that are contained in a text file to that OU.

My solution requires the Active Directory PowerShell module and at least PowerShell version 3.

I decided to do a little extra work and add some additional functionality to my solution. I start out by getting a list of all OU's and giving you a human readable OU name and also the distinguished name for that OU since the same name could exist in more than one place in Active Directory. I display that information to the user with <em>Out-GridView</em>:
<pre class="lang:ps decode:true ">Get-ADOrganizationalUnit -Filter * -Properties OU |
Select-Object -Property @{label='OU';expression={$_.OU -replace '{|}'}},
                        DistinguishedName |
Out-GridView -OutputMode Single -Title 'Select Destination OU'</pre>
As shown in the previous example, the "<em>-OutputMode Single</em>" parameter and value prevents the user from selecting more than one OU. I also set the title of the <em>Out-GridView</em> windows throughout this solution so the user has an idea of what they're suppose to do.

I know that only one item will be returned at this point, but in order to make this command a one-liner, I pipe the results of the OU that the user selected to the <em>ForEach-Object</em> cmdlet, then get the contents of the text file that contains the computer names. Those results (the list of computer names) are piped to <em>Get-ADComputer</em> because we need the computer's distinguished name to be able to move it with the <em>Move-ADObject</em> cmdlet. Those results are then piped to <em>Out-GridView</em> because I also wanted to allow the user to be able to select the computers to be moved instead of blindly moving all computers that were listed in the text file. The "<em>-OutputMode Multiple</em>" parameter and value is specified this time to allow the user to pick more than one computer to be moved if they so desire:
<pre class="lang:ps decode:true">ForEach-Object {
    Get-Content -Path .\computers.txt |
    Get-ADComputer |
    Out-GridView -OutputMode Multiple -Title 'Select Computers to Move' |</pre>
Those results are piped to <em>Move-ADObject</em> and the target OU is specified via parameter input and the value is used from the first portion of the command (what was piped to <em>ForEach-Object</em>). By default <em>Move-ADObject</em> doesn't return any results, it just "makes it so" without any feedback unless there's a problem. I wanted to know what computers were moved, where they were moved from, and what OU they were moved to so I added the <em>-PassThru</em> parameter to <em>Move-ADObject</em> so it would return results:
<pre class="lang:ps decode:true ">Move-ADObject -TargetPath $_.DistinguishedName -PassThru</pre>
I only wanted those specific items returned so I piped the results of the previous command to <em>Select-Object</em> specifying the <em>Name</em> property and since I already had the computer name, I wanted that portion of the distinguished name removed which required a custom property to be created and the easiest way to remove it was with a regular expression:
<pre class="lang:ps decode:true">Select-Object -Property Name,
                        @{label='DestinationOU';expression={$_.DistinguishedName -replace '^(.*?),'}</pre>
I also wanted to know where the computer account was before it was moved, so I went back and added the <em>-PipelineVariable</em> parameter to the end of the command just prior to <em>Move-ADObject</em> to obtain those results by storing them in a variable named "<em>info</em>":
<pre class="lang:ps decode:true">Out-GridView -OutputMode Multiple -Title 'Select Computers to Move' -PipelineVariable info |
</pre>
Another custom property later and I had what I needed:
<pre class="lang:ps decode:true ">@{label='SourceOU';expression={$info.DistinguishedName -replace '^(.*?),'}},</pre>
Here's the PowerShell code for my solution in it's entirety:
<pre class="lang:ps decode:true" title="My Solution to the Impractical One-liner Challenge">Get-ADOrganizationalUnit -Filter * -Properties OU |
Select-Object -Property @{label='OU';expression={$_.OU -replace '{|}'}},
                        DistinguishedName |
Out-GridView -OutputMode Single -Title 'Select Destination OU' |
ForEach-Object {
    Get-Content -Path .\computers.txt |
    Get-ADComputer |
    Out-GridView -OutputMode Multiple -Title 'Select Computers to Move' -PipelineVariable info |
    Move-ADObject -TargetPath $_.DistinguishedName -PassThru |
    Select-Object -Property Name,
                            @{label='SourceOU';expression={$info.DistinguishedName -replace '^(.*?),'}},
                            @{label='DestinationOU';expression={$_.DistinguishedName -replace '^(.*?),'}} |
    Out-GridView -Title 'Results'
}</pre>
Although the command shown in the previous block of code is on more than one physical line, it is still a PowerShell one-liner command because it is one continuous pipeline.

When that command is run, you're prompted with a selection of OU's:

<a href="http://mikefrobbins.com/wp-content/uploads/2014/09/oneliner-challenge1.jpg"><img class="alignnone size-full wp-image-10476" src="http://mikefrobbins.com/wp-content/uploads/2014/09/oneliner-challenge1.jpg" alt="oneliner-challenge1" width="877" height="643" /></a>

After clicking &lt;OK&gt;, a list of computers is shown so you can select the computer(s) you want to move to the OU that was selected in the previous step:

<a href="http://mikefrobbins.com/wp-content/uploads/2014/09/oneliner-challenge2.jpg"><img class="alignnone size-full wp-image-10477" src="http://mikefrobbins.com/wp-content/uploads/2014/09/oneliner-challenge2.jpg" alt="oneliner-challenge2" width="849" height="474" /></a>

I've selected all of my SQL servers and when I click &lt;OK&gt;, the computer accounts are moved to the OU we previously selected and the results of what was done are displayed:

<a href="http://mikefrobbins.com/wp-content/uploads/2014/09/oneliner-challenge3.jpg"><img class="alignnone size-full wp-image-10478" src="http://mikefrobbins.com/wp-content/uploads/2014/09/oneliner-challenge3.jpg" alt="oneliner-challenge3" width="722" height="210" /></a>

See the <a href="http://foxdeploy.com/2014/09/19/impractical-one-liner-challenge/" target="_blank">Impractical One-liner Challenge</a> blog article on Stephen Owen's blog site for the specific requirements that were to be met.

Want to learn more about PowerShell? I'm presenting two sessions at <a href="http://www.sqlsaturday.com/342/eventhome.aspx" target="_blank">SQL Saturday Mobile</a> in Mobile Alabama this Saturday, September 27th. The topics I'm presenting on are "<a href="http://www.sqlsaturday.com/viewsession.aspx?sat=342&amp;sessionid=23911" target="_blank">What's New in PowerShell Version 5</a>" and "<a href="http://www.sqlsaturday.com/viewsession.aspx?sat=342&amp;sessionid=23910" target="_blank">Learn PowerShell or Die! for the DBA</a>".

µ