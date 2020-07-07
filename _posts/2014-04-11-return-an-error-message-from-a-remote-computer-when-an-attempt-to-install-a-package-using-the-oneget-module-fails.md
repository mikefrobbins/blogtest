---
ID: 9582
post_title: >
  Return an Error Message from a Remote
  Computer when an Attempt to Install a
  Package using the OneGet Module Fails
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2014/04/11/return-an-error-message-from-a-remote-computer-when-an-attempt-to-install-a-package-using-the-oneget-module-fails/
published: true
post_date: 2014-04-11 07:30:44
---
This blog article will walk you through the issue of the OneGet module not returning an error message when an attempt to install a package fails on a remote computer via the <em>Install-Package</em> cmdlet and PowerShell remoting.

Attempting to install a package such as WireShark on a remote computer gives you a quick flash across the screen that's too quick to read and then it gives your prompt back with no errors and no feedback about whether or not the package was installed:
<pre class="lang:ps decode:true">Invoke-Command -ComputerName pc04 {Install-Package -Name wireshark}</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-10_19-30-15.png"><img class="alignnone size-full wp-image-9583" src="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-10_19-30-15.png" alt="2014-04-10_19-30-15" width="877" height="64" /></a>

Checking the remote machine, shows that the WireShark package was not installed:
<pre class="lang:ps decode:true">Invoke-Command -ComputerName pc04 {Get-Package}</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-10_19-31-03.png"><img class="alignnone size-full wp-image-9584" src="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-10_19-31-03.png" alt="2014-04-10_19-31-03" width="877" height="169" /></a>

Trying to install the WireShark package locally gives you a prompt about the chocolately package source being untrusted which I blogged about earlier in the week. I'll now attempt to install it using the <em>-Force</em> parameter which allows the install to complete for several other packages when using the same method to install them on a remote computer:
<pre class="lang:ps decode:true">Invoke-Command -ComputerName pc05 {Install-Package -Name wireshark -Force}</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-10_19-53-18.png"><img class="alignnone size-full wp-image-9585" src="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-10_19-53-18.png" alt="2014-04-10_19-53-18" width="877" height="138" /></a>

Initially, it seems to be working properly by specifying the <em>-Force</em> parameter as shown in the previous example, but then you end up with the following results:
<pre class="lang:ps decode:true">Invoke-Command -ComputerName pc05 {Install-Package -Name wireshark -Force}</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-10_19-53-52.png"><img class="alignnone size-full wp-image-9586" src="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-10_19-53-52.png" alt="2014-04-10_19-53-52" width="877" height="150" /></a>

These are actually dependencies and had this command to install the WireShark package been run interactively on the local computer, you would have seen that in the progress:

<a href="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-10_19-36-05.png"><img class="alignnone size-full wp-image-9587" src="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-10_19-36-05.png" alt="2014-04-10_19-36-05" width="838" height="94" /></a>

I decided to add some error handling to see what happened and that's where the strange behavior part of this begins (you've just entered the twilight zone):
<pre class="lang:ps decode:true">Invoke-Command -ComputerName pc06 {
    try {
        Install-Package -Name wireshark -ErrorAction Stop -ErrorVariable Problem
    }
    catch {
        Write-Error -Message "An error has occurred. Error details: $Problem"
    }
}</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-10_20-06-09.png"><img class="alignnone size-full wp-image-9590" src="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-10_20-06-09.png" alt="2014-04-10_20-06-09" width="877" height="436" /></a>

<em><span style="color: #ff0000;">An error has occurred. Error details: System.Management.Automation.ActionPreferenceStopException: The running command</span></em>
<em><span style="color: #ff0000;">stopped because the preference variable "ErrorActionPreference" or common parameter is set to Stop: Process Execution Failed:'AutoHotkey' -- The system cannot find the file specified at System.Management.Automation.ExceptionHandlingOps.CheckActionPreference(FunctionContext funcContext, Exception exception) at System.Management.Automation.Interpreter.ActionCallInstruction`2.Run(InterpretedFrame frame) at System.Management.Automation.Interpreter.EnterTryCatchFinallyInstruction.Run(InterpretedFrame frame) at System.Management.Automation.Interpreter.EnterTryCatchFinallyInstruction.Run(InterpretedFrame frame)</span></em>
<em><span style="color: #ff0000;">+ CategoryInfo : NotSpecified: (:) [Write-Error], WriteErrorException</span></em>
<em><span style="color: #ff0000;">+ FullyQualifiedErrorId : Microsoft.PowerShell.Commands.WriteErrorException</span></em>
<em><span style="color: #ff0000;">+ PSComputerName : pc06</span></em>

The strange part isn't the error message itself, but that I was able to interact with the remote session just like it was run locally and I was able to see the progress as if it were run locally. I actually thought maybe it did run locally until I verified the prerequisites that were installed, were indeed installed on the remote computer and not the local one:
<pre class="lang:ps decode:true">Get-Package
Invoke-Command -ComputerName pc06 {Get-Package}</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-10_20-13-28.png"><img class="alignnone size-full wp-image-9592" src="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-10_20-13-28.png" alt="2014-04-10_20-13-28" width="877" height="310" /></a>

I ran the same command against a different computer and received a different error:

<a href="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-10_19-36-43.png"><img class="alignnone size-full wp-image-9593" src="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-10_19-36-43.png" alt="2014-04-10_19-36-43" width="829" height="148" /></a>

Then I thought about adding the <em>-Force</em> parameter since that worked with other packages:
<pre class="lang:ps decode:true">Invoke-Command -ComputerName pc05 {
    try {
        Install-Package -Name wireshark -Force -ErrorAction Stop -ErrorVariable Problem
    }
    catch {
        Write-Error -Message "An error has occurred. Error details: $Problem"
    }
}</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-10_20-24-25.png"><img class="alignnone size-full wp-image-9596" src="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-10_20-24-25.png" alt="2014-04-10_20-24-25" width="877" height="301" /></a>

What I learned is, at least for now, if you want to receive errors back from a failure when attempting to install packages on remote computers using the OneGet module and PowerShell remoting is to add error handling and generate a terminating error if an error occurs (Try-Catch only handles terminating errors).

Attempting to install that package on the local computer fails as well with the same type of problem which appears to be an issue with a dependency either being unavailable or being unable to be installed on this particular operating system (Windows 8.1).
<pre class="lang:ps decode:true">Install-Package -Name wireshark -Force</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-10_20-34-10.png"><img class="alignnone size-full wp-image-9601" src="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-10_20-34-10.png" alt="2014-04-10_20-34-10" width="877" height="241" /></a>

µ