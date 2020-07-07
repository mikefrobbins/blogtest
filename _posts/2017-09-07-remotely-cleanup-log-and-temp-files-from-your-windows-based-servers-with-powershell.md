---
ID: 15565
post_title: >
  Remotely cleanup log and temp files from
  your Windows based servers with
  PowerShell
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2017/09/07/remotely-cleanup-log-and-temp-files-from-your-windows-based-servers-with-powershell/
published: true
post_date: 2017-09-07 07:30:24
---
Setting up a scheduled task or job on individual servers to cleanup log and temp files just doesn't scale very well because you have scheduled tasks or jobs setup on lots of individual servers that need to be maintained. Today it's this server and tomorrow it's two or three more. It's much easier to setup one scheduled task or job on a server that's designed for management to remotely cleanup the desired files on all of your servers.

The following log and temp file cleanup script is provided as a proof of concept and should be thoroughly tested before running it against any production systems. You should also test your backups by performing a restore in a sandbox environment even once you've tested a script like this. In other words, always have a back-out plan.

This script can be setup as a scheduled task or job. It retrieves a list of server names from a text file located on a centralized file server. It adds different parameters based on if the path is for log or temp files. It also tests for the specified paths since not all servers will be web servers, but if the web server role is added to an existing server, those log files will be removed without any additional modifications. When new servers are added, simply add them to the text file. That could also be automated depending on the structure of your Active Directory environment by querying Active Directory for a list of server names.
<pre class="lang:ps decode:true">Invoke-Command -ComputerName (Get-Content -Path '\\Server01\share\server-list.txt') {
    $Paths = "$env:SystemDrive\inetpub\logs", "$env:windir\System32\LogFiles", "$env:windir\Temp"
            
    foreach ($Path in $Paths) {
        $Params = @{
            Path = $Path
            Recurse = $true
        }
        
        if ($Path -match 'log') {
            $Params.Include = '*.log'
            $Date = (Get-Date -OutVariable Now).AddDays(-90)
        }
        else {
            $Date = (Get-Date -OutVariable Now).AddDays(-7)
        }
    
        if (Test-Path -Path $Path -PathType Container) {
            Get-ChildItem @Params |
            Where-Object {$_.CreationTime -le $Date} -OutVariable Results |
            Remove-Item -Force -ErrorAction SilentlyContinue
            
            $Results | Export-Csv -Path "$env:windir\Temp\files-removed-$($($Now).ToString('MMddyy')).csv" -Append -NoTypeInformation
            
        }
    }
}</pre>
The results are stored in a CSV file located in the temp folder on the server where the files were removed from. The files containing the results are cleaned up along with the other temporary files based on the specified interval in the script.

µ