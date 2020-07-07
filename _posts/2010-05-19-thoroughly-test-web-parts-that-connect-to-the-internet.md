---
ID: 867
post_title: >
  SharePoint Web Parts that Connect to the
  Internet
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2010/05/19/thoroughly-test-web-parts-that-connect-to-the-internet/
published: true
post_date: 2010-05-19 11:16:13
---
<a href="http://mikefrobbins.com/wp-content/uploads/2010/05/weather_webpart.png"><img class="alignleft size-full wp-image-870" title="weather_webpart" src="http://mikefrobbins.com/wp-content/uploads/2010/05/weather_webpart.png" alt="" width="143" height="126" /></a>A weather web part on your corporate intranet home page is something that sounds like a great idea since it will increase your end users productivity by reducing the amount of time they spend visiting weather web sites. One downside to adding these types of web parts is they could give the perception that your intranet is inaccessible in the event of an Internet outage.

A few days ago, while at one of my customers sites, they experienced a major telecom outage. All of their voice and data connections were severed by a construction mishap several miles away. A couple of minutes after learning about this issue, the corporate intranet which is hosted on a local SharePoint server was reported to be unavailable. After a few minutes of troubleshooting, everything on the SharePoint server appeared to be fine, but the home page wouldn’t load. The symptom in Internet Explorer was a blank page that showed "Connecting" as shown in the image below. At this point, all of their corporate users had the perception that SharePoint was down since it is their default home page.

<a href="http://mikefrobbins.com/wp-content/uploads/2010/05/ie_connecting.png"><img class="alignnone size-full wp-image-868" title="ie_connecting" src="http://mikefrobbins.com/wp-content/uploads/2010/05/ie_connecting.png" alt="" width="312" height="37" /></a>

<a href="http://mikefrobbins.com/wp-content/uploads/2010/05/weather_webpart_error.png"><img class="alignright size-full wp-image-869" title="weather_webpart_error" src="http://mikefrobbins.com/wp-content/uploads/2010/05/weather_webpart_error.png" alt="" width="150" height="115" /></a>After opening up the intranet home page and allowing it to load for what seemed like an eternity, the web page finally loaded with the weather web part displaying an error (shown on the right). Closing this web part resolved the issue since the web page loaded instantly afterwards. Testing the intranet from another computer confirmed that it was working properly. The moral of this story is that web parts such as the one for the weather may look cool and add functionality to your intranet homepage, but test them thoroughly to determine what happens when the Internet is unavailable.

µ