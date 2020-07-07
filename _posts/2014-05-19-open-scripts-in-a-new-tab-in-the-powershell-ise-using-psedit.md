---
ID: 9919
post_title: >
  Open Scripts in a new tab in the
  PowerShell ISE using PSEdit
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2014/05/19/open-scripts-in-a-new-tab-in-the-powershell-ise-using-psedit/
published: true
post_date: 2014-05-19 07:30:34
---
While at TechEd last week, PSEdit was used in one of the PowerShell sessions that I sat through. Afterwards a couple of friends asked me if I had ever heard of it and said they hadn't so I thought I would write a blog article about it to help spread the word.

PSEdit is a function that only exists in the PowerShell ISE. Type in PSEdit followed by the name of a PowerShell script and that script will be opened in a new tab in the ISE:
<pre class="lang:ps decode:true">psEdit .\Get-FSMORole.ps1</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/05/051814-1.jpg"><img class="alignnone size-full wp-image-9920" src="http://mikefrobbins.com/wp-content/uploads/2014/05/051814-1.jpg" alt="051814-1" width="706" height="152" /></a>

Try the same thing in the PowerShell console and you'll end up with an error message:
<pre class="lang:ps decode:true">psEdit .\Get-FSMORole.ps1</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/05/051814-2.jpg"><img class="alignnone size-full wp-image-9921" src="http://mikefrobbins.com/wp-content/uploads/2014/05/051814-2.jpg" alt="051814-2" width="877" height="156" /></a>

Since psEdit is a function and not a compiled cmdlet, you can take a look under the covers to see what it does behind the scenes:
<pre class="lang:ps decode:true">Get-Command -Name psEdit | Select-Object -ExpandProperty Definition</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/05/051814-3.jpg"><img class="alignnone size-full wp-image-9922" src="http://mikefrobbins.com/wp-content/uploads/2014/05/051814-3.jpg" alt="051814-3" width="700" height="270" /></a>

There's very little help for psEdit and this is even after running <em>Update-Help</em> from within the PowerShell ISE:
<pre class="lang:ps decode:true">help psEdit</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/05/051814-4.jpg"><img class="alignnone size-full wp-image-9923" src="http://mikefrobbins.com/wp-content/uploads/2014/05/051814-4.jpg" alt="051814-4" width="698" height="372" /></a>

Even though the <em>filenames</em> parameter doesn't show it accepting an array [], based on the code and a little testing, a common separated list can be used to open multiple scripts:
<pre class="lang:ps decode:true">psEdit -filenames '.\Get-FSMORole.ps1', '.\Get-Top10PRocesses.ps1'</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/05/051814-5.jpg"><img class="alignnone size-full wp-image-9925" src="http://mikefrobbins.com/wp-content/uploads/2014/05/051814-5.jpg" alt="051814-5" width="702" height="151" /></a>

I've actually written a <a href="http://mikefrobbins.com/2013/11/27/powershell-one-liner-to-locate-scripts-and-automatically-open-them-in-new-tabs-in-the-ise/" target="_blank">previous blog article</a> to locate and open scripts in the PowerShell ISE that uses the same behind the scenes code as psEdit that you may also find interesting.

µ