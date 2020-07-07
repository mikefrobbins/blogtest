---
ID: 1369
post_title: >
  Dell Systems Build DVD Removes All
  Existing Partitions
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2010/11/11/dell-systems-build-dvd-removes-all-existing-partitions/
published: true
post_date: 2010-11-11 05:30:12
---
<a href="http://mikefrobbins.com/wp-content/uploads/2010/11/dell-dvd.jpg"><img class="alignleft size-thumbnail wp-image-1370" title="dell-dvd" src="http://mikefrobbins.com/wp-content/uploads/2010/11/dell-dvd.jpg?w=148" alt="" width="148" height="150" /></a>So you've built a server and partitioned the RAID array into two logical partitions, one for the operating system and one for the data. For whatever reason, you need to reload the operating system on the server and you think you can format the OS partition and reload it without loosing the data that's on your data partition. If you were booting directly to an operating system CD or DVD, that would be true, however if you boot to the Dell Systems Build DVD and attempt to reload the operating system with it, it will perform a step of "Clearing Existing Partitions" even if you choose to retain the existing RAID configuration. Poof and and all of your partitions are gone including the data on them. Make sure you have a good backup if you are reloading an existing system with the Dell Systems Management Tools and Documentation DVD.

µ