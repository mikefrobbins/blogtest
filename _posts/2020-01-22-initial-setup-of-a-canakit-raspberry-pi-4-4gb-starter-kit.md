---
ID: 18183
post_title: >
  Initial Setup of a CanaKit Raspberry Pi
  4 4GB Starter Kit
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2020/01/22/initial-setup-of-a-canakit-raspberry-pi-4-4gb-starter-kit/
published: true
post_date: 2020-01-22 07:30:59
---
For Christmas, I received a <a href="https://www.amazon.com/CanaKit-Raspberry-Starter-Clear-Case/dp/B07YLY143F/" target="_blank" rel="noopener noreferrer">CanaKit Raspberry Pi 4 4GB Starter Kit with Clear Case (4GB RAM)</a>.

The pictures in this blog article can be clicked on for a larger more detailed image.

<a href="https://mikefrobbins.com/wp-content/uploads/2020/01/raspberry-pi1a.jpg"><img class="alignnone wp-image-18184 size-medium" src="https://mikefrobbins.com/wp-content/uploads/2020/01/raspberry-pi1a-300x177.jpg" alt="" width="300" height="177" /></a>

The kit contains everything you need minus an HDMI monitor and USB keyboard/mouse.

<a href="https://mikefrobbins.com/wp-content/uploads/2020/01/raspberry-pi2a.jpg"><img class="alignnone wp-image-18185 size-medium" src="https://mikefrobbins.com/wp-content/uploads/2020/01/raspberry-pi2a-300x192.jpg" alt="" width="300" height="192" /></a>

It includes the Raspberry Pi 4b (I received the one with 4GB of RAM, but it's also available with 1GB or 2GB of RAM), heatsinks, a power supply with a removable inline switch, case (I ordered the one with a clear case), case fan, micro HDMI to HDMI cable, micro SD card with NOOBS preinstalled (I ordered the one with a 32GB card), and a USB micro SD card reader in case you need to reload NOOBS on the SD card.

<a href="https://mikefrobbins.com/wp-content/uploads/2020/01/raspberry-pi3a.jpg"><img class="alignnone wp-image-18186 size-medium" src="https://mikefrobbins.com/wp-content/uploads/2020/01/raspberry-pi3a-300x224.jpg" alt="" width="300" height="224" /></a>

The Raspberry Pi 4 is small enough to fit in the palm of your hand which is amazing considering it has gigabit ethernet, wireless, bluetooth, and support for dual monitors.

The first task was to install the three heatsinks. The documentation wasn't clear on which chips to install them on, but based on my research, the heatsinks go on the processor, RAM, and USB controller.

<a href="https://mikefrobbins.com/wp-content/uploads/2020/01/raspberry-pi4a.jpg"><img class="alignnone wp-image-18187 size-medium" src="https://mikefrobbins.com/wp-content/uploads/2020/01/raspberry-pi4a-300x212.jpg" alt="" width="300" height="212" /></a>

Now for the installation of the Raspberry Pi into its case. The case snaps apart into three pieces.

<a href="https://mikefrobbins.com/wp-content/uploads/2020/01/raspberry-pi5a.jpg"><img class="alignnone wp-image-18188 size-medium" src="https://mikefrobbins.com/wp-content/uploads/2020/01/raspberry-pi5a-262x300.jpg" alt="" width="262" height="300" /></a>

Place the Pi onto the bottom which has numerous small holes in it and feet on the bottom of it.

<a href="https://mikefrobbins.com/wp-content/uploads/2020/01/raspberry-pi6a.jpg"><img class="alignnone wp-image-18189 size-medium" src="https://mikefrobbins.com/wp-content/uploads/2020/01/raspberry-pi6a-147x300.jpg" alt="" width="147" height="300" /></a>

Snap the middle portion of the case back together with the bottom piece.

<a href="https://mikefrobbins.com/wp-content/uploads/2020/01/raspberry-pi7a.jpg"><img class="alignnone wp-image-18190 size-medium" src="https://mikefrobbins.com/wp-content/uploads/2020/01/raspberry-pi7a-225x300.jpg" alt="" width="225" height="300" /></a>

I've gone ahead and placed the cover on the case, but will need to remove it again to install the case fan.

<a href="https://mikefrobbins.com/wp-content/uploads/2020/01/raspberry-pi8a.jpg"><img class="alignnone wp-image-18191 size-medium" src="https://mikefrobbins.com/wp-content/uploads/2020/01/raspberry-pi8a-192x300.jpg" alt="" width="192" height="300" /></a>

I researched to determine which direction the fan should go for proper airflow, but based on the results I found it didn't seem to matter much so I installed the fan label down since that looked better.

Initially, I plugged the fan into ground and the 5v header but ended up moving it to the 3.3v header to make it run a little slower and quieter. I tied the fan wires together in a knot.

<a href="https://mikefrobbins.com/wp-content/uploads/2020/01/raspberry-pi9a.jpg"><img class="alignnone wp-image-18192 size-medium" src="https://mikefrobbins.com/wp-content/uploads/2020/01/raspberry-pi9a-243x300.jpg" alt="" width="243" height="300" /></a>

This keeps the wires from being too long and gives it a cleaner look.

<a href="https://mikefrobbins.com/wp-content/uploads/2020/01/raspberry-pi10a.jpg"><img class="alignnone wp-image-18193 size-medium" src="https://mikefrobbins.com/wp-content/uploads/2020/01/raspberry-pi10a-206x300.jpg" alt="" width="206" height="300" /></a>

The SD card plugs in face down.

<a href="https://mikefrobbins.com/wp-content/uploads/2020/01/raspberry-pi11a.jpg"><img class="alignnone wp-image-18194 size-medium" src="https://mikefrobbins.com/wp-content/uploads/2020/01/raspberry-pi11a-206x300.jpg" alt="" width="206" height="300" /></a>

Upon booting the Raspberry Pi, you're prompted with two installation options. I chose the Raspbian Full installation option.

<a href="https://mikefrobbins.com/wp-content/uploads/2020/01/raspberry-pi12a.jpg"><img class="alignnone wp-image-18195 size-medium" src="https://mikefrobbins.com/wp-content/uploads/2020/01/raspberry-pi12a-300x251.jpg" alt="" width="300" height="251" /></a>

If you connect the Pi to the Internet first, you're presented with more installation options.

<a href="https://mikefrobbins.com/wp-content/uploads/2020/01/raspberry-pi13a.jpg"><img class="alignnone wp-image-18196 size-medium" src="https://mikefrobbins.com/wp-content/uploads/2020/01/raspberry-pi13a-300x251.jpg" alt="" width="300" height="251" /></a>

The remainder of the installation options from this point forward are self-explanatory but can be found on the "<a href="https://projects.raspberrypi.org/en/projects/raspberry-pi-setting-up/6" target="_blank" rel="noopener noreferrer">Finishing the Setup</a>" page of the official Raspberry Pi documentation.

µ