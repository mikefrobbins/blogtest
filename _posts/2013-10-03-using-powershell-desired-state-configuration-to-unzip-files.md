---
ID: 8308
post_title: >
  Using PowerShell Desired State
  Configuration to Unzip Files
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2013/10/03/using-powershell-desired-state-configuration-to-unzip-files/
published: true
post_date: 2013-10-03 07:30:20
---
A couple of months ago I wrote a blog about a "<a href="http://mikefrobbins.com/2013/08/15/powershell-function-to-unzip-files-using-the-net-framework-4-5-with-fallback-to-com/" target="_blank">PowerShell Function to Unzip Files Using the .NET Framework 4.5 with Fallback to COM</a>". I've since discovered that the new Desired State Configuration (DSC) feature in PowerShell version 4 can unzip files. You probably wouldn't use this feature just to unzip a single file to a single server, but it does open up some interesting possibilities.

This blog article is not meant to be an all inclusive tutorial on DSC, it's only meant to give you a peek inside the built-in DSC Archive Resource capabilities.

First, define your DSC configuration to create your MOF (Managed Object Format) file:
<pre class="lang:ps decode:true">configuration UnZipFile {

    param (
        [Parameter(Mandatory=$True)]
        [string[]]$ComputerName,

        [string]$Path,
        [string]$Destination
    )

    node $ComputerName {

        archive ZipFile {
        Path = $Path
        Destination = $Destination
        Ensure = 'Present'

        }

    }

}</pre>
Notice that by parameterizing the configuration, I'm able to use my parameters instead of having to rewrite the configuration each time:

<a href="http://mikefrobbins.com/wp-content/uploads/2013/10/dsc-archive1.png"><img class="alignnone size-full wp-image-8309" src="http://mikefrobbins.com/wp-content/uploads/2013/10/dsc-archive1.png" alt="dsc-archive1" width="750" height="302" /></a>

Voila! MOF file created:
<pre class="lang:ps decode:true">UnzipFile -ComputerName Server01 -Path \\dc01\Share\ServerAdd.zip -Destination C:\</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2013/10/dsc-archive2.png"><img class="alignnone size-full wp-image-8310" src="http://mikefrobbins.com/wp-content/uploads/2013/10/dsc-archive2.png" alt="dsc-archive2" width="802" height="283" /></a>

Since I defined the ComputerName parameter as mandatory, if I forget to specify that parameter I'll be prompted for a value for it. I can also specify more than one value and create multiple MOF files for different nodes, all with a single command:

<a href="http://mikefrobbins.com/wp-content/uploads/2013/10/dsc-archive3.png"><img class="alignnone size-full wp-image-8311" src="http://mikefrobbins.com/wp-content/uploads/2013/10/dsc-archive3.png" alt="dsc-archive3" width="749" height="360" /></a>

Now I'll apply this configuration to Server01 to place it in the desired state:
<pre class="lang:ps decode:true">Start-DscConfiguration UnZipFile -ComputerName Server01 -Wait -Verbose</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2013/10/dsc-archive4.png"><img class="alignnone size-full wp-image-8312" src="http://mikefrobbins.com/wp-content/uploads/2013/10/dsc-archive4.png" alt="dsc-archive4" width="877" height="504" /></a>

The first time I ran this command, I thought there was a problem with the archive file due to the messages about files missing or not being a file. This is actually DSC checking the destination to see if the files already exist.

I removed one of the files and reapplied the state to confirm that the messages about files missing are indeed about the destination and not the source:

<a href="http://mikefrobbins.com/wp-content/uploads/2013/10/dsc-archive5.png"><img class="alignnone size-full wp-image-8313" src="http://mikefrobbins.com/wp-content/uploads/2013/10/dsc-archive5.png" alt="dsc-archive5" width="877" height="347" /></a>

What I really like is that reapplying the state only extracted the one missing file and not the entire archive since all of the other files already existed.

µ