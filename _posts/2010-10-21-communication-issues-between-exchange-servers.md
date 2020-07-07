---
ID: 1267
post_title: >
  Communication Issues between Exchange
  Servers
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2010/10/21/communication-issues-between-exchange-servers/
published: true
post_date: 2010-10-21 21:30:40
---
Recently while transitioning one of my customers from Exchange 2007 to Exchange 2010, I experienced issues trying to replicate public folders between the servers. This was due to a larger mail flow communications problem between the two servers where mail between them was queuing up and never being delivered. To determine if you are experiencing this particular issue or not, use the Queue Viewer which is located in the toolbox section of the “Exchange Management Console”. If email that should be delivered to the other server or replication messages between the servers are queuing up, it’s a sure sign that you have this problem. To resolve this mail flow and public folder replication issue, a receive connector needs to be setup on each of the Exchange servers that allows “Exchange Server authentication” and “Integrated Windows authentication” as shown below. The scope of the connector only needs to apply to the specific ip addresses of your exchange servers. A more specific receive connector will take precedence over a broadly defined one. This means if an ip address is part of a receive connector for your entire subnet and defined as the only ip address for another receive connector, the settings for the connector with one ip address will apply.

<a href="http://mikefrobbins.com/wp-content/uploads/2010/10/pf-replication.png"><img class="alignnone size-medium wp-image-1338" title="pf-replication" src="http://mikefrobbins.com/wp-content/uploads/2010/10/pf-replication.png?w=266" alt="" width="266" height="300" /></a>

µ