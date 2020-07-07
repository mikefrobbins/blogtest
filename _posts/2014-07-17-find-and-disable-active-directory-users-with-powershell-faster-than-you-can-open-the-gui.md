---
ID: 10223
post_title: >
  Find and Disable Active Directory Users
  with PowerShell Faster than You can Open
  the GUI
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2014/07/17/find-and-disable-active-directory-users-with-powershell-faster-than-you-can-open-the-gui/
published: true
post_date: 2014-07-17 07:30:04
---
In this scenario, a support request has been escalated to you because the help desk is unable to find a user account in Active Directory that needs to be disabled.

The help desk included a screenshot where they attempted to search for the user who is named "William Doe":

<a href="http://mikefrobbins.com/wp-content/uploads/2014/07/finduser1.png"><img class="alignnone size-full wp-image-10224" src="http://mikefrobbins.com/wp-content/uploads/2014/07/finduser1.png" alt="finduser1" width="531" height="477" /></a>

The request you received also stated that the user is in the "Sales" department so you perform a quick search for users who have a last name of "Doe" and who are also in the "Sales" department:
<pre class="lang:ps decode:true ">Get-ADUser -Filter {Surname -eq 'Doe' -and Department -eq 'Sales'}</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/07/finduser2.png"><img class="alignnone size-full wp-image-10225" src="http://mikefrobbins.com/wp-content/uploads/2014/07/finduser2.png" alt="finduser2" width="877" height="238" /></a>

Based on the results, it appears that the user's first name is probably Scott with a middle name of William since the only result returned is "Scott W. Doe". Now to disable his account:
<pre class="lang:ps decode:true ">Get-ADUser -Filter {Surname -eq 'Doe' -and Department -eq 'Sales'} |
Set-ADUser -Enabled $false -PassThru</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/07/finduser3.png"><img class="alignnone size-full wp-image-10226" src="http://mikefrobbins.com/wp-content/uploads/2014/07/finduser3.png" alt="finduser3" width="877" height="262" /></a>

The user was located and disabled faster with PowerShell than you could have even opened the GUI.

µ