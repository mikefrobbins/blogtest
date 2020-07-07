---
ID: 12075
post_title: The PowerShell return keyword
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2015/07/23/the-powershell-return-keyword/
published: true
post_date: 2015-07-23 07:30:49
---
The return keyword is probably the most over used keyword in PowerShell that’s used in unnecessary scenarios. You’ll often find it used to simply return the output of a function:
<pre class="lang:ps decode:true">function New-MrGuid {
    $Guid = [System.Guid]::NewGuid()
    Return $Guid
}</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/07/return1a.jpg"><img class="alignnone size-full wp-image-12078" src="http://mikefrobbins.com/wp-content/uploads/2015/07/return1a.jpg" alt="return1a" width="877" height="131" /></a>

In that scenario, using the return keyword is totally unnecessary. If you do want to return the value of the variable, simply let PowerShell take care of returning the output:
<pre class="lang:ps decode:true">function New-MrGuid {
    $Guid = [System.Guid]::NewGuid()
    $Guid
}</pre>
I received a comment on Twitter from <a href="https://twitter.com/RandomNoun7/status/624208006310440960" target="_blank">Bill Hurt</a> about using the <em>Write-Output</em> cmdlet in this scenario and although I didn't specify it in the previous example, I typically do use <em>Write-Output</em> instead of just specifying the variable itself:
<pre class="lang:ps decode:true">function New-MrGuid {
    $Guid = [System.Guid]::NewGuid()
    Write-Output $Guid
}</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/07/return1a.jpg"><img class="alignnone size-full wp-image-12078" src="http://mikefrobbins.com/wp-content/uploads/2015/07/return1a.jpg" alt="return1a" width="877" height="131" /></a>

In the previous example, there’s no reason to store the value in a variable, simply create the new GUID and let PowerShell handle returning the output all in one command:
<pre class="lang:ps decode:true ">function New-MrGuid {
    [System.Guid]::NewGuid()
}</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/07/return1a.jpg"><img class="alignnone size-full wp-image-12078" src="http://mikefrobbins.com/wp-content/uploads/2015/07/return1a.jpg" alt="return1a" width="877" height="131" /></a>

The return keyword does have a valid use case though. The following function does not use the return keyword:
<pre class="lang:ps decode:true ">function Test-Return { 
    [CmdletBinding()]
    param (
        [int[]]$Number
    )
  
    foreach ($N in $Number) {        
        if ($N -ge 4) {
            $N
        }
    }
}</pre>
Without the return keyword any number greater than or equal to four is returned:
<pre class="lang:ps decode:true ">Test-Return -Number 3, 5, 7, 9</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/07/return2a.jpg"><img class="alignnone size-full wp-image-12080" src="http://mikefrobbins.com/wp-content/uploads/2015/07/return2a.jpg" alt="return2a" width="877" height="96" /></a>

Notice that the return keyword has been added to the function without any other changes:
<pre class="lang:ps decode:true">function Test-Return { 
    [CmdletBinding()]
    param (
        [int[]]$Number
    )
  
    foreach ($N in $Number) {        
        if ($N -ge 4) {
            Return $N
        }
    }
}</pre>
With the return keyword, the first value that is greater than or equal to 4 will be returned and then the foreach loop will exit without testing the numbers 7 or 9.
<pre class="lang:ps decode:true">Test-Return -Number 3, 5, 7, 9</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/07/return3a.jpg"><img class="alignnone size-full wp-image-12081" src="http://mikefrobbins.com/wp-content/uploads/2015/07/return3a.jpg" alt="return3a" width="877" height="71" /></a>

Not only does it exit the foreach loop, but the entire function so if additional code existed after the foreach loop, it wouldn't be executed either. A slightly modified version of the previous function will be used to demonstrate this.

For the first test, the return keyword is omitted:
<pre class="lang:ps decode:true ">function Test-Return { 
    [CmdletBinding()]
    param (
        [int[]]$Number
    )
    
    $i = 0

    foreach ($N in $Number) {        
        if ($N -ge 4) {
            $i++
            $N
        }
    }
    
    Write-Verbose -Message "A total of $i items were returned."
}</pre>
The verbose output after the foreach loop is included in the output when specifying the verbose parameter:

<a href="http://mikefrobbins.com/wp-content/uploads/2015/07/return4a.jpg"><img class="alignnone size-full wp-image-12086" src="http://mikefrobbins.com/wp-content/uploads/2015/07/return4a.jpg" alt="return4a" width="877" height="154" /></a>

The return keyword has been added to the following function:
<pre class="lang:ps decode:true ">function Test-Return { 
    [CmdletBinding()]
    param (
        [int[]]$Number
    )
    
    $i = 0

    foreach ($N in $Number) {        
        if ($N -ge 4) {
            $i++
            Return $N
        }
    }
    
    Write-Verbose -Message "A total of $i items were returned."
}</pre>
Notice that although the verbose parameter was specified, the verbose output is not included because the return keyword causes the function to exit before it gets to that point:

<a href="http://mikefrobbins.com/wp-content/uploads/2015/07/return5a.jpg"><img class="alignnone size-full wp-image-12087" src="http://mikefrobbins.com/wp-content/uploads/2015/07/return5a.jpg" alt="return5a" width="877" height="71" /></a>

Of course, if the portion of the code with the return keyword isn't run, the verbose output will be included in the output when the verbose parameter is specified:

<a href="http://mikefrobbins.com/wp-content/uploads/2015/07/return6a.jpg"><img class="alignnone size-full wp-image-12088" src="http://mikefrobbins.com/wp-content/uploads/2015/07/return6a.jpg" alt="return6a" width="877" height="70" /></a>

As <a href="http://twitter.com/Jaap_Brasser">Jaap Brasser</a> noted via a comment to this post, it's worth noting that the return keyword is actually required when working with classes in PowerShell version 5 so that's another valid use case for it. I'll plan to cover classes in a future blog article.

For more information about the return keyword, see the <a href="https://technet.microsoft.com/en-us/library/Hh847760.aspx" target="_blank">about_Return</a> help topic.

µ