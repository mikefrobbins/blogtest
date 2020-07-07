---
ID: 10146
post_title: >
  Extract the Name from an Active
  Directory Distinguished Name with
  PowerShell and a Regular Expression
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2014/07/10/extract-the-name-from-an-active-directory-distinguished-name-with-powershell-and-a-regular-expression/
published: true
post_date: 2014-07-10 07:30:02
---
This is actually something I had a small blurb about in my previous blog article, but I wanted to go back, revisit it, and write a dedicated blog article about it.

Sometimes there are properties in Active Directory like the one in the following example where the "Manager" property is being returned as a distinguished name and what you really wanted was just their name (in human readable format):
<pre class="lang:ps decode:true">Get-ADUser -Filter * -SearchBase 'OU=Northwind Users,OU=Users,OU=Test,DC=mikefrobbins,DC=com' -Properties Manager, Title |
Format-Table -Property Name, Title, Manager -AutoSize</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/07/distname1.jpg"><img class="alignnone size-full wp-image-10154" src="http://mikefrobbins.com/wp-content/uploads/2014/07/distname1.jpg" alt="distname1" width="877" height="239" /></a>

You could write a complicated function or script to query Active Directory for the "Managers" information and create a custom object to return both the actual users information and the managers information, but if you simply want the name of the manager (in this example), it's much easier to use the substring method or a regular expression. In this blog article, I'll be using a regular expression.

If you want to create a custom column name and/or a calculated property value with either the <em>Select-Object</em> cmdlet or the <em>Format-Table</em> cmdlet, start out by adding a hash table where you would normally have a property name as shown in the following example:
<pre class="lang:ps decode:true">Select-Object -Property @{}</pre>
You can mix and match multiple hash tables and/or hash tables and the normal property names in a comma separated list to return more than one property.

Add "label" or "name" and what you want the "name of the custom property" to be, preferably without spaces and end with a semicolon:
<pre class="lang:ps decode:true">Select-Object -Property @{label='Supervisor';}</pre>
Label can be abbreviated with an "L" and Name can be abbreviated with an "N". I treat these like aliases and I don't use the abbreviations in scripts or functions.

Then add the 'Expression' portion of the hash table. The calculated value portion of the expression goes in another set of curly braces:
<pre class="lang:ps decode:true">Select-Object -Property @{label='Supervisor';expression={$_.manager}}
</pre>
At this point, all it does is change the label or name of the manager property to Supervisor:
<pre class="lang:ps decode:true">Get-ADUser -Identity ndavolio -Properties manager |
Select-Object -Property @{label='Supervisor';expression={$_.manager}}</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/07/distname2.jpg"><img class="alignnone size-full wp-image-10155" src="http://mikefrobbins.com/wp-content/uploads/2014/07/distname2.jpg" alt="distname2" width="877" height="144" /></a>

I don't claim to be a master of regular expressions, but I can write a fairly simple one to accomplish the task of extracting the name from a distinguished name. I'll first start out replacing the "CN=" since all managers should have that at the beginning of them:
<pre class="lang:ps decode:true">Get-ADUser -Identity ndavolio -Properties manager |
Select-Object -Property @{label='Supervisor';expression={$_.manager -replace '^CN='}}</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/07/distname3.jpg"><img class="alignnone size-full wp-image-10157" src="http://mikefrobbins.com/wp-content/uploads/2014/07/distname3.jpg" alt="distname3" width="877" height="144" /></a>

The caret symbol (^) in the previous example means look only at the beginning.

For the sake of simplicity, I'll just write another regular expression to take care of everything after the name portion of the distinguished name:
<pre class="lang:ps decode:true">Get-ADUser -Identity ndavolio -Properties manager |
Select-Object -Property @{label='Supervisor';expression={$_.manager -replace ',.*$'}}</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/07/distname4a.jpg"><img class="alignnone size-full wp-image-10159" src="http://mikefrobbins.com/wp-content/uploads/2014/07/distname4a.jpg" alt="distname4a" width="877" height="143" /></a>

This one says match a comma, then any character zero or more times and the dollar sign matches the end of a string. This gets rid of everything after the first comma.

The pipe symbol is used to match more than one pattern in a regular expression which gives you the human readable name that you were looking for:
<pre class="lang:ps decode:true ">Get-ADUser -Identity ndavolio -Properties manager |
Select-Object -Property @{label='Supervisor';expression={$_.manager -replace '^CN=|,.*$'}}</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/07/distname5.jpg"><img class="alignnone size-full wp-image-10160" src="http://mikefrobbins.com/wp-content/uploads/2014/07/distname5.jpg" alt="distname5" width="877" height="143" /></a>

Now to apply this to the first example where multiple users and properties were returned:
<pre class="lang:ps decode:true ">Get-ADUser -Filter * -SearchBase 'OU=Northwind Users,OU=Users,OU=Test,DC=mikefrobbins,DC=com' -Properties Manager, Title |
Select-Object -Property Name, Title, @{label='Manager';expression={$_.manager -replace '^CN=|,.*$'}}</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/07/distname6.jpg"><img class="alignnone size-full wp-image-10161" src="http://mikefrobbins.com/wp-content/uploads/2014/07/distname6.jpg" alt="distname6" width="877" height="251" /></a>

µ