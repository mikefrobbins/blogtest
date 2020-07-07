---
ID: 14806
post_title: >
  Loop through a collection of items with
  the Pester TestCases parameter instead
  of using a foreach loop
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2016/12/09/loop-through-a-collection-of-items-with-the-pester-testcases-parameter-instead-of-using-a-foreach-loop/
published: true
post_date: 2016-12-09 07:30:42
---
One of the huge benefits of attending in-person technology events is getting to network with others. W<span style="font-size: 17px;">hile at the MVP Summit last month I had a chance to demonstrate some of my PowerShell code and Pester tests to </span><a style="font-size: 17px;" href="https://github.com/JamesWTruher" target="_blank">Jim Truher</a>.<span style="font-size: 17px;"> </span><span style="font-size: 17px;"> I was developing the code and tests for a session to present for the <a href="http://powershell.sqlpass.org/" target="_blank">PowerShell Virtual Chapter of SQL PASS</a> (the code and a video of that presentation can be found <a href="http://mikefrobbins.com/2016/11/17/video-automate-operational-readiness-and-validation-testing-of-sql-server-with-powershell-and-pester/" target="_blank">here</a>).</span>

<span style="font-size: 17px;">In one of my examples, I was looping through a collection of items (computer names of SQL Servers) to run Pester tests against each one using a foreach loop similar to what you'll find in <a href="http://mikefrobbins.com/2016/04/14/write-dynamic-unit-tests-for-your-powershell-code-with-pester/" target="_blank">this blog article</a>. Jim wrote the following code (on my computer) to demonstrate how the Pester TestCases parameter could be used to loop through a collection of items without the need for a foreach loop:</span>
<pre class="lang:ps decode:true">describe a {
    $cases = @{ one = 1 },@{ one = 1 },@{ one = 1 }
    it "one is &lt;one&gt; and testing all should be 1" -TestCases $cases {
        param ( $one )
        $one | should be 1
    }
}</pre>
<img class="alignnone size-full wp-image-14808" src="http://mikefrobbins.com/wp-content/uploads/2016/12/testcases1a.png" alt="testcases1a" width="859" height="176" />

He also demonstrated a failing test:
<pre class="lang:ps decode:true">describe a {
    $cases = @{ one = 1 },@{ one = 2 },@{ one = 1 }
    it "one is &lt;one&gt; and testing all should be 1" -TestCases $cases {
        param ( $one )
        $one | should be 1
    }
}</pre>
<img class="alignnone size-full wp-image-14809" src="http://mikefrobbins.com/wp-content/uploads/2016/12/testcases2a.png" alt="testcases2a" width="859" height="223" />

His examples could easily be modified to perform validation of multiple computers (SQL Servers in the following example):
<pre class="lang:ps decode:true ">Describe 'Simple Validation of a SQL Server' {
    $Servers = @{Server = 'sql011'}, @{Server = 'sql02'}
    It "The SQL Server Service on &lt;Server&gt; Should Be Running" -TestCases $Servers {
        param($Server)
        (Get-Service -ComputerName $Server -Name MSSQLServer).Status |
        Should Be 'Running'
    }
}</pre>
<img class="alignnone size-full wp-image-14810" src="http://mikefrobbins.com/wp-content/uploads/2016/12/testcases3a.png" alt="testcases3a" width="859" height="178" />

Thanks to Jim, I now know of a new more efficient way of looping through a collection of items for Pester tests.

µ