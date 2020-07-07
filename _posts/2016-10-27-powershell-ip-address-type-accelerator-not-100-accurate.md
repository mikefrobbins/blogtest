---
ID: 14659
post_title: 'PowerShell IP Address Type Accelerator Not 100% Accurate'
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2016/10/27/powershell-ip-address-type-accelerator-not-100-accurate/
published: true
post_date: 2016-10-27 07:30:35
---
Testing whether or not an IP Address is valid with PowerShell is a fairly simple process with the [ipaddress] type accelerator if your goal is to validate input for a script or function as shown in the following code example:
<pre class="lang:ps decode:true" title="Test-ValidInput">function Test-ValidInput {
    
    [CmdletBinding()]
    param (
        [ipaddress]$IpAddress
    )

    Write-Output $true

}
</pre>
When a valid IP address is specified, the function continues and when an invalid IP address is specified, the function terminates immediately and returns an error message:
<pre class="lang:ps decode:true">Test-ValidInput -IpAddress 192.168.0.1
Test-ValidInput -IpAddress 192.168.0.256</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2016/10/testip-1a.png"><img class="alignnone size-full wp-image-14661" src="http://mikefrobbins.com/wp-content/uploads/2016/10/testip-1a.png" alt="testip-1a" width="859" height="175" /></a>

The IP Address type accelerator can also be used to validate IPv6 addresses:
<pre class="lang:ps decode:true ">Test-ValidInput -IpAddress ::1
Test-ValidInput -IpAddress ::1:1</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2016/10/testip-6a.png"><img class="alignnone size-full wp-image-14669" src="http://mikefrobbins.com/wp-content/uploads/2016/10/testip-6a.png" alt="testip-6a" width="859" height="176" /></a>

As you can see in the previous sets of results, input validation of an IP address for a script or function in PowerShell is an almost no code solution although I've seen others write an enormous amount of code to accomplish this task, but is the IP address type accelerator in PowerShell accurate?

What I discovered is the IP address type accelerator in PowerShell isn't 100% accurate. Entering a number that's five 2's seems to be cast to a valid IP address so the type accelerator thinks it's valid (clearly it isn't):
<pre class="lang:ps decode:true">Test-ValidInput -IpAddress 22222
[ipaddress]'22222'</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2016/10/testip-2a.png"><img class="alignnone size-full wp-image-14662" src="http://mikefrobbins.com/wp-content/uploads/2016/10/testip-2a.png" alt="testip-2a" width="859" height="248" /></a>

One simple line of code can weed out those inaccurate results:
<pre class="lang:ps decode:true">'22222' -match [IPAddress]'22222'</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2016/10/testip-3a.png"><img class="alignnone size-full wp-image-14664" src="http://mikefrobbins.com/wp-content/uploads/2016/10/testip-3a.png" alt="testip-3a" width="859" height="68" /></a>

ValidateScript along with a little code can be used to prevent this scenario from occurring when performing input validation for IP addresses:
<pre class="lang:ps decode:true">function Test-ValidInput {
    
    [CmdletBinding()]
    param (
        [ValidateScript({
            If ($_ -match [IPAddress]$_) {
                $true
            }
            else {
                Throw "Cannot convert value `"$_`" to type `"System.Net.IPAddress`". Error: `"An invalid IP address was specified.`""
            }
        })]
        [string]$IpAddress
    )

    Write-Output $true

}</pre>
The IP address validation now works as expected as shown in the following example:
<pre class="lang:ps decode:true">Test-ValidInput -IpAddress 192.168.0.1
Test-ValidInput -IpAddress 192.168.0.256
Test-ValidInput -IpAddress 22222</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2016/10/testip-4a.png"><img class="alignnone size-full wp-image-14666" src="http://mikefrobbins.com/wp-content/uploads/2016/10/testip-4a.png" alt="testip-4a" width="859" height="284" /></a>

Please share your thoughts, suggestions, and/or comments via a comment to this blog article.

Update:

I also discovered that the same five digit number is translated to a valid IP address when using the ping command from the command prompt which completely eliminates PowerShell:
<pre class="lang:batch decode:true">ping 22222</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2016/10/testip-7a.png"><img class="alignnone size-full wp-image-14687" src="http://mikefrobbins.com/wp-content/uploads/2016/10/testip-7a.png" alt="testip-7a" width="859" height="189" /></a>

Maybe someone can explain why this occurs? See the comments to this blog article for the explanation.

The examples shown in this blog article were written and tested on Windows 10 Enterprise Edition running PowerShell version 5.1.14393.206.

Update #2:

There's been lots of discussion about this blog article on Twitter, one interesting response caught my eye and is also an IP address that you wouldn't think would be valid, but both the IP address accelerator in PowerShell and ping via the command prompt expand 10.1 as 10.0.0.1:

<a href="https://twitter.com/REOScotte/status/792866036026449921" target="_blank"><img class="alignnone wp-image-14691 size-full" src="http://mikefrobbins.com/wp-content/uploads/2016/10/testip-9a.png" alt="testip-9a" width="588" height="104" /></a>
<pre class="lang:ps decode:true ">[ipaddress]'10.1'</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2016/10/testip-10a.png"><img class="alignnone size-full wp-image-14692" src="http://mikefrobbins.com/wp-content/uploads/2016/10/testip-10a.png" alt="testip-10a" width="859" height="223" /></a>

The code used in my <em>Test-ValidInput</em> function previously shown in this blog article weeds out those unexpected results as well:
<pre class="lang:ps decode:true">Test-ValidInput -IpAddress '10.1'</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2016/10/testip-11a.png"><img class="alignnone size-full wp-image-14693" src="http://mikefrobbins.com/wp-content/uploads/2016/10/testip-11a.png" alt="testip-11a" width="859" height="153" /></a>

The results for integers like '22222' and IP addresses like '10.1' aren't necessarily wrong, but unexpected if you're trying to validate the input is a valid IP address specified in quad-dotted notation of four decimal integers.

µ