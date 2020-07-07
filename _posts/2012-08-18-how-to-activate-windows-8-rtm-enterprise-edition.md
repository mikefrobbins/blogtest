---
ID: 5207
post_title: >
  How to Activate Windows 8 RTM Enterprise
  Edition
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2012/08/18/how-to-activate-windows-8-rtm-enterprise-edition/
published: true
post_date: 2012-08-18 07:30:27
---
If you installed Windows 8 RTM Professional, you were prompted for a license key before it would even get started with the installation, but if you installed Windows 8 RTM Enterprise Edition, it didn't even prompt for a license key during the installation process.

Going to "Windows Activation" in control panel, shows "Error Code 0x8007232B DNS name does not exist." in the Activation details section:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/08/activate-win8-1.jpg"><img class="alignnone size-full wp-image-5208" title="activate-win8-1" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/08/activate-win8-1.jpg" width="640" height="346" /></a>

I couldn't find a place to enter a license key so they're probably assuming you have a Key Management Service (KMS) server running on your network since this is "Enterprise" edition. To work around this, open a command prompt as an admin. This is done by right clicking in an empty area on the metro interface screen and then selecting "All apps":

<a href="http://mikefrobbins.com/wp-content/uploads/2012/08/win8-metro-allapps.jpg"><img class="alignnone size-full wp-image-5209" title="win8-metro-allapps" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/08/win8-metro-allapps.jpg" width="305" height="172" /></a>

Right click on "Command Prompt" and then click "Run as administrator":

<a href="http://mikefrobbins.com/wp-content/uploads/2012/08/win8-metro-cmdasadmin.jpg"><img class="alignnone size-full wp-image-5210" title="win8-metro-cmdasadmin" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/08/win8-metro-cmdasadmin.jpg" width="640" height="363" /></a>

Run the following command, replacing the X's with a valid Multiple Activation Key (MAK):
<pre class="lang:batch decode:true">slmgr /ipk xxxxx-xxxxx-xxxxx-xxxxx-xxxxx</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2012/08/activate-win8-2.jpg"><img class="alignnone size-full wp-image-5212" title="activate-win8-2" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/08/activate-win8-2.jpg" width="495" height="89" /></a>

You'll receive a popup stating the product key was successfully installed:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/08/activate-win8-3.jpg"><img class="alignnone size-full wp-image-5213" title="activate-win8-3" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/08/activate-win8-3.jpg" width="455" height="155" /></a>

Refresh the Activation page and it will now show activated (with Internet access):

<a href="http://mikefrobbins.com/wp-content/uploads/2012/08/activate-win8-4.jpg"><img class="alignnone size-full wp-image-5214" title="activate-win8-4" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/08/activate-win8-4.jpg" width="565" height="357" /></a>

<span style="text-decoration: underline;">Update 8/18/12 5:30pm CST</span>
If you work in the technology sector and you're not on Twitter, you should be. After posting this blog and tweeting that it was posted, Jan Egil Ring (<a href="http://twitter.com/JanEgilRing" target="_blank">@JanEgilRing</a>) replied to my tweet stating that "slui 3" was another option so I decided to test it out:

On Windows 8 Enterprise Edition (RTM), from a "Command Prompt" that was run as an admin, the following command prompts for a license key using a GUI interface:
<pre class="lang:batch decode:true">slui 3</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2012/08/activate-win8-5.jpg"><img class="alignnone size-full wp-image-5229" title="activate-win8-5" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/08/activate-win8-5.jpg" width="640" height="484" /></a>

µ