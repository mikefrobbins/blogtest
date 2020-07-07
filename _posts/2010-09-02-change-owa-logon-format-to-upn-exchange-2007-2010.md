---
ID: 1249
post_title: >
  Change OWA Logon Format to UPN on
  Exchange 2007/2010
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2010/09/02/change-owa-logon-format-to-upn-exchange-2007-2010/
published: true
post_date: 2010-09-02 05:30:32
---
To change the logon screen of your OWA website to ask for the user principal name (UPN) or email address instead of “Domainuser name”, change the following to “User principal name (UPN)”. To access this screen, from within Exchange Management Console, go to “Server Configuration&gt;Client Access”, right click the OWA website listed under the “Outlook Web App” tab, select properties, and click the “Authentication” tab:
<a href="http://mikefrobbins.com/wp-content/uploads/2010/09/owaupn1.jpg"><img class="alignnone size-full wp-image-1252" title="owaupn1" src="http://mikefrobbins.com/wp-content/uploads/2010/09/owaupn1.jpg" alt="" width="448" height="519" /></a>

<a href="http://mikefrobbins.com/wp-content/uploads/2010/09/owaupn1.jpg"></a>Before:
<a href="http://mikefrobbins.com/wp-content/uploads/2010/09/owaupn2.jpg"><img class="alignnone size-full wp-image-1253" title="owaupn2" src="http://mikefrobbins.com/wp-content/uploads/2010/09/owaupn2.jpg" alt="" width="507" height="465" /></a>

<a href="http://mikefrobbins.com/wp-content/uploads/2010/09/owaupn2.jpg"></a>After:
<a href="http://mikefrobbins.com/wp-content/uploads/2010/09/owaupn3.jpg"><img class="alignnone size-full wp-image-1251" title="owaupn3" src="http://mikefrobbins.com/wp-content/uploads/2010/09/owaupn3.jpg" alt="" width="500" height="458" /></a>

<a href="http://mikefrobbins.com/wp-content/uploads/2010/09/owaupn3.jpg"></a>µ