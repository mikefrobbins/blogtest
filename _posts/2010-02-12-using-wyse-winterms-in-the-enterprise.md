---
ID: 505
post_title: >
  Using Wyse Winterms in the Enterprise to
  Reduce TCO
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2010/02/12/using-wyse-winterms-in-the-enterprise/
published: true
post_date: 2010-02-12 18:24:18
---
<a href="http://mikefrobbins.com/wp-content/uploads/2010/02/wt1200.jpg"><img class="alignleft size-full wp-image-506" title="wt1200" alt="" src="http://mikefrobbins.com/wp-content/uploads/2010/02/wt1200.jpg" width="159" height="130" /></a>Wyse Winterms are a cost effective solution for non-power users in your enterprise environment. They are great for users who use office, email, the Internet, etc, and they will handle most of the main stream applications that I have seen in the educational, financial, healthcare, and manufacturing industries. Models like the retired 1200LE and its replacement, the <a href="http://www.wyse.com/products/hardware/thinclients/S10/index.asp" target="_blank">S10</a> are good choices. They are low cost, easy to maintain, are more eco-friendly than computers since they use a lot less power (average power usage for the S10 is 6.6 watts including the keyboard, mouse, and monitor), produce less heat which reduces cooling requirements and cost, and their life span is at least twice that of a normal desktop computer. Wyse says the S10 has a lifespan of 5 to 7 years, but they last a lot longer than that. I have several customers with tons of 1200LE's still in production and that model was end-of-life'd by Wyse in 2006. The same winterms have been used with Terminal Servers running Windows 2000 Server , Windows Server 2003, and soon they will be used with Windows Server 2008. Both of the models listed above are dumb terminals without any hard drive (no moving parts in them at all) which means there's no software installed locally to maintain. Unlike computers, the individual clients have no programs installed to worry about updating, no virus or spyware problems on them, and upgrades are a snap since they're all done centrally on the terminal servers. Security is also increased because if a winterm is stolen, there's no data on it to worry about.

You will need at least one terminal server or preferably a terminal services farm with more than one terminal server in it for redundancy and scalability. There are a couple of special options that are required on your DHCP Server that directs the winterms to an FTP server and the directory in which it’s boot configuration files are stored. The instructions listed below are based off of remotely managing a DHCP server using the “<a href="http://www.microsoft.com/downloads/details.aspx?FamilyID=7d2f6ad7-656b-4313-a005-4e344e43997d&amp;displaylang=en" target="_blank">Remote Server Administration Tools for Windows 7</a>”. From the DHCP management tool, right click “IPv4” or the server name if using an older version that does not show the separate IPv4 and IPv6 options.

Right click and select “Set Predefined Options”:
<a href="http://mikefrobbins.com/wp-content/uploads/2010/02/dhcp_server_predefined_-options.jpg"><img class="alignnone size-full wp-image-507" title="dhcp_server_predefined_ options" alt="" src="http://mikefrobbins.com/wp-content/uploads/2010/02/dhcp_server_predefined_-options.jpg" width="318" height="477" /></a>

Click add:
<a href="http://mikefrobbins.com/wp-content/uploads/2010/02/add-dhcp-option.jpg"><img class="alignnone size-full wp-image-508" title="Add DHCP Option" alt="" src="http://mikefrobbins.com/wp-content/uploads/2010/02/add-dhcp-option.jpg" width="385" height="383" /></a>

Add an option type of "161". Use a data type of string if you would like to use a DNS name instead of an ip-address.
<a href="http://mikefrobbins.com/wp-content/uploads/2010/02/dhcp_option_161.jpg"><img class="alignnone size-full wp-image-509" title="dhcp_option_161" alt="" src="http://mikefrobbins.com/wp-content/uploads/2010/02/dhcp_option_161.jpg" width="375" height="223" /></a>

Add an option type of "162":
<a href="http://mikefrobbins.com/wp-content/uploads/2010/02/dhcp_option_162.jpg"><img class="alignnone size-full wp-image-510" title="dhcp_option_162" alt="" src="http://mikefrobbins.com/wp-content/uploads/2010/02/dhcp_option_162.jpg" width="375" height="223" /></a>

Add Option "161" to your DHCP Server Scope options and enter the ip-address or DNS name if you chose to go that route in the step two steps prior to this one:
<a href="http://mikefrobbins.com/wp-content/uploads/2010/02/dhcp_add_scope_option_161.jpg"><img class="alignnone size-full wp-image-511" title="dhcp_add_scope_option_161" alt="" src="http://mikefrobbins.com/wp-content/uploads/2010/02/dhcp_add_scope_option_161.jpg" width="414" height="461" /></a>

Add Option "162" to your DHCP Server Scope options. You only need to set this value if you do not use the default path settings on your FTP server.
<a href="http://mikefrobbins.com/wp-content/uploads/2010/02/dhcp_add_scope_option_162.jpg"><img class="alignnone size-full wp-image-512" title="dhcp_add_scope_option_162" alt="" src="http://mikefrobbins.com/wp-content/uploads/2010/02/dhcp_add_scope_option_162.jpg" width="414" height="461" /></a>

The DHCP Scope options should now look similar to this:

<a href="http://mikefrobbins.com/wp-content/uploads/2010/02/dhcp_scope_options.jpg"><img class="alignnone size-full wp-image-513" title="dhcp_scope_options" alt="" src="http://mikefrobbins.com/wp-content/uploads/2010/02/dhcp_scope_options.jpg" width="528" height="144" /></a>

Configure an FTP Server on the server that was specified in the “161” DHCP Server Scope option. Create the following directories on the FTP Server directly under the root path for the anonymous user:

/wyse/wnos/
/wyse/wnos/ini/
/wyse/wnos/bitmap/

If you use a custom path, you will need to specify it in the "162" DHCP Server Scope option as previously referenced. Create a file with notepad named wnos.ini and save it in the /wyse/wnos/ directory. The contents of the file should look similar to this:

autoload=1
privilege=none
signon=0
connect=RDP
description="Terminal Server Farm"
host="termsrv.mikefrobbins.com"
fullscreen=1
lowband=0
resolution=1280x1024
domainname="mikefrobbins"
autoconnect=1
icon=customlogo.bmp

You will need to setup roaming profiles for your terminal server users by using the "Terminal Services Profile" tab in "Active Directory Users and Computers" so they will receive their settings regardless of which terminal server in the farm they log into. It is preferable to use a domain based DFS namespace path for things like this since the file server's name that stores the user profiles could change during a future migration and with DFS, only the target server name in the DFS link would need to be changed. I recommend setting up folder redirection with group policy for the users "My Documents" folder and "Desktop" which will minimize the amount of time that it takes for a user to logon and logoff of a Terminal Server since the redirected items will not need to be replicated at those times. I also recommend redirecting the users terminal server start menu with group policy so that you can control exactly what is on it, you can give different sets of users different start menus based on their group membership, and it gives you the flexibility of being able to easily add or remove start menu items. I plan to write a more comprehensive blog in the future that focuses on building a scalable, secure, and robust terminal services environment for the enterprise that will cover the specific details of terminal server configuration and some of the pitfalls to avoid, but this blog should at least get you started on the right track for implementing Wyse Winterms and Terminal Services in your enterprise environment.

µ