---
ID: 9406
post_title: >
  Installing Software with the OneGet
  Module in PowerShell version 5
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2014/04/07/installing-software-with-the-oneget-module-in-powershell-version-5/
published: true
post_date: 2014-04-07 07:30:13
---
As referenced in <a href="http://mikefrobbins.com/2014/04/06/how-to-install-the-preview-version-of-powershell-version-5/" target="_blank">my previous blog article</a>, a preview version of PowerShell version 5 was released last week.

One of the new modules is named "OneGet" which contains a number of new PowerShell cmdlets:
<pre class="lang:ps decode:true">Get-Command -Module OneGet</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-06_15-50-52.png"><img class="alignnone size-full wp-image-9407" src="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-06_15-50-52.png" alt="2014-04-06_15-50-52" width="877" height="203" /></a>

There's only limited help available for these cmdlets since attempting to update the help fails for the two new modules that are part of the PowerShell version 5 preview:
<pre class="lang:ps decode:true">Update-Help</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-06_15-56-05.png"><img class="alignnone size-full wp-image-9408" src="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-06_15-56-05.png" alt="2014-04-06_15-56-05" width="877" height="168" /></a>
<h6><em><span style="color: #ff0000;">Update-Help : Failed to update Help for the module(s) 'NetworkSwitch, OneGet' with UI culture(s) {en-US} : For</span></em>
<em><span style="color: #ff0000;">security reasons DTD is prohibited in this XML document. To enable DTD processing set the DtdProcessing property on</span></em>
<em><span style="color: #ff0000;">XmlReaderSettings to Parse and pass the settings into XmlReader.Create method.</span></em>
<em><span style="color: #ff0000;">At line:1 char:1</span></em>
<em><span style="color: #ff0000;">+ Update-Help</span></em>
<em><span style="color: #ff0000;">+ ~~~~~~~~~~~</span></em>
<em><span style="color: #ff0000;"> + CategoryInfo : InvalidData: (:) [Update-Help], Exception</span></em>
<em><span style="color: #ff0000;"> + FullyQualifiedErrorId : HelpInfoXmlValidationFailure,Microsoft.PowerShell.Commands.UpdateHelpCommand</span></em></h6>
One of the things you can see in the help is that the "Name" parameter of <em>Find-Package</em> accepts more than one value (an array of strings):

<a href="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-06_16-00-48.png"><img class="alignnone size-full wp-image-9438" src="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-06_16-00-48.png" alt="2014-04-06_16-00-48" width="767" height="209" /></a>

You can also see that the "Name" parameter is the only one that accepts pipeline input:

<a href="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-06_16-03-57.png"><img class="alignnone size-full wp-image-9410" src="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-06_16-03-57.png" alt="2014-04-06_16-03-57" width="760" height="181" /></a>

What does all of that mean? It means you should be able to query the software that's installed on another machine and pipe those results to <em>Find-Package</em>.

Let's keep it simple for now though.

The first time you use the <em>Find-Package</em> cmdlet, you'll receive this prompt and it does require an Internet connection otherwise it will fail after a few minutes (without an error message). You'll receive this prompt each time you run this cmdlet until you answer yes, have an Internet connection, and allow it to install the NuGet Package Manager.
<pre class="lang:ps decode:true">Find-Package</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-06_16-29-14.png"><img class="alignnone size-full wp-image-9412" src="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-06_16-29-14.png" alt="2014-04-06_16-29-14" width="877" height="109" /></a>

Pressing Ctrl+C at this prompt will break it until you exit out of PowerShell and re-open it so don't use Ctrl+C to break out of this prompt (don't ask how I know this).

If everything works properly, the necessary software will be installed:
<pre class="lang:ps decode:true">Find-Package</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-06_14-43-35.png"><img class="alignnone size-full wp-image-9416" src="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-06_14-43-35.png" alt="2014-04-06_14-43-35" width="877" height="146" /></a>

I did figure out that if you run some of the other cmdlets first such as <em>Get-Package</em>, it will also install this software if it wasn't previously installed:
<pre class="lang:ps decode:true">Get-Package</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-06_16-44-52.png"><img class="alignnone size-full wp-image-9417" src="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-06_16-44-52.png" alt="2014-04-06_16-44-52" width="877" height="140" /></a>

That only happens one time for whichever cmdlet is run first.

Be prepared to be overwhelmed if you run <em>Find-Package</em> without any parameters:
<pre class="lang:ps decode:true">Find-Package</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-06_16-50-46.png"><img class="alignnone size-full wp-image-9419" src="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-06_16-50-46.png" alt="2014-04-06_16-50-46" width="877" height="643" /></a>

Now for the magic. I'll install Adobe Reader, ImgBurn, and WinRAR:
<pre class="lang:ps decode:true">Find-Package -Name AdobeReader, ImgBurn, WinRAR |
Install-Package</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-06_16-56-08.png"><img class="alignnone size-full wp-image-9421" src="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-06_16-56-08.png" alt="2014-04-06_16-56-08" width="877" height="253" /></a>

Oh no, that command returned the dreaded red text error message :-(. I wanted to make sure I showed you this error message since it's possible you'll run into the same issue as well. This test machine was built yesterday and is a default install so I haven't changed the script execution policy yet which caused the error.

I'll change the script execution policy to RemoteSigned:
<pre class="lang:ps decode:true">Set-ExecutionPolicy RemoteSigned</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-06_16-58-51.png"><img class="alignnone size-full wp-image-9422" src="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-06_16-58-51.png" alt="2014-04-06_16-58-51" width="877" height="132" /></a>

Then give it another try. So far so good, the first package is being downloaded:
<pre class="lang:ps decode:true">Find-Package -Name AdobeReader, ImgBurn, WinRAR |
Install-Package</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-06_17-00-10.png"><img class="alignnone size-full wp-image-9423" src="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-06_17-00-10.png" alt="2014-04-06_17-00-10" width="877" height="183" /></a>

Success! You'll notice that the installations created icons on my desktop:

<a href="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-06_17-02-29.png"><img class="alignnone size-full wp-image-9424" src="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-06_17-02-29.png" alt="2014-04-06_17-02-29" width="953" height="296" /></a>

The -Verbose parameter can be used to receive more detailed information:
<pre class="lang:ps decode:true">Find-Package -Name AdobeReader, ImgBurn, WinRAR |
Install-Package -Verbose</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-06_15-39-05.png"><img class="alignnone size-full wp-image-9425" src="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-06_15-39-05.png" alt="2014-04-06_15-39-05" width="874" height="544" /></a>

The <em>Get-Package</em> cmdlet can be used to see the packages that are installed:
<pre class="lang:ps decode:true">Get-Package</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-06_17-07-40.png"><img class="alignnone size-full wp-image-9426" src="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-06_17-07-40.png" alt="2014-04-06_17-07-40" width="877" height="161" /></a>

Be sure to take a look at <a href="http://mikefrobbins.com/tag/oneget/" target="_blank">my other blog articles about the OneGet module</a> in the preview version of PowerShell version 5 because this blog article is just the tip of the iceberg :-).

µ