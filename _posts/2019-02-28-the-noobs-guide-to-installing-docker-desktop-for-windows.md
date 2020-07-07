---
ID: 17639
post_title: >
  The Noobs Guide to Installing Docker
  Desktop for Windows
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2019/02/28/the-noobs-guide-to-installing-docker-desktop-for-windows/
published: true
post_date: 2019-02-28 07:30:15
---
I recently came across an article on "<a href="https://blog.destruktive.one/running-powershell-core-in-a-container/" target="_blank" rel="noopener noreferrer">Running PowerShell Core in a Container</a>". This article tweaked my interest because it was a simple way to run PowerShell in a clean and isolated environment that could be used for testing without the need to spin up an entire VM. It could also be recreated much easier than an entire VM.

The examples in the previously referenced article use a command (docker.exe) which I didn't have on my machine so the first order of business was to figure out how to run Docker on Windows 10. I headed over to Docker's website (no, not the men's clothing website). They have a docs page that's specific on how to "<a href="https://docs.docker.com/docker-for-windows/install/" target="_blank" rel="noopener noreferrer">Install Docker Desktop for Windows</a>". Definitely read through that page before proceeding because it contains specific information that will prevent you from experiencing problems. There's information for VirtualBox users which I ignored since I don't use it. Windows 10 64bit Pro, Enterprise, or Education is required along with being capable of running Hyper-V, and a minimum of 4Gb of RAM. My personal recommendation is a minimum of 8Gb of RAM. Based on information from that same webpage, there are also other possibilities of running Docker if you machine doesn't meet these requirements.

This blog article demonstrates how to install Docker Desktop for Windows Community Edition (CE) on Windows 10. <a href="https://www.docker.com/products/docker-desktop" target="_blank" rel="noopener noreferrer">Based on Docker's website</a>, there also seems to be a Desktop Enterprise version. <a href="https://hub.docker.com/editions/community/docker-ce-desktop-windows" target="_blank" rel="noopener noreferrer">Download Docker Desktop for Windows</a>. You'll need to sign up for a free account and although you may find URL's to download Docker without creating an account, you'll need to sign into the software once installed so you'd only be avoiding the inevitable.

The only option I changed from the defaults during the install was to use Windows containers instead of Linux ones. This can also be changed after the install. If you want to run the code from the "<a href="https://blog.destruktive.one/running-powershell-core-in-a-container/" target="_blank" rel="noopener noreferrer">Running PowerShell Core in a Container</a>" article that I previously referenced, this needs to be set to "Linux" containers.

<a href="https://mikefrobbins.com/wp-content/uploads/2019/02/docker-install1a.jpg"><img class="alignnone size-full wp-image-17651" src="https://mikefrobbins.com/wp-content/uploads/2019/02/docker-install1a.jpg" alt="" width="706" height="147" /></a>

The install is mostly a clickersize.

<a href="https://mikefrobbins.com/wp-content/uploads/2019/02/docker-install2a.jpg"><img class="alignnone size-full wp-image-17652" src="https://mikefrobbins.com/wp-content/uploads/2019/02/docker-install2a.jpg" alt="" width="706" height="489" /></a>

Here's a word to the wise: When you click "Close and log out" it does just that, it logs you out of Windows so be sure to save any data you may have open in any other applications!

<a href="https://mikefrobbins.com/wp-content/uploads/2019/02/docker-install3a.jpg"><img class="alignnone size-full wp-image-17653" src="https://mikefrobbins.com/wp-content/uploads/2019/02/docker-install3a.jpg" alt="" width="706" height="317" /></a>

I already had the Hyper-V feature enabled on my Windows 10 system so your mileage may vary during the installation process depending on your current system configuration. Once my system restarted, I launched Docker by clicking on its icon.

<a href="https://mikefrobbins.com/wp-content/uploads/2019/02/docker-install4a.jpg"><img class="alignnone size-full wp-image-17654" src="https://mikefrobbins.com/wp-content/uploads/2019/02/docker-install4a.jpg" alt="" width="108" height="108" /></a>

As I previously mentioned, you'll need to login to Docker with the free account that you created during the download process.

<a href="https://mikefrobbins.com/wp-content/uploads/2019/02/docker-install5a.jpg"><img class="alignnone size-full wp-image-17656" src="https://mikefrobbins.com/wp-content/uploads/2019/02/docker-install5a.jpg" alt="" width="356" height="633" /></a>

There is a system tray icon which can be right clicked to bring up the menu shown in the following example. From it, you can switch to Linux or Windows Containers depending on which one it's currently set to.

<a href="https://mikefrobbins.com/wp-content/uploads/2019/02/docker-install6a.jpg"><img class="alignnone size-full wp-image-17657" src="https://mikefrobbins.com/wp-content/uploads/2019/02/docker-install6a.jpg" alt="" width="255" height="319" /></a>

You'll receive a confirmation message when switching container types.

<a href="https://mikefrobbins.com/wp-content/uploads/2019/02/docker-install7a.jpg"><img class="alignnone size-full wp-image-17658" src="https://mikefrobbins.com/wp-content/uploads/2019/02/docker-install7a.jpg" alt="" width="689" height="298" /></a>

At some point, I received the following message. Nothing to be alarmed about, but be sure to save any data in other open applications since as soon as you click "Ok", your system will indeed restart as the message states.

<a href="https://mikefrobbins.com/wp-content/uploads/2019/02/docker-install8a.jpg"><img class="alignnone size-full wp-image-17660" src="https://mikefrobbins.com/wp-content/uploads/2019/02/docker-install8a.jpg" alt="" width="689" height="253" /></a>

At this point, simply open PowerShell as you normally would and you'll be able to run the commands shown in <a href="https://blog.destruktive.one/running-powershell-core-in-a-container/" target="_blank" rel="noopener noreferrer">this article</a> to run PowerShell Core in a container.

µ