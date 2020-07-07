---
ID: 5957
post_title: >
  Remove or Change Items on your
  PowerShell People Profile
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2012/11/15/remove-or-change-items-on-your-powershell-people-profile/
published: true
post_date: 2012-11-15 07:30:17
---
First, start out by reading my blog from two days ago titled "<a href="http://mikefrobbins.com/2012/11/13/meet-the-people-of-powershell-at-powershell-orgpeople-and-powershell-net/" target="_blank">Meet the People of PowerShell at PowerShell.org/People and PowerShell.net</a>" otherwise you'll be starting in the middle of the story and this blog will only end up causing confusion.

<a href="http://powershell.org/people/" target="_blank"><img class="alignleft size-thumbnail wp-image-5910" title="powershell-net-logo" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/11/powershell-net-logo.jpg?w=150" height="70" width="150" /></a>To change an item on your <a href="http://powershell.org/people/" target="_blank">PowerShell People profile</a> page, the item has to be removed and then re-added. With that said, we'll cover removing an item and see the previous blog article referenced in the first paragraph for how to add it back.

In this example, I'll remove twitter from <a href="http://powershell.org/people/mikefrobbins" target="_blank">my PowerShell People profile</a>:

<a href="http://powershell.org/people/mikefrobbins" target="_blank"><img class="alignnone size-full wp-image-5958" title="ps-people-r1" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/11/ps-people-r1.png" height="412" width="619" /></a>

I start out by running the prerequisites region of my <a href="http://gallery.technet.microsoft.com/scriptcenter/powershellorg-people-8ba6701f" target="_blank">sample script</a> and specifying my existing username and password when prompted:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/11/posh-people1a.png"><img class="alignnone size-full wp-image-5918" title="posh-people1a" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/11/posh-people1a.png" height="366" width="615" /></a>

There's an example of removing twitter in the sample script. I've highlighted and run only that section of the script. I can see based on the results, it removed twitter:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/11/ps-people-r2.png"><img class="alignnone size-full wp-image-5959" title="ps-people-r2" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/11/ps-people-r2.png" height="296" width="567" /></a>

Refreshing the web browser shows twitter has been removed from my profile:

<a href="http://powershell.org/people/mikefrobbins" target="_blank"><img class="alignnone size-full wp-image-5960" title="ps-people-r3" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/11/ps-people-r3.png" height="412" width="621" /></a>

Let's take a look at what really happens when twitter is removed. Prior to running the script to remove twitter, I can see a twitter tag in my profile with my twitter handle in it:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/11/ps-people-r4.png"><img class="alignnone size-full wp-image-5961" title="ps-people-r4" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/11/ps-people-r4.png" height="137" width="522" /></a>

In the previous example, Format-XML is used which is a cmdlet from the <a href="http://pscx.codeplex.com/" target="_blank">PowerShell Community Extensions</a> PowerShell module (pscx).

After running the command to remove twitter from my profile, the twitter tag is completely removed:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/11/ps-people-r5.png"><img class="alignnone size-full wp-image-5962" title="ps-people-r5" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/11/ps-people-r5.png" height="116" width="517" /></a>

Where this is really helpful, is in the following scenario. I'm now going to remove my blog website from my PowerShell People profile:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/11/ps-people-r6.png"><img class="alignnone size-full wp-image-5963" title="ps-people-r6" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/11/ps-people-r6.png" height="321" width="567" /></a>

Why didn't it remove my blog website? No error or anything? There must be something wrong with the PowerShell People profile site, right? Wrong, it is a <a href="http://en.wikipedia.org/wiki/User_error" target="_blank">PICNIC</a> error.

Let's go back and see what string is in the URL field for my blog website. The URL contains a backslash at the end of it that wasn't specified in my previous attempt to remove it:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/11/ps-people-r7.png"><img class="alignnone size-full wp-image-5964" title="ps-people-r7" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/11/ps-people-r7.png" height="149" width="527" /></a>

Specifying the URL that actually exists removes the website without issue:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/11/ps-people-r8.png"><img class="alignnone size-full wp-image-5965" title="ps-people-r8" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/11/ps-people-r8.png" height="312" width="560" /></a>

You should also be able guess what the type, tag name, and data is based on this information so that removing the information via PowerShell is fairly straight forward. You could also view your data along with all the tags very easily using a GUI, but then you wouldn't have to learn how to do this in PowerShell and what fun what that be?

µ