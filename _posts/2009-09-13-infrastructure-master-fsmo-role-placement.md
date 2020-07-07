---
ID: 94
post_title: >
  Infrastructure Master FSMO Role
  Placement
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2009/09/13/infrastructure-master-fsmo-role-placement/
published: true
post_date: 2009-09-13 17:17:02
---
The Infrastructure Master Role is one of the three domain operations masters. Its placement is like many other questions in Information Technology, that is, it depends. It depends on the number of domains in the forest and whether or not all domain controllers in a particular domain have been designated as a global catalog server. The infrastructure master is responsible for updating its domains references to objects in other domains in a multi-domain forest by checking its references with the global catalog. If you only have one domain in your forest or if every domain controller in the domain is a global catalog server, then there’s nothing for the Infrastructure Master to do so its placement is irrelevant. Its placement only matters when you have multiple domains in your forest and some of the domain controllers in your domain are not global catalog servers. In which case you should place the Infrastructure Master Role on a domain controller that is a non-global catalog domain controller that also has a direct connection to a global catalog server and preferably in the same AD site. There can only be one domain controller in each domain that holds the Infrastructure Master FSMO Role.
<p style="line-height: 14.25pt; background: white;"><span style="font-size: 10pt; font-family: &quot;color:black;"><img class="alignnone size-full wp-image-97" title="infrastructure_master" src="http://mikefrobbins.com/wp-content/uploads/2009/09/infrastructure_master.jpg" alt="infrastructure_master" width="398" height="448" /></span></p>
Each domain controller stores information about its own domain and some basic information about the forest, a domain controller that is designated as a Global Catalog server also stores some information about every object in every other domain in the forest. The global catalog server is used by clients to search for objects in other domains without having to be referred to a domain controller in another domain. Even in a single domain forest, it is important to have a least one global catalog server since many applications use the global catalog server for searching (port 3268).
<p style="line-height: 14.25pt; background: white;"><img class="alignnone size-full wp-image-101" title="global_catalog" src="http://mikefrobbins.com/wp-content/uploads/2009/09/global_catalog.jpg" alt="global_catalog" width="399" height="448" /></p>
For more information:
<a href="http://support.microsoft.com/kb/223346" target="_blank" rel="noopener">http://support.microsoft.com/kb/223346</a>

µ