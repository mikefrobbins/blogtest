---
ID: 8423
post_title: 'PowerShell Tip #2 from the Winner of the Advanced Category in the 2013 Scripting Games'
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2014/01/09/powershell-tip-2-from-the-winner-of-the-advanced-category-in-the-2013-scripting-games/
published: true
post_date: 2014-01-09 07:30:04
---
<strong>Tip #2 - Comment (Document) your code!</strong>

This is another one of those tips that probably isn't very popular, but regardless of how good you are at writing PowerShell scripts and functions, they're useless if no one else can figure out how to use them. You might be thinking that you're the only one who uses the PowerShell code that you write, but I'm sure that you like to go on vacation just like the rest of us and none of us are going to live forever.

In <a href="http://powershell.org/wp/2014/01/03/powershell-tip-1-from-the-winner-of-the-advanced-category-in-the-2013-scripting-games/" target="_blank">my tip #1 blog</a> you learned that you need to "Read the Help!". This tip builds on the first one because it allows others to "Read the Help!" for the PowerShell code that you write.

The type of help that you want to provide for your PowerShell functions and scripts is "Comment Based Help". Here's a simple example of a function with comment based help:
<pre class="lang:ps decode:true">function Get-BIOSInfo {

&lt;#
.SYNOPSIS
   Retrieves the BIOS information for the local computer.
.DESCRIPTION
   Get-BIOSInfo is a function that retrieves the BIOS information
   from WMI for the local computer.
.EXAMPLE
   Get-BIOSInfo
.INPUTS
   None
.OUTPUTS
   Win32_BIOS
#&gt;

    Get-WmiObject -Class Win32_BIOS

}</pre>
That little bit of information, when formatted properly, allows the user of this function to retrieve help for it just as they would for a cmdlet:

<a href="http://mikefrobbins.com/wp-content/uploads/2013/11/ps-tip2a.png"><img class="alignnone size-full wp-image-8845" alt="ps-tip2a" src="http://mikefrobbins.com/wp-content/uploads/2013/11/ps-tip2a.png" width="577" height="508" /></a>

See the "<a href="http://technet.microsoft.com/en-us/library/hh847834.aspx" target="_blank">about_Comment_Based_Help</a>" help topic for more details (read the help):
<pre class="lang:ps decode:true">help about_Comment_Based_Help</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/01/ps-tip2b.png"><img class="alignnone size-full wp-image-8847" alt="ps-tip2b" src="http://mikefrobbins.com/wp-content/uploads/2014/01/ps-tip2b.png" width="619" height="604" /></a>

If you're using the PowerShell ISE (Integrated Scripting Environment), the "Cntl + J" key combination will bring up code snippets and the first two options include the syntax for functions. They also include the syntax for comment based help:

<a href="http://mikefrobbins.com/wp-content/uploads/2014/01/ps-tip2c.png"><img class="alignnone size-full wp-image-8848" alt="ps-tip2c" src="http://mikefrobbins.com/wp-content/uploads/2014/01/ps-tip2c.png" width="699" height="331" /></a>

If you use the previous code snippet from the ISE, be sure to change the ".Synopsis" keyword to all caps since all of the other keywords are in all caps (be consistent).

If you happen to be using <a href="http://www.sapien.com/software/powershell_studio" target="_blank">SAPIEN PowerShell Studio</a>, the key combination to bring up code snippets is "Cntl + K":

<a href="http://mikefrobbins.com/wp-content/uploads/2014/01/ps-tip2d.png"><img class="alignnone size-full wp-image-8849" alt="ps-tip2d" src="http://mikefrobbins.com/wp-content/uploads/2014/01/ps-tip2d.png" width="679" height="535" /></a>

Unlike the ISE, there's a snippet in PowerShell Studio that is specifically for "Comment Based Help" and as you can see, the case of all the keywords is consistent (in all caps).

Inline help is another type of help as shown in the following example:
<pre class="lang:ps decode:true">function Get-BIOSInfo {

    #Attempting to retrieve the BIOS information from the local computer
    Get-WmiObject -Class Win32_BIOS

}</pre>
Single line inline help comments begin with an <a href="http://en.wiktionary.org/wiki/octothorpe" target="_blank">Octothorpe</a>. Yeah, I'd never heard of a pound or hash sign being called that either until I was listening to one of the <a href="http://PowerScripting.net" target="_blank">PowerScripting podcast</a> episodes.

Block comments use angle braces and octothorpes. They are multi-line inline comments:
<pre class="lang:ps decode:true">function Get-BIOSInfo {

    &lt;#
        Attempting to retrieve the BIOS
        information from the local computer
    #&gt;

    Get-WmiObject -Class Win32_BIOS
}</pre>
The problem is that inline help is absolutely useless because it will never be seen unless someone manually opens up your script and reads through it. Who really wants to read through hundreds or possibly even thousands of lines of code looking for comments? Maybe your turning this tool over to your help desk personnel and maybe they don't know much about PowerShell other than how to open the console and run certain commands. Do you really want them opening up your scripts and digging around in them looking for comments if they need help? With that said, <strong>don't use line help because there's a better way which I'll cover in my next blog article</strong> (tip #3).

Last but not least, comment but don't over comment. You're not writing literature.

µ