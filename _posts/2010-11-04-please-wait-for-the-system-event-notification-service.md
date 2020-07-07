---
ID: 1358
post_title: 'Please wait for the System Event Notification Service&#8230;'
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2010/11/04/please-wait-for-the-system-event-notification-service/
published: true
post_date: 2010-11-04 05:30:33
---
One of my customers that I provide level 3 support for recently contacted me with an issue where their system would hang on the message "Please wait for the System Event Notification Service..." when they attempted to logout of their Windows 2008 terminal server:<a href="http://mikefrobbins.com/wp-content/uploads/2010/10/2008_logoff_issue.png"><img class="alignnone size-full wp-image-1360" title="2008_logoff_issue" src="http://mikefrobbins.com/wp-content/uploads/2010/10/2008_logoff_issue.png" alt="" width="640" height="87" /></a>

We determined that their terminal server had recently received an update to Windows Live Messenger via Windows Update that was causing this issue. Removing Windows Live Messenger from their terminal server resolved their logout issues.

µ