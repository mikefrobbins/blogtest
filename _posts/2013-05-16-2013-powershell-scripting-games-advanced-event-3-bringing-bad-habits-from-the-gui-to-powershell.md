---
ID: 7353
post_title: '2013 PowerShell Scripting Games Advanced Event 3 &#8211; Bringing Bad Habits from the GUI to PowerShell'
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2013/05/16/2013-powershell-scripting-games-advanced-event-3-bringing-bad-habits-from-the-gui-to-powershell/
published: true
post_date: 2013-05-16 07:30:22
---
I’m seeing a trend with a lot of PowerShell scripters. Many of us have a GUI, non-scripting background and we moved to PowerShell because we didn't want to be button monkeys clicking through the same options in the GUI day in and day out. I've always heard, if you’re going to do something more than once, do it (script it) in PowerShell.

The trend I’m seeing is many are bringing their bad habit of wanting to repeat themselves over and over, day in and day out, to PowerShell. They write the same code over and over. I need a new tool to accomplish a task today. I’ll rewrite the same code that I've written every time I've had to accomplish a similar task again today so there will be fifty thousand copies of similar code out there, each in a different function to accomplish a similar task. No, no, no! Now you’re thinking like a script monkey instead of a button monkey, the only difference is you’re repeating yourself in PowerShell instead of the GUI.

A new day and a new way. Write reusable code, write it once and be done with it! That way when a modification needs to be made, it only needs to be made once or at least a lot less. Redundant data is a bad thing. What? I thought redundant data was what we wanted in IT? No (period). Redundancy of systems to prevent failure and to keep them online in case of a failure is a good thing. Redundancy in the form of backups is another good thing and all of these will assist you in keeping your job, but having multiple copies of the same data that are all being modified independently and not being synchronized is a nightmare.

The scenario for the 2013 Scripting Games Advanced Event #3 can be found here: http://scriptinggames.org/86122e78f3b1473045d1.pdf. I chose to first write a reusable function that would query the logical disk information and return it as generic objects that could be used to accomplish many different tasks. Want to create an HTML report as in this scenario, no problem. Want to display the output on the screen or place it in a text file, or use it as part of a larger project, those aren't problems either, and there's no reason to rewrite another function to retrieve the same information, ever. Hard coding this into the rest of the script or function is effectively the same thing and placing formatting cmdlets in you script or function because then you can't do much else with it. What are you going to pipe the output to if it's producing HTML files and not objects? Nothing, right? At least I can pipe format cmdlets to out cmdlets.

<a href="http://mikefrobbins.com/wp-content/uploads/2013/05/sg2013-event2a.png"><img class="alignnone size-large wp-image-7354" alt="sg2013-event2a" src="http://mikefrobbins.com/wp-content/uploads/2013/05/sg2013-event2a.png?w=640" width="640" height="681" /></a>

Here’s the output of that function:

<a href="http://mikefrobbins.com/wp-content/uploads/2013/05/sg2013-event2b1.png"><img class="alignnone size-full wp-image-7358" alt="sg2013-event2b1" src="http://mikefrobbins.com/wp-content/uploads/2013/05/sg2013-event2b1.png" width="553" height="148" /></a>

Note, I pulled the computer name from the computer with the other information. This is very important. Getting on my soapbox for a moment. What is PowerShell? Ultimately, it's a means to an end. At the end of the day if you don't accomplish the task at hand, it does not matter how perfect your PowerShell code is. The client, customer, or your boss won't care that you did something really cool and used best practices because ultimately the code didn't accomplish the task at hand. We should strive for all the above though, really cool, efficient code that accomplishes the goal your trying to accomplish and do it accurately!

Never depend on someone imputing the real computer name into your tool. It's all too common to have a DNS CNAME record for many machines which is not the true computer name. The IP address or something such as localhost could also be provided. If you're naming your HTML file and displaying the name that was provided via input on the webpage, there's a good chance that it's not the actual "Computer Name". You're already querying WMI and at least for the class I chose, Win32_LogicalDisk, the computer name can be retrieved from it as well so there's not much overhead in retrieving one additional property to achieve a higher level of accuracy. Climbing off my soapbox now.

<span style="line-height: 1.5;">Ultimately, the Get-DiskInformation function would be placed in a separate module. </span><span style="line-height: 1.5;">You can see in the image below that I placed it in the begin block of my advanced script. Never heard of an advanced script? Neither had I. I didn't</span><span style="line-height: 1.5;"> even know you could do all those cool advanced function things in a script. Placing the Get-DiskInformation function in the process block would cause it to run one time for each computer that is piped in so don't put it there because it only needs to be loaded once, then it's in memory and can be called as many times as necessary without having to reload it.</span>

While we're on the subject of a script, you're probably wondering why I chose a script instead of a function for this tool? Think about how it's going to be used, read between the lines in otherwords. Is someone actually going to manually run this script? Probably not. How fast is the data going to be out of date? Possibly very quick since it's a static HTML file. It's probably going to be setup as a scheduled task. That said, if it were a function it would need a script or some code placed inside of the scheduled task to dot source it which could get messy. I thought it would be cleaner to have everything in a script that could be called using PowerShell.exe in task scheduler. To me that approach was cleaner.

Don't forget about validating the path. That seems to be a common problem. I honestly didn't know how to do that myself and I took a few hits in event 1 because of it. <a href="http://powershell.org/wp/2013/05/03/ok-im-impressed-scripting-games-week-1/" target="_blank">Here's an article</a> that Glenn Sizemore wrote that taught me how to validate those types of parameters.

<a href="http://mikefrobbins.com/wp-content/uploads/2013/05/sg2013-event2c.png"><img class="alignnone size-full wp-image-7356" alt="sg2013-event2c" src="http://mikefrobbins.com/wp-content/uploads/2013/05/sg2013-event2c.png" width="484" height="328" /></a>

One small detail I did as well, as shown in the remainder of the script in the following image was to change the computer name to lower case for the output file because I prefer HTML files to be in lower case, but that's just my personal preference. I didn't change the case of the computer name that's displayed on the web page though.

<a href="http://mikefrobbins.com/wp-content/uploads/2013/05/sg2013-event2d.png"><img class="alignnone size-large wp-image-7357" alt="sg2013-event2d" src="http://mikefrobbins.com/wp-content/uploads/2013/05/sg2013-event2d.png?w=640" width="640" height="337" /></a>

Want to know more? I'm speaking this Saturday, May 18th on "PowerShell Fundamentals for Beginners" at <a href="http://www.sqlsaturday.com/220/eventhome.aspx" target="_blank">SQL Saturday #220 in Atlanta</a>.

<span style="text-decoration: underline;">Update 02/09/14</span>
The link to my solution is no longer valid so I’m posting it here since I’ve received some requests for it:
<pre class="lang:ps decode:true crayon-selected" title="Advanced Category Event #3 of the 2013 Scripting Games">&lt;#
.SYNOPSIS
This is a script, not a function! It creates one HTML file per specified ComputerName that contains logical
disk information which are saved in the Path location.
.DESCRIPTION
    New-HTMLDiskReport.ps1 is a script that outputs HTML reports, one HTML file per computer is saved to the
folder specified in via the Path parameter for each computer that is provided via the ComputerName parameter.
A force parameter is was added because this script will probably be setup to run as a scheduled task, running
periodically and the files will need to be overwritten. The default is not to overwrite.
    Why was a script chosen instead of a function for the main functionality? It will probably be setup as a
scheduled tasks and you would have to write a script or embed some PowerShell into the scheduled tasks to dot
source a function and then run it where a script like this one can simply be called directly with the scheduled
task using PowerShell.exe.
    A separate function was chosen for retrieving the disk related data because it is now a reusable function
that could be used with other commands that are written and not just this one. Ultimately, it could be moved
into a module. PowerShell version 2.0 is required to run this script.
    The date will be generated once per computer instead placing it in the begin block and generating it once
for the entire script because the date on the report will not be accurate if this script is run against a
million computers. Calculating the date is a cheap operation which should not impede performance even when
calculating it once per computer.
.PARAMETER ComputerName
Specifies the name of a target computer(s). The local computer is the default. You can also pipe this parameter
value.
.PARAMETER Path
Specifies the file system path where the HTML files will be saved to. This is a mandatory parameter.
.PARAMETER Force
Switch parameter that when specified overwrites destination files if they already exist.
.EXAMPLE
.\New-HTMLDiskReport.ps1 -Path 'c:\inetpub\wwwroot'
.EXAMPLE
.\New-HTMLDiskReport.ps1 -ComputerName 'Server1, 'Server2' -Path 'c:\inetpub\wwwroot' -Force
.EXAMPLE
'Server1', 'Server2' | .\New-HTMLDiskReport.ps1 -Path 'c:\inetpub\wwwroot'
.EXAMPLE
'Server1', 'Server2' | .\New-HTMLDiskReport.ps1 -Path 'c:\inetpub\wwwroot' -Force
.EXAMPLE
Get-Content -Path c:\ServerNames.txt | .\New-HTMLDiskReport.ps1 -Path 'c:\inetpub\wwwroot' -Force
.INPUTS
System.String
.OUTPUTS
None
#&gt;

[CmdletBinding()]
param(
    [Parameter(ValueFromPipeline=$True)]
    [ValidateNotNullorEmpty()]
    [string[]]$ComputerName = $env:COMPUTERNAME,

    [Parameter(Mandatory=$True)]
    [ValidateScript({Test-Path $_ -PathType 'Container'})]
    [string]$Path,

    [switch]$Force
)

BEGIN {
    $Params = @{
        ErrorAction = 'Stop'
        ErrorVariable = 'Issue'
    }

    function Get-DiskInformation {

    &lt;#
    .SYNOPSIS
    Retrieves logical disk information to include SystemName, Drive, Size in Gigabytes, and FreeSpace in Megabytes.
    .DESCRIPTION
    Get-DiskInformation is a function that retrieves logical disk information from machines that are provided via the
    computer name parameter. This function is designed for re-usability and could possibly be moved into a module at
    a later date. PowerShell version 2.0 is required to run this function.
    .PARAMETER ComputerName
    Specifies the name of a target computer. The local computer is the default. 
    .EXAMPLE
    Get-DiskInformation -ComputerName 'Server1'
    .INPUTS
    None
    .OUTPUTS
    System.Management.Automation.PSCustomObject
    #&gt;

        [CmdletBinding()]
        param(
            [ValidateNotNullorEmpty()]
            [string]$ComputerName = $env:COMPUTERNAME
        )

        $Params = @{
            ComputerName = $ComputerName
            NameSpace = 'root/CIMV2'
            Class = 'Win32_LogicalDisk'
            Filter = 'DriveType = 3'
            Property = 'DeviceID', 'Size', 'FreeSpace', 'SystemName'
            ErrorAction = 'Stop'
            ErrorVariable = 'Issue'
        }

        try {
            Write-Verbose -Message "Attempting to query logical disk information for $ComputerName."
            $LogicalDisks = Get-WmiObject @Params
            Write-Verbose -Message "Test-Connection was not used because it does not test DCOM connectivity, only ping."
        }
        catch {
            Write-Warning -Message "$Issue.Message.Exception"
        }

        foreach ($disk in $LogicalDisks){

        $DiskInfo = @{
            'SystemName' = $disk.SystemName
            'Drive' = $disk.DeviceID
            'Size(GB)' = "{0:N2}" -f ($disk.Size / 1GB)
            'FreeSpace(MB)' = "{0:N2}" -f ($disk.FreeSpace / 1MB)
        }

        New-Object PSObject -Property $DiskInfo

        }
    }

}

PROCESS {
    foreach ($Computer in $ComputerName) {

        $Problem = $false
        $Params.ComputerName = $Computer

        try {
            Write-Verbose -Message "Calling the Get-DiskInformation function."
            $DiskSpace = Get-DiskInformation @Params 
        }
        catch {
            $Problem = $True
            Write-Warning -Message "$Issue.Exception.Message"
        }

        if (-not($Problem)) {

            $DiskHTML = $DiskSpace | Select-Object -Property Drive, 'Size(GB)', 'FreeSpace(MB)' | ConvertTo-HTML -Fragment | Out-String
            $MachineName = $DiskSpace | Select-Object -ExpandProperty SystemName -Unique
            $Params.remove("ComputerName")

            $HTMLParams = @{
                'Title'="Drive Free Space Report"
                'PreContent'="&lt;H2&gt;Local Fixed Disk Report for $($MachineName) &lt;/H2&gt;"
                'PostContent'= "$DiskHTML &lt;HR&gt; $(Get-Date)"
            }

            try {
                Write-Verbose -Message "Attempting to create the filepath variable."
                $FilePath = Join-Path -Path $Path -ChildPath "$($MachineName.ToLower()).html" @Params

                Write-Verbose -Message "Attempting to convert to HTML and write the file to the $FilePath folder for $Computer"
                ConvertTo-HTML @HTMLParams | Out-File -FilePath $FilePath -NoClobber:(-not($Force)) @Params
            }
            catch {
                Write-Warning -Message "Use the -Force parameter to overwrite existing files. Error details: $Issue.Message.Exception"
            }
        }
    }
}</pre>
This PowerShell script can also be downloaded from the TechNet <a href="http://gallery.technet.microsoft.com/scriptcenter/2013-Scripting-Games-de5ab47c" target="_blank">script repository</a>.

µ