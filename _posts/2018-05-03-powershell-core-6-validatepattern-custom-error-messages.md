---
ID: 16485
post_title: 'PowerShell Core 6: ValidatePattern Custom Error Messages'
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2018/05/03/powershell-core-6-validatepattern-custom-error-messages/
published: true
post_date: 2018-05-03 07:30:21
---
Last week, I received a comment on <a href="http://mikefrobbins.com/2018/04/26/simplifying-parameter-validation-in-powershell-with-private-functions/" target="_blank" rel="noopener">my previous blog article</a> from fellow Microsoft MVP <a href="https://twitter.com/Jaykul" target="_blank" rel="noopener">Joel Bennett</a> which referenced using an <em>ErrorMessage</em> parameter similar to how <em>ValidatePattern</em> works in PowerShell Core version 6. I knew I'd seen some discussion about this on GitHub, but I wasn't aware that it had made it into the production release. Joel's message is shown in the following image.

<hr />

<a href="http://mikefrobbins.com/wp-content/uploads/2018/05/validation-customerror1a.jpg"><img class="alignnone size-full wp-image-16486" src="http://mikefrobbins.com/wp-content/uploads/2018/05/validation-customerror1a.jpg" alt="" width="858" height="267" /></a>

<hr />

I had to figure out how to use custom error messages with <em>ValidatePattern</em>. After all, that was the whole reason I avoided using <em>ValidatePattern</em> in the first place and wrote <a href="http://mikefrobbins.com/2013/08/08/powershell-parameter-validation-building-a-better-validatepattern-with-validatescript/" target="_blank" rel="noopener">my own better <em>ValidatePattern</em> using <em>ValidateScript</em></a>. Taking a look at the <a href="https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_functions_advanced_parameters" target="_blank" rel="noopener">about_Functions_Advanced_Parameters</a> help topic and searching around on the Internet revealed nothing at all. Since I knew I had previously seen something about it on GitHub, I headed over there for answers. Bingo! <a href="https://github.com/PowerShell/PowerShell/issues/3745" target="_blank" rel="noopener">Issue 3748 in the PowerShell repository</a> revealed what I was looking for. Not only had custom error message support been added to <em>ValidatePattern</em>, but it had also been added to <em>ValidateScript</em> and <em>ValidateSet</em>.

The following code shows a simple example of using a custom error message with the <em>ValidatePattern</em> parameter validation attribute in PowerShell Core 6.
<pre class="lang:ps decode:true " title="Test-ValidatePattern">function Test-ValidatePattern {
    [CmdletBinding()]
    param (
        [ValidatePattern('^(?!^(PRN|AUX|CLOCK\$|NUL|CON|COM\d|LPT\d|\..*)(\..+)?$)[^\x00-\x1f\\?*:\"";|/]+$',
                         ErrorMessage = "{0} is not a valid file name")]
        [string]$FileName
    )
    Write-Output $FileName
}</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2018/05/validation-customerror2a.jpg"><img class="alignnone size-full wp-image-16488" src="http://mikefrobbins.com/wp-content/uploads/2018/05/validation-customerror2a.jpg" alt="" width="859" height="312" /></a>

A custom error message can also be used with <em>ValidateScript</em> and <em>ValidateSet</em> just like with what's shown in the previous example, except using those parameter validation attributes instead of <em>ValidatePattern</em>. Keep in mind, this only works with PowerShell Core and not Windows PowerShell.

µ