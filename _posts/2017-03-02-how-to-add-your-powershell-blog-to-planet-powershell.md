---
ID: 15069
post_title: >
  How to add your PowerShell blog to
  Planet PowerShell
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2017/03/02/how-to-add-your-powershell-blog-to-planet-powershell/
published: true
post_date: 2017-03-02 07:30:43
---
Do you blog about PowerShell? If so, consider adding your blog site to <a href="http://www.planetpowershell.com/" target="_blank">Planet PowerShell</a> which is an aggregator of content from PowerShell Community members. There are some guidelines for submission on <a href="https://github.com/planetpowershell/planetpowershell" target="_blank">their GitHub page</a> so be sure to take a look at it before continuing. Instructions for adding your blog also exists on that page, but I've recently seen a number of tweets about it being too difficult or too much work. To be honest with you, if everything in IT was as easy as adding my blog to Planet PowerShell, I probably wouldn't have a job. I decided to try to make the process a little easier, regardless (I'm not affiliated with Planet PowerShell other than having added my blog to it).

First, you need to fork <a href="https://github.com/planetpowershell/planetpowershell" target="_blank">the Planet PowerShell repository on GitHub</a>:

<a href="http://mikefrobbins.com/wp-content/uploads/2017/03/planet-powershell1a.png"><img class="alignnone size-full wp-image-15070" src="http://mikefrobbins.com/wp-content/uploads/2017/03/planet-powershell1a.png" alt="" width="995" height="376" /></a>

Forking a repository creates your own personal copy of it underneath your GitHub account as denoted by #1 in the following image:

<a href="http://mikefrobbins.com/wp-content/uploads/2017/03/planet-powershell2a.png"><img class="alignnone size-full wp-image-15071" src="http://mikefrobbins.com/wp-content/uploads/2017/03/planet-powershell2a.png" alt="" width="996" height="534" /></a>

Go to your copy of the Planet PowerShell repository on GitHub. Click on the "Clone or download" button (#2) and then click on the "Copy to clipboard" button (#3).

Clone your copy of the repository to your computer. Be sure to change the source path to match your copy of the Planet PowerShell repository (use the path that you copied to your clipboard in the previous step). Change the destination path if needed. I'll store the destination path in a variable named $LocalPath since I'll be using it again later in this blog article.
<pre class="lang:ps decode:true">$LocalPath = 'U:\GitHub\planetpowershell'
git clone https://github.com/mikefrobbins/planetpowershell.git $LocalPath</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2017/03/planet-powershell3a.png"><img class="alignnone size-full wp-image-15072" src="http://mikefrobbins.com/wp-content/uploads/2017/03/planet-powershell3a.png" alt="" width="859" height="141" /></a>

If you don't already have Git installed (which is used in the previous example) or if you're not familiar with Git, you can find more information about it in my blog article titled "<a href="http://mikefrobbins.com/2016/01/21/getting-started-with-the-git-version-control-system/" target="_blank">Getting Started with the Git Version Control System</a>".

I'll store my first and last name in variables since they'll be used throughout this blog article. Your name should be in proper case.
<pre class="lang:ps decode:true">$FirstName = 'Mike'
$LastName = 'Robbins'</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2017/03/planet-powershell12a.png"><img class="alignnone size-full wp-image-15097" src="http://mikefrobbins.com/wp-content/uploads/2017/03/planet-powershell12a.png" alt="" width="859" height="68" /></a>

I wrote a PowerShell function to create your author file for Planet PowerShell. This function is part of my MrToolkit module which can be found in <a href="https://github.com/mikefrobbins/PowerShell" target="_blank">my PowerShell repository on GitHub</a>. It uses some of the other functions found in that module as well as my MrGeo module which can be found in <a href="https://github.com/mikefrobbins/ScriptingGames" target="_blank">my ScriptingGames repository on GitHub</a>.
<pre class="lang:ps decode:true " title="New-MrPlanetPowerShellAuthor">#Requires -Version 3.0 -Modules MrGeo
function New-MrPlanetPowerShellAuthor {

&lt;#
.SYNOPSIS
    Creates the author information required to add your PowerShell related blog to Planet PowerShell.
 
.DESCRIPTION
    New-MrPlanetPowerShellAuthor is an advanced function that creates the author information required
    to add your PowerShell related blog to Planet PowerShell (http://www.planetpowershell.com/). Planet
    PowerShell is an aggregator of content from PowerShell Community members.
 
.PARAMETER FirstName
    Author's first name.

.PARAMETER LastName
    Author's last name.

.PARAMETER Bio
    Short bio about the author.

.PARAMETER StateOrRegion
    Your geographical location, i.e.: Holland, New York, etc.

.PARAMETER EmailAddress
    Email address. Only enter if you want your email address to be publicly available.

.PARAMETER TwitterHandle
    Twitter handle without the leading @.

.PARAMETER GravatarEmailAddress
    The email address you use at gravatar.com. Entering this causes the picture used at Gravatar.com to
    be used as your author picture on Planet PowerShell. The email address is converted to the MD5 hash
    of the email address string.

.PARAMETER GitHubHandle
    GitHub handle without the leading @.

.PARAMETER BlogUri
    URL of your blog site.

.PARAMETER RssUri
    URL for the RSS feed to your blog site.

.PARAMETER MicrosoftMVP
    Switch parameter. Specify if you're a Microsoft MVP.

.PARAMETER FilterToPowerShell
    Switch parameter. Specify if you blog on more than just PowerShell.

.EXAMPLE
    New-MrPlanetPowerShellAuthor -FirstName Mike -LastName Robbins -Bio 'Microsoft PowerShell MVP and SAPIEN Technologies MVP. Leader &amp; Co-founder of MSPSUG' -StateOrRegion 'Mississippi, USA' -TwitterHandle mikefrobbins -GravatarEmailAddress mikefrobbins@users.noreply.github.com -GitHubHandle mikefrobbins -BlogUri mikefrobbins.com -RssUri mikefrobbins.com/feed -MicrosoftMVP -FilterToPowerShell |
    New-Item -Path C:\GitHub\planetpowershell\src\Firehose.Web\Authors\MikeRobbins.cs

.INPUTS
    None
 
.OUTPUTS
    System.String
 
.NOTES
    Author:  Mike F Robbins
    Website: http://mikefrobbins.com
    Twitter: @mikefrobbins
#&gt;

    [CmdletBinding()]
    param (
        [Parameter(Mandatory)]
        [string]$FirstName,

        [Parameter(Mandatory)]
        [string]$LastName,

        [string]$Bio,

        [string]$StateOrRegion,

        [string]$EmailAddress,

        [string]$TwitterHandle,

        [string]$GravatarEmailAddress,

        [string]$GitHubHandle,

        [Parameter(Mandatory)]
        [string]$BlogUri,

        [string]$RssUri,

        [switch]$MicrosoftMVP,

        [switch]$FilterToPowerShell
    )

    $BlogUrl = (Test-MrURL -Uri $BlogUri -Detailed).ResponseUri
    
    if ($PSBoundParameters.RssUri) {
        $RssUrl = (Test-MrURL -Uri $RssUri -Detailed).ResponseUri
    }
    
    $GravatarHash = (Get-MrHash -String $GravatarEmailAddress).ToLower()
    $Location = Get-MrGeoInformation
    $GeoLocation = -join ($Location.Latitude, ', ', $Location.Longitude)
    
    if ($MicrosoftMVP) {
        $Interface = 'IAmAMicrosoftMVP'
    }
    else {
        $Interface = 'IAmACommunityMember'
    }

    if ($FilterToPowerShell) {
        $Interface = "$Interface, IFilterMyBlogPosts"

        $SyndicationItem =
@'
public bool Filter(SyndicationItem item)
        {
            return item.Categories.Any(c =&gt; c.Name.ToLowerInvariant().Equals("powershell"));
        }
'@
    }

@"
using System;
using System.Collections.Generic;
using System.Linq;
using System.ServiceModel.Syndication;
using System.Web;
using Firehose.Web.Infrastructure;
namespace Firehose.Web.Authors
{
    public class $FirstName$LastName : $Interface
    {
        public string FirstName =&gt; `"$FirstName`";
        public string LastName =&gt; `"$LastName`";
        public string ShortBioOrTagLine =&gt; `"$Bio`";
        public string StateOrRegion =&gt; `"$StateOrRegion`";
        public string EmailAddress =&gt; `"$EmailAddress`";
        public string TwitterHandle =&gt; `"$TwitterHandle`";
        public string GitHubHandle =&gt; `"$GitHubHandle`";
        public string GravatarHash =&gt; `"$GravatarHash`";
        public GeoPosition Position =&gt; new GeoPosition($GeoLocation);

        public Uri WebSite =&gt; new Uri(`"$BlogUrl`");
        public IEnumerable&lt;Uri&gt; FeedUris { get { yield return new Uri(`"$RssUrl`"); } }

        $SyndicationItem
    }
}
"@

}</pre>
Simply run the function and provide your information via parameter input.

The output can be used to create the necessary .cs file:
<pre class="lang:ps decode:true">New-MrPlanetPowerShellAuthor -FirstName $FirstName -LastName $LastName -Bio 'Microsoft PowerShell MVP and SAPIEN Technologies MVP. Leader &amp; Co-founder of MSPSUG' -StateOrRegion 'Mississippi, USA' -TwitterHandle mikefrobbins -GravatarEmailAddress mikefrobbins@users.noreply.github.com -GitHubHandle mikefrobbins -BlogUri mikefrobbins.com -RssUri mikefrobbins.com/feed -MicrosoftMVP -FilterToPowerShell |
New-Item -Path $LocalPath\src\Firehose.Web\Authors\$FirstName$LastName.cs</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2017/03/planet-powershell4d.png"><img class="alignnone size-full wp-image-15107" src="http://mikefrobbins.com/wp-content/uploads/2017/03/planet-powershell4d.png" alt="" width="859" height="223" /></a>

Verify that the contents of the .cs file looks correct:
<pre class="lang:ps decode:true">Get-Content -Path $LocalPath\src\Firehose.Web\Authors\$FirstName$LastName.cs</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2017/03/planet-powershell6d.png"><img class="alignnone size-full wp-image-15108" src="http://mikefrobbins.com/wp-content/uploads/2017/03/planet-powershell6d.png" alt="" width="859" height="416" /></a>

Add the class to the .csproj file:
<pre class="lang:ps decode:true">$xml = [xml](Get-Content -Path "$LocalPath\src\Firehose.Web\Firehose.Web.csproj")
$element = $xml.CreateElement('Compile')
$element.SetAttribute('Include',"Authors\$FirstName$LastName.cs")
$xml.Project.ItemGroup[2].AppendChild($element)
$xml = [xml]$xml.OuterXml.Replace(" xmlns=`"`"", "")
$xml.Save("$LocalPath\src\Firehose.Web\Firehose.Web.csproj")</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2017/03/planet-powershell5b.png"><img class="alignnone size-full wp-image-15093" src="http://mikefrobbins.com/wp-content/uploads/2017/03/planet-powershell5b.png" alt="" width="859" height="188" /></a>

Verify the class has been added:
<pre class="lang:ps decode:true">([xml](Get-Content -Path "$LocalPath\src\Firehose.Web\Firehose.Web.csproj")).Project.ItemGroup[2].Compile |
Where-Object Include -like *authors*</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2017/03/planet-powershell7b.png"><img class="alignnone size-full wp-image-15081" src="http://mikefrobbins.com/wp-content/uploads/2017/03/planet-powershell7b.png" alt="" width="859" height="344" /></a>

I can see that one file has been added and one file has been modified in my local copy of my Planet PowerShell repo:
<pre class="lang:ps decode:true">git status</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2017/03/planet-powershell8a.png"><img class="alignnone size-full wp-image-15082" src="http://mikefrobbins.com/wp-content/uploads/2017/03/planet-powershell8a.png" alt="" width="859" height="247" /></a>

Do you like how my PowerShell prompt changes automatically when I'm in a directory that's part of a Git repository? If so, be sure to see my blog article on "<a href="http://mikefrobbins.com/2016/02/09/configuring-the-powershell-ise-for-use-with-git-and-github/" target="_blank">Configuring the PowerShell ISE for use with Git and GitHub</a>".

Add any new or modified files, commit them to the local copy of the repository, and push the changes to your copy of the repository on GitHub:
<pre class="lang:ps decode:true">git add .
git commit -m 'Added author information for Mike Robbins'
git push</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2017/03/planet-powershell9a.png"><img class="alignnone size-full wp-image-15083" src="http://mikefrobbins.com/wp-content/uploads/2017/03/planet-powershell9a.png" alt="" width="859" height="379" /></a>

Submit a pull request:

<a href="http://mikefrobbins.com/wp-content/uploads/2017/03/planet-powershell10a.png"><img class="alignnone size-full wp-image-15085" src="http://mikefrobbins.com/wp-content/uploads/2017/03/planet-powershell10a.png" alt="" width="996" height="465" /></a>

Click the "Create pull request button":

<a href="http://mikefrobbins.com/wp-content/uploads/2017/03/planet-powershell11a.png"><img class="alignnone size-full wp-image-15086" src="http://mikefrobbins.com/wp-content/uploads/2017/03/planet-powershell11a.png" alt="" width="993" height="614" /></a>

The pull request has to be reviewed and approved by the owners of Planet PowerShell. Once that occurs, it may take a few days for your blog articles to start showing up in their RSS feed.

<hr />

Update 3/30/2017 - I've added the one function in the MrGeo module to the MrToolkit module so only one module is required. I've also updated my MrToolkit module on the PowerShell Gallery so you can simply run <em>Install-Module -Name MrToolkit -Force</em> to install the module on a computer with PowerShell version 5.0 or higher or one that has installed the package management MSI for down-level versions.

µ