---
ID: 7589
post_title: 'PowerShell Version 4 New Feature: Requires Statements Now Let Users Require Administrator Access Rights If Needed'
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2013/06/26/powershell-version-4-new-feature-requires-statements-now-let-users-require-administrator-access-rights-if-needed/
published: true
post_date: 2013-06-26 07:30:08
---
In PowerShell version 4, there’s a new “<em>-RunAsAdministrator</em>” option for the <em>#Requires</em> statement that prevents a script from executing unless PowerShell is running as an administrator.

The information about this new option from the PowerShell version 4 "about_Requires" help topic is shown in the following image. To learn more about the requires statement, run "<em>help about_requires</em>" from within PowerShell.

<a href="http://mikefrobbins.com/wp-content/uploads/2013/06/ps4-requiresadmin-help.png"><img class="alignnone size-full wp-image-7586" alt="ps4-requiresadmin-help" src="http://mikefrobbins.com/wp-content/uploads/2013/06/ps4-requiresadmin-help.png" width="634" height="141" /></a>

Here’s the message that's displayed if this option is specified and the person running the script is not an administrator:

<a href="http://mikefrobbins.com/wp-content/uploads/2013/06/ps4-requiresadmin.png"><img class="alignnone size-full wp-image-7578" alt="ps4-requiresadmin" src="http://mikefrobbins.com/wp-content/uploads/2013/06/ps4-requiresadmin.png" width="595" height="238" /></a>

Previously, you had to use the .NET Framework to determine if PowerShell was running as an administrator. Here’s a snippet from <a href="http://gallery.technet.microsoft.com/scriptcenter/2013-Scripting-Games-e079e02a" target="_blank">my event 6 entry</a> in the 2013 Scripting Games advanced track showing how this was accomplished in previous versions of PowerShell:

<a href="http://mikefrobbins.com/wp-content/uploads/2013/06/ps3-requiresadmin.png"><img class="alignnone size-full wp-image-7581" alt="ps3-requiresadmin" src="http://mikefrobbins.com/wp-content/uploads/2013/06/ps3-requiresadmin.png" width="692" height="31" /></a>

Interested in learning more about PowerShell version 4? See the “<a href="http://technet.microsoft.com/library/hh857339.aspx" target="_blank">What’s New in Windows PowerShell</a>” article on TechNet.

µ