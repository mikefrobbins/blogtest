---
ID: 7216
post_title: >
  Lessons Learned from the Scripting Games
  Advanced Event 1
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2013/05/02/lessons-learned-from-the-scripting-games-advanced-event-1/
published: true
post_date: 2013-05-02 07:30:22
---
This is a continuation from my previous blog titled "<a href="http://mikefrobbins.com/2013/04/30/2013-powershell-scripting-games-advanced-event-1-parameters-dont-always-work-as-expected/" target="_blank">2013 PowerShell Scripting Games Advanced Event 1 – Parameters Don’t Always Work As Expected</a>".

This isn't the exact script, but sections of it. You'll notice at the bottom of the first image shown below, I retrieve the list of folder names from the files variable to keep from having to make another call to the file system. Going from one variable to another in memory is a cheap operation where as going to disk to retrieve something is more expensive from a resources standpoint. I received this comment from a reviewer: <em>"I thought this was pretty clever: $folders = $files.directory.name | Select-Object -Unique"</em>.

<a href="http://mikefrobbins.com/wp-content/uploads/2013/05/sg-event1ca.png"><img class="alignnone size-large wp-image-7223" alt="sg-event1ca" src="http://mikefrobbins.com/wp-content/uploads/2013/05/sg-event1ca.png?w=640" width="640" height="140" /></a>

Then I iterate through the list of folders one at a time instead of iterating through each file individually. I build my paths, storing them in variables. I check to see if the folder exists and create it if not, and then move the files a folder at a time. My goal was to make as few calls to the file system as possible.

<a href="http://mikefrobbins.com/wp-content/uploads/2013/05/sg-event1d.png"><img class="alignnone size-large wp-image-7225" alt="sg-event1d" src="http://mikefrobbins.com/wp-content/uploads/2013/05/sg-event1d.png?w=640" width="640" height="135" /></a>

It's not only about writing a command that's syntax is correct, or a command that works, but also a command that's as efficient as possible. I've trimmed out the error checking and verbose output from these examples to make them easier to understand.

<span style="line-height: 1.5;">Want to see my actual solution? Click this link: </span><span style="line-height: 1.5;">Advanced 1: An Archival Atrocity: http://scriptinggames.org/entrylist.php?entryid=279</span><span style="line-height: 1.5;">. You’ll have to be signed in to see the solution. If you like my script, please vote for it!</span>

<span style="text-decoration: underline;">Update 02/09/14</span>
The link to my solution is no longer valid so I’m posting it here since I’ve received some requests for it:
<pre class="lang:ps decode:true crayon-selected" title="Advanced Category Event #1 of the 2013 Scripting Games">function Move-LogFile {

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
This PowerShell script can also be downloaded from the TechNet <a href="http://gallery.technet.microsoft.com/scriptcenter/2013-Scripting-Games-b40bf776" target="_blank">script repository</a>.

µ