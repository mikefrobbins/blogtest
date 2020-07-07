---
ID: 1739
post_title: >
  Increase the size of a volume on an
  EqualLogic PS4000XV SAN
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2011/02/03/increase-the-size-of-a-volume-on-an-equallogic-ps4000xv-san/
published: true
post_date: 2011-02-03 07:30:42
---
You have a Windows 2008 R2 server that is nearly out of disk space on its ‘D’ drive. The ‘D’ drive is a volume on an EqualLogic PS4000XV Storage Area Network. This is a production server and the change needs to be done immediately in the middle of the day without service interruption. Whenever possible, I prefer to make changes like this outside of production hours or as scheduled downtime if you operate in a 24/7 environment since there is a chance that something could go wrong. When you have an issue like this that for whatever reason doesn’t get caught by your proactive monitoring systems and waiting will result in downtime anyway, the pros outweigh the cons of making a non-scheduled change (It may be a resume generating event otherwise).

Open the management console of your SAN and login as you normally would. If the volume you are going to expand is part of a “Volume Collection”, take a snapshot of the volume collection before beginning this process.

Locate the volume that needs to be expanded and select it. Verify you have the correct volume selected. Under “Activities” click “Modify volume settings”:
<a href="http://mikefrobbins.com/wp-content/uploads/2011/01/ev1.jpg"><img class="alignnone size-full wp-image-1757" title="ev1" src="http://mikefrobbins.com/wp-content/uploads/2011/01/ev1.jpg" alt="" width="596" height="318" /></a>

Click the “Space” Tab:
<a href="http://mikefrobbins.com/wp-content/uploads/2011/01/ev2.jpg"><img class="alignnone size-medium wp-image-1758" title="ev2" src="http://mikefrobbins.com/wp-content/uploads/2011/01/ev2.jpg?w=300" alt="" width="300" height="235" /></a>

Enter the new size for the volume keeping in mind that a volume cannot be shrunk:
<a href="http://mikefrobbins.com/wp-content/uploads/2011/01/ev3.jpg"><img class="alignnone size-medium wp-image-1759" title="ev3" src="http://mikefrobbins.com/wp-content/uploads/2011/01/ev3.jpg?w=300" alt="" width="300" height="235" /></a>

If you did not create a snapshot before beginning this process, click “Yes”. This will only create a snapshot of the volume that is being modified and not of all the volumes in the "Volume Collection” if it is part of one.
<a href="http://mikefrobbins.com/wp-content/uploads/2011/01/ev4.jpg"><img class="alignnone size-full wp-image-1760" title="ev4" src="http://mikefrobbins.com/wp-content/uploads/2011/01/ev4.jpg" alt="" width="333" height="138" /></a>

If you clicked yes in the previous step to create a snapshot, you will receive this dialog box. Enter a name for the snapshot and click “OK”:
<a href="http://mikefrobbins.com/wp-content/uploads/2011/01/ev5.jpg"><img class="alignnone size-full wp-image-1761" title="ev5" src="http://mikefrobbins.com/wp-content/uploads/2011/01/ev5.jpg" alt="" width="324" height="192" /></a>

The volume on the SAN has now been expanded. This doesn’t help your server though since it isn't yet aware that there’s more space on the drive.
<a href="http://mikefrobbins.com/wp-content/uploads/2011/01/ev6.jpg"><img class="alignnone size-full wp-image-1762" title="ev6" src="http://mikefrobbins.com/wp-content/uploads/2011/01/ev6.jpg" alt="" width="595" height="317" /></a>

Login to the server that is connected to this volume and launch Computer Management. In the Storage&gt;Disk Management section, you’ll notice the drive is still the same exact size as it was before and there is no free space that isn’t partitioned unless there was previously unpartitioned space.
<a href="http://mikefrobbins.com/wp-content/uploads/2011/01/ev7.jpg"><img class="alignnone size-full wp-image-1763" title="ev7" src="http://mikefrobbins.com/wp-content/uploads/2011/01/ev7.jpg" alt="" width="377" height="53" /></a>

Right click “Disk Management” and select “Refresh”:
<a href="http://mikefrobbins.com/wp-content/uploads/2011/01/ev8.jpg"><img class="alignnone size-full wp-image-1764" title="ev8" src="http://mikefrobbins.com/wp-content/uploads/2011/01/ev8.jpg" alt="" width="84" height="95" /></a>

The unpartioned free space will now show up on the drive, but hasn’t yet been added to the actual partition:
<a href="http://mikefrobbins.com/wp-content/uploads/2011/01/ev9.jpg"><img class="alignnone size-full wp-image-1765" title="ev9" src="http://mikefrobbins.com/wp-content/uploads/2011/01/ev9.jpg" alt="" width="376" height="53" /></a>

Right click the partition (‘D’ drive in the image below) and select “Extend Volume”:
<a href="http://mikefrobbins.com/wp-content/uploads/2011/01/ev10.jpg"><img class="alignnone size-full wp-image-1766" title="ev10" src="http://mikefrobbins.com/wp-content/uploads/2011/01/ev10.jpg" alt="" width="604" height="191" /></a>

Click “Next”:
<a href="http://mikefrobbins.com/wp-content/uploads/2011/01/ev11.jpg"><img class="alignnone size-medium wp-image-1767" title="ev11" src="http://mikefrobbins.com/wp-content/uploads/2011/01/ev11.jpg?w=300" alt="" width="300" height="239" /></a>

Click “Next” to add all of the new free space to the 'D' partition:
<a href="http://mikefrobbins.com/wp-content/uploads/2011/01/ev12.jpg"><img class="alignnone size-medium wp-image-1768" title="ev12" src="http://mikefrobbins.com/wp-content/uploads/2011/01/ev12.jpg?w=300" alt="" width="300" height="239" /></a>

Click “Finish”:
<a href="http://mikefrobbins.com/wp-content/uploads/2011/01/ev13.jpg"><img class="alignnone size-medium wp-image-1769" title="ev13" src="http://mikefrobbins.com/wp-content/uploads/2011/01/ev13.jpg?w=300" alt="" width="300" height="239" /></a>

You now have the additional space in the 'D' partition and your operating system can actually use it:
<a href="http://mikefrobbins.com/wp-content/uploads/2011/01/ev14.jpg"><img class="alignnone size-full wp-image-1770" title="ev14" src="http://mikefrobbins.com/wp-content/uploads/2011/01/ev14.jpg" alt="" width="377" height="52" /></a>

This process was performed in the middle of the day on a production server with no downtime or interruption to service. In this example, an EqualLogic PS4000XV SAN with firmware v4.3.0 and Windows Server 2008 R2 Enterprise Edition were used. Your results may differ if this process is performed on another operating system or a SAN with a different firmware version.

µ