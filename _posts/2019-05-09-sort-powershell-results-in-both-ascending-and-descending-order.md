---
ID: 17809
post_title: >
  Sort PowerShell Results in both
  Ascending and Descending Order
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2019/05/09/sort-powershell-results-in-both-ascending-and-descending-order/
published: true
post_date: 2019-05-09 07:30:26
---
A few weeks ago I was trying to figure out how to sort something in PowerShell with one property sorted descending and another one ascending. How to accomplish this was hiding in plain sight in the help examples for <a href="https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.utility/sort-object"><em>Sort-Object</em></a>, but I thought I would documented it here for future reference.

Use the <em>Property</em> parameter with a hash table to specify the property names and their sort orders.
<pre class="lang:ps decode:true">Get-Service -Name Win* |
Sort-Object -Property @{
    expression = 'Status'
    descending = $true
}, @{
    expression = 'DisplayName'
    descending = $false
}</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2019/05/sort-multiple-orders1a.jpg"><img class="alignnone size-full wp-image-17813" src="https://mikefrobbins.com/wp-content/uploads/2019/05/sort-multiple-orders1a.jpg" alt="" width="859" height="287" /></a>

This same command can be written on one line.
<pre class="lang:ps decode:true">Get-Service -Name Win* | Sort-Object -Property @{expression = 'Status';descending = $true}, @{expression = 'DisplayName';descending = $false}</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2019/05/sort-multiple-orders2a.jpg"><img class="alignnone size-full wp-image-17817" src="https://mikefrobbins.com/wp-content/uploads/2019/05/sort-multiple-orders2a.jpg" alt="" width="859" height="201" /></a>

It can also be shortened with aliases and positional parameters if you're running this in the console and don't plan to share or save it.
<pre class="lang:ps decode:true">gsv Win* | sort @{e='Status';desc=$true},@{e='DisplayName';desc=$false}</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2019/05/sort-multiple-orders3a.jpg"><img class="alignnone size-full wp-image-17818" src="https://mikefrobbins.com/wp-content/uploads/2019/05/sort-multiple-orders3a.jpg" alt="" width="859" height="186" /></a>

Technically, it doesn't appear that you even have to specify <em>Descending = $false</em> as that's the default sort order.
<pre class="lang:ps decode:true">gsv Win* | sort @{e='Status';desc=$true},DisplayName</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2019/05/sort-multiple-orders4a.jpg"><img class="alignnone size-full wp-image-17822" src="https://mikefrobbins.com/wp-content/uploads/2019/05/sort-multiple-orders4a.jpg" alt="" width="859" height="185" /></a>

You should of course use full cmdlet and parameter names in any command that you share or save.

µ