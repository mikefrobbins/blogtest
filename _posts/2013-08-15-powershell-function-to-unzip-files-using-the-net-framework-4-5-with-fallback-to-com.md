---
ID: 7823
post_title: >
  PowerShell Function to Unzip Files Using
  the .NET Framework 4.5 with Fallback to
  COM
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2013/08/15/powershell-function-to-unzip-files-using-the-net-framework-4-5-with-fallback-to-com/
published: true
post_date: 2013-08-15 07:30:50
---
A few days ago, I saw a tweet about someone needing to extract a zip file with PowerShell and wanting to accomplish the task without loading any third party extensions so I thought I would write a function to accomplish the task.

I start out by declaring the function followed by its name, it includes comment based help which is collapsed in the following image, then the [CmdletBinding()] attribute is specified to define this function as an advanced function. Then the param block is opened which is where the parameters are defined:

<a href="http://mikefrobbins.com/wp-content/uploads/2013/08/unzip-file2.png"><img class="alignnone size-full wp-image-7826" alt="unzip-file2" src="http://mikefrobbins.com/wp-content/uploads/2013/08/unzip-file2.png" width="269" height="101" /></a>

The first parameter is "File" which is for the complete path including the name of the zip file. This parameter is mandatory and is also accepted via the pipeline. ValidateScript is used to make sure the value provided is a valid leaf object and that it also at least ends in .zip otherwise an exception is thrown:

<a href="http://mikefrobbins.com/wp-content/uploads/2013/08/unzip-file3.png"><img class="alignnone size-large wp-image-7827" alt="unzip-file3" src="http://mikefrobbins.com/wp-content/uploads/2013/08/unzip-file3.png?w=640" width="640" height="160" /></a>

The second parameter is for the "Destination" folder path of where to extract the items contained in the zip file. [ValidateNotNullOrEmpty()] prevents the value specified for this parameter from being just that (the input cannot be Null or Empty). The path is validated to be a container otherwise an exception is thrown. If this parameter is not specified, the current path is used as the default value:

<a href="http://mikefrobbins.com/wp-content/uploads/2013/08/unzip-file4.png"><img class="alignnone size-large wp-image-7829" alt="unzip-file4" src="http://mikefrobbins.com/wp-content/uploads/2013/08/unzip-file4.png?w=640" width="640" height="143" /></a>

The last parameter is a switch parameter which means it's on or off (true or false). This parameter will be used to force the use of COM for the extraction process. Lastly, the param block is closed with a closing parenthesis:

<a href="http://mikefrobbins.com/wp-content/uploads/2013/08/unzip-file5.png"><img class="alignnone size-full wp-image-7831" alt="unzip-file5" src="http://mikefrobbins.com/wp-content/uploads/2013/08/unzip-file5.png" width="211" height="52" /></a>

There's a lot going on in the "If" block. First, $ForceCOM is checked to make sure it wasn't specified, then whether or not PowerShell version 3 or greater is being used is checked, and either the full or client profile version of the .NET Framework 4.5 must be present so that's a total of three things that have to evaluate to true in order for the code inside the "If" block to be executed:

<a href="http://mikefrobbins.com/wp-content/uploads/2013/08/unzip-file6a.png"><img class="alignnone size-large wp-image-7843" alt="unzip-file6a" src="http://mikefrobbins.com/wp-content/uploads/2013/08/unzip-file6a.png?w=640" width="640" height="130" /></a>

If the user of this function specified the $ForceCOM switch parameter, or if a version of PowerShell less than version 3 is being used (PowerShell version 2 would generate an error with the code that's in the "If" block) or if the .NET Framework 4.5 isn't installed on the machine this function is being run on, the else block will be executed which uses COM to perform the zip file extraction process:

<a href="http://mikefrobbins.com/wp-content/uploads/2013/08/unzip-file7.png"><img class="alignnone size-large wp-image-7835" alt="unzip-file7" src="http://mikefrobbins.com/wp-content/uploads/2013/08/unzip-file7.png?w=640" width="640" height="168" /></a>

I decided to post the code for this function on the <a href="http://gallery.technet.microsoft.com/scriptcenter/PowerShell-Function-to-727d6200" target="_blank">TechNet Script Repository</a> so it would be easier to download. You can also download it from here:
<pre class="lang:ps decode:true">function Unzip-File {

&lt;#
.SYNOPSIS
   Unzip-File is a function which extracts the contents of a zip file.

.DESCRIPTION
   Unzip-File is a function which extracts the contents of a zip file specified via the -File parameter to the
location specified via the -Destination parameter. This function first checks to see if the .NET Framework 4.5
is installed and uses it for the unzipping process, otherwise COM is used.

.PARAMETER File
    The complete path and name of the zip file in this format: C:\zipfiles\myzipfile.zip 

.PARAMETER Destination
    The destination folder to extract the contents of the zip file to. If a path is no specified, the current path
is used.

.PARAMETER ForceCOM
    Switch parameter to force the use of COM for the extraction even if the .NET Framework 4.5 is present.

.EXAMPLE
   Unzip-File -File C:\zipfiles\AdventureWorks2012_Database.zip -Destination C:\databases\

.EXAMPLE
   Unzip-File -File C:\zipfiles\AdventureWorks2012_Database.zip -Destination C:\databases\ -ForceCOM

.EXAMPLE
   'C:\zipfiles\AdventureWorks2012_Database.zip' | Unzip-File

.EXAMPLE
    Get-ChildItem -Path C:\zipfiles | ForEach-Object {$_.fullname | Unzip-File -Destination C:\databases}

.INPUTS
   String

.OUTPUTS
   None

.NOTES
   Author:  Mike F Robbins
   Website: http://mikefrobbins.com
   Twitter: @mikefrobbins

#&gt;

    [CmdletBinding()]
    param (
        [Parameter(Mandatory=$true, 
                   ValueFromPipeline=$true)]
        [ValidateScript({
            If ((Test-Path -Path $_ -PathType Leaf) -and ($_ -like "*.zip")) {
                $true
            }
            else {
                Throw "$_ is not a valid zip file. Enter in 'c:\folder\file.zip' format"
            }
        })]
        [string]$File,

        [ValidateNotNullOrEmpty()]
        [ValidateScript({
            If (Test-Path -Path $_ -PathType Container) {
                $true
            }
            else {
                Throw "$_ is not a valid destination folder. Enter in 'c:\destination' format"
            }
        })]
        [string]$Destination = (Get-Location).Path,

        [switch]$ForceCOM
    )

    If (-not $ForceCOM -and ($PSVersionTable.PSVersion.Major -ge 3) -and
       ((Get-ItemProperty -Path "HKLM:\Software\Microsoft\NET Framework Setup\NDP\v4\Full" -ErrorAction SilentlyContinue).Version -like "4.5*" -or
       (Get-ItemProperty -Path "HKLM:\Software\Microsoft\NET Framework Setup\NDP\v4\Client" -ErrorAction SilentlyContinue).Version -like "4.5*")) {

        Write-Verbose -Message "Attempting to Unzip $File to location $Destination using .NET 4.5"

        try {
            [System.Reflection.Assembly]::LoadWithPartialName("System.IO.Compression.FileSystem") | Out-Null
            [System.IO.Compression.ZipFile]::ExtractToDirectory("$File", "$Destination")
        }
        catch {
            Write-Warning -Message "Unexpected Error. Error details: $_.Exception.Message"
        }

    }
    else {

        Write-Verbose -Message "Attempting to Unzip $File to location $Destination using COM"

        try {
            $shell = New-Object -ComObject Shell.Application
            $shell.Namespace($destination).copyhere(($shell.NameSpace($file)).items())
        }
        catch {
            Write-Warning -Message "Unexpected Error. Error details: $_.Exception.Message"
        }

    }

}</pre>
µ