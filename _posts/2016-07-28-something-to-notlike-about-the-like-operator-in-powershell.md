---
ID: 14259
post_title: >
  Something to -notlike about the -like
  operator in PowerShell
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2016/07/28/something-to-notlike-about-the-like-operator-in-powershell/
published: true
post_date: 2016-07-28 07:30:04
---
I recently ran into a problem with the PowerShell <em>like</em> operator that I wanted to share since what's occurring may not be immediately apparent.

The <em>like</em> operator allows for comparison tests of strings using wildcard characters instead of exact matches. I think of it being similar to the match operator except <em>like</em> uses simple wildcards instead of regular expressions.
<pre class="lang:ps decode:true">'Microsoft Windows 10 Enterprise' -like '*Windows 10*'</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2016/07/like-operator1a.png"><img class="alignnone size-full wp-image-14261" src="http://mikefrobbins.com/wp-content/uploads/2016/07/like-operator1a.png" alt="like-operator1a" width="859" height="71" /></a>

Easy enough, right? A string on the left and another string with wildcards on the right. If it matches, true is returned otherwise if it doesn't match, false is returned.

The dilemma is when you decide to use wildcard characters on the left side instead of on the right:
<pre class="lang:ps decode:true ">'*Windows 10*' -like 'Microsoft Windows 10 Enterprise'</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2016/07/like-operator2a.png"><img class="alignnone size-full wp-image-14262" src="http://mikefrobbins.com/wp-content/uploads/2016/07/like-operator2a.png" alt="like-operator2a" width="859" height="70" /></a>

Conventional wisdom would make you think that either of these examples would return true but as you can see in the previous set of results, don't make assumptions otherwise you could spend countless hours troubleshooting a larger script or function where something like the second example is used and it could cost you a lot of time as well as your sanity.

The characters listed in the <a href="https://technet.microsoft.com/en-us/library/hh847812.aspx" target="_blank">about_Wildcards</a> help topic have special meaning when used on the right side of the <em>like</em> operator. If they're used on the left side of the <em>like</em> operator, they're literals and have no special meaning.

Their special meaning can be escaped on the right side of the <em>like</em> operator using the backtick character as mentioned in the <a href="https://technet.microsoft.com/en-us/library/hh847740.aspx" target="_blank">about_Quoting_Rules</a> help topic.
<pre class="lang:ps decode:true ">'Microsoft Windows 10 Enterprise' -like '`*Windows 10`*'</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2016/07/like-operator3a.png"><img class="alignnone size-full wp-image-14277" src="http://mikefrobbins.com/wp-content/uploads/2016/07/like-operator3a.png" alt="like-operator3a" width="859" height="71" /></a>

See the <a href="https://technet.microsoft.com/en-us/library/hh847759.aspx" target="_blank">about_Comparision_Operators</a> help topic to learn more about the PowerShell <em>like</em> operator. Also consider taking a look at the previously referenced <a href="https://technet.microsoft.com/en-us/library/hh847812.aspx" target="_blank">about_Wildcards</a> help topic to learn more about advanced wildcards that can be used with the <em>like</em> operator such as a range of characters.

µ