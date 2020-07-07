---
ID: 952
post_title: Joomla Invalid Session Error
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2010/06/17/joomla-invalid-session-error/
published: true
post_date: 2010-06-17 11:24:20
---
Problem:
Today when attempting to login to the administrator (administration) portion of one of my customers Joomla websites, I received an “Invalid Session” error. Internet Explorer only shows the error in the URL and just redirects you back to the login page: http://mikefrobbins.com/administrator/index.php?mosmsg=Invalid Session
<a href="http://mikefrobbins.com/wp-content/uploads/2010/06/ie_joomla_invalidsession.png"><img class="alignnone size-full wp-image-953" title="ie_joomla_invalidsession" src="http://mikefrobbins.com/wp-content/uploads/2010/06/ie_joomla_invalidsession.png" alt="" width="442" height="32" /></a>

Google Chrome actually shows an “Invalid Session” error on the login page:
<a href="http://mikefrobbins.com/wp-content/uploads/2010/06/chrome_joomla_invalidsession.png"><img class="alignnone size-full wp-image-955" title="chrome_joomla_invalidsession" src="http://mikefrobbins.com/wp-content/uploads/2010/06/chrome_joomla_invalidsession.png" alt="" width="423" height="39" /></a>

Solution:
You have a session directory specified in your php.ini file that does not exist:
session.save_path = "X:/my/session/directory/"
Create the directory and make sure it has the proper permissions to resolve this problem.

µ