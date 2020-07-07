---
ID: 7601
post_title: 'PowerShell Version 4 New Feature: A UserName Property Has Been Added To Get-Process Output Objects'
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2013/06/27/powershell-version-4-new-feature-a-username-property-has-been-added-to-get-process-output-objects/
published: true
post_date: 2013-06-27 07:30:09
---
Thanks to a tweet from <a href="http://twitter.com/PowerSchill" target="_blank">Mark Schill</a>, I discovered that <em>Get-Process</em> now has an "<em>IncludeUserName</em>" parameter in PowerShell version 4:

<a href="http://twitter.com/PowerSchill" target="_blank"><img alt="ps4-getprocess-tweet" src="http://mikefrobbins.com/wp-content/uploads/2013/06/ps4-getprocess-tweet.png" width="404" height="63" /></a>

This means no more having to use WMI to determine what processes are being run by which users:

<a href="http://mikefrobbins.com/wp-content/uploads/2013/06/ps4-getprocess1.png"><img class="alignnone size-full wp-image-7603" alt="ps4-getprocess1" src="http://mikefrobbins.com/wp-content/uploads/2013/06/ps4-getprocess1.png" width="560" height="501" /></a>

<span style="line-height: 1.5;">For those of us who work in a terminal services environment, this is a really big deal.</span>

I do find it odd that it was added via a switch parameter instead of just making it another property. It kind of reminds me of how the Active Directory cmdlets are implemented. My initial guess was there must be some additional overhead in retrieving this information and they didn't want to incur the resource cost unless the user was intentionally trying to retrieve this information.

This one additional "<em>UserName</em>" property only shows up in the output when the <em>-IncludeUserName</em> parameter is specified:

<a href="http://mikefrobbins.com/wp-content/uploads/2013/06/ps4-getprocess2.png"><img class="alignnone size-full wp-image-7605" alt="ps4-getprocess2" src="http://mikefrobbins.com/wp-content/uploads/2013/06/ps4-getprocess2.png" width="603" height="261" /></a>

One thing that's a little disappointing to see is the <em>-IncludeUserName</em> parameter can't be used with the <em>-ComputerName</em> parameter to target remote computers directly without using PowerShell remoting to receive this information. That's probably why it's implemented as a switch parameter. There must be an issue where it doesn't work natively against remote computers so it was implemented as a switch parameter so it could excluded from the parameter sets that include a <em>-ComputerName</em> parameter:

<a href="http://mikefrobbins.com/wp-content/uploads/2013/06/ps4-getprocess3.png"><img class="alignnone size-large wp-image-7620" alt="ps4-getprocess3" src="http://mikefrobbins.com/wp-content/uploads/2013/06/ps4-getprocess3.png?w=640" width="640" height="298" /></a>

Why is this a big deal? I should have no issue installing PowerShell version 4 on my workstation as soon as it's released, but I have machines that won't run PowerShell version 4 and it may take a while to get approval to install PowerShell version 4 on the ones it will run on. I was hoping to be able to target remote computers with this cmdlet from my workstation without the requirement of needing PowerShell version 4 installed on the remote computers. This is still possible, but in that scenario, the cmdlet will work just like it did in PowerShell version 2 and 3 which means no user information will be returned as it will be using one of the first three parameter sets shown in the previous image.

I double checked to make sure this wasn't a mistake in the help documentation and sure enough, the <em>-IncludeUserName</em> parameter does not work when also specifying the <em>-ComputerName</em> parameter:

<a href="http://mikefrobbins.com/wp-content/uploads/2013/06/ps4-getprocess4.png"><img class="alignnone size-large wp-image-7624" alt="ps4-getprocess4" src="http://mikefrobbins.com/wp-content/uploads/2013/06/ps4-getprocess4.png?w=640" width="640" height="491" /></a>

I guess it's back to using WMI in that scenario after all.

Same thing applies for the <em>-IncludeUserName</em> parameter and <em>-FileVersionInfo</em> or <em>-Module</em> parameters. Since they live in different parameter sets, they're mutually exclusive.

<a href="http://mikefrobbins.com/wp-content/uploads/2013/06/ps4-getprocess5.png"><img class="alignnone size-large wp-image-7625" alt="ps4-getprocess5" src="http://mikefrobbins.com/wp-content/uploads/2013/06/ps4-getprocess5.png?w=640" width="640" height="384" /></a>

This isn't really an issue with these other parameters though since the information is there and you can simply select it from the pipeline as shown in the following example where I've returned the information for the <em>-IncludeUserName</em> and what would have been returned for the <em>-FileVersionInfo</em> parameter manually:

<a href="http://mikefrobbins.com/wp-content/uploads/2013/06/ps4-getprocess6.png"><img class="alignnone size-large wp-image-7627" alt="ps4-getprocess6" src="http://mikefrobbins.com/wp-content/uploads/2013/06/ps4-getprocess6.png?w=640" width="640" height="112" /></a>

It makes me wonder why those two parameters can't be used together if I can return the information as in the example in the previous image? All of this is subject to change of course since this information is based off of PowerShell version 4 running on a preview version of Windows Server 2012 R2.

Interested in learning more about PowerShell version 4? See the “<a href="http://technet.microsoft.com/library/hh857339.aspx" target="_blank">What’s New in Windows PowerShell</a>” article on TechNet.

µ