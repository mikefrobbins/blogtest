---
ID: 10109
post_title: >
  Using PowerShell to Search for Specific
  Users in Active Directory without
  Knowing their Exact Information
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2014/06/24/using-powershell-to-search-for-specific-users-in-active-directory-without-knowing-their-exact-information/
published: true
post_date: 2014-06-24 07:30:44
---
You're looking for a user in your Active Directory environment who goes by the nickname of "JW". You know that's the user's initials and you need to find their AD user account.

Typically you'd use the <em>Identity</em> parameter, but that parameter doesn't allow wildcards:
<pre class="lang:ps decode:true">Get-ADUser -Identity j*w*</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/06/finduser1.jpg"><img class="alignnone size-full wp-image-10110" src="http://mikefrobbins.com/wp-content/uploads/2014/06/finduser1.jpg" alt="finduser1" width="877" height="155" /></a>

Verifying wildcard's are not allowed on the <em>Identity</em> parameter of <em>Get-ADUser</em>:
<pre class="lang:ps decode:true">help Get-ADUser -Parameter identity</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/06/finduser4.jpg"><img class="alignnone size-full wp-image-10113" src="http://mikefrobbins.com/wp-content/uploads/2014/06/finduser4.jpg" alt="finduser4" width="877" height="358" /></a>

What you'll need to do is use the <em>Filter</em> parameter instead:
<pre class="lang:ps decode:true">Get-ADUser -Filter {name -like 'j*w*'}</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/06/finduser2.jpg"><img class="alignnone size-full wp-image-10111" src="http://mikefrobbins.com/wp-content/uploads/2014/06/finduser2.jpg" alt="finduser2" width="877" height="767" /></a>

The previous results were close to what you wanted, but not exactly. It included users like "Jo Brown" since his name also matches the search criteria that was provided. This time let's try a compound filter and specify GivenName's that start with "J" and Surname's that start with "W":
<pre class="lang:ps decode:true">Get-ADUser -Filter {GivenName -like 'j*' -and Surname -like 'W*'}</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/06/finduser3.jpg"><img class="alignnone size-full wp-image-10112" src="http://mikefrobbins.com/wp-content/uploads/2014/06/finduser3.jpg" alt="finduser3" width="877" height="636" /></a>

The previous example is much, much better than using the <em>Where-Object</em> cmdlet to filter with since the previous example follows the best practice of filtering early or filtering left.

This is how NOT to accomplish the task because it is less efficient:
<pre class="lang:ps decode:true">Get-ADUser -Filter * | Where-Object {$_.GivenName -like 'j*' -and $_.Surname -like 'W*'}</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/06/finduser5.jpg"><img class="alignnone size-full wp-image-10115" src="http://mikefrobbins.com/wp-content/uploads/2014/06/finduser5.jpg" alt="finduser5" width="877" height="635" /></a>

Since I've already told you that the previous example is less efficient, I'll now show you that it's less efficient:
<pre class="lang:ps decode:true">Measure-Command {Get-ADUser -Filter {GivenName -like 'j*' -and Surname -like 'W*'}}

Measure-Command {Get-ADUser -Filter * | Where-Object {$_.GivenName -like 'j*' -and $_.Surname -like 'W*'}}</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/06/finduser6.jpg"><img class="alignnone size-full wp-image-10116" src="http://mikefrobbins.com/wp-content/uploads/2014/06/finduser6.jpg" alt="finduser6" width="877" height="466" /></a>

As you can see in the previous results, the example that used the <em>Filter</em> parameter took about 12 milliseconds to complete and the example that used the <em>Where-Object</em> cmdlet for the filtering took approximately 310 milliseconds to complete. There are a total of 305 Active Directory user accounts in the test environment that these examples were run against. The performance of the <em>Where-Object</em> example would be worse if more Active Directory user accounts existed in the environment.

The examples shown in this blog have been demonstrated on a Windows 8.1 client machine with the RSAT (Remote Server Administration Tools) installed. The client machine is part of a domain and the domain controllers are running Windows Server 2012 R2.

µ