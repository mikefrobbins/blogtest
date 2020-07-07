---
ID: 11392
post_title: >
  Create a New Empty File with the
  PowerShell Out-File Cmdlet
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2015/03/19/create-a-new-empty-file-with-the-powershell-out-file-cmdlet/
published: true
post_date: 2015-03-19 07:30:39
---
A few weeks ago I was setting up OpenSSL on a Windows machine and I was following a Linux tutorial which used the "<a href="http://www.linfo.org/touch.html" target="_blank">touch</a>" command to create a new empty file.

To accomplish the task of creating a new empty file with PowerShell, the most straight forward way is to use the <em>New-Item</em> cmdlet:
<pre class="lang:ps decode:true">New-Item -Name EmptyFile.txt -ItemType File</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/03/create-newemptyfile1a.jpg"><img class="alignnone size-full wp-image-11393" src="http://mikefrobbins.com/wp-content/uploads/2015/03/create-newemptyfile1a.jpg" alt="create-newemptyfile1a" width="877" height="178" /></a>

I inadvertently discovered another way to create a new empty file with PowerShell which I thought I would share with you guys, the readers of my blog.

You may see some people piping $null to the <em>Out-File</em> cmdlet, but when you pipe $null it doesn't produce any output so you could simply use the <em>Out-File</em> cmdlet by itself:
<pre class="lang:ps decode:true ">Out-File -FilePath EmptyFile2.txt</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/03/create-newemptyfile2a.jpg"><img class="alignnone size-full wp-image-11394" src="http://mikefrobbins.com/wp-content/uploads/2015/03/create-newemptyfile2a.jpg" alt="create-newemptyfile2a" width="877" height="202" /></a>

Even though the length of the file created using <em>Out-File</em> is two instead of zero, it is indeed an empty file.

If I were typing the previous command interactively in the PowerShell console, I would omit the <em>FilePath</em> positional parameter, but I consider it to be a best practice to always use full cmdlet and parameter names in my blog articles.

µ