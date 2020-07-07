---
ID: 16364
post_title: >
  Using Plaster to create a PowerShell
  Script Module template
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2018/02/15/using-plaster-to-create-a-powershell-script-module-template/
published: true
post_date: 2018-02-15 07:30:45
---
I have <a href="http://mikefrobbins.com/2016/06/30/powershell-function-for-creating-a-script-module-template/" target="_blank" rel="noopener">a function in my MrToolkit module named <em>New-MrScriptModule</em></a> that creates the scaffolding for a new PowerShell script module. It creates a PSM1 file and a module manifest (PSD1 file) along with the folder structure for a script module. To reduce the learning curve of Plaster as much as possible, I'm simply going to replace that existing functionality with Plaster in this blog article. Then moving forward, I'll add additional functionality.

For those of you who aren't familiar with Plaster, per <a href="https://github.com/PowerShell/Plaster/blob/master/README.md" target="_blank" rel="noopener">their GitHub readme</a>, "<em>Plaster is a template-based file and project generator written in PowerShell. Its purpose is to streamline the creation of PowerShell module projects, Pester tests, DSC configurations, and more. File generation is performed using crafted templates which allow the user to fill in details and choose from options to get their desired output</em>."

First, start out by installing the Plaster module.
<pre class="lang:ps decode:true">Install-Module -Name Plaster -Force</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2018/02/plaster1a.jpg"><img class="alignnone size-full wp-image-16366" src="http://mikefrobbins.com/wp-content/uploads/2018/02/plaster1a.jpg" alt="" width="859" height="61" /></a>

Create the initial Plaster template along with the metadata section which contains data about the manifest itself.
<pre class="lang:ps decode:true">$manifestProperties = @{
    Path         = 'C:\GitHub\PlasterTemplate\PlasterManifest.xml'
    TemplateName = 'ScriptModuleTemplate'
    TemplateType = 'Project'
    Author       = 'Mike F Robbins'
    Description  = 'Scaffolds the files required for a PowerShell script module'
    Tags         = 'PowerShell, Module, ModuleManifest'
}

$Folder = Split-Path -Path $manifestProperties.Path -Parent
if (-not(Test-Path -Path $Folder -PathType Container)) {
    New-Item -Path $Folder -ItemType Directory | Out-Null
}

New-PlasterManifest @manifestProperties</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2018/02/plaster2b.jpg"><img class="alignnone size-full wp-image-16375" src="http://mikefrobbins.com/wp-content/uploads/2018/02/plaster2b.jpg" alt="" width="859" height="259" /></a>

You could also run the same command without <a href="https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_splatting?view=powershell-6" target="_blank" rel="noopener">splatting</a>, although you'll need to make sure the folder structure already exists if you use the following example.
<pre class="lang:ps decode:true">New-PlasterManifest -Path C:\GitHub\PlasterTemplate\PlasterManifest.xml -TemplateName ScriptModuleTemplate -TemplateType Project -Author 'Mike F Robbins' -Description 'Scaffolds the files required for a PowerShell script module' -Tags 'PowerShell, Module, ModuleManifest'</pre>
That creates an XML file that looks like the one in the following example.
<pre class="lang:ps decode:true" title="PlasterManifest.xml">&lt;?xml version="1.0" encoding="utf-8"?&gt;
&lt;plasterManifest
  schemaVersion="1.1"
  templateType="Project" xmlns="http://www.microsoft.com/schemas/PowerShell/Plaster/v1"&gt;
  &lt;metadata&gt;
    &lt;name&gt;ScriptModuleTemplate&lt;/name&gt;
    &lt;id&gt;ee4fdc83-1b9a-47ae-b4e6-336053283a86&lt;/id&gt;
    &lt;version&gt;1.0.0&lt;/version&gt;
    &lt;title&gt;ScriptModuleTemplate&lt;/title&gt;
    &lt;description&gt;Scaffolds the files required for a PowerShell script module&lt;/description&gt;
    &lt;author&gt;Mike F Robbins&lt;/author&gt;
    &lt;tags&gt;PowerShell, Module, ModuleManifest&lt;/tags&gt;
  &lt;/metadata&gt;
  &lt;parameters&gt;&lt;/parameters&gt;
  &lt;content&gt;&lt;/content&gt;
&lt;/plasterManifest&gt;</pre>
Based on the information from the <a href="https://github.com/PowerShell/Plaster/blob/master/docs/en-US/about_Plaster_CreatingAManifest.help.md" target="_blank" rel="noopener">about_Plaster_CreatingAManifest</a> help file which can be viewed in PowerShell or in <a href="https://github.com/PowerShell/Plaster" target="_blank" rel="noopener">their GitHub repository</a>, <em>Name</em> is mandatory, <em>ID</em> is a GUID that's automatically generated if one isn't provided, <em>Version</em> defaults to 1.0.0 unless otherwise specified, <em>Title</em> defaults to the same value as <em>Name</em> unless specified, and <em>Description</em>, <em>Tags</em>, and <em>Author</em> are all optional. <em>Description</em> will be added, but blank if not specified. <em>Tags</em> and <em>Author</em> won't be added unless specified.

Notice that there's a parameter and content section in the previous example that was created. In this scenario, the parameters section is for adding values that may be different each time a new PowerShell script module is created. It's the same concept and no different than adding parameters to a PowerShell function.
<pre class="lang:ps decode:true ">&lt;parameters&gt;
    &lt;parameter name='Name' type='text' prompt='Name of the module' /&gt;
    &lt;parameter name='Description' type='text' prompt='Brief description of module (required for publishing to the PowerShell Gallery)' /&gt;
    &lt;parameter name='Version' type='text' default='0.1.0' prompt='Enter the version number of the module' /&gt;
    &lt;parameter name='Author' type='user-fullname' prompt="Module author's name" store='text' /&gt;
    &lt;parameter name='CompanyName' type='text' prompt='Name of your Company' default='mikefrobbins.com' /&gt;
    &lt;parameter name='PowerShellVersion' default='3.0' type='text' prompt='Minimum PowerShell version' /&gt;
&lt;/parameters&gt;</pre>
These are almost the same items that are parameters for my <em>New-MrScriptModule</em> function. I've added a <em>Version</em> parameter for the module version. <em>Path</em> will be specified when creating the script module instead of at this point. Also notice that <em>Author</em> is set to <em>"type=user-fullname"</em>. In my opinion, this is better than setting a default because if a module author isn't specified, this information will be retrieved from the users Git config. This also allows conditional defaults for the author depending on whether or not you're using the <em>"IncludeIf"</em> functionality in your Git config file. <em>IncludeIf</em> was introduced in Git version 2.13.

Next is the content section.
<pre class="lang:ps decode:true ">&lt;content&gt;
    &lt;message&gt;
    Creating folder structure
    &lt;/message&gt;
      &lt;file source='' destination='${PLASTER_PARAM_Name}'/&gt;
    &lt;message&gt;
      Deploying common files
    &lt;/message&gt;
    &lt;file source='module.psm1' destination='${PLASTER_PARAM_Name}\${PLASTER_PARAM_Name}.psm1'/&gt;
    &lt;message&gt;
      Creating Module Manifest
    &lt;/message&gt;
    &lt;newModuleManifest
      destination='${PLASTER_PARAM_Name}\${PLASTER_PARAM_Name}.psd1'
      moduleVersion='$PLASTER_PARAM_Version'
      rootModule='${PLASTER_PARAM_Name}.psm1'
      author='$PLASTER_PARAM_Author'
      companyName='$PLASTER_PARAM_CompanyName'
      description='$PLASTER_PARAM_Description'
      powerShellVersion='$PLASTER_PARAM_PowerShellVersion'
      encoding='UTF8-NoBOM'/&gt;
&lt;/content&gt;</pre>
I've placed my standard PSM1 file in my Plaster template folder and now I'm all set to create a new PowerShell script module. The completed Plaster template is shown below.
<pre class="lang:ps decode:true ">&lt;?xml version="1.0" encoding="utf-8"?&gt;
&lt;plasterManifest schemaVersion="1.1" templateType="Project" 
  xmlns="http://www.microsoft.com/schemas/PowerShell/Plaster/v1"&gt;
  &lt;metadata&gt;
    &lt;name&gt;ScriptModuleTemplate&lt;/name&gt;
    &lt;id&gt;ee4fdc83-1b9a-47ae-b4e6-336053283a86&lt;/id&gt;
    &lt;version&gt;1.0.0&lt;/version&gt;
    &lt;title&gt;ScriptModuleTemplate&lt;/title&gt;
    &lt;description&gt;Scaffolds the files required for a PowerShell script module&lt;/description&gt;
    &lt;author&gt;Mike F Robbins&lt;/author&gt;
    &lt;tags&gt;PowerShell, Module, ModuleManifest&lt;/tags&gt;
  &lt;/metadata&gt;
  &lt;parameters&gt;
    &lt;parameter name='Name' type='text' prompt='Name of the module' /&gt;
    &lt;parameter name='Description' type='text' prompt='Brief description of module (required for publishing to the PowerShell Gallery)' /&gt;
    &lt;parameter name='Version' type='text' default='0.1.0' prompt='Enter the version number of the module' /&gt;
    &lt;parameter name='Author' type='user-fullname' prompt="Module author's name" store='text' /&gt;
    &lt;parameter name='CompanyName' type='text' prompt='Name of your Company' default='mikefrobbins.com' /&gt;
    &lt;parameter name='PowerShellVersion' default='3.0' type='text' prompt='Minimum PowerShell version' /&gt;
  &lt;/parameters&gt;
  &lt;content&gt;
    &lt;message&gt;
    Creating folder structure
    &lt;/message&gt;
    &lt;file source='' destination='${PLASTER_PARAM_Name}'/&gt;
    &lt;message&gt;
      Deploying common files
    &lt;/message&gt;
    &lt;file source='module.psm1' destination='${PLASTER_PARAM_Name}\${PLASTER_PARAM_Name}.psm1'/&gt;
    &lt;message&gt;
      Creating Module Manifest
    &lt;/message&gt;
    &lt;newModuleManifest destination='${PLASTER_PARAM_Name}\${PLASTER_PARAM_Name}.psd1' moduleVersion='$PLASTER_PARAM_Version' rootModule='${PLASTER_PARAM_Name}.psm1' author='$PLASTER_PARAM_Author' companyName='$PLASTER_PARAM_CompanyName' description='$PLASTER_PARAM_Description' powerShellVersion='$PLASTER_PARAM_PowerShellVersion' encoding='UTF8-NoBOM'/&gt;
  &lt;/content&gt;
&lt;/plasterManifest&gt;</pre>
The verbose parameter provides additional information which can be helpful in troubleshooting problems.
<pre class="lang:ps decode:true">Invoke-Plaster -TemplatePath C:\GitHub\PlasterTemplate -DestinationPath C:\tmp2 -Verbose</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2018/02/plaster3a.jpg"><img class="alignnone size-full wp-image-16378" src="http://mikefrobbins.com/wp-content/uploads/2018/02/plaster3a.jpg" alt="" width="859" height="397" /></a>

A hash table can also be used to create the module without being prompted. The parameter names are <em>TemplatePath</em>, <em>DestinationPath</em>, and any parameters defined in the template. You must specify all of the parameters, even the ones that you've specified defaults for otherwise you'll be prompted to either enter a value or confirm the default.
<pre class="lang:ps decode:true ">$plasterParams = @{
    TemplatePath      = 'C:\GitHub\PlasterTemplate'
    DestinationPath   = 'C:\tmp2'
    Name              = 'MrTestModule'
    Description       = 'Mike Robbins Test Module'
    Version           = '0.9.0'
    Author            = 'Mike F Robbins'
    CompanyName       = 'mikefrobbins.com'
    PowerShellVersion = '3.0'
}
If (-not(Test-Path -Path $plasterParams.DestinationPath -PathType Container)) {
    New-Item -Path $plasterParams.DestinationPath -ItemType Directory | Out-Null
}
Invoke-Plaster @plasterParams</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2018/02/plaster4a.jpg"><img class="alignnone size-full wp-image-16379" src="http://mikefrobbins.com/wp-content/uploads/2018/02/plaster4a.jpg" alt="" width="859" height="440" /></a>

Technically, you could also specify all of the parameters and their values via parameter input, but the ones specified in the Plaster template won't tab-expand or show up in intellisense.
<pre class="lang:ps decode:true ">Invoke-Plaster -TemplatePath C:\GitHub\PlasterTemplate -DestinationPath C:\tmp2 -Name MrTestModule -Description 'Mike Robbins Test Module' -Version 0.9.0 -Author 'Mike F Robbins' -CompanyName mikefrobbins.com  -PowerShellVersion 3.0</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2018/02/plaster5a.jpg"><img class="alignnone size-full wp-image-16382" src="http://mikefrobbins.com/wp-content/uploads/2018/02/plaster5a.jpg" alt="" width="859" height="283" /></a>

Sometimes learning new things can seem overwhelming based on examples you'll find on the Internet. I'm not sure about others, but I've learned to keep it super simple to start with and add additional complexity one step at a time to reduce the learning curve. No one starts out as an expert at anything.

This is only the tip of the iceberg for Plaster. Other recommended resources include the documentation and examples in the Plaster GitHub repository and the following blog articles by others who have written about it:
<ul>
 	<li><a href="https://poshsecurity.com/blog/levelling-up-your-powershell-modules-with-plaster" target="_blank" rel="noopener">Levelling up your PowerShell modules with Plaster</a> by <a href="https://twitter.com/kjacobsen" target="_blank" rel="noopener">Kieran Jacobsen</a></li>
 	<li><a href="https://sqldbawithabeard.com/2017/11/09/using-plaster-to-create-a-new-powershell-module/" target="_blank" rel="noopener">Using Plaster To Create a New PowerShell Module</a> by <a href="https://twitter.com/sqldbawithbeard" target="_blank" rel="noopener">Rob Sewell</a></li>
 	<li><a href="https://kevinmarquette.github.io/2017-05-12-Powershell-Plaster-adventures-in/" target="_blank" rel="noopener">PowerShell: Adventures in Plaster</a> by <a href="https://twitter.com/KevinMarquette" target="_blank" rel="noopener">Kevin Marquette</a></li>
 	<li><a href="http://overpoweredshell.com/Working-with-Plaster/" target="_blank" rel="noopener">Working with Plaster</a> by <a href="https://twitter.com/dchristian3188" target="_blank" rel="noopener">David Christian</a></li>
</ul>
There's also <a href="http://mspsug.com/2017/06/20/video-working-with-plaster-a-powershell-scaffolding-module/" target="_blank" rel="noopener">a video on Working with Plaster</a> that David Christian presented for one of our <a href="http://mspsug.com/" target="_blank" rel="noopener">Mississippi PowerShell User Group</a> meetings.

The Plaster template shown in this blog article and ongoing updates to it can be downloaded from <a href="https://github.com/mikefrobbins/PlasterTemplate" target="_blank" rel="noopener">my PlasterTemplate repository on GitHub</a>.

<hr />

Update February 16th, 2018
Modified the code to create the Plaster manifest based on <a href="https://www.reddit.com/r/PowerShell/comments/7xy5zk/using_plaster_to_create_a_powershell_script/" target="_blank" rel="noopener">feedback from Lee Dailey on the PowerShell SubReddit</a>.

µ