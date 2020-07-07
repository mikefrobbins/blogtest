---
ID: 1592
post_title: >
  When was an Active Directory Group
  Created or Modified?
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2011/01/13/when-was-an-active-directory-group-created-or-modified/
published: true
post_date: 2011-01-13 17:30:33
---
This week I needed to figure out when a group was created in one of the Active Directory environments that I provide support for. I looked at the group using “Active Directory Users and Computers” and didn’t see anything that would tell me when it was created. I did a quick Google search and found a way to accomplish this for a similar item (a user object) using VBScript. The example for a user object that I found was on a “<a href="http://blogs.technet.com/b/heyscriptingguy/archive/2005/01/06/how-can-i-tell-on-what-date-an-active-directory-user-account-was-created.aspx" target="_blank">Hey, Scripting Guy! Blog</a>”.

Here’s an example of how to find out when a group in Active Directory was created:
<pre class="lang:vb decode:true">Set objGroup = GetObject("LDAP://cn=my group, ou=test, dc=mikefrobbins, dc=com")
Wscript.Echo objGroup.WhenCreated</pre>
And here’s an example of how to find out when a group in Active Directory was modified:
<pre class="lang:vb decode:true">Set objGroup = GetObject("LDAP://cn=my group, ou=test, dc=mikefrobbins, dc=com")
Wscript.Echo objGroup.WhenChanged</pre>
µ