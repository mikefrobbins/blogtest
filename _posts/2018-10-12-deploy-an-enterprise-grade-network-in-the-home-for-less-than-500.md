---
ID: 17191
post_title: >
  Deploy an Enterprise Grade Network in
  the Home for Less than $500
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2018/10/12/deploy-an-enterprise-grade-network-in-the-home-for-less-than-500/
published: true
post_date: 2018-10-12 07:30:10
---
Last month I decided to embark on a networking project to rid my home of poor connectivity. I <a href="https://twitter.com/mikefrobbins/status/1042053391294320640" target="_blank" rel="noopener">tweeted</a> out asking about the benefits of Cat 6 cable since I already owned enough Cat 5e to complete the entire project.

<a href="https://twitter.com/mikefrobbins/status/1042053391294320640" target="_blank" rel="noopener"><img class="alignnone size-full wp-image-17192" src="https://mikefrobbins.com/wp-content/uploads/2018/10/home-network1a.jpg" alt="" width="642" height="325" /></a>

The primary benefit of Cat 6 cable for a home environment seems to be future proofing since Cat 5e is perfectly capable of Gigabit. Ultimately, the free Cat 5e cable won out because it left more money in the budget for new networking equipment. If I had to pay someone to install the cable, I would have opted for Cat 6 since the labor to have it redone later would have been more than the cost of the cable itself. The other benefit of Cat 6 is better shielding which may help eliminate interference.

One of my goals with this project was to eliminate the clutter of network equipment from my desk. I had rented a cable modem a while back because I was tired of my ISP blaming connectivity problems on my equipment. It was one of those all in one units with built in WiFi. Some settings for it had to be managed via my ISP's web portal and it left a lot to be desired to say the least. I was port forwarding the necessary ports to a company provided SonicWALL for my IPSec VPN to work. While the connectivity across the VPN seemed to work fine most of the time, it would randomly drop packets. After months of dealing with the problem and testing with another cable modem, I determined that SonicWALL devices don't like to be behind a NAT device. The problem went away when either of the cable modems were in bridged mode. This however defeated the purpose of renting the cable modem since its WiFi can't be used in bridged mode. I returned the rented cable modem and went back to using my old <a href="https://arris.secure.force.com/consumers/ConsumerProductDetail?p=a0ha000000GNcscAAD&amp;c=SURFboard" target="_blank" rel="noopener">ARRIS SURFboard SB6121</a> DOCSIS 3.0 cable modem which is still supported by my ISP.

At this point, I needed a separate network for my family so they wouldn't be able to access the corporate network via the VPN. I carved out one of the ports on the SonicWALL for an isolated network and plugged it into an old 5 port gigabit switch. An old Linksys router was plugged into it to create a separate network within that network for my kids so they would receive different DHCP addresses and I could filter their content with OpenDNS. At this point, I had no wireless for the main family network since I was previously running it on the rented cable modem. I resorted to using a <a href="https://www.hootoo.com/hootoo-tripmate-nano-ht-tm02-wireless-portable-router.html" target="_blank" rel="noopener">HooToo wireless travel router</a> temporarily and started researching wireless access points. I had a hot mess for a network!

I ordered a <a href="https://www.ubnt.com/unifi/unifi-ap-ac-lite/" target="_blank" rel="noopener">Unifi UAP-AC-LITE access point</a>. One of the deciding factors was that it included a PoE injector since I didn't currently own a PoE switch.
<blockquote>Note: Older versions of the Unifi UAP-AC-LITE access point (made prior to 2017) require 24 volt passive PoE which is different than the 802.3af PoE that you'll find on most PoE switches.</blockquote>
The newer version of the access point will have a blue triangle sticker in the top left corner stating that it supports 802.3af.

<a href="https://mikefrobbins.com/wp-content/uploads/2018/10/home-network5a.jpg"><img class="alignnone size-full wp-image-17242" src="https://mikefrobbins.com/wp-content/uploads/2018/10/home-network5a.jpg" alt="" width="855" height="477" /></a>

I learned that <a href="https://www.ubnt.com/download/unifi" target="_blank" rel="noopener">Ubiquiti's Unifi SDN (Software Defined Networking) Controller</a> was required to configure the access point. One thing to note is that if you're logged into your computer as a normal user (non-admin), the installation will prompt you for admin credentials during the install. The shortcut's will be located under the specified admin's account and not the user you're logged in as. I moved the shortcuts to the all users profile as this seems to be an oversight by their developers.

Their SDN controller doesn't have to be running all the time, only during the configuration unless you want to be able to monitor the access point. Once configured, the access point seems to store the configuration locally because it retained the configuration even when the power to the access point was cycled.

About a week after starting to use the old cable modem, I noticed random problems where there would be no access to the Internet even though the cable modem appeared to be connected. This required the cable modem to be powered off and back on to resolve the problem. A little research and I found others had similar problems with cable modems that use an Intel Puma chipset. It's also a 4x4 modem (no, not four wheel drive) which means it has four channels for downstream and four channels for upstream data from the ISP. After researching price, performance, reviews, etc, I decided to go with a <a href="https://motorolanetwork.com/mb7621.html" target="_blank" rel="noopener">Motorola MB7621</a> which uses a Broadcom chipset. It's a 24x8 DOCSIS 3.0 modem. I considered a DOCSIS 3.1 version, but the price was double and based on the current speeds offered by my ISP, there's no need for it (this is another one of those future proof things if you have deep pockets). I didn't count this towards my budget as I could have just as easily gone back to renting a cable modem.

Nothing changed except for the cable modem and it now had 16 channels active for downstream and three for upstream, although I still had the same speeds of approximately 100Mb down and 12Mb up. Over the next week or so, I saw lots of corrected and uncorrected errors in the logs of the new cable modem, but the Internet never stopped working. One of the other things that I like about the new cable modem is that it hands out a public IP from the ISP by default without having to change any settings for bridged mode, although you are able to access the management interface with a 192.168.x.x address. This means you definitely need some sort of router/firewall between it and your computers.

I decided I wanted to try to eliminate the corporate provided SonicWALL from my network as it seemed to make the Internet sluggish under a decent load. Having my personal networks hanging off of it probably didn't help. Since I was extremely happy with the Unifi access point, I decided to research other products in their Unifi lineup. <a href="https://www.troyhunt.com/heres-what-this-ubiquiti-unifi-stuff-is-all-about/" target="_blank" rel="noopener">Troy Hunt's videos about Ubiquiti's Unifi products</a> were a big help as were the ones from <a href="https://www.youtube.com/channel/UCVS6ejD9NLZvjsvhcbiDzjw" target="_blank" rel="noopener">Crosstalk Solutions</a> and <a href="https://www.youtube.com/user/williehowe" target="_blank" rel="noopener">Willie Howe</a>.

I decided to replace the SonicWALL with a <a href="https://www.ubnt.com/unifi-routing/usg/" target="_blank" rel="noopener">Unifi Security Gateway</a> (Model: USG) and to go ahead and purchase a <a href="https://www.ubnt.com/unifi-switching/unifi-switch-8/" target="_blank" rel="noopener">Unifi PoE switch</a> (Model: US‑8‑60W). Most of the information I found on the Internet directs you toward the 8 port 150 watt switch which has the ability to provide 24 volt passive PoE or standard 802.3af PoE on all 8 ports. I chose the 8 port 60 watt version which has 4 PoE ports (802.3af only, no 24 volt passive PoE). As long as you don't need more than four PoE ports and have the newer version of the UAP-AC-LITE-US access point, it saves a lot of money at about half the cost of the 150 watt version. Unfortunately, there's no way to know for sure that you'll receive the new version of the access point since the old and new ones have the exact same part number. This was a blunder on Ubiquiti's part in my opinion as the new one should have a different part number.

I was able to get my hands on a <a href="https://www.ubnt.com/unifi/unifi-ap-ac-pro/" target="_blank" rel="noopener">Unifi UAP-AC-PRO-E-US access point</a> for some testing and to my astonishment, it wasn't any faster even when tested with a channel width of VHT80 on 5Ghz and with devices that have a 4x4 MU-MIMO capable network card. After searching through numerous forums, I came across<a href="https://www.youtube.com/watch?v=HRhZniqyey8" target="_blank" rel="noopener"> this video</a> which I highly recommend watching before wasting your money on anything better than the lite version of the access point. I ended up purchasing a second Unifi UAP-AC-LITE-US access point. One for the family room and one at the end of the hallway for better coverage in the back of the house. For most people, one access point centrally located would provide sufficient coverage, although you'd be connecting at 2.4Ghz in the outlying areas of the house instead of 5Ghz. What's better, one top of the line access point or two good access points? Well, it depends, but from my experience, it's better to have two so they're closer to your clients. The thing that I've found that kills your wireless performance is a device that's too far away from the access point, like a device left in the car overnight that's barely able to connect as it slows the network down for everything else (remember, WiFi is shared media like a hub, not a switch).

I mounted one of the access points near the edge of my dining room that's closest to the beginning of the family room vaulted ceiling.

<a href="https://mikefrobbins.com/wp-content/uploads/2018/10/home-network3a.jpg"><img class="alignnone size-full wp-image-17225" src="https://mikefrobbins.com/wp-content/uploads/2018/10/home-network3a.jpg" alt="" width="859" height="644" /></a>

Speaking of mounting, you'll need to turn the sheetrock into Swiss cheese to mount the access point on the ceiling or wall. There are four screws for the mount and another hole is needed for the cable for a total of five holes that need to be drilled into the sheetrock to mount one of them, although you could probably get by with two for the mount. The second access point is mounted on the ceiling at the end of the hallway.

<a href="https://mikefrobbins.com/wp-content/uploads/2018/10/home-network4a.jpg"><img class="alignnone size-full wp-image-17226" src="https://mikefrobbins.com/wp-content/uploads/2018/10/home-network4a.jpg" alt="" width="859" height="644" /></a>

The blue lights can be disabled, but it kind of acts like a night light. I found <a href="https://www.scivision.co/wifi-channel-ssid-power-best-practices/" target="_blank" rel="noopener">this article</a> very useful when trying to determine the best channel, power, and layout for the access points.

So what's the difference in the Pro and Lite versions of their access points? The number of radios, 3x3 MIMO for the Pro and 2x2 MIMO for the Lite. The pro version has two Ethernet ports instead of one and is rated for outdoors. I read numerous forum posts where people assume the pro has a faster processor and/or more RAM. Nope, they have the same, at least according to <a href="https://www.smallnetbuilder.com/wireless/wireless-reviews/33084-ubiquiti-ac-pro-and-ac-lite-access-points-reviewed" target="_blank" rel="noopener">a teardown by SmallNetBuilder</a>. There's also a <a href="https://www.ubnt.com/unifi/unifi-ap-ac-lr/" target="_blank" rel="noopener">LR</a> (Long Range) version. It's falls between the Lite and Pro version as it supports 3x3 MIMO on 2.4Ghz and 2x2 MIMO on 5Ghz. Its claim to fame is an innovative antenna design. It sounds more like marketing fluff than anything else. Even if the access point can transmit farther, it's two way communication and the client may not be able to reach talking back to the access point because it won't have a special long range antenna design. They also have a <a href="https://unifi-nanohd.ubnt.com/" target="_blank" rel="noopener">nanoHD</a> and <a href="https://unifi-hd.ubnt.com/" target="_blank" rel="noopener">HD</a> access point version. They support MU-MIMO, but it only works on 5Ghz which means if you're not close to the access point, it will be useless and the nanoHD is exactly the same (2x2 MIMO) as the lite version at 2.4Ghz. Considering some of my clients have dated hardware, the number of clients, and my Internet speed, I didn't even consider one of the HD devices.

I also purchased a <a href="https://www.ubnt.com/unifi/unifi-cloud-key/" target="_blank" rel="noopener">Unifi Cloud Key</a> (Model: UC‑CK) which takes the place of the software I mentioned earlier. It's a small device that plugs into a PoE port that the software runs on all the time so you can get statistics on what's happening on your network. If you're performing the initial installation using the cloud key instead of the downloaded software that I previously mentioned, you'll need to figure out what IP address was assigned to the cloud key and browse to it via a web browser. Going to the IP address of the Unifi Security Gateway will tell you to install the software which you don't need if you have a cloud key. There's also a <a href="https://chrome.google.com/webstore/detail/ubiquiti-device-discovery/hmpigflbjeapnknladcfphgkemopofig" target="_blank" rel="noopener">Chrome extension</a> for this, but I didn't use it as I wasn't aware of it until after I'd completed the configuration. New versions (Gen2) of the cloud key were announced after I purchased mine and while I was within the return period for it, the new ones start at more than double the price. I would still choose the cheaper version based on my needs and budget.

I was able to find all of the equipment slightly below MSRP as reflected in the following prices.

UniFi Security Gateway: $109.99
UniFi Switch 8 60W: $105.99
UniFi Cloud Key: $75.99
UniFi AC Lite: $79.99 (x2)
12 Port Patch Panel: $19.99
10 Pack RJ45 Keystone Jacks: $14.99
1 Six Port Keystone Wall Plate: $1.99
Total Cost: $488.92

All of the networking equipment is now mounted on a shelf in the front corner of my laundry room near the ceiling. It's not even noticeable with the door open unless you walk into the room.

<a href="https://mikefrobbins.com/wp-content/uploads/2018/10/home-network2a.jpg"><img class="alignnone size-full wp-image-17212" src="https://mikefrobbins.com/wp-content/uploads/2018/10/home-network2a.jpg" alt="" width="859" height="644" /></a>

I also wired my <a href="https://www.obitalk.com/info/products/obi200" target="_blank" rel="noopener">ObiTalk VoIP device</a> into the home phone wiring (disconnecting it from the phone company's connection of course) so I could use a regular phone in any outlet of the house with Google voice.

After moving the equipment, the modem connected on all 24 downstream channels and three upstream ones. I'm not aware of any changes the ISP made, but I'm starting to believe that maybe there was a problem with the coax cable drop where the modem previously resided. I'm now seeing approximately 120Mb of downstream and 12Mb of upstream from my ISP.

The Unifi equipment allows for more than one SSID on the same access point via separate VLAN's. I configured two completely separate wireless networks with different settings for the kids versus adults network. The kids network is throttled at 50Mb downstream and 6 up (they can have half my data, but not all of it). I was also able to configure their network so it goes offline automatically at 10pm during the week, midnight on weekends, and then comes back online at 5:30am. When it goes offline, their SSID doesn't even show up as a network.

I was also able to create a dedicated VLAN for work and an IPSec VPN to the corporate office so only certain ports on the switch have access to the corporate network. While I was able to make it work in the GUI, it caused errors on the corporate end because it defaulted to all subnets on my end. I ended up having to use a custom JSON configuration file to narrow down the subnets on my end to only the specific one.

The Unifi software is so much better than anything else I've seen. This is a high level overview of the traffic and you can drill down to see specifics about it.

What is the majority of the traffic on my network used for? According to the Unifi software, it's streaming media.

<a href="https://mikefrobbins.com/wp-content/uploads/2018/10/home-network6a.jpg"><img class="alignnone size-full wp-image-17244" src="https://mikefrobbins.com/wp-content/uploads/2018/10/home-network6a.jpg" alt="" width="859" height="280" /></a>

Clicking on streaming media in the previous example gives specifics on that category. The overwhelming majority of it is Netflix.

<a href="https://mikefrobbins.com/wp-content/uploads/2018/10/home-network7a.jpg"><img class="alignnone size-full wp-image-17245" src="https://mikefrobbins.com/wp-content/uploads/2018/10/home-network7a.jpg" alt="" width="533" height="174" /></a>

Who is speeding so much time on Netflix? Whoever is using the iPad Mini.

<a href="https://mikefrobbins.com/wp-content/uploads/2018/10/home-network8a.jpg"><img class="alignnone size-full wp-image-17246" src="https://mikefrobbins.com/wp-content/uploads/2018/10/home-network8a.jpg" alt="" width="533" height="174" /></a>

I've very satisfied with how everything turned out. The Unifi devices seem to deliver an extreme amount of value for the money. The installation of all the equipment and cabling is nice and clean.

None of the manufacturers played any role in this blog article and none were contacted prior to publishing it. All items were purchased by me (none were provided to me).

µ