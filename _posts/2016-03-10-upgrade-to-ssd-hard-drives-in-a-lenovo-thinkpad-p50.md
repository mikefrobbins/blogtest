---
ID: 13540
post_title: >
  Upgrade to SSD Hard Drives in a Lenovo
  ThinkPad P50
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2016/03/10/upgrade-to-ssd-hard-drives-in-a-lenovo-thinkpad-p50/
published: true
post_date: 2016-03-10 07:30:04
---
I recently purchased a <a href="http://shop.lenovo.com/us/en/laptops/thinkpad/p-series/p50/" target="_blank">Lenovo ThinkPad P50</a>. It's certainly not the sleekest machine on the market, but don't judge a book by its cover. I picked this model because it offers an <a href="http://ark.intel.com/products/89608/Intel-Xeon-Processor-E3-1505M-v5-8M-Cache-2_80-GHz" target="_blank">Intel Xeon CPU</a> and up to 64 GB of RAM in a 15 inch laptop. My previous computer was a Dell Latitude e6530 which is about the same form factor, although the older one is a little thicker and slightly larger and heavier due to the extended battery that sticks out of the back of it. The ThinkPad doesn't have an optical drive but in my scenario it's unnecessary since the ThinkPad offers multiple internal hard drive bays which is what I was using the optical drive bay for on the Dell.

My old Dell Latitude e6530 is shown on the left and my new Lenovo ThinkPad P50 (in the docking station) is shown on the right:

<a href="http://mikefrobbins.com/wp-content/uploads/2016/03/p50-9a.jpg" rel="attachment wp-att-13569"><img class="alignnone size-full wp-image-13569" src="http://mikefrobbins.com/wp-content/uploads/2016/03/p50-9a.jpg" alt="p50-9a" width="800" height="364" /></a>

I started out with the cheaper configuration on the Lenovo site and added the options I wanted. I selected the Xeon CPU (this selection also updates the video card to a 4 GB NVidia Quadro), 64 GB of non-ECC memory, backlit keyboard, and a 3 year depot warranty. I stayed with the default 1920x1080 non-touch display and a single 500 GB hard drive. I also purchased a <a href="http://shop.lenovo.com/us/en/itemdetails/40A50230US/460/6D501EE899104FF9A362D739642CFC27" target="_blank">ThinkPad Workstation Dock</a> (Docking Station) with it. I'm in no way affiliated with Lenovo other than being a customer and I did NOT receive any type of discount or compensation for writing this blog article.

I planned to add a second 2.5 inch SSD hard drive which didn't work out as expected. After numerous searches on the Internet and in the Lenovo forums, I found that I needed a proprietary cable for the second hard drive along with a hard drive caddy. I found the part number for the cable, but based on information in the forums it appeared that the cable wasn't available due to lack of demand. The hard drive caddy didn't even seem to have a part number. It appears that if you're going to go this route of wanting to add a second 2.5 inch drive, it's best to order it with that configuration from the factory if for no other reason than to get all of the necessary propriety parts and pieces that you'll need to swap it out with a SSD.

I decided to ask <a href="http://twitter.com/JeffHicks" target="_blank">Jeff Hicks</a> about his upgrade path since I'd heard that he was planning to perform upgrades on his as well. He pointed me in the direction of M.2 hard drives which was another option in the second hard drive bay. As a matter of fact, two M.2 hard drives can be added in the second bay for a total of three hard drives. I discovered that a tray was needed for each M.2 hard drive and confirmed this with Jeff. The M.2 hard drive trays can be found <a href="http://shop.lenovo.com/us/en/itemdetails/4XB0K59917/460/7EF7D50A5A7047049A355BF42AAF3C5C" target="_blank">here</a>. I recommend going ahead and ordering two of them.

I ordered one of the drives that Jeff recommended which is the Samsung 950 PRO Series 512GB PCIe NVMe M.2 Internal SSD. They can be found on <a href="http://www.amazon.com/Samsung-950-512GB-PCIe-NVMe/dp/B01639694M/ref=sr_1_2?rps=1&amp;ie=UTF8&amp;qid=1456079637&amp;sr=8-2&amp;keywords=m.2+nvme&amp;refinements=p_85%3A2470955011" target="_blank">Amazon.com</a> or at <a href="http://www.newegg.com/Product/Product.aspx?Item=9SIA12K3U60461&amp;cm_re=MZ-V5P512BW-_-20-147-467-_-Product" target="_blank">NewEgg</a>. Looking at the <a href="http://www.crucial.com/usa/en/thinkpad-p50/CT7977069" target="_blank">Crucial</a> system configurator shows that the system also accepts M.2 drives that based on the specs appear to go through the SATA bus which are cheaper than the Samsung one that I ordered which bypasses the SATA bus and plugs directly into a PCI Express interface. The cheaper drives have two notches in them and the more expensive ones only have a single notch. ARS Technica has an article on "<em><a href="http://arstechnica.com/gadgets/2015/02/understanding-m-2-the-interface-that-will-speed-up-your-next-ssd/" target="_blank">Understanding M.2, the interface that will speed up your next SSD</a></em>" that helps clarify the different M.2 interfaces. I've heard of booting issues with the Samsung drives in the Lenovo forums and also not being able to do RAID unless it's ordered with it from the factory, but I can't confirm either of these issues.

One thing that I noticed is the default 500GB 7200PRM drive that ships in the ThinkPad is sluggish when compared to the hybrid boot drive that shipped in my old Dell Latitude. Sluggishness wasn't something that I was willing to accept so I decided to take the 500GB 2.5 inch SSD drive out of the optical drive bay of my old Dell Latitude and replace the boot drive in the ThinkPad with it.

<span style="color: #ff0000;"><em>Disclaimer: All data and information provided on this site is for informational purposes only. Mike F Robbins (mikefrobbins.com) makes no representations as to accuracy, completeness, currentness, suitability, or validity of any information on this site and will not be liable for any errors, omissions, or delays in this information or any losses, injuries, or damages arising from its display or use. All information is provided on an as-is basis.</em></span>

<a href="https://support.lenovo.com/us/en/documents/pd101151" target="_blank">Remove the battery</a> and then hold down the power button to make sure all power is drained out of the system before proceeding. <a href="https://support.lenovo.com/us/en/documents/pd101152" target="_blank">Remove the bottom cover</a>. The screws that are holding the cover on don't come all the way out. You should now be at this point:

<a href="http://mikefrobbins.com/wp-content/uploads/2016/03/p50-1c.jpg" rel="attachment wp-att-13552"><img class="alignnone size-full wp-image-13552" src="http://mikefrobbins.com/wp-content/uploads/2016/03/p50-1c.jpg" alt="p50-1c" width="800" height="500" /></a>

<span style="font-size: 17px; line-height: 1.7em;">If you're replacing the existing SATA drive as I did, </span><a style="font-size: 17px; line-height: 1.7em;" href="https://support.lenovo.com/us/en/documents/pd101153" target="_blank">remove the HDD cable and hard drive</a>:

<a href="http://mikefrobbins.com/wp-content/uploads/2016/03/p50-2a.jpg" rel="attachment wp-att-13554"><img class="alignnone size-full wp-image-13554" src="http://mikefrobbins.com/wp-content/uploads/2016/03/p50-2a.jpg" alt="p50-2a" width="800" height="500" /></a>

The old drive is stamped with 7.2mm on the metal shield so keep this in mind when ordering a replacement. Place the drives next to one another as you're dissembling them so things don't get reassembled incorrectly:

<a href="http://mikefrobbins.com/wp-content/uploads/2016/03/p50-3a.jpg" rel="attachment wp-att-13556"><img class="alignnone size-full wp-image-13556" src="http://mikefrobbins.com/wp-content/uploads/2016/03/p50-3a.jpg" alt="p50-3a" width="800" height="300" /></a>

Reassemble in the reverse order using the 2.5 inch SSD drive:

<a href="http://mikefrobbins.com/wp-content/uploads/2016/03/p50-4a.jpg" rel="attachment wp-att-13558"><img class="alignnone size-full wp-image-13558" src="http://mikefrobbins.com/wp-content/uploads/2016/03/p50-4a.jpg" alt="p50-4a" width="800" height="500" /></a>

Add the M.2 drive. Pay attention to this section because it's not as straight forward as replacing an existing drive. Mount the M.2 drive in the tray prior to installing the tray in the computer. Lift up the plastic cover on the tray and align the drive properly:

<a href="http://mikefrobbins.com/wp-content/uploads/2016/03/p50-5a.jpg" rel="attachment wp-att-13560"><img class="alignnone size-full wp-image-13560" src="http://mikefrobbins.com/wp-content/uploads/2016/03/p50-5a.jpg" alt="p50-5a" width="800" height="764" /></a>

With the chips on the drive facing down, insert the drive making sure the notch is aligned properly:

<a href="http://mikefrobbins.com/wp-content/uploads/2016/03/p50-6a.jpg" rel="attachment wp-att-13562"><img class="alignnone size-full wp-image-13562" src="http://mikefrobbins.com/wp-content/uploads/2016/03/p50-6a.jpg" alt="p50-6a" width="535" height="500" /></a>

The screw for mounting the M.2 drive in the tray is included with the tray. Mount the M.2 card with the included black screw (#1) and remove the silver screen from the laptop (#2) which will be used to mount the tray itself in the computer:

<a href="http://mikefrobbins.com/wp-content/uploads/2016/03/p50-7a.jpg" rel="attachment wp-att-13564"><img class="alignnone size-large wp-image-13564" src="http://mikefrobbins.com/wp-content/uploads/2016/03/p50-7a-741x1024.jpg" alt="p50-7a" width="741" height="1024" /></a>

Be sure if you're only adding one drive that you place it in slot 0. Align the screen holes (#3) and use the silver screws that were removed in the previous step to mount the trays in the computer. This prevents them from becoming unseated:

<a href="http://mikefrobbins.com/wp-content/uploads/2016/03/p50-8b.jpg" rel="attachment wp-att-13597"><img class="alignnone size-full wp-image-13597" src="http://mikefrobbins.com/wp-content/uploads/2016/03/p50-8b.jpg" alt="p50-8b" width="800" height="671" /></a>

Reassemble the cover and battery using the reverse procedure and you should be all set.

If you replaced the boot drive, you'll need to reload the operating system or clone the original boot drive, both of which are outside the scope of this blog article.

µ