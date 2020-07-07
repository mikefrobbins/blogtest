---
ID: 5580
post_title: >
  Use PowerShell to Obtain a List of
  Processes Where the Executable has been
  Modified in the Past 90 Days
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2012/10/11/use-powershell-to-obtain-a-list-of-processes-where-the-executable-has-been-modified-in-the-past-90-days/
published: true
post_date: 2012-10-11 07:30:38
---
Use PowerShell to obtain a list of currently running processes where the executable file has been modified in the past 90 days.

The number of days is a parameterized value so it can be specified when running the script without having to manually modify the script each time you want to change the value. The script uses a foreach loop to iterate through each individual process that is returned by the <em>Get-Process</em> cmdlet. The process's path property must contain a value or it will not be listed. Each process is only returned once even if the same executable is running multiple times as a separate process. A new PSObject is created to combine the properties from both <em>Get-Process</em> and <em>Get-ChildItem</em> in the same output. This script uses PowerShell version 3 simplified syntax for <em>Where-Object</em> (It will only work using PowerShell version 3 unless it is modified).
<pre class="lang:ps decode:true">param (
 $days = '90'
)
foreach($process in Get-Process |
 where Path |
 select -Unique) {
 $dir = $process |
 Get-ChildItem;
New-Object -TypeName PSObject -Property @{'Name' = $process.name;
 'Description' = $process.Description;
 'File Version' = $process.FileVersion;
 'Product' = $process.Product;
 'Path' = $process.Path;
 'Modified Date' = $dir.LastWriteTime;} |
 where 'Modified Date' -gt (Get-Date).AddDays(-$days)}</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2012/10/process-modified1.png"><img class="alignnone size-full wp-image-5582" title="process-modified1" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/10/process-modified1.png" width="590" height="306" /></a>

Does this look interesting? Want to learn how to decrypt the PowerShell Help and PowerShell objects to determine if one cmdlet can be piped to another ByValue or ByPropertyName? Join me in Atlanta on October 27th at <a href="http://powershellsaturday.com/003/" target="_blank">PowerShell Saturday 003</a> where I'll be covering those concepts along with many more in my <a href="http://powershellsaturday.com/003/presentation/powershell-fundamentals-for-beginners/" target="_blank">PowerShell Fundamentals for Beginners</a> session.

µ