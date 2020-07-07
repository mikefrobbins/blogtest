---
ID: 11903
post_title: 'PowerShell $Profile: The six options and their precedence'
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2015/07/02/powershell-profile-the-six-options-and-their-precedence/
published: true
post_date: 2015-07-02 07:30:49
---
There are a total of six different profiles than can be created in PowerShell by default. Four of them can exist that are applied to the PowerShell console:
<pre class="lang:ps decode:true">$PROFILE | Format-List -Force</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/06/profiles1a.jpg"><img class="alignnone size-full wp-image-11904" src="http://mikefrobbins.com/wp-content/uploads/2015/06/profiles1a.jpg" alt="profiles1a" width="877" height="180" /></a>

And four of them can also exist that are applied to the PowerShell ISE (Integrated Scripting Environment):
<pre class="lang:ps decode:true">$profile | Format-List -Force</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/06/profiles2a.jpg"><img class="alignnone size-full wp-image-11905" src="http://mikefrobbins.com/wp-content/uploads/2015/06/profiles2a.jpg" alt="profiles2a" width="890" height="282" /></a>

Two of the profiles are the same between the PowerShell console and ISE which gives you a total of six possible profile combinations that can be used to load items when you start PowerShell depending on if you want it to load for all users or for the current user and for all hosts or for the current host.

Keep in mind, if you're sharing scripts with others, be sure to test your code with no profile before sharing it. To accomplish this, start the PowerShell console with -noprofile:
<pre class="lang:ps decode:true">powershell.exe /?</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/06/profiles3a.jpg"><img class="alignnone size-full wp-image-11906" src="http://mikefrobbins.com/wp-content/uploads/2015/06/profiles3a.jpg" alt="profiles3a" width="877" height="643" /></a>

The ISE can also be started with the -noprofile option:
<pre class="lang:ps decode:true">powershell_ise.exe /?</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/06/profiles4a.jpg"><img class="alignnone size-full wp-image-11907" src="http://mikefrobbins.com/wp-content/uploads/2015/06/profiles4a.jpg" alt="profiles4a" width="480" height="440" /></a>

I created all of the possible profiles for the ISE and based on my testing, this is the order they're applied in:

<a href="http://mikefrobbins.com/wp-content/uploads/2015/06/profiles5a.jpg"><img class="alignnone size-full wp-image-11918" src="http://mikefrobbins.com/wp-content/uploads/2015/06/profiles5a.jpg" alt="profiles5a" width="877" height="176" /></a>

I also created all of the possible profiles for the PowerShell console, added variable creation, and incremented the value of the variable in each profile to get a better idea of the precedence of each profile script:

<a href="http://mikefrobbins.com/wp-content/uploads/2015/06/profiles6a.jpg"><img class="alignnone size-full wp-image-11919" src="http://mikefrobbins.com/wp-content/uploads/2015/06/profiles6a.jpg" alt="profiles6a" width="877" height="192" /></a>

One thing that the variables that I've defined in each of my profile scripts also tells us is that even though profiles are merely scripts that execute when PowerShell is launched, that any variable defined in them loads in the global scope by default. This can also be confirmed by taking a look at the global section of the <a href="https://technet.microsoft.com/en-us/library/hh847849.aspx" target="_blank">about_Scopes</a> help topic.

To learn more about PowerShell profiles see the <a href="https://technet.microsoft.com/en-us/library/hh847857.aspx" target="_blank">about_Profiles</a> help topic.

µ