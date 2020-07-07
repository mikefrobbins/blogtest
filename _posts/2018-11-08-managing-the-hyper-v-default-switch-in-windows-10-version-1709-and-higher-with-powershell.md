---
ID: 17361
post_title: >
  Managing the Hyper-V Default Switch in
  Windows 10 version 1709 and higher with
  PowerShell
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2018/11/08/managing-the-hyper-v-default-switch-in-windows-10-version-1709-and-higher-with-powershell/
published: true
post_date: 2018-11-08 07:30:36
---
Windows 10 version 1709 introduced a default Hyper-V virtual switch which is installed when the Hyper-V role is added. As you can see in the following example, by default on Windows 10, the default virtual switch does not exist because the Hyper-V role hasn't been added.
<pre class="lang:ps decode:true">Get-NetAdapter</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2018/11/hyperv-defaultswitch1a.jpg"><img class="alignnone size-full wp-image-17363" src="https://mikefrobbins.com/wp-content/uploads/2018/11/hyperv-defaultswitch1a.jpg" alt="" width="859" height="145" /></a>

Now that the Hyper-V role has been added, you can see that a new network adapter named "vEthernet (Default Switch)" exists.
<pre class="lang:ps decode:true ">Get-NetAdapter | Format-Table -AutoSize</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2018/11/hyperv-defaultswitch2a.jpg"><img class="alignnone size-full wp-image-17365" src="https://mikefrobbins.com/wp-content/uploads/2018/11/hyperv-defaultswitch2a.jpg" alt="" width="859" height="159" /></a>

While you wouldn't think this would be a problem, I've seen some latency problems on the host operating system once this default switch is added. The problem seems to be related to the interface metric being the same on the physical network adapter and the default switch.
<pre class="lang:ps decode:true ">Get-NetIPInterface</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2018/11/hyperv-defaultswitch3a.jpg"><img class="alignnone size-full wp-image-17366" src="https://mikefrobbins.com/wp-content/uploads/2018/11/hyperv-defaultswitch3a.jpg" alt="" width="859" height="214" /></a>

While you would think the default route would take precedence, something just doesn't seem right. Having the metric set exactly the same by default doesn't seem like a good idea.
<pre class="lang:ps decode:true ">Get-NetRoute | Format-Table -AutoSize</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2018/11/hyperv-defaultswitch4b.jpg"><img class="alignnone size-full wp-image-17368" src="https://mikefrobbins.com/wp-content/uploads/2018/11/hyperv-defaultswitch4b.jpg" alt="" width="859" height="467" /></a>

Maybe you're like me and think that disabling the default switch would be the way to resolve this problem? Bad idea. Trust me, learn from the mistakes of others.
<pre class="lang:ps decode:true ">Disable-NetAdapter -Name 'vEthernet (Default Switch)' -PassThru -Confirm:$false</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2018/11/hyperv-defaultswitch5a.jpg"><img class="alignnone size-full wp-image-17370" src="https://mikefrobbins.com/wp-content/uploads/2018/11/hyperv-defaultswitch5a.jpg" alt="" width="859" height="145" /></a>

Everything seems to work fine after disabling it, but wait.
<pre class="lang:ps decode:true ">Get-NetAdapter</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2018/11/hyperv-defaultswitch6a.jpg"><img class="alignnone size-full wp-image-17371" src="https://mikefrobbins.com/wp-content/uploads/2018/11/hyperv-defaultswitch6a.jpg" alt="" width="859" height="158" /></a>

Guess what happens after you reboot? The disabled network adapter goes into a "Not Present" state and a new default switch is created which is enabled.
<pre class="lang:ps decode:true">Get-NetAdapter</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2018/11/hyperv-defaultswitch7a.jpg"><img class="alignnone size-full wp-image-17372" src="https://mikefrobbins.com/wp-content/uploads/2018/11/hyperv-defaultswitch7a.jpg" alt="" width="859" height="174" /></a>

I received a response from one of the program managers of the Hyper-V team about this issue <a href="https://twitter.com/mikefrobbins/status/928658643075158019" target="_blank" rel="noopener">on Twitter last year</a> and provided them with the information they requested.

Now you not only have a latency problem, but you also have the problem of getting rid of a network adapter that's not present. Unfortunately, there doesn't seem to be a PowerShell command for removing a network adapter. Maybe like me, you decide to resort to some arcane DOS command to nuke all of your network settings.
<pre class="lang:ps decode:true ">netcfg -d</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2018/11/hyperv-defaultswitch8b.jpg"><img class="alignnone size-full wp-image-17384" src="https://mikefrobbins.com/wp-content/uploads/2018/11/hyperv-defaultswitch8b.jpg" alt="" width="859" height="328" /></a>

That would also be a bad idea as you'll end up with another default switch and this time, one of them is in limbo.
<pre class="lang:ps decode:true ">Get-NetAdapter</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2018/11/hyperv-defaultswitch9a.jpg"><img class="alignnone size-full wp-image-17375" src="https://mikefrobbins.com/wp-content/uploads/2018/11/hyperv-defaultswitch9a.jpg" alt="" width="859" height="185" /></a>

I had to resort to using the GUI to remove these network adapters.

<a href="https://mikefrobbins.com/wp-content/uploads/2018/11/hyperv-defaultswitch10a.jpg"><img class="alignnone size-full wp-image-17376" src="https://mikefrobbins.com/wp-content/uploads/2018/11/hyperv-defaultswitch10a.jpg" alt="" width="858" height="538" /></a>

After removing them and rebooting, I was back to square one.
<pre class="lang:ps decode:true ">Get-NetAdapter</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2018/11/hyperv-defaultswitch11a.jpg"><img class="alignnone size-full wp-image-17377" src="https://mikefrobbins.com/wp-content/uploads/2018/11/hyperv-defaultswitch11a.jpg" alt="" width="859" height="160" /></a>

The solution seems to be to change the metric on the default switch so the host OS prefers the physical adapter.
<pre class="lang:ps decode:true ">Get-NetIPInterface -InterfaceAlias 'vEthernet (Default Switch)'
Get-NetIPInterface -InterfaceAlias 'vEthernet (Default Switch)' | Set-NetIPInterface -InterfaceMetric 5000 -PassThru</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2018/11/hyperv-defaultswitch13a.jpg"><img class="alignnone size-full wp-image-17378" src="https://mikefrobbins.com/wp-content/uploads/2018/11/hyperv-defaultswitch13a.jpg" alt="" width="859" height="284" /></a>

This seems to be a hot mess as you'd think Microsoft would prevent disabling the default switch if it causes problems, otherwise you can end up with numerous copies of it and that can't be good.
<pre class="lang:ps decode:true ">Get-NetAdapter</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2018/11/hyperv-defaultswitch14a.jpg"><img class="alignnone size-full wp-image-17379" src="https://mikefrobbins.com/wp-content/uploads/2018/11/hyperv-defaultswitch14a.jpg" alt="" width="859" height="214" /></a>

I built a Windows 10 version 1809 VM and enabled Hyper-V on it. The metric for its default switch was already set to 5000, so this problem may be resolved with newer fresh installations of Windows 10. It is not however resolved for Windows 10 installations that are updated from previous versions to version 1809.

µ