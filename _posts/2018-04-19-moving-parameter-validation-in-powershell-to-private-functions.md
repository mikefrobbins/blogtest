---
ID: 16453
post_title: >
  Moving Parameter Validation in
  PowerShell to Private Functions
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2018/04/19/moving-parameter-validation-in-powershell-to-private-functions/
published: true
post_date: 2018-04-19 07:30:16
---
While presenting one of my presentations at the PowerShell + DevOps Global Summit last week, I demonstrated why you wouldn't want to use ValidatePattern for parameter validation because of the useless error message that it returns when the input doesn't match the regular expression that's being used for validation.
<pre class="lang:ps decode:true ">function Test-ValidatePattern {
    [CmdletBinding()]
    param (
        [ValidatePattern('^(?!^(PRN|AUX|CLOCK\$|NUL|CON|COM\d|LPT\d|\..*)(\..+)?$)[^\x00-\x1f\\?*:\"";|/]+$')]
        [string]$FileName
    )
    Write-Output $FileName
}</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2018/04/private-param-validation1a.png"><img class="alignnone size-full wp-image-16454" src="http://mikefrobbins.com/wp-content/uploads/2018/04/private-param-validation1a.png" alt="" width="859" height="185" /></a>

I then demonstrated how ValidateScript could be used to build a better ValidatePattern. I have <a href="http://mikefrobbins.com/2013/08/08/powershell-parameter-validation-building-a-better-validatepattern-with-validatescript/" target="_blank" rel="noopener">an older blog article that details this process</a> if that's something you're interested in learning more about.
<pre class="lang:ps decode:true ">function Test-ValidateScript {
    [CmdletBinding()]
    param (
        [ValidateScript({
            If ($_ -match '^(?!^(PRN|AUX|CLOCK\$|NUL|CON|COM\d|LPT\d|\..*)(\..+)?$)[^\x00-\x1f\\?*:\"";|/]+$') {
                $True
            }
            else {
                Throw "$_ is either not a valid filename or it is not recommended."
            }
        })]
        [string]$FileName
    )
    Write-Output $FileName
}</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2018/04/private-param-validation2a.png"><img class="alignnone size-full wp-image-16455" src="http://mikefrobbins.com/wp-content/uploads/2018/04/private-param-validation2a.png" alt="" width="859" height="170" /></a>

A great question was asked during this presentation: Can you use a function for parameter validation instead of having some complicated script embedded within your function that's performing the validation? Just to be clear, you don't want your code to continue on a path that it can't possible complete successfully any further than necessary, which is one of the reasons you want to use parameter validation and not write custom validation within the body of the function itself. Another reason to use the parameter validation attributes is so your validation is at least similar to the validation that others would write.

With that said, if you're going to write custom code inside of the ValidateScript block, I think that breaking it out into private functions is a great idea because of several reasons. You could reuse the same validation such as the regular expression in the previous example to validate the input of numerous functions while only having to write the code once (there's no sense in writing redundant code) and if you ever have to update the code that's performing the validation, there's only one copy to update instead of trying to find all of the places you used it.

A private function could be written similar to the following for the example shown in this blog article.
<pre class="lang:ps decode:true ">function Test-FileName {
    param (
        [string]$FileName
    )
    
    If ($FileName -match '^(?!^(PRN|AUX|CLOCK\$|NUL|CON|COM\d|LPT\d|\..*)(\..+)?$)[^\x00-\x1f\\?*:\"";|/]+$') {
        $True
    }
    else {
        $false
    }
}</pre>
And then it could be called within the ValidateScript block to achieve the same result as the first function shown in this blog article.
<pre class="lang:ps decode:true ">function New-MrFile {
    [CmdletBinding()]
    param (
        [ValidateScript({
            If (Test-FileName -FileName $_) {
                $True
            }
            else {
                Throw "$_ is either not a valid filename or it is not recommended."
            }
        })]
        [string]$FileName
    )
    Write-Output $FileName
}</pre>
I've also created a companion <a href="https://youtu.be/__F17QvdXXc" target="_blank" rel="noopener">video</a> for this blog article to show what I'm talking about.

https://youtu.be/__F17QvdXXc

This really isn't any different than using built-in commands such as <em>Test-Path</em> within the ValidateScript block to validate a user is providing an existing and valid path as input for a parameter. You wouldn't rewrite all of the code for <em>Test-Path</em> within the ValidateScript block every time you wanted to validate a path.

I'd love to hear your thoughts on this topic. Please post them as a comment to this blog article. Also, let me know what you think about having short companion videos to accompany my blog articles.

µ