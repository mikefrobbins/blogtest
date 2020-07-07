---
ID: 679
post_title: >
  HP MFP Digital Sending Software on
  Windows Server 2008
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2010/03/25/hp-mfp-digital-sending-software/
published: true
post_date: 2010-03-25 11:35:26
---
I recently updated and migrated some HP MFP Digital Sending Software (DSS) from version 4.13 to 4.16 and from a Windows 2003 Server to a Windows 2008 Server. One thing to note is that even the newest version of the HP DSS Software (4.16) will not run on a 64bit operating system so the newest operating system that can be used is a 32bit version of Windows 2008 (non-R2 since R2 is 64bit only). After loading the HP DSS software, I received the following error when attempting to add the actual digital senders:
<a href="http://mikefrobbins.com/wp-content/uploads/2010/03/mfp-error.jpg"><img class="alignnone size-full wp-image-680" title="mfp-error" src="http://mikefrobbins.com/wp-content/uploads/2010/03/mfp-error.jpg" alt="" width="354" height="149" /></a>

The resolution to this problem was to re-run Windows Update since some of the components that the HP DSS software installs need to be updated for the software to work correctly:
<a href="http://mikefrobbins.com/wp-content/uploads/2010/03/mfp-resolution.jpg"><img class="alignnone size-full wp-image-681" title="mfp-resolution" src="http://mikefrobbins.com/wp-content/uploads/2010/03/mfp-resolution.jpg" alt="" width="600" height="450" /></a>

<a href="http://mikefrobbins.com/wp-content/uploads/2010/03/mfp-resolution.jpg"></a>µ