---
ID: 685
post_title: >
  Using HP MFP Digital Sending Software
  4.x with a Castelle FaxPress
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2010/03/25/using-hp-digital-sending-software-with-castelle-faxpress/
published: true
post_date: 2010-03-25 11:53:45
---
Problem:
Your Castelle FaxPress will not send faxes created with a HP Digital Sender using the HP MFP Digital Sending Software 4.x (DSS).

The 4.x version of the HP MFP Digital Sending Software defaults to long filenames when setting it up for use with a fax product. A Castelle FaxPress does not support long file names and will not pick up the fax files even though everything else is set up correctly. I have verified that version 8.x and 9.x of the Castelle FaxPress software does not support long file names.

Solution:
Modify the hpbs2e.ini file located in the "C:\Program Files\Hewlett-Packard\HP MFP Digital Sending Software" directory. If you chose a non-default location when installing the HP DSS software, look in that location for the file or search for it.

Edit the following setting and change it to "0" for short file names:
LanFax Can Use Long Files Names 0isNO 1isYES=1

Once the edit is complete, it should look like this:
LanFax Can Use Long Files Names 0isNO 1isYES=0

µ