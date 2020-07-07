---
ID: 418
post_title: >
  System State Backup Failures with
  Symantec Backup Exec
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2009/12/17/windows-server-2008-system-state-backup-failure/
published: true
post_date: 2009-12-17 09:51:10
---
Problem:
You receive the message: "The job failed with the following error: A failure occurred accessing the object list." in your backup job notification email when attempting to backup the system state of a Windows 2008 Server with Symantec Backup Exec. The actual error from the backup server job is: “e000fedd - A failure occurred accessing the object list.” and the error code is: “V-79-57344-65245”.

Solution:
This error is fairly generic and can be caused by several different problems; the issue I encountered was specifically due to using the combination of Trend Micro Worry-Free Business Security Advanced (Antivirus), Symantec Backup Exec 12.5, and Microsoft Windows Server 2008. To resolve this problem, stop the “Trend Micro Unauthorized Change Prevention Service” if it is running and modify the following registry settings on the server you’re attempting to backup:

<span style="text-decoration: underline;">Go to the following location in the registry:</span>
HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\TMBMServer

<span style="text-decoration: underline;">Change the value of “HomeDir” from:</span>
"C:\Program Files (x86)\Trend Micro\Client Server Security Agent..BM"
To:
"C:\Program Files (x86)\Trend MicroBM"

<span style="text-decoration: underline;">Change the value of “ImagePath” from:</span>
"C:\Program Files (x86)\Trend Micro\Client Server Security Agent..BMTMBMSRV.exe" /service
To:
"C:\Program Files (x86)\Trend Micro\BMTMBMSRV.exe" /service

The above example is from a machine running a 64 bit operating system and a 32 bit version of Trend. The beginning of the path should be the same as the original and may differ on 32 bit and 64 bit operating systems. Once these registry settings are updated, the system state backups should complete successfully.

For more information and for different problems that can cause this error see: <a href="http://www.symantec.com/business/support/knowledge_base_sli.jsp?umi=EvtID=V-79-57344-65245">http://www.symantec.com/business/support/knowledge_base_sli.jsp?umi=EvtID=V-79-57344-65245</a>

µ