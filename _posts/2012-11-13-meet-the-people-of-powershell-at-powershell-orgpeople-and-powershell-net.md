---
ID: 5909
post_title: >
  Meet the People of PowerShell at
  PowerShell.org/People and PowerShell.net
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2012/11/13/meet-the-people-of-powershell-at-powershell-orgpeople-and-powershell-net/
published: true
post_date: 2012-11-13 07:30:28
---
Do you consider yourself a PowerShell person? If you haven't heard, there's a new online directory of the PowerShell community called <a href="http://powershell.org/people/" target="_blank">PowerShell People</a>. The site is a place to find other PowerShell enthusiast's blogs, webs sites, twitter feeds, and other items they've chosen to share.

<a href="http://powershell.org/people/" target="_blank"><img class="alignleft size-thumbnail wp-image-5910" title="powershell-net-logo" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/11/powershell-net-logo.jpg?w=150" height="70" width="150" /></a>What's needed to get a profile page on the PowerShell People community site? Simply a knowledge of PowerShell. You heard me correctly, a knowledge of PowerShell is the only prerequisite for creating a PowerShell People Profile. Why do I need to know PowerShell to create a profile? It's not called PowerShell People for nothing, you actually use PowerShell to create your PowerShell People profile on the site.

I'm going to walk you through the entire process of creating and customizing your profile. First, <a href="http://powershell.org/people/login.php" target="_blank">visit the PowerShell People site and accept the terms and conditions</a> before creating a profile. Second, download my <a href="http://gallery.technet.microsoft.com/scriptcenter/powershellorg-people-8ba6701f" target="_blank">sample script</a> which was updated on November 12, 2012.

Highlight the prerequisites section of the script and run just that portion:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/11/posh-people1a.png"><img class="alignnone size-full wp-image-5918" title="posh-people1a" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/11/posh-people1a.png" height="366" width="615" /></a>

If you don't have a profile yet, modify the "Create Your Profile Page" region to contain your full name the way you want it to be shown on your profile page, then run that region of the script to create your profile page. This should only be run once:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/11/posh-people5a.png"><img class="alignnone size-full wp-image-5943" title="posh-people5a" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/11/posh-people5a.png" height="246" width="615" /></a>

I'm not actually going to run that region since I already have a profile. If I did run it, a profile page with the username I specified in the prerequisites section would be created. The username is part of the URL: <a href="http://powershell.org/people/mikefrobbins" target="_blank">http://powershell.org/people/<span style="color:#ff0000;">mikefrobbins</span></a> and <a href="http://powershell.net/mikefrobbins" target="_blank">http://powershell.net/<span style="color:#ff0000;">mikefrobbins</span></a> so keep this in mind when choosing a username.

Your basic PowerShell people profile page has now been created:

<a href="http://powershell.org/people/mikefrobbins" target="_blank"><img class="alignnone size-full wp-image-5919" title="posh-people3" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/11/posh-people3.jpg" height="357" width="640" /></a>

The next important thing to do is to check the interface version to make sure it matches the version of the sample script you've downloaded otherwise you may end up with unexpected results. You have to specify a username and password to check the version otherwise we would have checked it earlier:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/11/posh-people2.png"><img class="alignnone size-full wp-image-5917" title="posh-people2" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/11/posh-people2.png" height="293" width="520" /></a>

Modify the sample script to contain your information. Here's an example of modifying the twitter section of the script to contain my information:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/11/posh-people4.png"><img class="alignnone size-full wp-image-5923" title="posh-people4" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/11/posh-people4.png" height="232" width="557" /></a>

Make sure you have the "Populate Your Profile" region customized with your information and you may want to start out by adding an item or two at a time to make sure you understand how this process works. I'm going to run the entire region:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/11/posh-people3.png"><img class="alignnone size-full wp-image-5920" title="posh-people3" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/11/posh-people3.png" height="486" width="452" /></a>

My entire profile is now populated with the information from my personalized script:

<a href="http://powershell.org/people/mikefrobbins" target="_blank"><img class="alignnone size-full wp-image-5921" title="posh-people4" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/11/posh-people4.jpg" height="643" width="640" /></a>

I've been working with <a href="http://powershell.org/people/donj" target="_blank">Don Jones</a> over the past several days to identify issues with the site. Be warned though, the site is still in beta so be sure to save your personalized script in case you need to recreate your profile. I want to thank Don for giving me the opportunity to work with him on this project.

Check back on Thursday, November 15th when I'll continue this blog by discussing <a href="http://mikefrobbins.com/2012/11/15/remove-or-change-items-on-your-powershell-people-profile/" target="_blank">how to remove items</a> (currently the way to modify an item is to remove and re-add it). I'll also cover some of the issues that I ran into and how to resolve those issues.

µ