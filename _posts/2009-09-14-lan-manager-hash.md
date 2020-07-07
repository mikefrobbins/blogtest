---
ID: 114
post_title: >
  Removing the LAN Manager Hash security
  risk
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2009/09/14/lan-manager-hash/
published: true
post_date: 2009-09-14 20:49:00
---
Problem:
Windows stores a LAN Manager Hash for backwards compatibility with older operating systems. These Hash values are stored in local SAM databases and Active Directory. They are considered to be a major security risk and should be disabled unless you are running Windows 95, Windows 98, or Macintosh Outlook 2001 Clients.

Solution #1:
Using group policy to disable the LAN Manager Hash is the recommended solution since you can easily push the settings out to all of your workstations and servers. Set the “Network security: Do not store LAN Manager Hash value on next password change” to enabled. Passwords need to be changed for this setting to take effect.

<img class="alignnone size-full wp-image-115" title="lanmanager_gpo" src="http://mikefrobbins.com/wp-content/uploads/2009/09/lanmanager_gpo.png" alt="lanmanager_gpo" width="600" height="283" />

Solution #2:
For computers that are not a member of a domain, modify the registry. Navigate to: HKLM\System\CurrentControlSet\Control\Lsa and add a key named NoLMHash for Windows 2000. Add a DWORD Value named NoLMHash with a value of 1 for Windows XP and 2003. As with the above solution, passwords need to be changed for this to take effect.

Solution #3:
Use Passwords that are 15 characters or longer.

For more information:
<a href="http://support.microsoft.com/kb/299656" target="_blank" rel="noopener">http://support.microsoft.com/kb/299656</a>

µ