---
ID: 18093
post_title: 'PowerShell Productivity Hacks: How I use Get-Command'
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2019/09/05/powershell-productivity-hacks-how-i-use-get-command/
published: true
post_date: 2019-09-05 07:30:53
---
If you've been using PowerShell for very long at all, then you should already be familiar with <em>Get-Command</em> and <em>Get-Help</em>. While I like to say that <em>Get-Command</em> is for finding commands and <em>Get-Help</em> is for learning how to use those commands once you've found them, there is overlap between these two commands depending on how you use them.

I believe in following the best practice of not using aliases or positional parameters in any code that I save or share with others, but in this blog article, I'm going to show how things work in the real world. I will provide the full cmdlet and parameter names and then show what I really type since the code is not shared or saved at that point in time.

Who really wants to read pages and pages of help information for a command when much of it is irrelevant to what you're trying to accomplish? Once I've found a PowerShell command to accomplish the task at hand, I use <em>Get-Command</em> with the <em>Syntax</em> parameter to quickly determine how to use it.
<pre class="lang:ps decode:true">Get-Command -Name Get-NetAdapter -Syntax
gcm Get-NetAdapter -Syntax</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2019/08/gcm-syntax-param1a.jpg"><img class="alignnone size-full wp-image-18094" src="https://mikefrobbins.com/wp-content/uploads/2019/08/gcm-syntax-param1a.jpg" alt="" width="859" height="200" /></a>

All of the syntax that's returned means something. Square brackets means optional unless it's two square brackets together and that means it accepts more than one value.

I can see that <em>Get-NetAdapter</em> has three parameter sets.

<a href="https://mikefrobbins.com/wp-content/uploads/2019/08/gcm-syntax-param3a.jpg"><img class="alignnone size-full wp-image-18098" src="https://mikefrobbins.com/wp-content/uploads/2019/08/gcm-syntax-param3a.jpg" alt="" width="859" height="200" /></a>

In the first parameter set, I can tell that the <em>Name</em> parameter isn't mandatory because the parameter and the type of value it accepts are both completely enclosed in square brackets.

<a href="https://mikefrobbins.com/wp-content/uploads/2019/08/gcm-syntax-param4a.jpg"><img class="alignnone size-full wp-image-18099" src="https://mikefrobbins.com/wp-content/uploads/2019/08/gcm-syntax-param4a.jpg" alt="" width="859" height="200" /></a>

Within those, I can tell that the <em>Name</em> parameter is positional because it's surrounded by its own set of square brackets.

<a href="https://mikefrobbins.com/wp-content/uploads/2019/08/gcm-syntax-param5a.jpg"><img class="alignnone size-full wp-image-18100" src="https://mikefrobbins.com/wp-content/uploads/2019/08/gcm-syntax-param5a.jpg" alt="" width="859" height="200" /></a>

When the <em>Name</em> parameter is used positionally, it must be in the first position since it's listed first.

<a href="https://mikefrobbins.com/wp-content/uploads/2019/08/gcm-syntax-param6a.jpg"><img class="alignnone size-full wp-image-18101" src="https://mikefrobbins.com/wp-content/uploads/2019/08/gcm-syntax-param6a.jpg" alt="" width="859" height="200" /></a>

The <em>Name</em> parameter accepts a datatype of string because that's what follows the name of the parameter.

<a href="https://mikefrobbins.com/wp-content/uploads/2019/08/gcm-syntax-param7a.jpg"><img class="alignnone size-full wp-image-18102" src="https://mikefrobbins.com/wp-content/uploads/2019/08/gcm-syntax-param7a.jpg" alt="" width="859" height="200" /></a>

Also, it accepts an array of strings because two square brackets follow the datatype for that particular parameter.

<a href="https://mikefrobbins.com/wp-content/uploads/2019/08/gcm-syntax-param8a.jpg"><img class="alignnone size-full wp-image-18103" src="https://mikefrobbins.com/wp-content/uploads/2019/08/gcm-syntax-param8a.jpg" alt="" width="859" height="200" /></a>

Notice that switch parameters such as <em>IncludeHidden</em> don't have a datatype after them.

<a href="https://mikefrobbins.com/wp-content/uploads/2019/08/gcm-syntax-param9a.jpg"><img class="alignnone size-full wp-image-18104" src="https://mikefrobbins.com/wp-content/uploads/2019/08/gcm-syntax-param9a.jpg" alt="" width="859" height="200" /></a>

If a switch parameter is specified, it's on or true and if it's not specified, it's off or false. Depending on what's being done, a switch parameter such as <em>Confirm</em> might be on by default. To turn it off, you can specify "<em>-Confirm:$false</em>" without the quotes. Also, to use a switch parameter in a hash table when splatting, set the value using the Boolean $true or $false. Example: "<em>Confirm = $false</em>".

If for some reason, I need to know more about a particular parameter, I'll view the help for that particular parameter instead of all of the full help for the entire command.
<pre class="lang:ps decode:true ">help Get-NetAdapter -Parameter Name</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2019/08/gcm-syntax-param2a.jpg"><img class="alignnone size-full wp-image-18097" src="https://mikefrobbins.com/wp-content/uploads/2019/08/gcm-syntax-param2a.jpg" alt="" width="859" height="254" /></a>

Thoughts, questions, comments? Please post them as a comment to this blog article.

µ