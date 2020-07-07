---
ID: 13990
post_title: 'Why isn&#8217;t Test Driven Development more widely adopted and accepted by the PowerShell community?'
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2016/05/12/why-isnt-test-driven-development-more-widely-adopted-and-accepted-by-the-powershell-community/
published: true
post_date: 2016-05-12 07:30:30
---
We've all heard that TDD (<a href="https://en.wikipedia.org/wiki/Test-driven_development" target="_blank">Test Driven Development</a>) means that you write unit tests before writing any code. Most of us are probably writing functional or acceptance tests after the fact because the idea of Test Driven Development isn't clearly defined, at least not in my opinion. I originally thought it meant to write thorough unit tests to test all functionality for a specific piece of code such as a PowerShell function from start to finish before writing any of the production code for the function itself. This would be very difficult to accomplish in the real world and in my opinion, it's one of the reasons Test Driven Development isn't more widely adopted.

The idea of writing all of the tests first is a false premise and maybe a misconception on my part as well as others. The unit tests are actually written in parallel with the production code. A simple failing test is written first and then just enough production code is written to make that one simple test pass. Then another simple failing test is written and then the production code to make it pass is written and so on. The goal is to write each one in very small, quick, and simple iterations otherwise TDD adds unnecessary overhead and won't be adopted.

My workflow for TDD is shown below. I created this myself (it's NOT some image copied from the Internet).

<a href="http://mikefrobbins.com/wp-content/uploads/2016/05/tdd-workflow875x400.png"><img class="alignnone size-full wp-image-14052" src="http://mikefrobbins.com/wp-content/uploads/2016/05/tdd-workflow875x400.png" alt="tdd-workflow875x400" width="875" height="400" /></a>

My setup for writing PowerShell code when using Test Driven Development is to open two tabs in the PowerShell ISE or <a href="https://www.sapien.com/software/powershell_studio" target="_blank">SAPIEN PowerShell Studio</a>. One of the tabs is for my tests and the other is for a PowerShell function that I'm developing. I also have the PowerShell console open on a separate monitor and I run the following function in the console to assist me in keeping on task as far as my TDD workflow goes:
<pre class="lang:ps decode:true" title="Workflow for Test Driven Development">#Requires -Version 4.0 -Modules Pester
function Invoke-MrTDDWorkflow {

    [CmdletBinding()]
    param (
        [ValidateScript({
          If (Test-Path -Path $_ -PathType Container) {
            $true
          }
          else {
            Throw "'$_' is not a valid directory."
          }
        })]
        [string]$Path = (Get-Location),

        [ValidateNotNullOrEmpty()]
        [int]$Seconds = 30
    )

    Add-Type -AssemblyName System.Windows.Forms
    Clear-Host

    while (-not $Complete) {       
    
        if ((Invoke-Pester -Script $Path -Quiet -PassThru -OutVariable Results).FailedCount -eq 0) {

            if ([System.Windows.Forms.MessageBox]::Show('Is the code complete?', 'Status', 4, 'Question', 'Button2') -eq 'Yes') {
                $Complete = $true
            }
            else {
                $Complete = $False
                Write-Output "Write a failing unit test for a simple feature that doesn't yet exist."
            
                if ($psISE) {
                    [System.Windows.Forms.MessageBox]::Show('Click Ok to Continue')
                }
                else {
                    Write-Output 'Press any key to continue ...'
                    $Host.UI.RawUI.ReadKey('NoEcho, IncludeKeyDown') | Out-Null
                }

                Clear-Host
            }
              
        }
        else {
            Write-Output "Write code until unit test: '$(@($Results.TestResult).Where({$_.Passed -eq $false}, 'First', 1).Name)' passes"
            Start-Sleep -Seconds $Seconds
            Clear-Host
        }    

    }

}</pre>
The most recent version of the <em>Invoke-MrTDDWorkflow</em> function shown in the previous code example can be found in <a href="https://github.com/mikefrobbins/PowerShell" target="_blank">my PowerShell repository on GitHub</a>.

I'll start out with an empty folder:
<pre class="lang:ps decode:true ">Get-ChildItem -Path .\TDD\</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2016/05/tdd-workflow1a.jpg"><img class="alignnone size-full wp-image-13994" src="http://mikefrobbins.com/wp-content/uploads/2016/05/tdd-workflow1a.jpg" alt="tdd-workflow1a" width="859" height="58" /></a>

The <em>Invoke-MrTDDWorkflow</em> function is run, it asks the question "<em>Is the code complete?</em>" since there are currently no failing tests. The default button is "<em>No</em>" so the &lt;enter&gt; key can be pressed instead of having to reach for the mouse which would take more time.
<pre class="lang:ps decode:true">Invoke-MrTDDWorkflow -Path .\TDD\</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2016/05/tdd-workflow2a.jpg"><img class="alignnone size-full wp-image-13996" src="http://mikefrobbins.com/wp-content/uploads/2016/05/tdd-workflow2a.jpg" alt="tdd-workflow2a" width="859" height="254" /></a>

After selecting "<em>No</em>", the following message is displayed:

<a href="http://mikefrobbins.com/wp-content/uploads/2016/05/tdd-workflow3a.jpg"><img class="alignnone size-full wp-image-13999" src="http://mikefrobbins.com/wp-content/uploads/2016/05/tdd-workflow3a.jpg" alt="tdd-workflow3a" width="859" height="60" /></a>

This is where most who are new to TDD make the mistake of thinking they have to write a complete suite of tests to thoroughly test every aspect of the code they're going to write. Some may disagree with me but that methodology is incorrect and it's what drives people away from using TDD. Also keep in mind that you don't necessarily know how the task is going to be accomplished at this point so try to write tests generically enough that the result could be achieved different ways since there are so many different ways to accomplish the same task in PowerShell. This will help prevent having to rewrite tests when code is refactored in the future.

I'm going to write a PowerShell function that doesn't exist yet. With TDD, you're not allowed to write any production code until a test is written for it first. You're also not allowed to write more of a test than is sufficient to fail. Those are the first two laws of TDD. I'll simply write a test to see if the function exists.

Depending on whether or not <em>-ErrorAction SilentlyContinue</em> is included, the test returns more or less output. I prefer including it to minimize the output since I simply want to know the result of the test without all of the details. Lots of tests will be added and run by the time the function is completed and more details per test means more time to determine if each individual test passed or not.
<pre class="lang:ps decode:true ">$here = Split-Path -Parent $MyInvocation.MyCommand.Path
$sut = (Split-Path -Leaf $MyInvocation.MyCommand.Path).Replace('.Tests.', '.')
. "$here\$sut"

Describe 'Get-MrSystemInfo' {
    It 'Should exist' {
        Get-Command -Name Get-MrSystemInfo -ErrorAction SilentlyContinue |
        Should Be $true 
    }
}</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2016/05/tdd-workflow5a.jpg"><img class="alignnone size-full wp-image-14002" src="http://mikefrobbins.com/wp-content/uploads/2016/05/tdd-workflow5a.jpg" alt="tdd-workflow5a" width="859" height="455" /></a>

Now that I have one simple failing test, I need to write code for the <em>Get-MrSystemInfo</em> function until that one test passes. This is the third law of TDD. You are not allowed to write more production code than is required to make the current test pass. I press &lt;enter&gt; in my console session so my workflow goes to the next step and by default it will run the tests every 30 seconds and prompt me with the following message until all tests pass:
<pre class="lang:ps decode:true ">&lt;enter&gt;</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2016/05/tdd-workflow6a.jpg"><img class="alignnone size-full wp-image-14005" src="http://mikefrobbins.com/wp-content/uploads/2016/05/tdd-workflow6a.jpg" alt="tdd-workflow6a" width="859" height="53" /></a>

As mentioned before, I've only written the code required to make the current test pass. No more, no less:
<pre class="lang:ps decode:true">function Get-MrSystemInfo {
}</pre>
The test passes and I'm once again asked: "<em>Is the code complete?</em>" since there are currently no failing tests:

<a href="http://mikefrobbins.com/wp-content/uploads/2016/05/tdd-workflow2a.jpg"><img class="alignnone size-full wp-image-13996" src="http://mikefrobbins.com/wp-content/uploads/2016/05/tdd-workflow2a.jpg" alt="tdd-workflow2a" width="859" height="254" /></a>

I answer no again and repeat the same process of writing another test:

<a href="http://mikefrobbins.com/wp-content/uploads/2016/05/tdd-workflow3a.jpg"><img class="alignnone size-full wp-image-13999" src="http://mikefrobbins.com/wp-content/uploads/2016/05/tdd-workflow3a.jpg" alt="tdd-workflow3a" width="859" height="60" /></a>

The function needs to return the system manufacturer so I add that one single test:
<pre class="lang:ps decode:true">$here = Split-Path -Parent $MyInvocation.MyCommand.Path
$sut = (Split-Path -Leaf $MyInvocation.MyCommand.Path).Replace('.Tests.', '.')
. "$here\$sut"

Describe 'Get-MrSystemInfo' {
    It 'Should exist' {
        Get-Command -Name Get-MrSystemInfo -ErrorAction SilentlyContinue |
        Should Be $true 
    }
    It 'Should return the system manufacturer' {
        (Get-MrSystemInfo).Manufacturer |
        Should Not BeNullOrEmpty
    }
}</pre>
After saving the new test in the <em>Get-MrSystemInfo.Tests.ps1</em> file, I press enter again and a different test is specified in my workflow:
<pre class="lang:ps decode:true ">&lt;enter&gt;</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2016/05/tdd-workflow7a.jpg"><img class="alignnone size-full wp-image-14009" src="http://mikefrobbins.com/wp-content/uploads/2016/05/tdd-workflow7a.jpg" alt="tdd-workflow7a" width="859" height="53" /></a>

I write only the code that makes this new test pass:
<pre class="lang:ps decode:true ">function Get-MrSystemInfo {
    Get-CimInstance -ClassName Win32_ComputerSystem |
    Select-Object -Property Manufacturer
}</pre>
The test passes and I'm asked again if the code is complete. I'm not so I'm back in the same workflow loop as before, writing another failing test. One thing to keep in mind is each iteration of writing a test and then production code to make the test pass should be quick and easy.
<pre class="lang:ps decode:true">$here = Split-Path -Parent $MyInvocation.MyCommand.Path
$sut = (Split-Path -Leaf $MyInvocation.MyCommand.Path).Replace('.Tests.', '.')
. "$here\$sut"

Describe 'Get-MrSystemInfo' {
    It 'Should exist' {
        Get-Command -Name Get-MrSystemInfo -ErrorAction SilentlyContinue |
        Should Be $true 
    }
    It 'Should return the system manufacturer' {
        (Get-MrSystemInfo).Manufacturer |
        Should Not BeNullOrEmpty
    }
    It 'Should return the hard disk size' {
        (Get-MrSystemInfo).'DiskSize(GB)' |
        Should BeOfType [int]
    }
}</pre>
Writing the production code for the function to make this new test pass requires some of the existing code be refactored:
<pre class="lang:ps mark:7 decode:true ">function Get-MrSystemInfo {
    $CS = Get-CimInstance -ClassName Win32_ComputerSystem
    $Disks = Get-CimInstance -ClassName Win32_LogicalDisk -Filter 'DriveType = 3'

    foreach ($Disk in $Disks) {
        [pscustomobject]@{
            Manufacturer = $CS.Maufacturer
            'DiskSize(GB)' = $Disk.Size / 1GB -as [int]
        }
    }
}</pre>
When new code or the refactoring of existing code breaks existing functionality, I can immediately see it as in this scenario where the second test that previously passed is now failing for some reason:

<a href="http://mikefrobbins.com/wp-content/uploads/2016/05/tdd-workflow7a.jpg"><img class="alignnone size-full wp-image-14009" src="http://mikefrobbins.com/wp-content/uploads/2016/05/tdd-workflow7a.jpg" alt="tdd-workflow7a" width="859" height="53" /></a>

The problem is "<em>Manufacturer</em>" was misspelled in the previous code example when it was refactored. This problem has been corrected and now all tests pass.
<pre class="lang:ps decode:true ">function Get-MrSystemInfo {
    $CS = Get-CimInstance -ClassName Win32_ComputerSystem
    $Disks = Get-CimInstance -ClassName Win32_LogicalDisk -Filter 'DriveType = 3'

    foreach ($Disk in $Disks) {
        [pscustomobject]@{
            Manufacturer = $CS.Manufacturer
            'DiskSize(GB)' = $Disk.Size / 1GB -as [int]
        }
    }
}</pre>
You might be thinking the hard drive capacity or size won't do much good without the drive letter and you're right so that would be the next test to write.

Keep repeating this process until the function is complete. The production code can be refactored as much as you want even months down the road, even when your organization has experienced turnover and the person or people who originally wrote the code are no longer with your organization. Simply rerun the unit tests and you'll know if the code still produces the desired results. Best of all, you'll no longer have to worry about breaking production code when adding new features or refactoring existing code in the future.

µ