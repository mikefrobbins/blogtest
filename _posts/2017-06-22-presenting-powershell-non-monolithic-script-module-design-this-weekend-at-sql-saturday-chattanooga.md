---
ID: 15339
post_title: >
  Presenting PowerShell Non-Monolithic
  Script Module Design this weekend at SQL
  Saturday Chattanooga
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2017/06/22/presenting-powershell-non-monolithic-script-module-design-this-weekend-at-sql-saturday-chattanooga/
published: true
post_date: 2017-06-22 07:30:10
---
If you’re interested in some free PowerShell training, I’ll be presenting a session on "<a href="http://www.sqlsaturday.com/624/Sessions/Details.aspx?sid=64554" target="_blank" rel="noopener noreferrer">PowerShell Non-Monolithic Script Module Design</a>" at <a href="http://www.sqlsaturday.com/624/eventhome.aspx" target="_blank" rel="noopener noreferrer">SQL Saturday #624</a> in Chattanooga, Tennessee this Saturday, June 24th.

<a href="http://www.sqlsaturday.com/624/eventhome.aspx" target="_blank" rel="noopener noreferrer"><img class="alignnone wp-image-15340 size-full" src="http://mikefrobbins.com/wp-content/uploads/2017/06/sqlsat624.png" alt="" width="600" height="151" /></a>

My presentation begins at 1:15pm eastern time and is an intermediate level session. There's also a <a href="https://www.eventbrite.com/e/building-scalable-robust-solutions-in-sql-powershell-tickets-33182821713" target="_blank" rel="noopener noreferrer">PowerShell precon</a> on Friday being presented by Microsoft MVP <a href="https://twitter.com/SQLvariant" target="_blank" rel="noopener noreferrer">Aaron Nelson</a> (the precon is not free) and a <a href="http://www.sqlsaturday.com/624/Sessions/Details.aspx?sid=59605" target="_blank" rel="noopener noreferrer">PowerShell DSC (Desired State Configuration) session</a> on Saturday being presented by Microsoft MVP <a href="https://twitter.com/TechTrainerTim" target="_blank" rel="noopener noreferrer">Tim Warner</a>.

Here's a little information about what you can expect from my session:

<strong>PowerShell Non-Monolithic Script Module Design</strong>
<em>Creating a script module in PowerShell is a very simplistic process, but there are a number of reasons why you might not want to create one huge monolithic PSM1 script module file that contains all of your module’s functions. During this session, Microsoft MVP Mike F Robbins will demonstrate how to separate each of your module’s functions into its own dedicated PS1 file that’s dot-sourced from your script module’s PSM1 file along with discussing this design methodology and the challenges that it creates. Many times resolving one problem seems to create more problems such as cmdlets from other modules showing up as being exported by your module. The solution to these problems and more will be provided during this session. Mike will also demonstrate using a Pester test to validate that all of the functions are indeed exported along with using a function to help automate the module manifest update process when additional functions are added to your module.</em>

My session will be located in room 203 of the UT Chattanooga EMC building. The address is 784 Vine Street, Chattanooga, Tennessee 37403 (USA). Be sure to check <a href="http://www.sqlsaturday.com/624/Sessions/Schedule.aspx" target="_blank" rel="noopener noreferrer">the event schedule</a> for any last minute changes.

The PowerShell code used during my demonstrations and the slide deck from my session will be uploaded to <a href="https://github.com/mikefrobbins/Presentations" target="_blank" rel="noopener noreferrer">my presentations repository on GitHub</a>.

I hope to see you there!

µ