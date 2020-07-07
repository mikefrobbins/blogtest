---
ID: 10603
post_title: >
  Using Pester for Test Driven Development
  in PowerShell
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2014/10/09/using-pester-for-test-driven-development-in-powershell/
published: true
post_date: 2014-10-09 07:30:51
---
How do you normally write your PowerShell code? Your answer is probably like mine and most other IT pros. You have a task that you need to accomplish and instead of performing a clicker-size in the GUI whenever that task is required to be performed, you write a PowerShell one-liner, script, or function to automate that task. You test it a little to make sure there aren't any major issues and you implement it, moving on to the next task.

Have you heard of a PowerShell module named <a href="https://github.com/pester/Pester" target="_blank">Pester</a>? I'd heard of Pester in the past but never really got past the developer oriented terms that were associated with it. I was missing out big time and didn't know it and you might be too.

Pester is a PowerShell module designed for performing <a href="http://en.wikipedia.org/wiki/Unit_testing" target="_blank">unit testing</a> on your PowerShell code. Unit testing? What? I can hear most of you now saying "I'm not a developer". Well, neither am I which is why I added a link to an article on Wikipedia about that subject.

While we're on the subject of not being a developer, I've started using the phrase "writing PowerShell code" to refer to writing PowerShell one-liners, scripts, functions, etc. That's what you're writing so get over it IT pros because you can either learn to write some PowerShell code or find a new profession.

While I'm throwing out those nasty developer oriented terms, here's another one: TDD (<a href="http://en.wikipedia.org/wiki/Test-driven_development" target="_blank">Test-driven development</a>). Once again, I've linked this foreign term to an article on Wikipedia on the subject for us IT pros. Go read those articles &lt;now&gt;.

Here's the thought process: Instead of sitting down and writing code to perform some task or resolve a problem, why not write down what your trying to accomplish in the form of tests before jumping in head first and writing code to fix the problem you think you have (you might actually create more problems otherwise). Make sure all of those tests fail and then write PowerShell code to accomplish the necessary tasks until all of the tests pass.

Sounds interesting right? Today is a new day for us IT pros &lt;whether you like it or not&gt;.

First, start out by <a href="https://github.com/pester/Pester" target="_blank">downloading the Pester PowerShell module from GitHub</a>:
<pre class="lang:ps decode:true ">Invoke-WebRequest -Uri 'https://github.com/pester/Pester/archive/master.zip' -OutFile "$env:userprofile\Downloads\pester.zip" -Verbose</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/10/pester1.jpg"><img class="alignnone size-full wp-image-10604" src="http://mikefrobbins.com/wp-content/uploads/2014/10/pester1.jpg" alt="pester1" width="859" height="97" /></a>

Unblock the downloaded zip file:
<pre class="lang:ps decode:true ">Unblock-File -Path "$env:userprofile\Downloads\pester.zip" -Verbose</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/10/pester2.jpg"><img class="alignnone size-full wp-image-10605" src="http://mikefrobbins.com/wp-content/uploads/2014/10/pester2.jpg" alt="pester2" width="859" height="73" /></a>

Extract the files contained in the downloaded zip file to a path in your $env:PSModulePath:
<pre class="lang:ps decode:true ">Expand-Archive -Path "$env:userprofile\Downloads\pester.zip" -DestinationPath "$env:ProgramFiles\WindowsPowershell\Modules" -Verbose</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/10/pester3.jpg"><img class="alignnone size-full wp-image-10606" src="http://mikefrobbins.com/wp-content/uploads/2014/10/pester3.jpg" alt="pester3" width="859" height="634" /></a>

Rename the "Pester-master" folder to "Pester":
<pre class="lang:ps decode:true ">Rename-Item -Path "$env:ProgramFiles\WindowsPowershell\Modules\Pester-master" -NewName "$env:ProgramFiles\WindowsPowershell\Modules\Pester" -Verbose</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/10/pester4.jpg"><img class="alignnone size-full wp-image-10609" src="http://mikefrobbins.com/wp-content/uploads/2014/10/pester4.jpg" alt="pester4" width="859" height="96" /></a>

Import the Pester module:

<a href="http://mikefrobbins.com/wp-content/uploads/2014/10/pester5.jpg"><img class="alignnone size-full wp-image-10610" src="http://mikefrobbins.com/wp-content/uploads/2014/10/pester5.jpg" alt="pester5" width="859" height="300" /></a>

Create a test folder for Pester:
<pre class="lang:ps decode:true ">New-Item -Path C:\Pester -ItemType Directory -Verbose</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/10/pester6.jpg"><img class="alignnone size-full wp-image-10611" src="http://mikefrobbins.com/wp-content/uploads/2014/10/pester6.jpg" alt="pester6" width="859" height="193" /></a>

We're ready to begin on our first test-driven development task. You've been asked to create a PowerShell function to determine the <a href="http://en.wikipedia.org/wiki/Parity_(mathematics)" target="_blank">parity</a>  (odd or even) of one or more numbers.

When starting on a new project, task, etc, the first step is to use the <em>New-Fixture</em> function from the Pester module which creates a folder specified via the <em>-Path</em> parameter and two .ps1 files. The .ps1 file is for your PowerShell code and the test.ps1 file is where the tests go:
<pre class="lang:ps decode:true">New-Fixture -Name Get-NumberParity -Path C:\Pester\NumberParity -Verbose</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/10/pester7b.jpg"><img class="alignnone size-full wp-image-10614" src="http://mikefrobbins.com/wp-content/uploads/2014/10/pester7b.jpg" alt="pester7b" width="859" height="192" /></a>

Change to the directory created in the previous step:
<pre class="lang:ps decode:true">Set-Location -Path C:\Pester\NumberParity</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/10/pester8.jpg"><img class="alignnone size-full wp-image-10616" src="http://mikefrobbins.com/wp-content/uploads/2014/10/pester8.jpg" alt="pester8" width="859" height="62" /></a>

The <em>Invoke-Pester</em> function runs the tests:
<pre class="lang:ps decode:true">Invoke-Pester</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/10/pester9.jpg"><img class="alignnone size-full wp-image-10617" src="http://mikefrobbins.com/wp-content/uploads/2014/10/pester9.jpg" alt="pester9" width="859" height="214" /></a>

Open both of the files from the NumberParity folder in the PowerShell ISE.

Currently the Get-NumberParity.ps1 file contains a function that doesn't do anything:
<pre class="lang:ps decode:true " title="Get-NumberParity.ps1">function Get-NumberParity {

}</pre>
The Get-NumberParity.Tests.ps1 file contains a test that will always fail (which is why all of the test failed when we previous ran the <em>Invoke-Pester</em> function):
<pre class="lang:ps decode:true" title="Get-NumberParity.Tests.ps1">$here = Split-Path -Parent $MyInvocation.MyCommand.Path
$sut = (Split-Path -Leaf $MyInvocation.MyCommand.Path).Replace(".Tests.", ".")
. "$here\$sut"

Describe "Get-NumberParity" {
    It "does something useful" {
        $true | Should Be $false
    }
}</pre>
Now to define the specifications for what our code is suppose to accomplish in the form of tests. I'll keep it simple in this blog article and only use the "Should Be" assertion:
<pre class="lang:ps decode:true " title="Get-NumberParity.Tests.ps1">$here = Split-Path -Parent $MyInvocation.MyCommand.Path
$sut = (Split-Path -Leaf $MyInvocation.MyCommand.Path).Replace(".Tests.", ".")
. "$here\$sut"

Describe "Get-NumberParity" {
    It "Should determine 1 is Odd" {
        Get-NumberParity -Number 1 | Should Be Odd
    }
    It "Should determine 2 is Even" {
        Get-NumberParity -Number 2 | Should Be Even
    }
    It "Should determine -1 is Odd" {
        Get-NumberParity -Number -1 | Should Be Odd
    }
    It "Should accept more than one number via parameter input" {
        (Get-NumberParity -Number 1,2,3).Length | Should Be 3
    }
    It "Should accept more than one number via pipeline input" {
        (1..5 | Get-NumberParity).Length | Should Be 5
    }
}</pre>
Run our test:

<a href="http://mikefrobbins.com/wp-content/uploads/2014/10/pester10.jpg"><img class="alignnone size-full wp-image-10618" src="http://mikefrobbins.com/wp-content/uploads/2014/10/pester10.jpg" alt="pester10" width="859" height="348" /></a>

Notice that all of our tests failed. At this step in the process, "failure = success" because we haven't written any code yet, at least not for the actual Get-NumberParity function and all of the tests are suppose to fail at this point.

As you accomplish tasks when writing your code, test it (test as you go and test often). It's time to write the PowerShell code for what I think will accomplish the task at hand:
<pre class="lang:ps decode:true" title="Get-NumberParity.ps1">function Get-NumberParity {
    [CmdletBinding()]
    param (
        [Parameter(Mandatory, 
                   ValueFromPipeline)]
        [int[]]$Number
    )

    PROCESS {
        foreach ($n in $Number) {
            switch ($n % 2) {
                0 {[string]'Even'; break}
                1 {[string]'Odd'; break}
            }
        }
    }
}</pre>
Now to test our newly created <em>Get-NumberParity</em> function by running <em>Invoke-Pester:</em>

<a href="http://mikefrobbins.com/wp-content/uploads/2014/10/pester11.jpg"><img class="alignnone size-full wp-image-10619" src="http://mikefrobbins.com/wp-content/uploads/2014/10/pester11.jpg" alt="pester11" width="859" height="205" /></a>

Agh...one of the test is still failing! The problem with using the <a href="http://en.wikipedia.org/wiki/Modulo_operation" target="_blank">modulus</a> operator is that negative odd numbers return -1 instead of 1.

<a href="http://mikefrobbins.com/wp-content/uploads/2014/10/pester12.jpg"><img class="alignnone size-full wp-image-10620" src="http://mikefrobbins.com/wp-content/uploads/2014/10/pester12.jpg" alt="pester12" width="859" height="193" /></a>

By the way, we're lucky we decided to write tests for this because there's no telling how long this code might have been in production before this problem was detected.

We could add a line to our switch statement to account for a value of -1, but that wouldn't seem to be as clean as it could be and there has to be a better way.

We could divide the number by two and see if it is an integer (has a remainder or not):
<pre class="lang:ps decode:true" title="Get-NumberParity.ps1">function Get-NumberParity {
    [CmdletBinding()]
    param (
        [Parameter(Mandatory, 
                   ValueFromPipeline)]
        [int[]]$Number
    )

    PROCESS {
        foreach ($n in $Number) {
            switch ($n / 2) {
                {$_ -is [int]} {[string]'Even'; break}
                {$_ -isnot [int]} {[string]'Odd'; break}
            }
        }
    }
}</pre>
That code meets our specifications and passes our tests:

<a href="http://mikefrobbins.com/wp-content/uploads/2014/10/pester13.jpg"><img class="alignnone size-full wp-image-10622" src="http://mikefrobbins.com/wp-content/uploads/2014/10/pester13.jpg" alt="pester13" width="859" height="167" /></a>

I guess I'm too much of a perfectionist though because there still seems like there should be a better way to accomplish this task and indeed there is, at least in my opinion. We'll use one of the bitwise operators:
<pre class="lang:ps decode:true" title="Get-NumberParity.ps1">function Get-NumberParity {
    [CmdletBinding()]
    param (
        [Parameter(Mandatory, 
                   ValueFromPipeline)]
        [int[]]$Number
    )

    PROCESS {
        foreach ($n in $Number) {
            switch ($n -band 1) {
                0 {[string]'Odd'; break}
                1 {[string]'Even'; break}
            }
        }
    }
}</pre>
This code also passes all of our tests with Pester:

<a href="http://mikefrobbins.com/wp-content/uploads/2014/10/pester13.jpg"><img class="alignnone size-full wp-image-10622" src="http://mikefrobbins.com/wp-content/uploads/2014/10/pester13.jpg" alt="pester13" width="859" height="167" /></a>

An additional benefit is that you'll save the tests along with the PowerShell code and in the future when a new feature is needed, you can not only add the new feature using the same test-driven development approach, but you'll have the original tests to rerun to make sure you don't break anything in the process of adding new functionality.

The next time your boss shows up and wants to know what you're working on, tell him you're <a href="http://en.wikipedia.org/wiki/Code_refactoring" target="_blank">refactoring</a> your PowerShell code.

µ