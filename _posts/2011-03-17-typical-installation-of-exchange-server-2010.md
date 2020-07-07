---
ID: 1974
post_title: >
  Typical Installation of Exchange Server
  2010
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2011/03/17/typical-installation-of-exchange-server-2010/
published: true
post_date: 2011-03-17 07:30:33
---
This blog will walk you through the steps of a typical Exchange Server 2010 Installation. Be sure to follow the instructions in my “<a href="http://mikefrobbins.com/2011/02/24/installation-of-exchange-server-2010-prerequisites/" target="_blank">Installation of Exchange Server 2010 Prerequisites</a>” blog.

By having completed the steps in that previous blog article, you can jump right into Step 3:
<a href="http://mikefrobbins.com/wp-content/uploads/2011/02/e2010install-1.jpg"><img class="alignnone size-medium wp-image-1975" title="e2010install-1" src="http://mikefrobbins.com/wp-content/uploads/2011/02/e2010install-1.jpg?w=300" alt="" width="300" height="224" /></a>

Select the appropriate option. In the example below, I chose “Install only languages from the DVD”:
<a href="http://mikefrobbins.com/wp-content/uploads/2011/02/e2010install-2.jpg"><img class="alignnone size-medium wp-image-1976" title="e2010install-2" src="http://mikefrobbins.com/wp-content/uploads/2011/02/e2010install-2.jpg?w=300" alt="" width="300" height="224" /></a>

Click on the link for Step 4:
<a href="http://mikefrobbins.com/wp-content/uploads/2011/02/e2010install-3.jpg"><img class="alignnone size-medium wp-image-1977" title="e2010install-3" src="http://mikefrobbins.com/wp-content/uploads/2011/02/e2010install-3.jpg?w=300" alt="" width="300" height="224" /></a>

Click “Next”:
<a href="http://mikefrobbins.com/wp-content/uploads/2011/02/e2010install-4.jpg"><img class="alignnone size-medium wp-image-1978" title="e2010install-4" src="http://mikefrobbins.com/wp-content/uploads/2011/02/e2010install-4.jpg?w=300" alt="" width="300" height="261" /></a>

Accept the license and select “Next”:
<a href="http://mikefrobbins.com/wp-content/uploads/2011/02/e2010install-5.jpg"><img class="alignnone size-medium wp-image-1979" title="e2010install-5" src="http://mikefrobbins.com/wp-content/uploads/2011/02/e2010install-5.jpg?w=300" alt="" width="300" height="261" /></a>

Select the appropriate option. I chose “No” in the example below:
<a href="http://mikefrobbins.com/wp-content/uploads/2011/02/e2010install-6.jpg"><img class="alignnone size-medium wp-image-1980" title="e2010install-6" src="http://mikefrobbins.com/wp-content/uploads/2011/02/e2010install-6.jpg?w=300" alt="" width="300" height="261" /></a>

Since this is an SMB customer, I’m choosing to perform a “Typical Exchange Server Installation”. The installation defaults to the “C” drive. I recommend having the operating system on the “C” drive and the installation of Exchange on a data drive. I chose “D” in the example shown below. If you want to keep the default path which I did, you’ll have to manually build it since the installer doesn’t give you the option to just change the drive letter.
<a href="http://mikefrobbins.com/wp-content/uploads/2011/02/e2010install-7.jpg"><img class="alignnone size-medium wp-image-1981" title="e2010install-7" src="http://mikefrobbins.com/wp-content/uploads/2011/02/e2010install-7.jpg?w=300" alt="" width="300" height="261" /></a>

Select the appropriate option. I chose “No” in the example below. If you do have Outlook 2003 clients in your environment, I recommend upgrading them before installing Exchange 2010 if at all possible.
<a href="http://mikefrobbins.com/wp-content/uploads/2011/02/e2010install-8.jpg"><img class="alignnone size-medium wp-image-1982" title="e2010install-8" src="http://mikefrobbins.com/wp-content/uploads/2011/02/e2010install-8.jpg?w=300" alt="" width="300" height="261" /></a>

If you are performing a typical installation, this will more than likely be an Internet facing server.
<a href="http://mikefrobbins.com/wp-content/uploads/2011/02/e2010install-9.jpg"><img class="alignnone size-medium wp-image-1983" title="e2010install-9" src="http://mikefrobbins.com/wp-content/uploads/2011/02/e2010install-9.jpg?w=300" alt="" width="300" height="261" /></a>

Once again, choose the appropriate option and click next:
<a href="http://mikefrobbins.com/wp-content/uploads/2011/02/e2010install-10.jpg"><img class="alignnone size-medium wp-image-1984" title="e2010install-10" src="http://mikefrobbins.com/wp-content/uploads/2011/02/e2010install-10.jpg?w=300" alt="" width="300" height="261" /></a>

The install will begin performing readiness checks to make sure you’ve installed all of the prerequisites:
<a href="http://mikefrobbins.com/wp-content/uploads/2011/02/e2010install-11.jpg"><img class="alignnone size-medium wp-image-1985" title="e2010install-11" src="http://mikefrobbins.com/wp-content/uploads/2011/02/e2010install-11.jpg?w=300" alt="" width="300" height="261" /></a>

If you receive an error at this point, you probably did not install all of the prerequisites.
<a href="http://mikefrobbins.com/wp-content/uploads/2011/02/e2010install-12.jpg"><img class="alignnone size-medium wp-image-1986" title="e2010install-12" src="http://mikefrobbins.com/wp-content/uploads/2011/02/e2010install-12.jpg?w=300" alt="" width="300" height="261" /></a>

The installation should complete in roughly 15 minutes or less from this point forward.
<a href="http://mikefrobbins.com/wp-content/uploads/2011/02/e2010install-13.jpg"><img class="alignnone size-medium wp-image-1987" title="e2010install-13" src="http://mikefrobbins.com/wp-content/uploads/2011/02/e2010install-13.jpg?w=300" alt="" width="300" height="261" /></a>

µ