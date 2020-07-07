---
ID: 12708
post_title: >
  Using Pester and the Operation
  Validation Framework to Verify a System
  is Working
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2015/11/12/powershell-using-pester-tests-and-the-operation-validation-framework-to-verify-a-system-is-operational/
published: true
post_date: 2015-11-12 07:30:33
---
If you haven't seen the <a href="https://github.com/PowerShell/Operation-Validation-Framework" target="_blank">Operation Validation Framework</a> on GitHub, it's definitely something worth taking a look at. This framework allows you to write Pester tests to perform end-to-end validation that a system is operating properly. Pester is typically used to perform <a href="https://en.wikipedia.org/wiki/Test-driven_development" target="_blank">test driven development</a> or <a href="https://en.wikipedia.org/wiki/Unit_testing" target="_blank">unit testing</a> on your PowerShell code, but in this scenario it's used for operational testing.

You need to have <a href="https://github.com/pester/Pester" target="_blank">Pester</a> installed, but if you're running Windows 10 then you already have Pester. Download the <a href="https://github.com/PowerShell/Operation-Validation-Framework" target="_blank">Operation Validation Framework</a> and place it in your $env:PSModulePath just like you would any other module.

Write your operational tests:
<pre class="lang:ps decode:true ">Describe "Simple Validation of a SQL Server" {
    
    $ServerName = 'SQL04'
    $Session = New-PSSession -ComputerName $ServerName
    
    It "The SQL Server service should be running" {
        (Invoke-Command -Session $Session {Get-Service -Name MSSQLSERVER}).status |
        Should be 'Running'
    }
    It "The SQL Server agent service should be running" {
        (Invoke-Command -Session $Session {Get-Service -Name SQLSERVERAGENT}).status  |
        Should be 'Running'
    }
    It "Should be listening on port 1433" {
        Test-NetConnection -ComputerName $ServerName -Port 1433 -InformationLevel Quiet |
        Should be $true
    }
    It "Should be able to query information from the SQL Server" {
        (Invoke-MrSqlDataReader -ServerInstance $ServerName -Database Master -Query "select name from sys.databases where name = 'master'").name |
        Should be 'master'
    }
    Remove-PSSession -Session $Session
}</pre>
The last test uses a function that can be found in <a href="https://github.com/mikefrobbins/SQL" target="_blank">my MrSQL module</a> on GitHub if your interested in it. I'm simply using it to remove the requirement of needing the SQLPS module installed to query a SQL Server with PowerShell.

One thing I would like to figure out is how to skip tests (set dependencies) based on the results of previous tests in my Pester tests since all of the subsequent tests will fail if the first one fails in this scenario and those failures take a lot more time to return results than successes do. Based on some responses to a tweet of mine, skipping tests may not be the best way to approach this, it may be better to run a simple test and only run a comprehensive test if the simple one completes successfully. I noticed that <em>Invoke-OperationValidation</em> has a <em>TestType</em> parameter and the valid values for it are <em>simple</em> and <em>comprehensive</em> with the default being both <em>@('Simple', 'Comprehensive')</em> so I'll plan to research that option.

Now to run the operational tests using the <em>Invoke-OperationValidation</em> function. Omitting the <em>IncludePesterOutput</em> parameter would eliminate the top portion of the results (the top portion of the output is what you would typically see for results when using Pester).
<pre class="lang:ps decode:true">Invoke-OperationValidation -testFilePath C:\tests\SQLServer\sqlserver.tests.ps1 -IncludePesterOutput</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/11/op-validate1a.jpg"><img class="alignnone size-full wp-image-12712" src="http://mikefrobbins.com/wp-content/uploads/2015/11/op-validate1a.jpg" alt="op-validate1a" width="859" height="287" /></a>

I came up with this scenario because I recently experienced a problem with a SQL Server where all of the necessary services were running and it was listening on port 1433, but it was unresponsive when trying to query actual data from any of its databases.

Operational tests can not only be used to test a single system as you've seen in this scenario, but to also perform end-to-end validation of system operation where you have multiple tiers such as web front-end servers, application servers, reporting servers, and back-end database servers along with other things that systems like that depend on such as directory services and network connectivity.

Just think, you could use these types of operational tests to check the amount of latency a system is experiencing and automatically spin up or remove VM's or containers as needed once a certain threshold is reached. You could also take corrective action based on failing tests. It's my understanding that the default output from <em>Invoke-OperationValidation</em> is returned in a format that can be used by other monitoring systems.

I knew learning how to write Pester tests would pay off, not only in the form of writing better code with fewer bugs in it especially when changes are made to it down the road, but now we're starting to see other uses for Pester tests such as this in the form of operational tests.

By the way, I've been blogging on this site since 2009 and today's blog article is number 400.

µ