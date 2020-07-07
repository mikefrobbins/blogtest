---
ID: 16990
post_title: 'PowerShell Script Module Design: Plaster Template for Creating Modules'
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2018/08/30/powershell-script-module-design-plaster-template-for-creating-modules/
published: true
post_date: 2018-08-30 07:30:56
---
I recently began updating my PowerShell script module build process. Updating my Plaster template was one of the first things I needed to do. If you haven't already read my blog article about "<a href="https://mikefrobbins.com/2018/02/15/using-plaster-to-create-a-powershell-script-module-template/" target="_blank" rel="noopener">Using Plaster to create a PowerShell Script Module template</a>", I'd recommend beginning there as this blog article assumes you already have a basic understanding of how to use Plaster.

All of the information from my previous Plaster template is still there with the exception of the required PowerShell version as I plan to obtain that information and update it a different way.

The first addition to my template that you'll notice is the option to create both public and private folders within the module folder. By default, both of them are created.
<pre class="lang:ps decode:true ">&lt;parameter name="Folders" type="multichoice" default="0,1" prompt="Select folders to create"&gt;
    &lt;choice label="&amp;amp;public" value="public" help="Adds a public folder to module root"/&gt;
    &lt;choice label="&amp;amp;private" value="private" help="Adds a private folder to module root"/&gt;
&lt;/parameter&gt;</pre>
<blockquote>The code shown in the previous example has a bug in it. See the update at the bottom of this blog article for the corrected code.</blockquote>
When I create a module (for development), I want to create a root folder as a Git repo and then the module's root folder as a subfolder within that folder. I've added this ability to my Plaster template along with the option of not having the top level Git folder.
<pre class="lang:ps decode:true ">&lt;parameter name="Git" type="choice" default="0" prompt="Create Git top level folder?"&gt;
     &lt;choice label="&amp;amp;Yes" value="Yes" help="Create a top level folder for Git with module folder nested in it."/&gt;
     &lt;choice label="&amp;amp;No" value="No" help="No Git top level folder. Module folder will be the root."/&gt;
&lt;/parameter&gt;</pre>
If the option to create the Git folder is selected (which is the default), then a conditional parameter asks for the name for the Git repo.
<pre class="lang:ps decode:true ">&lt;parameter condition="$PLASTER_PARAM_Git -eq 'Yes'" name="GitRepoName" type="text" prompt="Git repo name for this module? " default="${PLASTER_PARAM_Name}"/&gt;</pre>
I ran into a problem with Plaster where paths for conditional parameters were validated even when the condition evaluated to false. As of this writing, Plaster version 1.1.3 is the newest version in the PowerShell Gallery and has this problem. <a href="https://github.com/PowerShell/Plaster" target="_blank" rel="noopener">Version 1.1.4 is available from GitHub</a> and this problem is resolved in it. See <a href="https://github.com/PowerShell/Plaster/issues/342" target="_blank" rel="noopener">this issue on GitHub</a> for more details about the problem.

The final parameter is a multi-selection one and it's also conditional based on whether or not creating a Git folder is selected.
<pre class="lang:ps decode:true ">&lt;parameter condition="$PLASTER_PARAM_Git -eq 'Yes'" name="Options" type="multichoice" prompt="Select one or more of the following options:" default="0,1,2,3" store="text"&gt;
     &lt;choice label="Add &amp;amp;MIT License" help="Adds MIT LICENSE file" value="License"/&gt;
     &lt;choice label="Add &amp;amp;Readme.md file" help="Adds Readme.md file" value="Readme"/&gt;
     &lt;choice label="Add &amp;amp;Git .gitignore file" help="Adds a .gitignore file." value="GitIgnore"/&gt;
     &lt;choice label="Add G&amp;amp;it .gitattributes file" help="Adds a .gitattributes file." value="GitAttributes"/&gt;
     &lt;choice label="&amp;amp;None" help="No options specified." value="None"/&gt;
&lt;/parameter&gt;</pre>
The default selection adds an MIT license, a Readme.md file, and both .gitignore and .gitattributes files.

While those parameters are nice, they don't do anything except ask questions. They need to be wired up to accomplish the desired tasks. The content section is where that happens.
<pre class="lang:ps decode:true">&lt;content&gt;
    &lt;message&gt;Creating folder structure&lt;/message&gt;
    &lt;file condition="$PLASTER_PARAM_Folders -contains 'public' -and $PLASTER_PARAM_Git -eq 'Yes'" source="" destination="${PLASTER_PARAM_GitRepoName}\${PLASTER_PARAM_Name}\public"/&gt;
    &lt;file condition="$PLASTER_PARAM_Folders -contains 'private' -and $PLASTER_PARAM_Git -eq 'Yes'" source="" destination="${PLASTER_PARAM_GitRepoName}\${PLASTER_PARAM_Name}\private"/&gt;
    &lt;file condition="$PLASTER_PARAM_Folders -contains 'public' -and $PLASTER_PARAM_Git -eq 'No'" source="" destination="${PLASTER_PARAM_Name}\public"/&gt;
    &lt;file condition="$PLASTER_PARAM_Folders -contains 'private' -and $PLASTER_PARAM_Git -eq 'No'" source="" destination="${PLASTER_PARAM_Name}\private"/&gt;
    &lt;message&gt;Deploying common files&lt;/message&gt;
    &lt;file condition="$PLASTER_PARAM_Git -eq 'Yes'" source="module.psm1" destination="${PLASTER_PARAM_GitRepoName}\${PLASTER_PARAM_Name}\${PLASTER_PARAM_Name}.psm1"/&gt;
    &lt;file condition="$PLASTER_PARAM_Git -eq 'No'" source="module.psm1" destination="${PLASTER_PARAM_Name}\${PLASTER_PARAM_Name}.psm1"/&gt;
    &lt;message&gt;Creating Module Manifest&lt;/message&gt;
    &lt;newModuleManifest condition="$PLASTER_PARAM_Git -eq 'Yes'" destination="${PLASTER_PARAM_GitRepoName}\${PLASTER_PARAM_Name}\${PLASTER_PARAM_Name}.psd1" moduleVersion="$PLASTER_PARAM_Version" rootModule="${PLASTER_PARAM_Name}.psm1" author="$PLASTER_PARAM_Author" companyName="$PLASTER_PARAM_CompanyName" description="$PLASTER_PARAM_Description" encoding="UTF8-NoBOM"/&gt;
    &lt;newModuleManifest condition="$PLASTER_PARAM_Git -eq 'No'" destination="${PLASTER_PARAM_Name}\${PLASTER_PARAM_Name}.psd1" moduleVersion="$PLASTER_PARAM_Version" rootModule="${PLASTER_PARAM_Name}.psm1" author="$PLASTER_PARAM_Author" companyName="$PLASTER_PARAM_CompanyName" description="$PLASTER_PARAM_Description" encoding="UTF8-NoBOM"/&gt;
    &lt;message condition="$PLASTER_PARAM_Options -notcontains 'None'"&gt;Deploying optional components&lt;/message&gt;
    &lt;templateFile condition="$PLASTER_PARAM_Options -contains 'License' -and $PLASTER_PARAM_Options -notcontains 'None'" source="LICENSE" destination="${PLASTER_PARAM_GitRepoName}\LICENSE"/&gt;
    &lt;templateFile condition="$PLASTER_PARAM_Options -contains 'Readme' -and $PLASTER_PARAM_Options -notcontains 'None'" source="xReadme.md" destination="${PLASTER_PARAM_GitRepoName}\Readme.md"/&gt;
    &lt;file condition="$PLASTER_PARAM_Options -contains 'GitIgnore' -and $PLASTER_PARAM_Options -notcontains 'None'" source=".gitignore" destination="${PLASTER_PARAM_GitRepoName}\.gitignore"/&gt;
    &lt;file condition="$PLASTER_PARAM_Options -contains 'GitAttributes' -and $PLASTER_PARAM_Options -notcontains 'None'" source=".gitattributes" destination="${PLASTER_PARAM_GitRepoName}\.gitattributes"/&gt;
&lt;/content&gt;</pre>
I'll use this template to create a new module named <em>MrModuleBuildTools</em> where I'll be placing the tools that I'll be creating to build PowerShell script modules.
<pre class="lang:ps decode:true ">$plasterParams = @{
    TemplatePath      = 'U:\GitHub\Plaster\Template'
    DestinationPath   = 'U:\GitHub'
    Name              = 'MrModuleBuildTools'
    Description       = 'PowerShell Script Module Building Toolkit'
    Version           = '1.0.0'
    Author            = 'Mike F. Robbins'
    CompanyName       = 'mikefrobbins.com'
    Folders           = 'public', 'private'
    Git               = 'Yes'
    GitRepoName       = 'ModuleBuildTools'
    Options           = ('License', 'Readme', 'GitIgnore', 'GitAttributes')
}
If (-not(Test-Path -Path $plasterParams.DestinationPath -PathType Container)) {
    New-Item -Path $plasterParams.DestinationPath -ItemType Directory | Out-Null
}
Invoke-Plaster @plasterParams</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2018/08/mod-build-tools1a.png"><img class="alignnone size-full wp-image-16994" src="https://mikefrobbins.com/wp-content/uploads/2018/08/mod-build-tools1a.png" alt="" width="859" height="491" /></a>

At this point, I created a repo on GitHub for my <em>ModuleBuildTools</em>. I created it as a private repo until I'm ready to make it public.

<a href="https://mikefrobbins.com/wp-content/uploads/2018/08/mod-build-tools2a.png"><img class="alignnone size-full wp-image-16998" src="https://mikefrobbins.com/wp-content/uploads/2018/08/mod-build-tools2a.png" alt="" width="859" height="600" /></a>

One thing I haven't figured out is if there's a way to create the repo on GitHub from the command line?

Finally, I initialize the local folder as a Git repo, add all of the files that exist so far, commit them to the local repo, add the repo on GitHub as a remote, and push the changes to the repo on GitHub.

<a href="https://mikefrobbins.com/wp-content/uploads/2018/08/mod-build-tools3a.png"><img class="alignnone size-full wp-image-16999" src="https://mikefrobbins.com/wp-content/uploads/2018/08/mod-build-tools3a.png" alt="" width="859" height="369" /></a>

The basic structure for the development version of my <em>MrModuleBuildTools</em> PowerShell script module is now complete.

<a href="https://mikefrobbins.com/wp-content/uploads/2018/08/mod-build-tools4a.png"><img class="alignnone size-full wp-image-17001" src="https://mikefrobbins.com/wp-content/uploads/2018/08/mod-build-tools4a.png" alt="" width="859" height="713" /></a>

The xReadme.md template file contains the following content.
<pre class="lang:ps decode:true "># &lt;%=$PLASTER_PARAM_GitRepoName%&gt;

&lt;%=$PLASTER_PARAM_Description%&gt;</pre>
You might be wondering why the template file isn't called Readme.md without the leading "x"? I didn't want its content to show up on GitHub because it's only meaningful to Plaster. Those variables are automatically replaced when the following line of the template is executed and the leading "x" is removed from the destination file during this process.
<pre class="lang:ps decode:true ">&lt;templateFile condition="$PLASTER_PARAM_Options -contains 'Readme' -and $PLASTER_PARAM_Options -notcontains 'None'" source="xReadme.md" destination="${PLASTER_PARAM_GitRepoName}\Readme.md"/&gt;</pre>
This means the first line is replace by whatever is specified as the "<em>GitRepoName</em>" and the third line is replaced by the "<em>Description</em>".

The information in the MIT license is similar so that the current year is always specified along with the name of the module author.
<pre class="lang:ps decode:true ">Copyright (c) &lt;%=(Get-Date).Year%&gt; &lt;%=$PLASTER_PARAM_Author %&gt;</pre>
My complete Plaster template can be found in <a href="https://github.com/mikefrobbins/Plaster" target="_blank" rel="noopener">my Plaster repository on GitHub</a>. I used <a href="https://www.sapien.com/software/primalxml" target="_blank" rel="noopener">SAPIEN PrimalXML</a> to format the XML in my template.

Be sure to keep an eye on this blog site as I'll be adding tools to the <em>MrModuleBuildTools</em> module and blogging about them over the next few weeks.

<hr />

<strong>Update - August 31, 2018</strong>

One of the huge benefits of open-sourcing your code and sharing it online is free code reviews! <a href="https://twitter.com/thetommymaynard" target="_blank" rel="noopener">Tommy Maynard</a> contacted me about a bug in the Public/Private folder creation process when my template is used manually with <em>Invoke-Plaster</em>. Which would your like? Select "<em>P</em>" or "<em>P</em>" as shown in the following example.

<a href="https://mikefrobbins.com/wp-content/uploads/2018/08/mod-build-tools5a.png"><img class="alignnone size-full wp-image-17046" src="https://mikefrobbins.com/wp-content/uploads/2018/08/mod-build-tools5a.png" alt="" width="859" height="321" /></a>

The letter for the hotkey is the character immediately after "<em>&amp;amp;</em>" so the solution is to simply shift it one (or more) place(s) to the right.
<pre class="lang:ps decode:true ">&lt;parameter name="Folders" type="multichoice" default="0,1" prompt="Select folders to create"&gt;
    &lt;choice label="&amp;amp;Public" value="public" help="Adds a public folder to module root"/&gt;
    &lt;choice label="P&amp;amp;rivate" value="private" help="Adds a private folder to module root"/&gt;
&lt;/parameter&gt;</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2018/08/mod-build-tools6a.png"><img class="alignnone size-full wp-image-17047" src="https://mikefrobbins.com/wp-content/uploads/2018/08/mod-build-tools6a.png" alt="" width="859" height="318" /></a>

Thanks to Tommy for pointing out this bug! I've also made the corrections to the template in my Plaster repo on GitHub.

µ