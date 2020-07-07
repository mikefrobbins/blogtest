---
ID: 5078
post_title: >
  Hyper-V Live Merge Snapshot Feature in
  Windows Server 2012
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2012/08/14/hyper-v-live-merge-snapshot-feature-in-windows-server-2012/
published: true
post_date: 2012-08-14 07:30:32
---
One of the best new features I've noticed while testing Hyper-V on the release candidate of Windows Server 2012 is the ability to merge snapshots while a virtual machine is running without the need for a restart as was required by Hyper-V in Windows Server 2008 R2.

The Windows 7 VM shown below is currently running and it has one snapshot:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/08/2012-livemerge1.jpg"><img class="alignnone size-full wp-image-5079" title="2012-livemerge1" src="http://mikefrobbins.com/wp-content/uploads/2012/08/2012-livemerge1.jpg" alt="" width="622" height="258" /></a>

When a snapshot is created a .avhdx file is created and any changes from that point forward are written to it instead of the .vhdx file:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/08/2012-livemerge2.jpg"><img class="alignnone size-full wp-image-5080" title="2012-livemerge2" src="http://mikefrobbins.com/wp-content/uploads/2012/08/2012-livemerge2.jpg" alt="" width="640" height="277" /></a>

Here are the files shown on the file system. These (.vhdx and .avhdx) are new file types in Windows Server 2012. They are .vhd and .avhd in Windows Server 2008 R2.

<a href="http://mikefrobbins.com/wp-content/uploads/2012/08/2012-livemerge3.jpg"><img class="alignnone size-full wp-image-5086" title="2012-livemerge3" src="http://mikefrobbins.com/wp-content/uploads/2012/08/2012-livemerge3.jpg" alt="" width="469" height="144" /></a>

I've selected the snapshot for this VM and I'm deleting it:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/08/2012-livemerge4.jpg"><img class="alignnone size-full wp-image-5081" title="2012-livemerge4" src="http://mikefrobbins.com/wp-content/uploads/2012/08/2012-livemerge4.jpg" alt="" width="614" height="379" /></a>

After deleting a snapshot in Hyper-V on Windows 2008 R2, the changes in the .avhd wouldn't be merged  into the .vhd until the VM was shutdown, but with Windows Server 2012, the changes in the .avhdx file are merged into the .vhdx file on the fly:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/08/2012-livemerge5.jpg"><img class="alignnone size-full wp-image-5082" title="2012-livemerge5" src="http://mikefrobbins.com/wp-content/uploads/2012/08/2012-livemerge5.jpg" alt="" width="640" height="227" /></a>

As you can see, the Windows 7 VM no longer has any snapshots:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/08/2012-livemerge6.jpg"><img class="alignnone size-full wp-image-5083" title="2012-livemerge6" src="http://mikefrobbins.com/wp-content/uploads/2012/08/2012-livemerge6.jpg" alt="" width="640" height="227" /></a>

The virtual hard drive for this VM now references the .vhdx file:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/08/2012-livemerge7.jpg"><img class="alignnone size-full wp-image-5084" title="2012-livemerge7" src="http://mikefrobbins.com/wp-content/uploads/2012/08/2012-livemerge7.jpg" alt="" width="640" height="273" /></a>

µ