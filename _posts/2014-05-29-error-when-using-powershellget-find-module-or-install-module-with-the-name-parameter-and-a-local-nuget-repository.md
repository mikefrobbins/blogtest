---
ID: 9957
post_title: >
  Error when Using PowerShellGet
  Find-Module or Install-Module with the
  Name Parameter and a Local NuGet
  Repository
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2014/05/29/error-when-using-powershellget-find-module-or-install-module-with-the-name-parameter-and-a-local-nuget-repository/
published: true
post_date: 2014-05-29 07:30:08
---
If you're <a href="http://twitter.com/mikefrobbins" target="_blank">following me on Twitter</a> then you probably know that I've been working a lot with the new features in PowerShell version 5, with my focus being on the cmdlets in the OneGet and PowerShellGet modules.

I've been fighting an issue where an error is received when the <em>Name</em> parameter is specified with <em>Find-Module</em> or <em>Install-Module</em> when using a local NuGet Repository:

<a href="http://mikefrobbins.com/wp-content/uploads/2014/05/052814-1.jpg"><img class="alignnone size-full wp-image-9959" src="http://mikefrobbins.com/wp-content/uploads/2014/05/052814-1.jpg" alt="052814-1" width="877" height="288" /></a>

<em><span style="color: #ff0000;">Find-PSGetExtModule : Module 'Pscx' was not found in the Gallery.</span></em> <em><span style="color: #ff0000;">At C:\Windows\system32\WindowsPowerShell\v1.0\Modules\PowerShellGet\PSGet.psm1:72 char:13</span></em> <em><span style="color: #ff0000;">+ Find-PSGetExtModule -Name $Name `</span></em> <em><span style="color: #ff0000;">+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~</span></em> <em><span style="color: #ff0000;"> + CategoryInfo : InvalidOperation: (:) [Write-Error], WriteErrorException</span></em> <em><span style="color: #ff0000;"> + FullyQualifiedErrorId : ModuleNotFound,Find-PSGetExtModule</span></em>

I can also tell you this isn't an issue that's specific to a NuGet repository that's created with ProGet because I started out with a NuGet repository that was created with Visual Studio:

<a href="http://mikefrobbins.com/wp-content/uploads/2014/05/052814-2.jpg"><img class="alignnone size-full wp-image-9960" src="http://mikefrobbins.com/wp-content/uploads/2014/05/052814-2.jpg" alt="052814-2" width="877" height="263" /></a>

The strange thing is this works without issue with the default online repository:

<a href="http://mikefrobbins.com/wp-content/uploads/2014/05/052814-3.jpg"><img class="alignnone size-full wp-image-9962" src="http://mikefrobbins.com/wp-content/uploads/2014/05/052814-3.jpg" alt="052814-3" width="877" height="132" /></a>

To confirm that it wasn't an issue for just me I contacted <a href="http://twitter.com/ToreGroneng" target="_blank">Tore Groneng</a> via twitter since I knew he had recently written <a href="http://asaconsultant.blogspot.no/2014/05/build-your-local-powershell-module.html" target="_blank">a blog article about setting up an internal PowerShellGet repository with ProGet</a>. He tested the issue I was experiencing and confirmed it was a problem for him as well.

As stated in the error message, I headed to line 72, character 13 of the file: C:\Windows\system32\WindowsPowerShell\v1.0\Modules\PowerShellGet\PSGet.psm1

<a href="http://mikefrobbins.com/wp-content/uploads/2014/05/052814-4.jpg"><img class="alignnone size-full wp-image-9964" src="http://mikefrobbins.com/wp-content/uploads/2014/05/052814-4.jpg" alt="052814-4" width="703" height="239" /></a>

That led me to the <em>Find-PSGetExtModule</em> function in the PSGallery module which is located in a sub-directory of the PowerShellGet module:

<a href="http://mikefrobbins.com/wp-content/uploads/2014/05/052814-5.jpg"><img class="alignnone size-full wp-image-9966" src="http://mikefrobbins.com/wp-content/uploads/2014/05/052814-5.jpg" alt="052814-5" width="704" height="323" /></a>

I tested the code down to line 298 and determined the problem exists on line 293:

<a href="http://mikefrobbins.com/wp-content/uploads/2014/05/052814-6.jpg"><img class="alignnone size-full wp-image-9967" src="http://mikefrobbins.com/wp-content/uploads/2014/05/052814-6.jpg" alt="052814-6" width="843" height="234" /></a>

When the code on line 298 is run, the following command is executed in the example where I received the error:

<a href="http://mikefrobbins.com/wp-content/uploads/2014/05/052814-7a.jpg"><img class="alignnone size-full wp-image-9988" src="http://mikefrobbins.com/wp-content/uploads/2014/05/052814-7a.jpg" alt="052814-7a" width="876" height="82" /></a>

I'm really not sure what the "id:"on line 293 does since I couldn't find it in any of the documentation for NuGet.exe, but simply removing it returns the expected results:

<a href="http://mikefrobbins.com/wp-content/uploads/2014/05/052814-8.jpg"><img class="alignnone size-full wp-image-9969" src="http://mikefrobbins.com/wp-content/uploads/2014/05/052814-8.jpg" alt="052814-8" width="877" height="84" /></a>

I then removed "id:" from line 293 of the PSGallery module (this required some changes to the NTFS permissions of the file itself):

<a href="http://mikefrobbins.com/wp-content/uploads/2014/05/052814-9.jpg"><img class="alignnone size-full wp-image-9970" src="http://mikefrobbins.com/wp-content/uploads/2014/05/052814-9.jpg" alt="052814-9" width="834" height="233" /></a>

After re-importing the PowerShellGet module, the <em>Name</em> parameter worked without issue:

<a href="http://mikefrobbins.com/wp-content/uploads/2014/05/052814-10.jpg"><img class="alignnone size-full wp-image-9971" src="http://mikefrobbins.com/wp-content/uploads/2014/05/052814-10.jpg" alt="052814-10" width="877" height="277" /></a>

It also still works with the default online repository as well so there must be something where it parses the "id:" out somehow that is specific to the online repository:

<a href="http://mikefrobbins.com/wp-content/uploads/2014/05/052814-11.jpg"><img class="alignnone size-full wp-image-9973" src="http://mikefrobbins.com/wp-content/uploads/2014/05/052814-11.jpg" alt="052814-11" width="877" height="132" /></a>

<em>Note: PowerShell version 5 is currently in preview and shouldn't be installed in a production environment. Normally the Microsoft supplied modules aren't script modules so they're not modifiable and even though this one is, modification of the native modules is definitely not recommended and could lead to unexpected results to say the least.</em>

There is already a <a href="https://connect.microsoft.com/PowerShell/feedback/details/878779/powershellget-may-preview-find-install-module-does-not-work-against-file-share" target="_blank">Connect Bug</a> logged about an issue that sounds similar to this problem.

By the way, I'm speaking on "<a href="http://www.atltechstravaganza.com/presentation/whats-new-in-powershell-version-5-preview/" target="_blank">What's New in PowerShell Version 5 (Preview)?</a>" at <a style="color: #ff4b33;" href="http://www.atltechstravaganza.com/" target="_blank">TechStravaganza in Atlanta</a> on Friday, June 6th. It's a free technology event, however you do need a ticket if you plan to attend. There are currently a few tickets still available.

<span style="text-decoration: underline;">Update:</span>
I verified with <a href="http://twitter.com/StevenMurawski" target="_blank">Steve Murawski</a> that the previously referenced Connect Bug is indeed the same issue so I recommend voting it up. Steve also provided me with <a href="http://docs.nuget.org/docs/reference/search-syntax" target="_blank">a link to the NuGet documentation for the "id:" parameter</a>.

µ