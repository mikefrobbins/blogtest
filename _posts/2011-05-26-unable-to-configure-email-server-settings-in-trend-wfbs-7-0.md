---
ID: 2312
post_title: >
  Unable to Configure Email Server
  Settings in Trend WFBS 7.0
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2011/05/26/unable-to-configure-email-server-settings-in-trend-wfbs-7-0/
published: true
post_date: 2011-05-26 07:30:41
---
Problem:
When attempting to configure email server specific settings in Trend Micro Worry Free Business Security – Advanced 7.0, you receive the error: “Internet Explorer cannot display the webpage”:
<a href="http://mikefrobbins.com/wp-content/uploads/2011/05/tecp1.png"><img class="alignnone size-full wp-image-2315" title="tecp1" src="http://mikefrobbins.com/wp-content/uploads/2011/05/tecp1.png" alt="" width="640" height="192" /></a>

When you hover over an option such as “Disable” in the Anti-spam section, you notice a URL that points to your email server. By default it points to port 16372 on the email server. This is the first clue that gives you an idea that this is potentially a problem with the firewall on the email server:
<a href="http://mikefrobbins.com/wp-content/uploads/2011/05/tecp2.png"><img class="alignnone size-full wp-image-2316" title="tecp2" src="http://mikefrobbins.com/wp-content/uploads/2011/05/tecp2.png" alt="" width="640" height="436" /></a>

When looking at the firewall settings on the email server for the “Trend Micro Messaging Security Agent”, it shows only the “Public” network as being allowed by default:
<a href="http://mikefrobbins.com/wp-content/uploads/2011/05/tecp3.png"><img class="alignnone size-full wp-image-2317" title="tecp3" src="http://mikefrobbins.com/wp-content/uploads/2011/05/tecp3.png" alt="" width="566" height="254" /></a>

Looking at the details of the firewall entry for the "Trend Micro Messaging Security Agent" shows that it does indeed allow port “16372":
<a href="http://mikefrobbins.com/wp-content/uploads/2011/05/tecp4.png"><img class="alignnone size-full wp-image-2318" title="tecp4" src="http://mikefrobbins.com/wp-content/uploads/2011/05/tecp4.png" alt="" width="393" height="318" /></a>

Solution:
In the firewall settings on the email server, allow the “Domain” network access to the “Trend Micro Messaging Security Agent” by checking the appropriate checkbox as shown in the image below:
<a href="http://mikefrobbins.com/wp-content/uploads/2011/05/tecp5.png"><img class="alignnone size-full wp-image-2319" title="tecp5" src="http://mikefrobbins.com/wp-content/uploads/2011/05/tecp5.png" alt="" width="567" height="254" /></a>

You should now be able to configure the email server specific settings from within the Trend Worry Free Business Security console.

µ