---
ID: 18359
post_title: >
  How many Services does Microsoft Azure
  Offer?
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2020/03/04/how-many-services-does-microsoft-azure-offer/
published: true
post_date: 2020-03-04 07:30:49
---
I've recently wondered how many service offerings Azure has. I've read anywhere from 150 to 600 and I even went so far as to ask an Azure Product Manager but still didn't receive a clear answer. What I did find out is that Microsoft maintains an Azure services directory on <a href="https://azure.microsoft.com/en-us/services/" target="_blank" rel="noopener noreferrer">their Azure products website</a>.

I figured that was a good place to start looking and while the website is informative, it didn't provide a count of the service offerings. Manually counting them wouldn't be an efficient or accurate way of accomplishing the task of determining how many services are offered so I decided to use PowerShell for the task.

First, if you're prototyping a command, it's best to store the results in a variable and work with the variable. If I heard Ed Wilson, The Scripting Guy (retired) say it one time, I've heard him say it a thousand times: "If you're going to eat an elephant, only eat it once."

I queried the previous referenced website and stored the results in a variable. Then I determined what the properties were and took a look at the type of data each property returned.
<pre class="lang:ps decode:true ">$AzureServices = Invoke-WebRequest -Uri https://azure.microsoft.com/en-us/services/
$AzureServices | Get-Member</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2020/03/azure-services1a.jpg"><img class="alignnone size-full wp-image-18360" src="https://mikefrobbins.com/wp-content/uploads/2020/03/azure-services1a.jpg" alt="" width="979" height="466" /></a>

The <em>Links</em> property looked promising and with a little filtering, I was able to return a list of the services.
<pre class="lang:ps decode:true">$AzureServices.Links |
Where-Object href -like '/en-us/services/?*' |
Select-Object -Property data-event-property, href</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2020/03/azure-services2a.jpg"><img class="alignnone size-full wp-image-18361" src="https://mikefrobbins.com/wp-content/uploads/2020/03/azure-services2a.jpg" alt="" width="979" height="512" /></a>

So far, it appears that 243 Azure services are listed on the webpage.
<pre class="lang:ps decode:true">($AzureServices.Links |
Where-Object href -like '/en-us/services/?*' |
Select-Object -Property data-event-property, href).Count</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2020/03/azure-services3a.jpg"><img class="alignnone size-full wp-image-18362" src="https://mikefrobbins.com/wp-content/uploads/2020/03/azure-services3a.jpg" alt="" width="979" height="114" /></a>

What I started noticing though is there are numerous services listed more than once in different categories.
<pre class="lang:ps decode:true">$AzureServices.Links |
Where-Object href -like '/en-us/services/?*' |
Select-Object -Property data-event-property, href |
Group-Object -Property data-event-property, href -NoElement |
Where-Object -Property Count -gt 1 |
Sort-Object -Property Count -Descending |
Format-Table -AutoSize</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2020/03/azure-services4a.jpg"><img class="alignnone size-full wp-image-18363" src="https://mikefrobbins.com/wp-content/uploads/2020/03/azure-services4a.jpg" alt="" width="979" height="512" /></a>

I decided to sort the results uniquely by their relative URL (href) and their name (data-event-property).
<pre class="lang:ps decode:true">$AzureServices.Links |
Where-Object href -like '/en-us/services/?*' |
Select-Object -Property data-event-property, href |
Sort-Object -Property href, data-event-property -Unique</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2020/03/azure-services5a.jpg"><img class="alignnone size-full wp-image-18364" src="https://mikefrobbins.com/wp-content/uploads/2020/03/azure-services5a.jpg" alt="" width="979" height="512" /></a>

And finally for a unique count of the Azure services listed on the webpage.
<pre class="lang:ps decode:true ">($AzureServices.Links |
Where-Object href -like '/en-us/services/?*' |
Select-Object -Property data-event-property, href |
Sort-Object -Property href, data-event-property -Unique).Count</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2020/03/azure-services6a.jpg"><img class="alignnone size-full wp-image-18365" src="https://mikefrobbins.com/wp-content/uploads/2020/03/azure-services6a.jpg" alt="" width="979" height="128" /></a>

There are 169 unique Azure services listed on the <a href="https://azure.microsoft.com/en-us/services/" target="_blank" rel="noopener noreferrer">Azure products webpage</a> as of the writing of this blog article.

µ