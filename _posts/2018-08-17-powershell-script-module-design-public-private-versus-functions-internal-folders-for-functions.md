---
ID: 16889
post_title: 'PowerShell Script Module Design: Public/Private versus Functions/Internal folders for Functions'
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2018/08/17/powershell-script-module-design-public-private-versus-functions-internal-folders-for-functions/
published: true
post_date: 2018-08-17 07:30:01
---
There's been a lot of debate about script module design as of lately and instead of tweeting something out asking for responses, I thought I would post it here via a blog article.

Back when I first started creating PowerShell script modules, I placed all of my functions in the PSM1 file and later started placing each function in a separate PS1 file that was dot-sourced from the PSM1 file.
<pre class="lang:ps decode:true " title="module.psm1">#Dot source all functions in all ps1 files located in the module folder
Get-ChildItem -Path $PSScriptRoot\*.ps1 -Exclude *.tests.ps1, *profile.ps1 |
ForEach-Object {
    . $_.FullName
}</pre>
I would simply place the PS1 files in the root folder of the script module. Later, when I started writing functions that I didn't want to make public, I created a folder named <em>"Private"</em> and placed the private functions in that folder.

I've recently updated <a href="https://github.com/mikefrobbins/Plaster" target="_blank" rel="noopener">my Plaster template</a> for creating script modules and decided to place the PS1 files for the public functions in a sub-folder instead of in the module's root folder. The dilemma is whether those should be called <em>"Public"</em> and <em>"Private"</em> or <em>"Functions"</em> and <em>"Internal"</em>. I see a lot of both options from others in the PowerShell community.

At this point, I'm leaning towards using <em>"Functions"</em> and <em>"Internal"</em> for the folder names because to me it's more declarative for someone who may not understand what's going on and when you start adding things like <em>"Classes"</em> into their own sub-folder, it only seems to make more sense.

This blog article isn't some rant about whether or not you should place your functions into separate PS1 files or in the PSM1 file. Moving forward, I plan to have functions in separate files for development which is what you'll find in <a href="https://github.com/mikefrobbins?tab=repositories" target="_blank" rel="noopener">my GitHub repositories</a>. I plan to use a build process to merge all of the functions from the separate PS1 files back into the PSM1 file for production and that's what you'll find me publishing on <a href="https://www.powershellgallery.com/" target="_blank" rel="noopener">the PowerShell Gallery</a>. This module creation process will give me the best of both worlds. Separate functions while in development to avoid things like merge conflicts and if someone wants just one of my functions and not the entire module, they can simply download it from GitHub. The module will be packaged up for use in a production environment and if you install it from the PowerShell Gallery, that's what you'll get. This will eliminate things like slowness when the module is loaded due to the functions being in separate PS1 files. It will also be a lot simpler to sign fewer files before I publish them to the PowerShell gallery.

Did you know that one of <a href="https://docs.microsoft.com/en-us/powershell/gallery/concepts/publishing-guidelines" target="_blank" rel="noopener">the best practices for publishing your modules to the PowerShell Gallery</a> is to sign them?

I've created <a href="https://twitter.com/mikefrobbins/lists/psscriptmoduledesign" target="_blank" rel="noopener">a list on Twitter</a> of the community members that I've seen building and promoting PowerShell script modules using similar designs. If I've missed someone, please post a link to their script module design content as a reply to this blog article.

I'd love to hear your thoughts or suggestions on the public/private versus functions/internal naming scheme debate. Please post them as a reply to this blog article.

<hr />

Update based on <a href="https://twitter.com/mikefrobbins/status/1030461063748354048" target="_blank" rel="noopener">Twitter responses</a>.

What I'm hearing so far based on the responses on Twitter is Public/Private seems to be more widely accepted by the community. I'll probably end up using those since that's the whole point of this blog article (standardization).

µ