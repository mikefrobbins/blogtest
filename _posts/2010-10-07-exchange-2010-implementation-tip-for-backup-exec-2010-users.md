---
ID: 1319
post_title: >
  Exchange 2010 Implementation Tip for
  Backup Exec 2010 Users
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2010/10/07/exchange-2010-implementation-tip-for-backup-exec-2010-users/
published: true
post_date: 2010-10-07 05:30:14
---
You should plan to move your Backup Exec 2010 Media Server to 64bit before implementing Exchange Server 2010. A Backup Exec 2010 Media Server that is running a 32bit operating system is unable to backup an Exchange 2010 Information Store. This is because Microsoft does not support 32bit Exchange Management Tools for Exchange 2010. Here's the error message you'll receive if you attempt to backup an Exchange 2010 Information Store with a 32bit Backup Exec 2010 Media Server:

<em>Error category    : Resource ErrorsError             : e000120f - This operation is not supported on 32-bit media servers because Microsoft does not support 32-bit Exchange Management Tools for Exchange Server 2010. You must use a 64-bit media server for this operation.
For additional information regarding this error refer to link </em><a href="http://www.symantec.com/business/support/index?page=answers&amp;startover=y&amp;question_box=V-79-57344-4623" target="_blank"><em>V-79-57344-4623</em></a>

µ