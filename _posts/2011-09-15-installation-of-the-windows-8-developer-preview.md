---
ID: 2875
post_title: >
  Installation of the Windows 8 Developer
  Preview
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2011/09/15/installation-of-the-windows-8-developer-preview/
published: true
post_date: 2011-09-15 07:30:58
---
The Windows 8 Developer Preview was publicly released this past Tuesday evening via <a href="http://msdn.microsoft.com/en-us/windows/home/" target="_blank">the new Windows Dev Center</a>. I actually thought I was going to miss out on the opportunity to try out this preview version since I’m not currently a MSDN subscriber. I was happy to learn that it was made available for anyone to download.

Since this is a preview version, I decided to load it as a virtual machine on a Hyper-V server. This kept me from tying up any of my machines that I need to work properly on a consistent basis and that's something that everyone needs to keep in mind, this is not a finished product that is ready or meant for a production environment.

I also attempted to install this preview as a VM under Windows Virtual PC on a computer running Windows 7. You can't run a 64bit OS as a VM under Windows Virtual PC and if you try to install the 32bit version of the preview, you'll end up the error: "Your PC ran into a problem that it couldn't handle, and now it needs to restart. You can search for the error online: HAL INITIALIZATION FAILED".
<a href="http://mikefrobbins.com/wp-content/uploads/2011/09/win8pre-13.png"><img class="alignnone size-full wp-image-2896" title="win8pre-13" src="http://mikefrobbins.com/wp-content/uploads/2011/09/win8pre-13.png" alt="" width="568" height="330" /></a>

I was able to install this preview OS as a VM running under VirtualBox on a computer running Windows 7 without issue by using the settings I found on <a href="http://www.sysprobs.com/guide-install-windows-8-virtualbox" target="_blank">sysprobs.com</a>.

Here's what the initial boot screen looks like when installing this operating system:
<a href="http://mikefrobbins.com/wp-content/uploads/2011/09/win8pre-1.png"><img class="alignnone size-full wp-image-2876" title="win8pre-1" src="http://mikefrobbins.com/wp-content/uploads/2011/09/win8pre-1.png" alt="" width="515" height="441" /></a>

From this point forward, the installation process looks similar to Windows 7 and 2008 R2. I’m not going to post the screen shots of the installation process unless they are new to Windows 8. During the installation, this particular screen was flashing in such a way that I thought something was wrong. I left it alone and the installation continued without issue. This issue only occurred during the installation of the VM on Hyper-V.
<a href="http://mikefrobbins.com/wp-content/uploads/2011/09/win8pre-2.png"><img class="alignnone size-full wp-image-2877" title="win8pre-2" src="http://mikefrobbins.com/wp-content/uploads/2011/09/win8pre-2.png" alt="" width="640" height="482" /></a>

At the end of the installation, you'll end up on this screen:
<a href="http://mikefrobbins.com/wp-content/uploads/2011/09/win8pre-3.png"><img class="alignnone size-full wp-image-2878" title="win8pre-3" src="http://mikefrobbins.com/wp-content/uploads/2011/09/win8pre-3.png" alt="" width="640" height="105" /></a>

You'll be asked to give the computer a name:
<a href="http://mikefrobbins.com/wp-content/uploads/2011/09/win8pre-4.png"><img class="alignnone size-full wp-image-2879" title="win8pre-4" src="http://mikefrobbins.com/wp-content/uploads/2011/09/win8pre-4.png" alt="" width="584" height="229" /></a>

I chose to "Use Express Settings" on this page:
<a href="http://mikefrobbins.com/wp-content/uploads/2011/09/win8pre-5.png"><img class="alignnone size-full wp-image-2880" title="win8pre-5" src="http://mikefrobbins.com/wp-content/uploads/2011/09/win8pre-5.png" alt="" width="640" height="499" /></a>

The log on portion is great since you can use a Windows Live ID. That's one less password that I don't have to worry about remembering:
<a href="http://mikefrobbins.com/wp-content/uploads/2011/09/win8pre-6.png"><img class="alignnone size-full wp-image-2881" title="win8pre-6" src="http://mikefrobbins.com/wp-content/uploads/2011/09/win8pre-6.png" alt="" width="640" height="498" /></a>

You'll be prompted to enter your Windows Live ID and password:
<a href="http://mikefrobbins.com/wp-content/uploads/2011/09/win8pre-7.png"><img class="alignnone size-full wp-image-2882" title="win8pre-7" src="http://mikefrobbins.com/wp-content/uploads/2011/09/win8pre-7.png" alt="" width="551" height="246" /></a>

I chose to "Use an alternate email instead" on the password recovery screen:
<a href="http://mikefrobbins.com/wp-content/uploads/2011/09/win8pre-8.png"><img class="alignnone size-full wp-image-2883" title="win8pre-8" src="http://mikefrobbins.com/wp-content/uploads/2011/09/win8pre-8.png" alt="" width="640" height="297" /></a>

Enter the alternate email address if you chose that option:
<a href="http://mikefrobbins.com/wp-content/uploads/2011/09/win8pre-9.png"><img class="alignnone size-full wp-image-2884" title="win8pre-9" src="http://mikefrobbins.com/wp-content/uploads/2011/09/win8pre-9.png" alt="" width="640" height="230" /></a>

Your account is being created at this point:
<a href="http://mikefrobbins.com/wp-content/uploads/2011/09/win8pre-10.png"><img class="alignnone size-full wp-image-2885" title="win8pre-10" src="http://mikefrobbins.com/wp-content/uploads/2011/09/win8pre-10.png" alt="" width="640" height="498" /></a>

This is where you are actually being logged in:
<a href="http://mikefrobbins.com/wp-content/uploads/2011/09/win8pre-11.png"><img class="alignnone size-full wp-image-2886" title="win8pre-11" src="http://mikefrobbins.com/wp-content/uploads/2011/09/win8pre-11.png" alt="" width="394" height="218" /></a>

You'll end up on this snazzy new start screen:
<a href="http://mikefrobbins.com/wp-content/uploads/2011/09/win8pre-12.png"><img class="alignnone size-full wp-image-2887" title="win8pre-12" src="http://mikefrobbins.com/wp-content/uploads/2011/09/win8pre-12.png" alt="" width="640" height="481" /></a>

I'll be posting more blog articles about the Windows 8 preview during the next few weeks.

µ