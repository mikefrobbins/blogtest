---
ID: 7200
post_title: '2013 PowerShell Scripting Games Advanced Event 1 &#8211; Parameters Don&#8217;t Always Work As Expected'
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2013/04/30/2013-powershell-scripting-games-advanced-event-1-parameters-dont-always-work-as-expected/
published: true
post_date: 2013-04-30 07:30:15
---
The scenario for this event states the following which has been paraphrased:

<em><span style="line-height: 1.5;">Someone allowed log files to build up for around two years and they're starting to causing disk space issues on the server. You need to build a tool to archive the log files because this has happened more than once. Applications write log files to a sub-folder in "C:\Application\Log". Examples are C:\Application\Log\App1, C:\Application\Log\OtherApp, and C:\Application\Log\ThisAppAlso. The log files have random file names and a .log extension. After the log files are written to disk, they're no longer used by the applications.</span></em>

<em>Your tool should archive log files older than 90 days and move them to "\\NASServer\Archives, maintaining the source sub-folder stucture such as "\\NASServer\Archives\App1".</em>

<em>The source location, destination location, and file age to archive should each be a parameter. Any errors that happen during the move should be clearly displayed. Output should not be displayed if the command completes successfully.</em>

Using <em>Get-ChildItem</em> with the <em>-Filter</em> parameter:

I started out working with <em>Get-ChildItem</em> and was almost set on using the <em>-Filter</em> parameter to filter the results down to only log files, the problem is that using <em>-Filter</em> in that manner would also return any file extension that begins with log such as .log1 as shown in the following results:

<a href="http://mikefrobbins.com/wp-content/uploads/2013/04/sg-event1.png"><img class="alignnone size-full wp-image-7202" src="http://mikefrobbins.com/wp-content/uploads/2013/04/sg-event1.png" alt="sg-event1" width="631" height="407" /></a>

So unfortunately if you used the <em>-Filter</em> parameter with <em>Get-ChildItem</em> in your solution, it doesn't meets the requirements of this scenario since it will archive more than just .log files.

Using <em>Get-ChildItem</em> with the <em>-Include</em> parameter:

The other parameter that I thought would accomplish the task was <em>Get-ChildItem</em> with the <em>-Include</em> parameter. The issue I ran into with it is that it only works when using the <em>-Recurse</em> parameter. At first, using the <em>-Recurse</em> parameter didn't seem to be an issue:

<a href="http://mikefrobbins.com/wp-content/uploads/2013/04/sg-event1a.png"><img class="alignnone size-large wp-image-7206" src="http://mikefrobbins.com/wp-content/uploads/2013/04/sg-event1a.png?w=640" alt="sg-event1a" width="640" height="392" /></a>

After re-reading the scenario, I decided that I only wanted to move log files in the first level sub-folders and not in the actual "C:\Application\Log" folder and not in sub-folders deeper than the first level sub-folders so as you can see in the previous image, the <em>-Include</em> parameter doesn't accomplish that task.

I ended up using something similar to the following which retrieves only the files in the first level sub-folders that are older than 90 days without using either the <em>-Filter</em> or <em>-Include</em> parameter:

<a href="http://mikefrobbins.com/wp-content/uploads/2013/04/sg-event1b.png"><img class="alignnone size-full wp-image-7209" src="http://mikefrobbins.com/wp-content/uploads/2013/04/sg-event1b.png" alt="sg-event1b" width="519" height="394" /></a>

<span style="line-height: 1.5;">I'll continue this blog post tomorrow to discuss how I dynamically </span>retrieved this information since the scenario states the folder names could change. I'll discuss how I determined the folder names without making an additional call to the file system. I'll also show you how I used the list of folder names instead of iterating through each file individually to move the files to the correct sub-folder on the destination end.

Want to see my solution? Click this link: Advanced 1: An Archival Atrocity: http://scriptinggames.org/entrylist.php?entryid=279. You'll have to be signed in to see the solution. If you like my script, please vote for it!

<span style="text-decoration: underline;">Update 02/09/14</span>
The link to my solution is no longer valid so I’m posting it here since I’ve received some requests for it:
<pre class="lang:ps decode:true " title="Advanced Category Event #1 of the 2013 Scripting Games">function Move-LogFile {

&lt;#
.SYNOPSIS
Move files with a .log extension from the SourcePath to the DestinationPath that are older than Days. 
.DESCRIPTION
Move-LogFile is a function that moves files with a .log extension from the first level subfolders that are specified via
the SourcePath parameter to the same subfolder name in the DestinationPath parameter that are older than the number of days
specified in the Days parameter. If a subfolder does not exist in the destination with the same name as the source, it will
be created. This function requires PowerShell version 3.
.PARAMETER SourcePath
The parent path of the subfolders where the log files reside. Log files in the actual SourcePath folder will not be archived,
only first level subfolders of the specified SourcePath location.
.PARAMETER DestinationPath
Parent Path of the Destination folder to archive the log files. The name of the original subfolder where the log files reside
will be created if it doesn't already exist in the Destination folder. Destination subfolders are only created if one or more
files need to be archived based on the days parameter. Empty subfolders are not created until needed.
.PARAMETER Days
Log files not written to in more than the number of days specified in this parameter are moved to the destination folder location.
.PARAMETER Force
Switch parameter that when specified overwrites destination files if they already exist.
.EXAMPLE
Move-LogFile -SourcePath 'C:\Application\Log' -DestinationPath '\\NASServer\Archives' -Days 90
.EXAMPLE
Move-LogFile -SourcePath 'C:\Application\Log' -DestinationPath '\\NASServer\Archives' -Days 90 -Force
#&gt;

    [CmdletBinding()]
    param (
        [string]$SourcePath = 'C:\Application\Log',
        [string]$DestinationPath = '\\NASServer\Archives',
        [int]$Days = 90,
        [switch]$Force
    )

    BEGIN {        
        Write-Verbose "Retrieving a list of files to be archived that are older than $($Days) days"
        try {
            $files = Get-ChildItem -Path (Join-Path -Path $SourcePath -ChildPath '*\*.log') -ErrorAction Stop |
                     Where-Object LastWriteTime -lt (Get-Date).AddDays(-$days)
        }
        catch {
            Write-Warning $_.Exception.Message
        }

        $folders = $files.directory.name | Select-Object -Unique
        Write-Verbose "A total of $($files.Count) files have been found in $($folders.Count) folders that require archival"
    }

    PROCESS {
        foreach ($folder in $folders) {

            $problem = $false
            $ArchiveDestination = Join-Path -Path $DestinationPath -ChildPath $folder
            $ArchiveSource = Join-Path -Path $SourcePath -ChildPath $folder
            $ArchiveFiles = $files | Where-Object directoryname -eq $ArchiveSource

            if (-not (Test-Path $ArchiveDestination)) {
                Write-Verbose "Creating a directory named $($folder) in $($DestinationPath)"
                try {
                    New-Item -ItemType directory -Path $ArchiveDestination -ErrorAction Stop | Out-Null
                }
                catch {
                    $problem = $true
                    Write-Warning $_.Exception.Message
                }
            }

            if (-not $problem) {
                Write-Verbose "Archiving $($ArchiveFiles.Count) files from $($ArchiveSource) to $($ArchiveDestination)"
                try {
                    If ($Force) {
                        $ArchiveFiles | Move-Item -Destination $ArchiveDestination -Force -ErrorAction Stop
                    }
                    Else {
                        $ArchiveFiles | Move-Item -Destination $ArchiveDestination -ErrorAction Stop
                    }
                }
                catch {
                    Write-Warning $_.Exception.Message
                }
            }

        }
    }

    END {
        Remove-Variable -Name SourcePath, DestinationPath, Days, Force, files, folders, folder,
        problem, ArchiveDestination, ArchiveSource, ArchiveFiles -ErrorAction SilentlyContinue
    }

}</pre>
This PowerShell script can also be downloaded from the TechNet <a href="http://gallery.technet.microsoft.com/scriptcenter/2013-Scripting-Games-b40bf776" target="_blank" rel="noopener">script repository</a>.

µ