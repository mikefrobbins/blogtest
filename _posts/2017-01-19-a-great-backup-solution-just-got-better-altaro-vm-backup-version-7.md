---
ID: 14924
post_title: 'A Great Backup Solution Just Got Better: Altaro VM Backup Version 7'
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2017/01/19/a-great-backup-solution-just-got-better-altaro-vm-backup-version-7/
published: true
post_date: 2017-01-19 07:30:51
---
Earlier this week, <a href="http://www.altaro.com/" target="_blank">Altaro</a> released a new version (version 7) of their award winning VM Backup software. Version 7 contains a number of new features that are compelling enough to warrant an upgrade if you have a previous version installed in your environment or a new installation if you're currently using a different product for your disaster recovery solution.

The top new features in version 7 include:
<ul>
 	<li>Augmented Inline Deduplication</li>
 	<li>Boot from Backup</li>
 	<li>Support for Windows Server 2016</li>
</ul>
<a href="http://www.altaro.com/vm-backup/whats-new.php" target="_blank"><img class="alignnone size-full wp-image-14947" src="http://mikefrobbins.com/wp-content/uploads/2017/01/v7-badge.png" alt="" width="170" height="78" /></a>

For a full list of new features, see Altaro's webpage on "<em><a href="http://www.altaro.com/vm-backup/whats-new.php" target="_blank">What’s new in version 7 of Altaro VM Backup?</a></em>"

The focus of this blog article is on a Hyper-V environment, although Altaro VM Backup supports VMware as well.

I'm currently supporting Altaro version 6.5.9.0 in a production environment at one of customer locations that I'll be upgrading to version 7. Altaro has a support article on "<a href="http://support.altaro.com/customer/en/portal/articles/817215-how-do-i-upgrade-to-the-latest-version-of-altaro-vm-backup-" target="_blank"><em>How do I upgrade to the latest build of Altaro VM Backup?</em></a>". That article references following the steps in <a href="http://www.altaro.com/vm-backup/upgrade-key.php" target="_blank">a different article</a> if you're running version 6.5 or later.

Note: It appears that Altaro updated their website with the release of version 7. I experienced a problem where their updated website didn't render properly in Google Chrome because it was attempting to use a cached copy. Holding down shift while clicking on the refresh button in Chrome resolved the problem for me.

The previously referenced <a href="http://www.altaro.com/vm-backup/upgrade-key.php" target="_blank">webpage</a> contains the information needed to convert your version 6 license to version 7. That webpage also contains a link to <a href="http://support.altaro.com/customer/portal/articles/2652584" target="_blank">their version 7 upgrade guide</a> which states that customers with a current SMA (Software Maintenance Agreement) are entitled to a free upgrade. If you're not sure if your SMA is current, follow the directions in <a href="http://support.altaro.com/customer/portal/articles/2716029" target="_blank">this support article</a> to check the status of it.

Step 2 of the instructions states: <em>"The upgrade from version 5.x or 6.x to 7.0 is a simple over the top upgrade". </em>The next sentence in step 2 states: <em>"First, uninstall Altaro VM Backup from the Windows Program and Features screen, then simply download v7.0 using the download link below, then run the installer."</em>

First of all, an over the top upgrade by definition doesn't require removal and re-installation, you just install over the top of what's already there. My recommendation is to make sure you can download the new version before removing the old one. Also, the new version uses some additional network ports so make sure that won't be a problem before proceeding. The required network ports are documented in <a href="http://support.altaro.com/customer/en/portal/articles/808716" target="_blank">Altaro's system requirements support article</a>.

Luckily step 2 also states that <em>"All settings and previous backups will be kept" and "The installation of v7.0 will NOT require a reboot (unless a previous reboot is already pending from some other installation)."</em>

I chose to de-install and re-install instead of performing a simple over the top upgrade which is what I think the instructions meant:

<a href="http://mikefrobbins.com/wp-content/uploads/2017/01/altaro7-1a.png"><img class="alignnone size-full wp-image-14932" src="http://mikefrobbins.com/wp-content/uploads/2017/01/altaro7-1a.png" alt="" width="859" height="444" /></a>

The installation is a clicker-size in the GUI (next, next, next) and took less than 5 minutes in my environment:

<a href="http://mikefrobbins.com/wp-content/uploads/2017/01/altaro7-2a.png"><img class="alignnone size-full wp-image-14933" src="http://mikefrobbins.com/wp-content/uploads/2017/01/altaro7-2a.png" alt="" width="509" height="399" /></a>

Indeed the instructions were correct in stating that a reboot is not required. Neither the de-install nor the re-install required a reboot in my scenario on Windows Server 2012 R2.

Upon launching the Altaro Management Console, I noticed the following notification:

<a href="http://mikefrobbins.com/wp-content/uploads/2017/01/altaro7-3a.png"><img class="alignnone size-full wp-image-14934" src="http://mikefrobbins.com/wp-content/uploads/2017/01/altaro7-3a.png" alt="" width="859" height="108" /></a>

I also noticed the message next to "<em>Hosts</em>" in the Altaro Management Console that says "<em>Needs Action</em>":

<a href="http://mikefrobbins.com/wp-content/uploads/2017/01/altaro7-4a.png"><img class="alignnone size-full wp-image-14935" src="http://mikefrobbins.com/wp-content/uploads/2017/01/altaro7-4a.png" alt="" width="859" height="235" /></a>

After selecting "<em>Hosts</em>" from the menu on the left, simply click on the "<em>Update</em>" button under "<em>Hyper-V Host Agent</em>" and enter admin credentials for the host:

<a href="http://mikefrobbins.com/wp-content/uploads/2017/01/altaro7-5a.png"><img class="alignnone size-full wp-image-14937" src="http://mikefrobbins.com/wp-content/uploads/2017/01/altaro7-5a.png" alt="" width="437" height="319" /></a>

No need to wait for one host to complete the update before continuing to the next. Once the update is complete, the "<em>Hyper-V Host Agent</em>" column will display "<em>Installed and Running</em>" and the number of VM's on the host will be displayed under the "<em>Virtual Machines</em>" column:

<a href="http://mikefrobbins.com/wp-content/uploads/2017/01/altaro7-6a.png"><img class="alignnone size-full wp-image-14938" src="http://mikefrobbins.com/wp-content/uploads/2017/01/altaro7-6a.png" alt="" width="807" height="318" /></a>

Be sure to read the "<em>Important Notes About The Upgrade!</em>" section of the <a href="http://support.altaro.com/customer/portal/articles/2652584" target="_blank">Altaro Upgrade Guide</a>. It states that version 7 uses a new deduplication format which isn't enabled by default. It has to be manually enabled from advanced settings (but read the remainder of this article before enabling it):

<a href="http://mikefrobbins.com/wp-content/uploads/2017/01/altaro7-7a.png"><img class="alignnone size-full wp-image-14940" src="http://mikefrobbins.com/wp-content/uploads/2017/01/altaro7-7a.png" alt="" width="859" height="420" /></a>

Take note that enabling this feature will cause a full backup (a new base image in my terminology) to occur during the next regularly scheduled backup which will potentially take a lot more time than a normal nightly backup. For example, one of my VM's takes approximately 2 hours per night to backup but a full (new) backup takes over 13 hours. My recommendation is wait until a window of opportunity to enable this feature. Keep in mind that it can be enabled on a few VM's per day until you have all of them transitioned to use this new feature. According to the upgrade documentation, backups will continue to use the Altaro version 6 type of deduplication until this feature has been enabled. New VM's that are protected from this point forward will default to using the new type of deduplication.

<a href="http://mikefrobbins.com/wp-content/uploads/2017/01/altaro7-8a.png"><img class="alignnone size-full wp-image-14964" src="http://mikefrobbins.com/wp-content/uploads/2017/01/altaro7-8a.png" alt="" width="859" height="315" /></a>

Also note that offsite backups don't have to be reseeded, they will be automatically reseeded from the previous offsite backup when this feature is enabled. This is really slick since most backup vendors don't care how many hoops that you have to jump through when upgrading their software. The upgrade documentation does however state that the first offsite backup after enabling the new type of deduplication will take longer than normal.

The charts shown on the dashboard won't show any data or be updated until the new type of deduplication has been enabled and a backup run afterwards:

<a href="http://mikefrobbins.com/wp-content/uploads/2017/01/altaro7-9a.png"><img class="alignnone size-large wp-image-14965" src="http://mikefrobbins.com/wp-content/uploads/2017/01/altaro7-9a-1024x329.png" alt="" width="1020" height="328" /></a>

Be sure to update any offsite servers you may have as well as the management tools that you may have installed on any clients. Both of these are separate downloads from the server installation itself and can be downloaded from <a href="http://www.altaro.com/vm-backup/download-tools.php" target="_blank">Altaro's Download Tools webpage</a>.

Altaro's upgrade process to version 7 seems to be well thought out and seamless based on my experience.

Be sure to read my blog article on "<a href="http://mikefrobbins.com/2016/09/01/use-powershell-and-pester-for-operational-readiness-testing-of-altaro-vm-backup/" target="_blank"><em>Use PowerShell and Pester for Operational Readiness Testing of Altaro VM Backup</em></a>". I'll also be writing a future blog article on my experience with the performance differences using the new augmented inline deduplication feature in Altaro version 7 as well as the new boot from backup feature.

µ