---
ID: 11924
post_title: >
  Function to Import the SQLPS PowerShell
  Module or Snap-in
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2015/06/25/function-to-import-the-sqlps-powershell-module-or-snap-in/
published: true
post_date: 2015-06-25 07:30:28
---
The SQL Server 2014 basic management tools have been installed on the Windows 8.1 workstation that's being used in this blog article.

When attempting to import the SQLPS (SQL Server PowerShell) module on your workstation, you'll be unable to import it and you'll receive the following error message if the PowerShell script execution policy is set to the default of restricted:
<pre class="lang:ps decode:true">Import-Module -Name SQLPS</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/06/import-sqlmodule1a.jpg"><img class="alignnone size-full wp-image-11927" src="http://mikefrobbins.com/wp-content/uploads/2015/06/import-sqlmodule1a.jpg" alt="import-sqlmodule1a" width="877" height="155" /></a>

<em><span style="color: #ff0000;">Import-Module : File C:\Program Files (x86)\Microsoft SQL Server\120\Tools\PowerShell\Modules\SQLPS\Sqlps.ps1 cannot </span></em><em><span style="color: #ff0000;">be loaded because running scripts is disabled on this system. For more information, see about_Execution_Policies at </span></em><em><span style="color: #ff0000;">http://go.microsoft.com/fwlink/?LinkID=135170.</span></em>
<em><span style="color: #ff0000;">At line:1 char:1</span></em>
<em><span style="color: #ff0000;">+ Import-Module -Name SQLPS</span></em>
<em><span style="color: #ff0000;">+ ~~~~~~~~~~~~~~~~~~~~~~~~~</span></em>
<em><span style="color: #ff0000;"> + CategoryInfo : SecurityError: (:) [Import-Module], PSSecurityException</span></em>
<em><span style="color: #ff0000;"> + FullyQualifiedErrorId : UnauthorizedAccess,Microsoft.PowerShell.Commands.ImportModuleCommand</span></em>

It takes a while for the previous error message to be generated and it's almost like that's one of the last steps that's performed when attempting to import the SQLPS module so I'll plan to check this early on in my function and warn the user as soon as possible.

I recommend setting the script execution policy to RemoteSigned, but you should read the <a href="https://technet.microsoft.com/en-us/library/hh847748.aspx" target="_blank">about_Execution_Policies</a> help topic before changing it.
<pre class="lang:ps decode:true">Set-ExecutionPolicy -ExecutionPolicy RemoteSigned</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/06/import-sqlmodule3a.jpg"><img class="alignnone size-full wp-image-11929" src="http://mikefrobbins.com/wp-content/uploads/2015/06/import-sqlmodule3a.jpg" alt="import-sqlmodule3a" width="877" height="131" /></a>

Once that's done, you'll be able to import the module but you'll receive a warning message about unapproved verbs and the current location is changed. Both are very annoying:
<pre class="lang:ps decode:true">Import-Module -Name SQLPS</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/06/import-sqlmodule2a.jpg"><img class="alignnone size-full wp-image-11928" src="http://mikefrobbins.com/wp-content/uploads/2015/06/import-sqlmodule2a.jpg" alt="import-sqlmodule2a" width="877" height="94" /></a>

<span style="color: #ff0000;"><em>WARNING: The names of some imported commands from the module 'SQLPS' include unapproved verbs that might make them less </em><em>discoverable. To find the commands with unapproved verbs, run the Import-Module command again with the Verbose </em><em>parameter. For a list of approved verbs, type Get-Verb.</em></span>

You could write a block of code to work around this problem and add it to your scripts:
<pre class="lang:ps decode:true ">if (-not(Get-Module -Name SQLPS)) {
    if (Get-Module -ListAvailable -Name SQLPS) {
        Push-Location
        Import-Module -Name SQLPS -DisableNameChecking
        Pop-Location
    }
}</pre>
I've seen other variations of the previous code floating around the Internet, but notice my version uses <a href="http://mikefrobbins.com/2014/01/23/powershell-tip-from-the-head-coach-of-the-2014-winter-scripting-games-design-for-performance-and-efficiency/" target="_blank">the best practice of filtering left</a>. No need to grab a list of all the modules on your machine with <em>Get-Module -ListAvailable</em> only to pipe them to <em>Where-Object</em> to filter them down to the SQLPS module. That's inefficient when compared to filtering left by using <em>Get-Module</em> with the <em>-Name</em> and <em>-ListAvailable</em> parameters.

The problem with adding the previous block of code to all of your SQL related PowerShell scripts is then you'll be adding redundant code to potentially thousands of scripts. Even worse is the nightmare of finding and modifying all of them if you ever need to change that block of code for some reason. Good luck with that or keep reading to learn a better way.

I also want it to load the SQL PSSnapin if the machine has the SQL Server 2008 or 2008 R2 management tools installed instead of the SQL Server 2012 or higher module:
<pre class="lang:ps decode:true">if (-not(Get-PSSnapin -Name SqlServerCmdletSnapin100, SqlServerProviderSnapin100 -ErrorAction SilentlyContinue)) {
    if (Get-PSSnapin -Name SqlServerCmdletSnapin100, SqlServerProviderSnapin100 -Registered -ErrorAction SilentlyContinue) {
        Add-PSSnapin -Name SqlServerCmdletSnapin100, SqlServerProviderSnapin100
    }
}</pre>
I decided to write a function that would take care of all of that for me and by adding it to a script module, I can simple reference the function from any script that I write:
<pre class="lang:ps decode:true " title="Import-MrSqlModule">function Import-MrSqlModule {

&lt;#
.SYNOPSIS
    Imports the SQL Server PowerShell module or snapin.
 
.DESCRIPTION
    Import-MrSQLModule is a PowerShell function that imports the SQLPS PowerShell
    module (SQL Server 2012 and higher) or adds the SQL PowerShell snapin (SQL
    Server 2008 &amp; 2008R2).
 
.EXAMPLE
     Import-MrSqlModule
 
.NOTES
    Author:  Mike F Robbins
    Website: http://mikefrobbins.com
    Twitter: @mikefrobbins
#&gt;

    [CmdletBinding()]
    param ()

    if (-not(Get-Module -Name SQLPS) -and (-not(Get-PSSnapin -Name SqlServerCmdletSnapin100, SqlServerProviderSnapin100 -ErrorAction SilentlyContinue))) {
    Write-Verbose -Message 'SQLPS PowerShell module or snapin not currently loaded'

        if (Get-Module -Name SQLPS -ListAvailable) {
        Write-Verbose -Message 'SQLPS PowerShell module found'

            Push-Location
            Write-Verbose -Message "Storing the current location: '$((Get-Location).Path)'"

            if ((Get-ExecutionPolicy) -ne 'Restricted') {
                Import-Module -Name SQLPS -DisableNameChecking -Verbose:$false
                Write-Verbose -Message 'SQLPS PowerShell module successfully imported'
            }
            else{
                Write-Warning -Message 'The SQLPS PowerShell module cannot be loaded with an execution policy of restricted'
            }
            
            Pop-Location
            Write-Verbose -Message "Changing current location to previously stored location: '$((Get-Location).Path)'"
        }
        elseif (Get-PSSnapin -Name SqlServerCmdletSnapin100, SqlServerProviderSnapin100 -Registered -ErrorAction SilentlyContinue) {
        Write-Verbose -Message 'SQL PowerShell snapin found'

            Add-PSSnapin -Name SqlServerCmdletSnapin100, SqlServerProviderSnapin100
            Write-Verbose -Message 'SQL PowerShell snapin successfully added'

            [System.Reflection.Assembly]::LoadWithPartialName('Microsoft.SqlServer.Smo') | Out-Null
            Write-Verbose -Message 'SQL Server Management Objects .NET assembly successfully loaded'
        }
        else {
            Write-Warning -Message 'SQLPS PowerShell module or snapin not found'
        }
    }
    else {
        Write-Verbose -Message 'SQL PowerShell module or snapin already loaded'
    }

}</pre>
You're probably thinking "That's a lot of code just to load the module or snap-in". Look at the bigger picture! It's not that much when you consider how much space the help and verbose messages are taking up and since I'm only going to write this once, it's actually a lot less code than hard coding just a few lines in hundreds or possibly even thousands of scripts. Not to mention when I need to modify this code, I go to one script module and modify it instead of having to locate and modify those few lines in each of those scripts.

If the SQLPS module or SQL snapin doesn't exist on the machine, you'll receive a warning:

<a href="http://mikefrobbins.com/wp-content/uploads/2015/06/import-sqlmodule4b.jpg"><img class="alignnone size-full wp-image-11934" src="http://mikefrobbins.com/wp-content/uploads/2015/06/import-sqlmodule4b.jpg" alt="import-sqlmodule4b" width="877" height="71" /></a>

If the execution policy is restricted, this warning will be displayed almost immediately:

<a href="http://mikefrobbins.com/wp-content/uploads/2015/06/import-sqlmodule5a.jpg"><img class="alignnone size-full wp-image-11931" src="http://mikefrobbins.com/wp-content/uploads/2015/06/import-sqlmodule5a.jpg" alt="import-sqlmodule5a" width="877" height="70" /></a>

Using the <em>Verbose</em> parameter allows you to see exactly what's occurring otherwise no output is generated if no errors occur:

<a href="http://mikefrobbins.com/wp-content/uploads/2015/06/import-sqlmodule6a.jpg"><img class="alignnone size-full wp-image-11932" src="http://mikefrobbins.com/wp-content/uploads/2015/06/import-sqlmodule6a.jpg" alt="import-sqlmodule6a" width="877" height="214" /></a>

Using verbose output is much better than inline comments because the user of the function can see the messages without having to open the function or script module as in this case and read through possibly thousands of lines of code to find your comments.

Running the same function on a machine with PowerShell version 2 and the SQL 2008R2 client tools loads the SQL PSSnapin and it also loads the SMO (SQL Server Management Objects) .NET assembly:

<a href="http://mikefrobbins.com/wp-content/uploads/2015/06/import-sqlmodule7a.jpg"><img class="alignnone size-full wp-image-11933" src="http://mikefrobbins.com/wp-content/uploads/2015/06/import-sqlmodule7a.jpg" alt="import-sqlmodule7a" width="877" height="112" /></a>

Why? Because I frequently use PowerShell with SMO and I can't ever remember how to load the necessary assembly since this occurs automatically when using the PowerShell module that's included with the SQL Server 2012 and higher management tools.

The <em>Import-MrSqlModule</em> shown in this blog article is part of my <em>MrSQL</em> PowerShell module which can be downloaded from <a href="https://github.com/mikefrobbins/SQL" target="_blank">GitHub</a>.

µ