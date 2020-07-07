---
ID: 9857
post_title: >
  How to Create Reusable PowerShell
  Functions for Microsoft SQL Server
  Database Administration
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2014/05/08/how-to-create-reusable-powershell-functions-for-microsoft-sql-server-database-administration/
published: true
post_date: 2014-05-08 07:30:23
---
In my last blog article on "<a href="http://mikefrobbins.com/2014/04/24/getting-started-with-administering-and-managing-microsoft-sql-server-with-powershell/" target="_blank">Getting Started with Administering and Managing Microsoft SQL Server with PowerShell</a>", I left off by introducing you to how to query information about your SQL Server using PowerShell with SQL Server Management Objects (SMO).

In this blog article, I'll pick up where I left off and write a reusable PowerShell function to return information about SQL Server files and file groups while making the use of SMO transparent to the user of the function.

I'm going to start off by importing the SQL Server 2014 PowerShell module that's installed on the Windows 8.1 workstation that's being used in this demonstration. For specifics about this, see the previous blog article that was referenced.
<pre class="lang:ps decode:true">Import-Module -Name SQLPS -DisableNameChecking</pre>
Create a new SMO Object in PowerShell and store it in a variable:
<pre class="lang:ps decode:true">$SQL = New-Object('Microsoft.SqlServer.Management.Smo.Server') -ArgumentList 'SQL01'</pre>
It looks like the information can be retrieved like this:
<pre class="lang:ps decode:true">$sql.databases |
Select-Object -First 1 -Property Name,
                                 @{label='FileGroups';expression={$_.filegroups}},
                                 @{label='Files';expression={$_.filegroups.files}}</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/05/sql-050614-1a.jpg"><img class="alignnone size-full wp-image-9860" src="http://mikefrobbins.com/wp-content/uploads/2014/05/sql-050614-1a.jpg" alt="sql-050614-1a" width="877" height="180" /></a>

The problem with retrieving it that way is it doesn't really scale since when more than one item is returned, you end up with a collection of items. So what you need to do is to iterate through each item that could be a collections of items (databases, filegroups, and files), create a custom object, and then return that object:
<pre class="lang:ps decode:true">Import-Module -Name SQLPS -DisableNameChecking
$SQL = New-Object('Microsoft.SqlServer.Management.Smo.Server') -ArgumentList $ComputerName

foreach ($database in $SQL.Databases) {

    foreach ($filegroup in $database.filegroups) {

        foreach ($file in $filegroup.files) {

            [PSCustomObject]@{
                DatabaseName = $database.Name
                FileGroup = $filegroup
                Name = $file.Name
                PrimaryFile = $file.IsPrimaryFile                  
            }

        }
    }
}</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/05/sql-050614-2.jpg"><img class="alignnone size-full wp-image-9862" src="http://mikefrobbins.com/wp-content/uploads/2014/05/sql-050614-2.jpg" alt="sql-050614-2" width="877" height="431" /></a>

That looks better, but it's very static. It would need to be modified manually each time for a different database server name or SQL instance and do you really want a junior level DBA or System Administrator that you might delegate this task to, to be manually modifying the PowerShell code? I didn't think so. That's why this function needs to be turned into a parameterized function so the values such as ComputerName (ServerName) can be specified dynamically on the fly with no modification to the code whatsoever:
<pre class="lang:ps decode:true">function Get-MrDbFileInfo {

    [CmdletBinding()]
    param (
        [Parameter(Mandatory)]
        [Alias('ServerName')]
        [string]$ComputerName
    )

    Import-Module -Name SQLPS -DisableNameChecking
    $SQL = New-Object('Microsoft.SqlServer.Management.Smo.Server') -ArgumentList $ComputerName

    foreach ($database in $SQL.Databases) {

        foreach ($filegroup in $database.filegroups) {

            foreach ($file in $filegroup.files) {

                [PSCustomObject]@{
                    DatabaseName = $database.Name
                    FileGroup = $filegroup
                    Name = $file.Name
                    PrimaryFile = $file.IsPrimaryFile                  
                }

            }
        }
    }
}</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/05/sql-050614-3.jpg"><img class="alignnone size-full wp-image-9864" src="http://mikefrobbins.com/wp-content/uploads/2014/05/sql-050614-3.jpg" alt="sql-050614-3" width="877" height="374" /></a>

That's a little better. The function is named <em>Get-MrDbFileInfo</em> and it's been saved in a ps1 file named Get-MrDbFileInfo.ps1. I used my initials (Mr for Mike Robbins) to help prevent name collisions with other functions that someone else may have created that could have the same name (<em>Get-DbFileInfo</em>).

In the previous example, the function was dot sourced and then run against a database server running SQL Server 2014 named SQL01, it was then run against another database server running SQL Server 2008 R2 named SQL02.

The problem now is what if you wanted to run it against a named instance instead of the default one and maybe you're only looking for information about a specific database?
<pre class="lang:ps decode:true">function Get-MrDbFileInfo {

    [CmdletBinding()]
    param (
        [Parameter(Mandatory)]
        [Alias('ServerName')]
        [string]$ComputerName,

        [ValidateNotNullOrEmpty()]
        [string]$InstanceName = 'Default',
        
        [ValidateNotNullOrEmpty()]
        [string[]]$DatabaseName = '*'
    )

    Import-Module -Name SQLPS -DisableNameChecking

    if ($InstanceName -eq 'Default') {
                $SQLInstance = $ComputerName
    }
    else {
        $SQLInstance = "$ComputerName\$InstanceName"
    }

    $SQL = New-Object('Microsoft.SqlServer.Management.Smo.Server') -ArgumentList $SQLInstance
    $databases = $SQL.Databases | Where-Object Name -like "$DatabaseName"

    foreach ($database in $databases) {

        foreach ($filegroup in $database.filegroups) {

            foreach ($file in $filegroup.files) {

                [PSCustomObject]@{
                    DatabaseName = $database.Name
                    FileGroup = $filegroup
                    Name = $file.Name
                    PrimaryFile = $file.IsPrimaryFile                  
                }
            }
        }
    }
}</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/05/sql-050614-4.jpg"><img class="alignnone size-full wp-image-9869" src="http://mikefrobbins.com/wp-content/uploads/2014/05/sql-050614-4.jpg" alt="sql-050614-4" width="877" height="382" /></a>

With all this additional functionality that's being added to this function, it's becoming a versatile, reusable tool to query file and filegroup related information from your SQL servers. No need for the user of it to make any manual changes if they want to query information for a specific database server, named instance of SQL, specific database, multiple databases, or to use wildcards in the database name to return information for multiple databases with similar names without having to manually specify each one.

More polish is still needed though. Being able to accept pipeline input on the database name parameter would be nice so the output of one command that returns database names can be piped into this function. Error handling and comment based help are needed as well.
<pre class="lang:ps decode:true" title="Get-MrDbFileInfo">#Requires -Version 3.0
function Get-MrDbFileInfo {

&lt;#
.SYNOPSIS
    Returns database file information for a Microsoft SQL Server database. 

.DESCRIPTION
    Get-MrDbFileInfo is a function that returns the database file information
    for one or more Microsoft SQL Server databases.

.PARAMETER ComputerName
    The computer that is running Microsoft SQL Server that your targeting to
    query database file information on.

.PARAMETER InstanceName
    The instance name of SQL Server to return database file informmation for.
    The default is the default SQL Server instance.
 
.PARAMETER DatabaseName
    The database(s) to return file informmation for. The default is all databases.

.EXAMPLE
    Get-MrDbFileInfo -ComputerName sql01
 
.EXAMPLE
     Get-MrDbFileInfo -ComputerName sql01 -DatabaseName master, msdb, model

.EXAMPLE
     Get-MrDbFileInfo -ComputerName sql01 -InstanceName MrSQL -DatabaseName master,
     msdb, model
 
.EXAMPLE
    'master', 'msdb', 'model' | Get-MrDbFileInfo -ComputerName sql01
 
.INPUTS
    String
 
.OUTPUTS
    PSCustomObject

.NOTES
    Author:  Mike F Robbins
    Website: http://mikefrobbins.com
    Twitter: @mikefrobbins
#&gt;

    [CmdletBinding()]
    param (
        [Parameter(Mandatory)]
        [Alias('ServerName')]
        [string]$ComputerName,

        [ValidateNotNullOrEmpty()]
        [string]$InstanceName = 'Default',
        
        [Parameter(ValueFromPipeline)]
        [ValidateNotNullOrEmpty()]
        [string[]]$DatabaseName = '*'
    )

    BEGIN {
        $problem = $false

        if (-not (Get-Module -Name SQLPS)) {          
            try {
                Import-Module -Name SQLPS -DisableNameChecking -ErrorAction Stop
            }
            catch {
                $problem = $true
                Write-Warning -Message "An error has occured.  Error details: $_.Exception.Message"
            }
        }
        
        if (-not ($problem)) {
            if ($InstanceName -eq 'Default') {
                $SQLInstance = $ComputerName
            }
            else {
                $SQLInstance = "$ComputerName\$InstanceName"
            }

            $SQL = New-Object('Microsoft.SqlServer.Management.Smo.Server') -ArgumentList $SQLInstance
        }                
    }
    
    PROCESS {

        try {
            $databases = $SQL.Databases | Where-Object Name -like "$DatabaseName"
        }
        catch {
            $problem = $true
            Write-Warning -Message "An error has occured.  Error details: $_.Exception.Message"
        }

        if (-not $problem) {
            foreach ($database in $databases) {
                Write-Verbose -Message "Retrieving information for database: $database."

                foreach ($filegroup in $database.filegroups) {
                    Write-Verbose -Message "Retrieving information for filegroup: $filegroup."

                    foreach ($file in $filegroup.files) {
                        Write-Verbose -Message "Retrieving information for file: $file."

                        [PSCustomObject]@{
                            ComputerName = $SQL.Information.ComputerNamePhysicalNetBIOS
                            InstanceName = $(if ($SQL.InstanceName){$SQL.InstanceName} else{'Default'})
                            DatabaseName = $database.Name
                            FileGroup = $filegroup
                            Name = $file.Name
                            FileName = $file.FileName
                            DefaultPath = $(if (($file.filename -replace '[^\\]+$') -eq ($SQL.DefaultFile)){'True'} else{'False'})
                            PrimaryFile = $file.IsPrimaryFile
                            'Size(MB)' = '{0:N2}' -f ($file.Size / 1KB)
                            'FreeSpace(MB)' = '{0:N2}' -f ($file.AvailableSpace / 1KB)
                            MaxSize = $file.MaxSize
                            "Growth($($file.GrowthType))" = $file.Growth
                            'VolumeFreeSpace(GB)' = '{0:N2}' -f ($file.VolumeFreeSpace / 1MB)
                            NumberOfDiskReads = $file.NumberOfDiskReads
                            'ReadFromDisk(MB)' = '{0:N2}' -f ($file.BytesReadFromDisk / 1MB)
                            NumberOfDiskWrites = $file.NumberOfDiskWrites
                            'WrittenToDisk(MB)' = '{0:N2}' -f ($file.BytesWrittenToDisk / 1MB)
                            ID = $file.ID                    
                            Offline = $file.IsOffline                    
                            ReadOnly = $file.IsReadOnly
                            ReadOnlyMedia = $file.IsReadOnlyMedia
                            Sparse = $file.IsSparse
                            DesignMode = $file.IsDesignMode
                            Parent = $file.Parent                    
                            State = $file.State
                            UsedSpace = $file.UsedSpace
                            UserData = $file.UserData                    
                        }
                    }
                }
            }
        }
    }
}</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/05/sql-050614-5.jpg"><img class="alignnone size-full wp-image-9872" src="http://mikefrobbins.com/wp-content/uploads/2014/05/sql-050614-5.jpg" alt="sql-050614-5" width="877" height="203" /></a>

As you can see, the error handling is working as I forgot which database server my MrSharePoint SQL instance was installed on. I was also able to pipe the database name in and not only pipe it in, but pipe it in with a wildcard.

The problem now is that too may properties have been added and I only want certain properties returned by default in the table and list views:

<a href="http://mikefrobbins.com/wp-content/uploads/2014/05/sql-050614-6a.jpg"><img class="alignnone size-full wp-image-9912" src="http://mikefrobbins.com/wp-content/uploads/2014/05/sql-050614-6a.jpg" alt="sql-050614-6a" width="877" height="441" /></a>

I also don't want to have to dot source the function every time I want to use it. I'll resolve these issues in my next blog article by creating a module, module manifest, and custom formatting via a .ps1xml file.

µ