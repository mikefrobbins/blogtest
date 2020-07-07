---
ID: 1989
post_title: >
  Spam Filtering for Microsoft Exchange
  Server
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2011/03/03/spam-filtering-for-microsoft-exchange-server/
published: true
post_date: 2011-03-03 07:30:47
---
ORF Enterprise Edition is my spam filter of choice and has been through several generations of Exchange Server versions. The product is licensed per server so regardless of mailboxes or users you only need one license per Exchange Server. The initial year is $239 and each year after that is $99 per year. I haven't seen any other spam filtering product for an Exchange Server that offers a better price to performance ratio. A fully functional 30 day evaluation version is available for download with no registration being required on the <a href="http://www.vamsoft.com/" target="_blank">Vamsoft</a> website.

The installation of ORF is straightforward so no need to show all of the screenshots.
<a href="http://mikefrobbins.com/wp-content/uploads/2011/02/orf1.png"><img class="alignnone size-full wp-image-1990" title="orf1" src="http://mikefrobbins.com/wp-content/uploads/2011/02/orf1.png" alt="" width="408" height="145" /></a>

The default location is fine.
<a href="http://mikefrobbins.com/wp-content/uploads/2011/02/orf2.png"><img class="alignnone size-medium wp-image-1991" title="orf2" src="http://mikefrobbins.com/wp-content/uploads/2011/02/orf2.png?w=300" alt="" width="300" height="229" /></a>

The installation requires that the Exchange Transport service be restarted.
<a href="http://mikefrobbins.com/wp-content/uploads/2011/02/orf3.png"><img class="alignnone size-full wp-image-1992" title="orf3" src="http://mikefrobbins.com/wp-content/uploads/2011/02/orf3.png" alt="" width="411" height="178" /></a>

When the installation finishes, launch the Administration Tool.
<a href="http://mikefrobbins.com/wp-content/uploads/2011/02/orf4.png"><img class="alignnone size-medium wp-image-1993" title="orf4" src="http://mikefrobbins.com/wp-content/uploads/2011/02/orf4.png?w=300" alt="" width="300" height="229" /></a>

Verify the DNS settings. It is preferable to have a minimum of two DNS servers listed.
<a href="http://mikefrobbins.com/wp-content/uploads/2011/02/orf5.png"><img class="alignnone size-full wp-image-1994" title="orf5" src="http://mikefrobbins.com/wp-content/uploads/2011/02/orf5.png" alt="" width="640" height="459" /></a>

Test the DNS settings.
<a href="http://mikefrobbins.com/wp-content/uploads/2011/02/orf6.png"><img class="alignnone size-full wp-image-1995" title="orf6" src="http://mikefrobbins.com/wp-content/uploads/2011/02/orf6.png" alt="" width="392" height="488" /></a>

I prefer to deselect the “Allow sending statistics anonymously to Vamsoft Ltd in email”.
<a href="http://mikefrobbins.com/wp-content/uploads/2011/02/orf7.png"><img class="alignnone size-full wp-image-1996" title="orf7" src="http://mikefrobbins.com/wp-content/uploads/2011/02/orf7.png" alt="" width="640" height="459" /></a>

Enable DNS caching with the following settings.
<a href="http://mikefrobbins.com/wp-content/uploads/2011/02/orf8.png"><img class="alignnone size-full wp-image-1997" title="orf8" src="http://mikefrobbins.com/wp-content/uploads/2011/02/orf8.png" alt="" width="640" height="459" /></a>

Configure the tests as shown in the image below.
<a href="http://mikefrobbins.com/wp-content/uploads/2011/02/orf9.png"><img class="alignnone size-full wp-image-1998" title="orf9" src="http://mikefrobbins.com/wp-content/uploads/2011/02/orf9.png" alt="" width="640" height="613" /></a>

Enabled the “Spamhaus ZEN” DNS blacklist and move it to the top of the list.
<a href="http://mikefrobbins.com/wp-content/uploads/2011/02/orf10.png"><img class="alignnone size-full wp-image-1999" title="orf10" src="http://mikefrobbins.com/wp-content/uploads/2011/02/orf10.png" alt="" width="640" height="521" /></a>

Enable the "Is not a Fully Qualified Domain Name (FQDN)" setting.
<a href="http://mikefrobbins.com/wp-content/uploads/2011/02/orf11.png"><img class="alignnone size-full wp-image-2000" title="orf11" src="http://mikefrobbins.com/wp-content/uploads/2011/02/orf11.png" alt="" width="640" height="397" /></a>

Change the “Tarpit Delay” to 90 seconds.
<a href="http://mikefrobbins.com/wp-content/uploads/2011/02/orf12.png"><img class="alignnone size-full wp-image-2001" title="orf12" src="http://mikefrobbins.com/wp-content/uploads/2011/02/orf12.png" alt="" width="640" height="451" /></a>

Enable the "uribl.com Blacklist" and move it to the top of the list.
<a href="http://mikefrobbins.com/wp-content/uploads/2011/02/orf13.png"><img class="alignnone size-full wp-image-2002" title="orf13" src="http://mikefrobbins.com/wp-content/uploads/2011/02/orf13.png" alt="" width="640" height="451" /></a>

Save the configuration.
<a href="http://mikefrobbins.com/wp-content/uploads/2011/02/orf14.png"><img class="alignnone size-full wp-image-2003" title="orf14" src="http://mikefrobbins.com/wp-content/uploads/2011/02/orf14.png" alt="" width="640" height="452" /></a>

Start the ORF service and you’re now filtering spam.
<a href="http://mikefrobbins.com/wp-content/uploads/2011/02/orf15.png"><img class="alignnone size-full wp-image-2004" title="orf15" src="http://mikefrobbins.com/wp-content/uploads/2011/02/orf15.png" alt="" width="640" height="451" /></a>

Over time you’ll see similar results and best of all, false positives are basically nonexistent.
<a href="http://mikefrobbins.com/wp-content/uploads/2011/02/orf16.png"><img class="alignnone size-full wp-image-2005" title="orf16" src="http://mikefrobbins.com/wp-content/uploads/2011/02/orf16.png" alt="" width="640" height="459" />
</a><a href="http://mikefrobbins.com/wp-content/uploads/2011/02/orf18.png"><img class="alignnone size-full wp-image-2007" title="orf18" src="http://mikefrobbins.com/wp-content/uploads/2011/02/orf18.png" alt="" width="519" height="481" /></a>
<a href="http://mikefrobbins.com/wp-content/uploads/2011/02/orf17.png"><img class="alignnone size-full wp-image-2006" title="orf17" src="http://mikefrobbins.com/wp-content/uploads/2011/02/orf17.png" alt="" width="497" height="549" />
</a>

ORF also includes a very useful reporting tool and log viewer.

I recommend disabling the spam filtering feature of your Antivirus product and the spam filtering features that are built into Exchange Server so if you do have an issue, there is only one product to resolve it in.

µ