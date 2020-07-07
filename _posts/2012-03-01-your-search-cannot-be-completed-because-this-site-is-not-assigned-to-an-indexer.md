---
ID: 3358
post_title: >
  Your Search Cannot Be Completed Because
  This Site Is Not Assigned To An Indexer.
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2012/03/01/your-search-cannot-be-completed-because-this-site-is-not-assigned-to-an-indexer/
published: true
post_date: 2012-03-01 07:30:19
---
Your attempting to search a SharePoint 2010 Site (Foundation Edition) and you receive the following error message "Your search cannot be completed because this site is not assigned to an indexer."
<a href="http://mikefrobbins.com/wp-content/uploads/2012/02/sp2010f-search1.png"><img class="alignnone size-full wp-image-3360" title="sp2010f-search1" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/02/sp2010f-search1.png" width="635" height="228" /></a>

Search has never been configured for this SharePoint farm so we'll be configuring it from the ground up. First you'll need two service accounts, one for the search service to run as and one for the actual crawling. Todd Klindt has a great <a href="http://www.toddklindt.com/blog/Lists/Posts/Post.aspx?ID=237" target="_blank">blog on service accounts</a> that I use for reference. I used the following PowerShell command to create these accounts:
<pre class="lang:ps decode:true">Import-Module ActiveDirectory
$Password = Read-Host -assecurestring "SP Search Account Password"
$Name = "spExtranetSearch"
$UPN = "spExtranetSearch@mikefrobbins.com"
$Description = "SharePoint Search Account - Extranet"
$Path = "ou=service accts,dc=mikefrobbins,dc=com"
New-ADUser -Name $Name -AccountPassword $Password -Description $Description `
-Enabled $true -PasswordNeverExpires $true -Path $Path -SamAccountName $Name `
-UserPrincipalName $UPN
$Password = Read-Host -assecurestring "SP Content Account Password"
$Name = "spExtranetContent"
$UPN = "spExtranetContent@mikefrobbins.com"
$Description = "SharePoint Content Account for Search Crawling - Extranet"
$Path = "ou=service accts,dc=mikefrobbins,dc=com"
New-ADUser -Name $Name -AccountPassword $Password -Description $Description `
-Enabled $true -PasswordNeverExpires $true -Path $Path -SamAccountName $Name `
-UserPrincipalName $UPN</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2012/02/sp2010f-search2.png"><img class="alignnone size-full wp-image-3362" title="sp2010f-search2" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/02/sp2010f-search2.png" width="606" height="267" /></a>

Register the spNameSearch service account as a SharePoint Managed Account:
<pre class="lang:ps decode:true">Add-PSSnapin Microsoft.SharePoint.PowerShell
$PoolCredential = Get-Credential -credential mikefrobbins\spExtranetSearch
New-SPManagedAccount -Credential $PoolCredential</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2012/02/sp2010f-search21.png"><img class="alignnone size-full wp-image-3374" title="sp2010f-search21" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/02/sp2010f-search21.png" width="587" height="171" /></a>

Go to the Central Administration website &gt; System Settings &gt; Manage services on server and click "Start" next to "SharePoint Foundation Search":
<a href="http://mikefrobbins.com/wp-content/uploads/2012/02/sp2010f-search3.png"><img class="alignnone size-full wp-image-3372" title="sp2010f-search3" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/02/sp2010f-search3.png" width="640" height="429" /></a>

Select the spNameSearch account from the drop down list in the Service Account section. It will only show up in the list if it has been registered as a SharePoint Managed Account. Use the domainspNameContent account in the Content Access Account section. I've also modified the database name to match my naming scheme for the other SharePoint databases. Modify the indexing schedule as needed depending on the size of your farm and the performance impact you can accept. If in doubt, start with the default index schedule.
<a href="http://mikefrobbins.com/wp-content/uploads/2012/02/sp2010f-search4.png"><img class="alignnone size-full wp-image-3376" title="sp2010f-search4" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/02/sp2010f-search4.png" width="640" height="598" /></a>

Assign a search server to the content database by going to Central Administration &gt; Manage Content Databases and selecting your content database.
<a href="http://mikefrobbins.com/wp-content/uploads/2012/02/sp2010f-search5.png"><img class="alignnone size-full wp-image-3378" title="sp2010f-search5" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/02/sp2010f-search5.png" width="640" height="44" /></a>

Now you just have to wait until the content is crawled. Until then you won't return any search results, but you also won't receive the error that was originally displayed.
<a href="http://mikefrobbins.com/wp-content/uploads/2012/02/sp2010f-search6.png"><img class="alignnone size-full wp-image-3379" title="sp2010f-search6" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/02/sp2010f-search6.png" width="531" height="393" /></a>

µ