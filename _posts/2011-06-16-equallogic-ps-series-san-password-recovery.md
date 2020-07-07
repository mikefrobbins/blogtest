---
ID: 2385
post_title: >
  EqualLogic PS Series SAN Password
  Recovery
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2011/06/16/equallogic-ps-series-san-password-recovery/
published: true
post_date: 2011-06-16 07:30:19
---
Problem:<a href="http://mikefrobbins.com/wp-content/uploads/2011/06/san-pw-recovery0.png">
</a><a href="http://mikefrobbins.com/wp-content/uploads/2011/06/san-pw-recovery0.png"><img class="alignleft size-full wp-image-2387" title="san-pw-recovery0" src="http://mikefrobbins.com/wp-content/uploads/2011/06/san-pw-recovery0.png" alt="" width="268" height="115" /></a>You're unable to login to your EqualLogic PS Series Storage Area Network (<a href="http://en.wikipedia.org/wiki/Storage_area_network" target="_blank">SAN</a>) to perform<a href="http://mikefrobbins.com/wp-content/uploads/2011/06/san-pw-recovery0.png">
</a> administrative functions. When you attempt to login, you receive a login exception message stating "Invalid username or password".

Solution:
The grpadmin account password can be reset by connecting a computer to the serial port of the active controller on your EqualLogic SAN. You’ll need one of the serial cables that shipped with your SAN which is a standard 9 pin female to female serial cable. You'll also need a USB to Serial Port adapter unless your computer has a COM port. Launch HyperTerminal and setup a connection using 9600, 8, None,1 and no flow control.

<a href="http://mikefrobbins.com/wp-content/uploads/2011/06/san-pw-recovery1.png"><img class="alignright size-full wp-image-2382" title="san-pw-recovery1" src="http://mikefrobbins.com/wp-content/uploads/2011/06/san-pw-recovery1.png" alt="" width="270" height="199" /></a>Press enter and you should now be at a login prompt. Give EqualLogic support a call using the support phone number listed on their <a href="http://support.dell.com/support/topics/global.aspx/support/enterprise_support/en/equal_logic?c=us&amp;l=en&amp;s=bsdr" target="_blank">website</a> for your specific region. They'll want your service tag number so have it ready. Enter “recoverpassword” as the login. Give the firmware version and challenge numeric sequence to the support representative. They will give you a response to enter.

After entering the response, you'll receive two messages: “Login to group using the lost password recovery procedure succeeded.” and “Login to account grpadmin succeeded.”
<a href="http://mikefrobbins.com/wp-content/uploads/2011/06/san-pw-recovery2.png"><img class="alignnone size-full wp-image-2383" title="san-pw-recovery2" src="http://mikefrobbins.com/wp-content/uploads/2011/06/san-pw-recovery2.png" alt="" width="640" height="229" /></a>

Enter "account select grpadmin passwd". You will be prompted twice to enter a new password for the grpadmin account.
<a href="http://mikefrobbins.com/wp-content/uploads/2011/06/san-pw-recovery3.png"><img class="alignnone size-full wp-image-2384" title="san-pw-recovery3" src="http://mikefrobbins.com/wp-content/uploads/2011/06/san-pw-recovery3.png" alt="" width="385" height="56" /></a>

You have now successfully reset the password for the grpadmin account. Login to the web interface for the SAN to verify the password reset process was successful before closing out of the command line interface (<a href="http://en.wikipedia.org/wiki/Command-line_interface" target="_blank">CLI</a>).

To keep this problem from happening again in the future, set the grpadmin account to a password that is generated with <a href="http://keepass.info/" target="_blank">KeePass</a> and store the password in a KeePass password safe. Create a user specific administrative account on the SAN for each of your SAN Administrators. Never use the grpadmin account again unless all of the SAN admin accounts that you've created become locked out. In that case, use the grpadmin account to login and reset the passwords for the user specific admin accounts.

µ