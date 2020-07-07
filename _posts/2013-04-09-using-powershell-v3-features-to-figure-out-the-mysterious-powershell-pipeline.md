---
ID: 7067
post_title: >
  Using PowerShell v3 Features to Figure
  Out the Mysterious PowerShell Pipeline
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2013/04/09/using-powershell-v3-features-to-figure-out-the-mysterious-powershell-pipeline/
published: true
post_date: 2013-04-09 07:30:54
---
I actually wrote this blog a couple of weeks ago and postponed it until I could do further testing because I thought of some scenarios where it possibly wouldn’t work. I wrote a couple of new blogs over the past couple of weeks and forgot about this one. Low and behold, I had rescheduled this blog for today and forgot all about it so I had two blogs posted today!

I also have to say the PowerShell community is awesome! I had a PowerShell MVP send me a very courteous email that confirmed my previous thoughts. That this blog wasn’t really accurate so I decided to do a complete re-write and here it is:

There are two ways of connecting PowerShell commands together via the pipeline, one is "By Value" and the other is "By Property Name".

Initially, I thought I could use a new feature of PowerShell version 3, the “<em>-ParameterType</em>” parameter of the <em>Get-Command</em> cmdlet to determine what cmdlets could accept pipeline input (I was wrong). What it can do is help you find what cmdlets accept that that type of input, although not necessarily by pipeline. It could be via pipeline input and/or via parenthesis after the parameter name depending on if that parameter actually accepts pipeline input "By Value" for that particular parameter.

Here’s the details of the “<em>-ParameterType</em>” parameter of the <em>Get-Command</em> cmdlet:

<a href="http://mikefrobbins.com/wp-content/uploads/2013/04/gcm-paramtype0.png"><img class="alignnone size-large wp-image-7068" alt="gcm-paramtype0" src="http://mikefrobbins.com/wp-content/uploads/2013/04/gcm-paramtype0.png?w=640" width="640" height="205" /></a>

To determine what PowerShell cmdlets can accept input from the <em>Get-VM</em> cmdlet that's part of the Hyper-V Module, start out by piping <em>Get-VM</em> to <em>Get-Member</em> as shown in the following example to determine what type of object <em>Get-VM</em> produces:

<a href="http://mikefrobbins.com/wp-content/uploads/2013/04/gcm-paramtype1.png"><img class="alignnone size-full wp-image-7069" alt="gcm-paramtype1" src="http://mikefrobbins.com/wp-content/uploads/2013/04/gcm-paramtype1.png" width="532" height="146" /></a>

The <em>Get-VM</em> cmdlet produces a "Microsoft.HyperV.PowerShell.VirtualMachine" object which is commonly referred to as the last portion of the name after the last dot. In the previous example, it would be called a "VirtualMachine" object.

Now we'll run the <em>Get-Command</em> cmdlet specifying the <em>-ParameterType</em> parameter and use the object type name that we previously determined that <em>Get-VM</em> produced as the value for the <em>-ParameterType</em> parameter as shown in the following example:

<a href="http://mikefrobbins.com/wp-content/uploads/2013/04/gcm-paramtype2.png"><img class="alignnone size-large wp-image-7070" alt="gcm-paramtype2" src="http://mikefrobbins.com/wp-content/uploads/2013/04/gcm-paramtype2.png?w=640" width="640" height="446" /></a>

This returned a list of cmdlets that can each accept input from the <em>Get-VM</em> cmdlet. A subset of the returned results are shown in the previous image. A total of 82 cmdlets were returned on my Windows 8 machine that has the Hyper-V PowerShell module installed. That's a lot of cmdlets that can accept input from the <em>Get-VM</em> cmdlet. Try figuring that out manually, but if you're using PowerShell, why would you want to do anything manually?

<a href="http://mikefrobbins.com/wp-content/uploads/2013/04/gcm-paramtype3.png"><img class="alignnone size-large wp-image-7071" alt="gcm-paramtype3" src="http://mikefrobbins.com/wp-content/uploads/2013/04/gcm-paramtype3.png?w=640" width="640" height="51" /></a>

One thing I found during my testing, is you can specify the last portion of the object type as shown in this example of piping <em>Get-Process</em> to <em>Get-Member</em>:

<a href="http://mikefrobbins.com/wp-content/uploads/2013/04/gcm-paramtype4.png"><img class="alignnone size-full wp-image-7072" alt="gcm-paramtype4" src="http://mikefrobbins.com/wp-content/uploads/2013/04/gcm-paramtype4.png" width="530" height="135" /></a>

The same results are returned with only the last portion of the object type or the entire object type:

<a href="http://mikefrobbins.com/wp-content/uploads/2013/04/gcm-paramtype5.png"><img class="alignnone size-large wp-image-7073" alt="gcm-paramtype5" src="http://mikefrobbins.com/wp-content/uploads/2013/04/gcm-paramtype5.png?w=640" width="640" height="271" /></a>

When I tested this against a function such as <em>Get-NetAdapter</em>, specifying the entire object type returned no results.

<a href="http://mikefrobbins.com/wp-content/uploads/2013/04/gcm-paramtype6.png"><img class="alignnone size-large wp-image-7074" alt="gcm-paramtype6" src="http://mikefrobbins.com/wp-content/uploads/2013/04/gcm-paramtype6.png?w=640" width="640" height="117" /></a>

Specifying only the last portion after the last forward slash returned the expected results:

<a href="http://mikefrobbins.com/wp-content/uploads/2013/04/gcm-paramtype7.png"><img class="alignnone size-full wp-image-7075" alt="gcm-paramtype7" src="http://mikefrobbins.com/wp-content/uploads/2013/04/gcm-paramtype7.png" width="569" height="191" /></a>

That's neat and all, but can't I accomplish all of this in a one-liner instead of having to pipe a cmdlet to <em>Get-Member</em> and then run a second command to figure out what cmdlets accept the output of the first cmdlet as their input? Sure, as a matter of fact, this exact example is #14 in the help for <em>Get-Command</em>:

<a href="http://mikefrobbins.com/wp-content/uploads/2013/04/gcm-paramtype8.png"><img class="alignnone size-full wp-image-7082" alt="gcm-paramtype8" src="http://mikefrobbins.com/wp-content/uploads/2013/04/gcm-paramtype8.png" width="617" height="187" /></a>

The <em>-ParameterType</em> parameter along with the <em>-ParameterName</em> parameter of the <em>Get-Command</em> cmdlet are some of the best hidden and most underrated features in PowerShell version 3.

<span style="line-height:1.5;">µ</span>