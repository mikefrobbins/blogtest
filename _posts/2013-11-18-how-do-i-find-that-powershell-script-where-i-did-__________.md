---
ID: 8502
post_title: >
  How do I find that PowerShell Script
  where I did __________?
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2013/11/18/how-do-i-find-that-powershell-script-where-i-did-__________/
published: true
post_date: 2013-11-18 07:30:21
---
I saw a tweet from <a href="http://twitter.com/juneb_get_help" target="_blank">June Blender</a> a few days ago that I thought was interesting:

<a href="http://twitter.com/juneb_get_help" target="_blank"><img class="alignnone size-full wp-image-8503" alt="search-childitem1" src="http://mikefrobbins.com/wp-content/uploads/2013/11/search-childitem1.png" width="520" height="98" /></a>

I knew you could use <em>Select-String</em> for searching documents like this but it's not something that I knew the exact syntax for off the top of my head. It's something that would have taken me at least a few minutes to figure out, probably with a little trial and error.

It just so happens that a few days later, I was looking for how I had done something with the range operator in one of my PowerShell scripts. Manually looking for this through thousands of scripts I've saved wouldn't have been worth the effort, but by referring back to June's tweet I'd marked as a favorite, I was able to quickly write a similar one liner to locate what I was looking for:
<pre class="lang:ps decode:true">Get-ChildItem -Path 'C:\Scripts\*.ps1' -Recurse |
Select-String -Pattern "1.." -SimpleMatch |
Select-Object -Property Path -Unique</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2013/11/search-childitem2a.png"><img class="alignnone size-full wp-image-8515" alt="search-childitem2a" src="http://mikefrobbins.com/wp-content/uploads/2013/11/search-childitem2a.png" width="528" height="173" /></a>

Although I marked June's tweet as a favorite on twitter, over time that favorite will become more difficult to locate which is why I'm blogging about it. My blog site is indexed so I'll be able to quickly find this information the next time I need it.

µ