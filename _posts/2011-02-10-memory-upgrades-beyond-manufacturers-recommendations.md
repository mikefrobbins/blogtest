---
ID: 1804
post_title: 'Memory Upgrades Beyond Manufacturer&#8217;s Recommendations'
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2011/02/10/memory-upgrades-beyond-manufacturers-recommendations/
published: true
post_date: 2011-02-10 07:30:58
---
We've probably all heard to check with your computer system or motherboard manufacturer to find out what the maximum installable amount of memory is for your computer. I'm guessing that's what most of the third party memory companies do also since who in their right mind would go against the manufacturer's recommendations? I suggest also checking with the motherboard chipset manufacturer.

<a href="http://mikefrobbins.com/wp-content/uploads/2011/02/xps410-8gb1.jpg"><img class="alignleft size-thumbnail wp-image-1812" title="xps410-8gb1" src="http://mikefrobbins.com/wp-content/uploads/2011/02/xps410-8gb1.jpg?w=150" alt="" width="150" height="108" /></a>I own a <a href="http://www.dell.com/us/en/dfh/desktops/xpsdt_410/pd.aspx?refid=xpsdt_410&amp;cs=22&amp;s=dfh" target="_blank">Dell XPS 410</a> that's used as a test machine. The operating system is Windows Server 2008 R2 with the Hyper-V role installed to allow for multiple guest virtual machines, but with a manufacturer supported maximum memory limit of 4 GB, it leaves a lot to be desired as a virtualization host.

<a href="http://mikefrobbins.com/wp-content/uploads/2011/02/xps410-8gb2.jpg"><img class="alignleft size-thumbnail wp-image-1813" title="xps410-8gb2" src="http://mikefrobbins.com/wp-content/uploads/2011/02/xps410-8gb2.jpg?w=150" alt="" width="150" height="75" /></a>Searching for memory upgrades on <a href="http://accessories.us.dell.com/sna/category.aspx?c=us&amp;category_id=6436&amp;cs=19&amp;l=en&amp;s=dhs&amp;mfgpid=167776&amp;chassisid=8371" target="_blank">Dell's website</a> finds the information shown in the image to the left. This is the exact same information you'll also find on all of the major third party memory manufacturer's websites.

On Dell's website, the motherboard <a href="http://support.dell.com/support/edocs/systems/xps410/en/SM_EN/specs.htm" target="_blank">specs</a> state it uses an Intel P965 Express chipset and supports 512 MB or 1 GB memory chips for a maximum of 4 GB of RAM:
<a href="http://mikefrobbins.com/wp-content/uploads/2011/02/xps410-8gb3.jpg"><img class="alignnone size-full wp-image-1814" title="xps410-8gb3" src="http://mikefrobbins.com/wp-content/uploads/2011/02/xps410-8gb3.jpg" alt="" width="516" height="377" /></a>

On Intel's website, the <a href="http://www.intel.com/cd/products/services/emea/eng/chipsets/288795.htm" target="_blank">specs</a> for the P965 Express chipset state it supports 8 GB of RAM:
<a href="http://mikefrobbins.com/wp-content/uploads/2011/02/xps410-8gb4.jpg"><img class="alignnone size-full wp-image-1815" title="xps410-8gb4" src="http://mikefrobbins.com/wp-content/uploads/2011/02/xps410-8gb4.jpg" alt="" width="378" height="69" /></a>

The question at this point: Does the chipset support 8 GB of memory and the motherboard not have enough memory slots to take advantage of it since the Dell specs state a maximum of 1 GB memory chips? The motherboard has four memory slots.

Since I use my XPS 410 as a test server running multiple guest VM’s at the same time, this was definitely of interest to me.  I decided to take a chance and purchase two <a href="http://www.corsair.com/vs4gbkit800d2.html" target="_blank">Corsair 4 GB memory kits</a> each of which included two 2 GB matched sticks of 800 MHz DDR2 RAM for a total of 8 GB. Based on the specs from Dell, these memory chips should not have worked.
<a href="http://mikefrobbins.com/wp-content/uploads/2011/02/xps410-8gb5.jpg"><img class="alignnone size-full wp-image-1816" title="xps410-8gb5" src="http://mikefrobbins.com/wp-content/uploads/2011/02/xps410-8gb5.jpg" alt="" width="347" height="190" /></a>

The system completed <a href="http://en.wikipedia.org/wiki/Power-on_self-test" target="_blank">POST</a> (power on self test) with the 2 GB memory chips installed:
<a href="http://mikefrobbins.com/wp-content/uploads/2011/02/xps410-8gb6.jpg"><img class="alignnone size-medium wp-image-1817" title="xps410-8gb6" src="http://mikefrobbins.com/wp-content/uploads/2011/02/xps410-8gb6.jpg?w=300" alt="" width="300" height="150" /></a>

BIOS System Info showing the model and BIOS version:
<a href="http://mikefrobbins.com/wp-content/uploads/2011/02/xps410-8gb7.jpg"><img class="alignnone size-medium wp-image-1818" title="xps410-8gb7" src="http://mikefrobbins.com/wp-content/uploads/2011/02/xps410-8gb7.jpg?w=300" alt="" width="300" height="83" /></a>

BIOS Memory Info showing the system detected the 2 GB memory chips without issue:
<a href="http://mikefrobbins.com/wp-content/uploads/2011/02/xps410-8gb8.jpg"><img class="alignnone size-full wp-image-1819" title="xps410-8gb8" src="http://mikefrobbins.com/wp-content/uploads/2011/02/xps410-8gb8.jpg" alt="" width="640" height="249" /></a>

OS System Information:
<a href="http://mikefrobbins.com/wp-content/uploads/2011/02/xps410-8gb9.jpg"><img class="alignnone size-full wp-image-1820" title="xps410-8gb9" src="http://mikefrobbins.com/wp-content/uploads/2011/02/xps410-8gb9.jpg" alt="" width="456" height="81" /></a>

You'll need an operating system that supports 8 GB of RAM, but other than that, I haven't experienced any issues with using 8 GB of RAM in my Dell XPS 410.

My test server is now much more capable of running multiple guest VM's at the same time while maintaining a remarkable level of performance. I'm taking advantage of the new Hyper-V <a href="http://technet.microsoft.com/en-us/library/ff817651(WS.10).aspx" target="_blank">Dynamic Memory</a> feature in the release candidate of SP1 for Windows Server 2008 R2 since 8 GB of RAM still isn't much memory for a virtualization host server. Here's a screenshot of Hyper-V Manager with the dynamic memory feature in use:<a href="http://mikefrobbins.com/wp-content/uploads/2011/02/xps410-8gb91.jpg"><img class="alignnone size-full wp-image-1882" title="xps410-8gb9" src="http://mikefrobbins.com/wp-content/uploads/2011/02/xps410-8gb91.jpg" alt="" width="640" height="106" /></a>

µ