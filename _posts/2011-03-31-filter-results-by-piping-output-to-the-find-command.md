---
ID: 2087
post_title: >
  Filter Results by Piping Output to the
  Find Command
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2011/03/31/filter-results-by-piping-output-to-the-find-command/
published: true
post_date: 2011-03-31 07:30:23
---
Piping the output of a command line utility to the find command is a way to easily narrow down the results that are returned.

Here's an example of piping the output of the netstat command to find only the ports that your server is listening on:
<a href="http://mikefrobbins.com/wp-content/uploads/2011/03/netstat-find1.png"><img class="alignnone size-full wp-image-2088" title="netstat-find1" alt="" src="http://mikefrobbins.com/wp-content/uploads/2011/03/netstat-find1.png" width="640" height="372" /></a>
<pre class="lang:batch decode:true">netstat -an -p TCP | find /I "listening"</pre>
You can pipe the output multiple times to narrow down your search results:
<a href="http://mikefrobbins.com/wp-content/uploads/2011/03/netstat-find4.png"><img class="alignnone size-full wp-image-2093" title="netstat-find4" alt="" src="http://mikefrobbins.com/wp-content/uploads/2011/03/netstat-find4.png" width="640" height="79" /></a>

The find command can also be used to search text files:
<a href="http://mikefrobbins.com/wp-content/uploads/2011/03/netstat-find2.png"><img class="alignnone size-full wp-image-2090" title="netstat-find2" alt="" src="http://mikefrobbins.com/wp-content/uploads/2011/03/netstat-find2.png" width="640" height="160" /></a>

Here's the syntax of the find command:
<a href="http://mikefrobbins.com/wp-content/uploads/2011/03/netstat-find3.png"><img class="alignnone size-full wp-image-2091" title="netstat-find3" alt="" src="http://mikefrobbins.com/wp-content/uploads/2011/03/netstat-find3.png" width="640" height="241" /></a>

µ