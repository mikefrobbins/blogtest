---
ID: 3442
post_title: How Do I Get-Started With PowerShell?
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2012/03/29/how-do-i-get-started-with-powershell/
published: true
post_date: 2012-03-29 07:30:40
---
Commands in PowerShell are called cmdlets (pronounced "command lets") and they are in the form of singular verb-noun commands like <em>Get-Alias</em> (not <em>Get-Aliases</em>).

In my opinion, there are three cmdlets that are the key to figuring out how to use PowerShell and finding help when you need it. These cmdlets are:

<em>Get-Help</em> (<em>help</em>)
<em>Get-Command</em> (<em>gcm</em>)
<em>Get-Member</em> (<em>gm</em>)

<em>Get-Help</em> or <em>Help</em> for short. Help isn't actually an alias, but it works exactly the same way if your using the PowerShell ISE. I've run <em>help *alias*</em> in the screenshot below:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/03/ps102-1.png"><img class="alignnone size-full wp-image-3443" title="ps102-1" src="http://mikefrobbins.com/wp-content/uploads/2012/03/ps102-1.png" alt="" width="640" height="118" /></a>

As you can see in the above screenshot, help doesn't search for cmdlets, it searches for help topics and while it can help you find cmdlets, its power is in displaying the help topic for a particular cmdlet such as when I run <em>help Get-Help </em>to display the help topic on <em>Get-Help</em>:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/03/ps102-21.png"><img class="alignnone size-full wp-image-3460" title="ps102-21" src="http://mikefrobbins.com/wp-content/uploads/2012/03/ps102-21.png" alt="" width="640" height="172" /></a>

You can use the <em>-Detailed</em>, <em>-Examples</em>, <em>-Full</em>, or <em>-Online</em> optional parameters with the help function to display additional information or the online help depending on which one is used.

One of the lesser publicized  parameters that's useful if you want to know something specific about a particular parameter for a cmdlet is the <em>-Parameter</em> parameter which gives you detailed information about a particular parameter as shown below:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/03/ps102-22.png"><img class="alignnone size-full wp-image-3571" title="ps102-22" src="http://mikefrobbins.com/wp-content/uploads/2012/03/ps102-22.png" alt="" width="640" height="155" /></a>

The cmdlet that's better for searching for actual cmdlets is <em>Get-Command</em> because it has an option in one of its parameter sets for searching for the CommandType of Cmdlet or cmdlets that use certain verbs and/or nouns via its other parameter set. You can't use both the <em>-CommandType</em> and (<em>-Noun</em> or <em>-Verb</em>) options at the same time since they're in different parameter sets. There's also an option to search for cmdlets that a module added.

<a href="http://mikefrobbins.com/wp-content/uploads/2012/03/ps102-3.png"><img class="alignnone size-full wp-image-3445" title="ps102-3" src="http://mikefrobbins.com/wp-content/uploads/2012/03/ps102-3.png" alt="" width="640" height="147" /></a>

I specified the <em>-Full</em> parameter in the above command even though you can't see all of the returned information in the screenshot. It contains very useful information such as whether or not parameters are required, positional, what type of input they accept, etc:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/03/ps102-5.png"><img class="alignnone size-full wp-image-3462" title="ps102-5" src="http://mikefrobbins.com/wp-content/uploads/2012/03/ps102-5.png" alt="" width="640" height="146" /></a>

If I'm looking for a cmdlet so I can find out what aliases exist in PowerShell to keep from having to always type out full cmdlet names. I'm going to use <em>Get-Command</em> to search for all cmdlets about aliases. I'm going to use wildcards on each side of the singular noun "alias" since I'm unsure of the actual cmdlet name.

<a href="http://mikefrobbins.com/wp-content/uploads/2012/03/ps102-6.png"><img class="alignnone size-full wp-image-3463" title="ps102-6" src="http://mikefrobbins.com/wp-content/uploads/2012/03/ps102-6.png" alt="" width="404" height="127" /></a>

Now that I have the cmdlet name I want to use (<em>Get-Alias</em>), I can get the help topic on it to figure out how to use it:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/03/ps102-7.png"><img class="alignnone size-full wp-image-3464" title="ps102-7" src="http://mikefrobbins.com/wp-content/uploads/2012/03/ps102-7.png" alt="" width="640" height="294" /></a>

I want to know if there are any aliases for <em>Get-Command</em>. I'm going to use the <em>-Definition</em> optional parameter with <em>Get-Alias</em> to return the aliases for <em>Get-Command</em>. I've piped the results of this command to <em>Format-Table</em> and used the <em>-Autosize</em> parameter to keep the results as narrow as possible for easier viewing on this blog.

<a href="http://mikefrobbins.com/wp-content/uploads/2012/03/ps102-8.png"><img class="alignnone size-full wp-image-3465" title="ps102-8" src="http://mikefrobbins.com/wp-content/uploads/2012/03/ps102-8.png" alt="" width="499" height="75" /></a>

I can see that the alias for <em>Get-Command</em> is "<em>gcm</em>" so I can use that alias instead of typing out the full command. If you're writing a script or providing code to someone else, use the full cmdlet name instead of the alias.

I'll cover using the <em>-Module</em> optional parameter for <em>Get-Command</em> in my next blog article and the <em>Get-Member</em> cmdlet in a future blog article.

µ