---
ID: 13908
post_title: >
  Be Mindful of Object Types when Writing
  Unit Tests and Performing Operational
  Validation in PowerShell with Pester
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2016/04/28/be-mindful-of-object-types-when-writing-unit-tests-and-performing-operational-validation-in-powershell-with-pester/
published: true
post_date: 2016-04-28 07:30:54
---
I recently wrote a Pester test that performs some basic operational validation (smoke tests) of SQL Servers. I've previously written similar tests as functions as shown in my "<em><a href="http://mikefrobbins.com/2016/04/14/write-dynamic-unit-tests-for-your-powershell-code-with-pester/" target="_blank">Write Dynamic Unit Tests for your PowerShell Code with Pester</a></em>" blog article, but I decided to write this one as a script with the naming convention that seems to be recommended. The name of this particular test is "<em>Validate-MrSQLServer.Tests.ps1</em>". You're probably thinking "<em>Validate</em>" isn't an approved verb and you're right, but this isn't a function, it's a script.

Just because I decided to write a script instead of a function doesn't mean that I can't take advantage of features such as a Requires statement, parameter input along with parameter validation, and error handling. It just needs to be written as an advanced script like an advanced function that you're probably already familiar with. Pester tests are simply written with PowerShell code, so why not take advantage of any language feature that you want?
<pre class="lang:ps decode:true " title="Validate-MrSQLServer.Tests.ps1">#Requires -Version 3.0 -Modules MrToolKit
[CmdletBinding()]
param (
    [ValidateNotNullOrEmpty()]
    [string[]]$ComputerName = $env:COMPUTERNAME,

    [System.Management.Automation.Credential()]$Credential = [System.Management.Automation.PSCredential]::Empty
)

foreach ($Computer in $ComputerName) {

    Describe "Validation of a SQL Server: $Computer" {
        
        try {
            $Session = New-PSSession -ComputerName $Computer -Credential $Credential -ErrorAction Stop
        }
        catch {
            Write-Warning -Message "Unable to establish a connection to: '$Computer'."
        }

        It 'The SQL Server service should be running' {
            (Invoke-Command -Session $Session {Get-Service -Name MSSQLSERVER}).status |
            Should be 'Running'
        }

        It 'The SQL Server agent service should be running' {
            (Invoke-Command -Session $Session {Get-Service -Name SQLSERVERAGENT}).status  |
            Should be 'Running'
        }

        It 'The SQL Server service should be listening on port 1433' {
            (Test-Port -Computer $Computer -Port 1433).Open |
            Should be $true
        }

        It 'Should be able to query information from the SQL Server' {(
            Invoke-Command -Session $Session {
                if (Get-PSSnapin -Name SqlServerCmdletSnapin* -Registered -ErrorAction SilentlyContinue) {
                    Add-PSSnapin -Name SqlServerCmdletSnapin*
                }
                elseif (Get-Module -Name SQLPS -ListAvailable){
                    Import-Module -Name SQLPS -DisableNameChecking -Function Invoke-Sqlcmd
                }
                else {
                    Throw 'SQL PowerShell Snapin or Module not found'
                }
                Invoke-SqlCmd -Database Master -Query "select name from sys.databases where name = 'master'"
            }
        ).name |
            Should be 'master'
        }

        Remove-PSSession -Session $Session

    }

}</pre>
Woohoo! Everything works great!
<pre class="lang:ps decode:true ">.\Validate-MrSQLServer.Tests.ps1 -ComputerName SQL01, SQL02</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2016/04/object-types1a.jpg"><img class="alignnone size-full wp-image-13920" src="http://mikefrobbins.com/wp-content/uploads/2016/04/object-types1a.jpg" alt="object-types1a" width="859" height="181" /></a>

Not so fast. The first rule of writing unit tests was violated. Write the tests first and make sure they fail before writing any code (functions, scripts, etc). You might ask, how to do that for operational validation since you're not testing any code? The obvious answer would be to write the tests before building the environment but that's not possible when the environment already exists. In that scenario, the answer is to run the tests in a simulated environment where the servers and/or services don't exist.

In this example, I'll simply use the name of a SQL Server that doesn't exist to simulate writing the operational tests before the environment is created:
<pre class="lang:ps decode:true ">.\Validate-MrSQLServer.Tests.ps1 -ComputerName DoesNotExist</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2016/04/object-types2b.jpg"><img class="alignnone size-full wp-image-13925" src="http://mikefrobbins.com/wp-content/uploads/2016/04/object-types2b.jpg" alt="object-types2b" width="859" height="276" /></a>

"<em>Houston, we have a problem.</em>" The problem is that one of the tests passes successfully when it should fail since it's being run against a machine that doesn't exist. In other words we have a false positive. Oh, and by the way, make sure the machine you're testing against really doesn't exist.

The funny thing is that based on the way the code is written, everything looks like it should work. So what's the problem? The problem is an assumption was made for the type of objects that <em>Test-Port</em> returns. The "<em>Open</em>" property returns a string and it's being compared to a Boolean.

Always double check the type of output you're receiving instead of making assumptions:
<pre class="lang:ps decode:true ">Test-Port -computer SQL01 -port 1433 | Get-Member</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2016/04/object-types3a.jpg"><img class="alignnone size-full wp-image-13935" src="http://mikefrobbins.com/wp-content/uploads/2016/04/object-types3a.jpg" alt="object-types3a" width="859" height="262" /></a>

To resolve this problem, the test can be changed to compare the output of <em>Test-Port</em> with the string 'True' instead of the Boolean $true.
<pre class="lang:ps decode:true ">It 'The SQL Server service should be listening on port 1433' {
    (Test-Port -Computer $Computer -Port 1433).Open |
    Should be 'True'
}</pre>
This change is no longer necessary since the creator (<a href="https://twitter.com/proxb" target="_blank">PowerShell MVP Boe Prox</a>) of the <em>Test-Port</em> function that I'm using has updated it so the "<em>Open</em>" property now produces a Boolean instead of a string. The <em>Test-Port</em> function can be downloaded from <a href="https://github.com/proxb/PowerShell_Scripts" target="_blank">Boe's PowerShell Scripts Repository on GitHub</a>. I've added it to my MrToolkit module which can be downloaded from <a href="https://github.com/mikefrobbins/PowerShell" target="_blank">my PowerShell repository on GitHub</a>.

The moral of the story here is don't assume anything and make sure you're comparing apples to apples and not strings to Booleans or vice versa.

The <em>Validate-MrSQLServer.Tests.ps1</em> test shown in this blog article can be downloaded from <a href="https://github.com/mikefrobbins/OperationalValidation" target="_blank">my Operational Validation repository on GitHub</a>.

µ