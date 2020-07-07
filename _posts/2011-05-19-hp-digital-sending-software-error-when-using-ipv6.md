---
ID: 2296
post_title: >
  HP Digital Sending Software Error when
  using IPv6
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2011/05/19/hp-digital-sending-software-error-when-using-ipv6/
published: true
post_date: 2011-05-19 07:30:01
---
I recently ran into an issue where one of my customers HP Digital Senders would no longer resolve email addresses automatically to Active Directory using the HP Digital Sending Software version 4.91.

It generated the error: “The operation failed. Please verify your configuration and try again. (LDAP error ServerDown occurred.)”:
<a href="http://mikefrobbins.com/wp-content/uploads/2011/05/hpdss-error1.png"><img class="alignnone size-full wp-image-2305" title="hpdss-error1" src="http://mikefrobbins.com/wp-content/uploads/2011/05/hpdss-error1.png" alt="" width="364" height="148" /></a>

It also generated the error: “The operation failed. Please verify your configuration and try again. (AUTH_LDAP_SERVER_NOT_AVAILABLE)":
<a href="http://mikefrobbins.com/wp-content/uploads/2011/05/hpdss-error2.png"><img class="alignnone size-full wp-image-2306" title="hpdss-error2" src="http://mikefrobbins.com/wp-content/uploads/2011/05/hpdss-error2.png" alt="" width="358" height="141" /></a>

While many different problems may cause these same errors, this particular problem was due to the DSS server communicating via IPv6 with the domain controller specified in the addressing portion of the configuration. Disabling IPv6 via the network properties on the server running the HP DSS software resolved the problem.

µ