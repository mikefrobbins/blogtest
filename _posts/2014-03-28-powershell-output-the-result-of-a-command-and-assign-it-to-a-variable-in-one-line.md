---
ID: 9351
post_title: 'PowerShell: Output the Result of a Command and Assign it to a Variable in One Line'
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2014/03/28/powershell-output-the-result-of-a-command-and-assign-it-to-a-variable-in-one-line/
published: true
post_date: 2014-03-28 07:30:54
---
As of today, there is one month left until the <a href="http://powershell.org/wp/community-events/summit/powershell-summit-north-america/" target="_blank">PowerShell Summit North America 2014</a>. I tweeted something out last night and thought I would write a quick blog about it since I often find myself looking for a tweet months later when I can't remember how I did something that I previously tweeted out.

This tweet used all 140 characters that twitter allows:
<pre class="lang:ps decode:true">"There are $(($i=New-TimeSpan -End 2014-04-28T09:00-07).Days) days &amp; $($i.Hours) hours left until the #PowerShell Summit North America 2014"</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/03/2014-03-28_14-50-59.png"><img class="alignnone size-full wp-image-9358" alt="2014-03-28_14-50-59" src="http://mikefrobbins.com/wp-content/uploads/2014/03/2014-03-28_14-50-59.png" width="877" height="86" /></a>

This portion of the command is assigned to a variable named $i (technically it's assigned to a variable named "i"):
<pre class="lang:ps decode:true">New-TimeSpan -End 2014-04-28T09:00-07</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/03/2014-03-28_14-30-45.png"><img class="alignnone size-full wp-image-9353" alt="2014-03-28_14-30-45" src="http://mikefrobbins.com/wp-content/uploads/2014/03/2014-03-28_14-30-45.png" width="877" height="253" /></a>

It determines the amount of time until 9am on April 28, 2014 in the GMT -7 timezone (the current timezone for Seattle) so you can run this command from any time zone and it will display accurate information.

Here I've assigned the value to the variable $i and displayed the value of the days property, all in one line:

<a href="http://mikefrobbins.com/wp-content/uploads/2014/03/2014-03-28_14-38-16.png"><img class="alignnone size-full wp-image-9354" alt="2014-03-28_14-38-16" src="http://mikefrobbins.com/wp-content/uploads/2014/03/2014-03-28_14-38-16.png" width="877" height="74" /></a>

At this point $i contains the following value:
<pre class="lang:ps decode:true">$i</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/03/2014-03-28_14-41-48.png"><img class="alignnone size-full wp-image-9355" alt="2014-03-28_14-41-48" src="http://mikefrobbins.com/wp-content/uploads/2014/03/2014-03-28_14-41-48.png" width="877" height="254" /></a>

When you surround it by quotation marks you end up with a hot mess:
<pre class="lang:ps decode:true">"($i=New-TimeSpan -End 2014-04-28T09:00-07).days"</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/03/2014-03-28_14-43-41.png"><img class="alignnone size-full wp-image-9356" alt="2014-03-28_14-43-41" src="http://mikefrobbins.com/wp-content/uploads/2014/03/2014-03-28_14-43-41.png" width="877" height="71" /></a>

Enclosing the command in dollar sign parenthesis resolves the issue:
<pre class="lang:ps decode:true">"$(($i=New-TimeSpan -End 2014-04-28T09:00-07).days)"</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/03/2014-03-28_14-47-47.png"><img class="alignnone size-full wp-image-9357" alt="2014-03-28_14-47-47" src="http://mikefrobbins.com/wp-content/uploads/2014/03/2014-03-28_14-47-47.png" width="877" height="73" /></a>

Now you can use the $i variable on the same line to display the hours in addition to the days since you wouldn't want to show up to the PowerShell Summit too many hours early:
<pre class="lang:ps decode:true crayon-selected">Write-Output "There are $(($i=New-TimeSpan -End 2014-04-28T09:00-07).Days) days &amp; $($i.Hours) hours left until the #PowerShell Summit North America 2014"</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/03/2014-03-28_14-24-56.png"><img class="alignnone size-full wp-image-9352" alt="2014-03-28_14-24-56" src="http://mikefrobbins.com/wp-content/uploads/2014/03/2014-03-28_14-24-56.png" width="877" height="82" /></a>

µ