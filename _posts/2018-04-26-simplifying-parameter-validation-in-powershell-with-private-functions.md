---
ID: 16469
post_title: >
  Simplifying Parameter Validation in
  PowerShell with Private Functions
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2018/04/26/simplifying-parameter-validation-in-powershell-with-private-functions/
published: true
post_date: 2018-04-26 07:30:59
---
In <a href="http://mikefrobbins.com/2018/04/19/moving-parameter-validation-in-powershell-to-private-functions/" target="_blank" rel="noopener">my previous blog article</a>, I described how to move code from ValidateScript to a private function for parameter validation in PowerShell. This all came about from a question I received in one of my sessions at the PowerShell + DevOps Global Summit a couple of weeks ago. I enjoy following up with attendees of my presentations when they have questions so I sent a message and a link to my previous blog article to the person who asked if that was possible.

They responded by asking if it was possible to move the custom message that Throw returns to the private function. At first, I didn't think this would be possible, but decided to try the code to make an accurate determination instead of just assuming it wasn't possible.

I've now learned something else which makes the whole process of moving the validation from the ValidateScript block to a private function much more user friendly which is what I think the person who asked the question was trying to accomplish.
<pre class="lang:ps decode:true ">function Test-FileName {
    param (
        [string]$FileName
    )
    
    If ($FileName -match '^(?!^(PRN|AUX|CLOCK\$|NUL|CON|COM\d|LPT\d|\..*)(\..+)?$)[^\x00-\x1f\\?*:\"";|/]+$') {
        $True
    }
    else {
        Throw "$_ is either not a valid filename or it is not recommended."
    }
}</pre>
The code within ValidateScript becomes so much simpler.
<pre class="lang:ps decode:true ">function New-MrFile {
    [CmdletBinding()]
    param (
        [ValidateScript({
                Test-FileName -FileName $_
        })]
        [string]$FileName
    )
    Write-Output $FileName
}</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2018/04/improve-private-param-validation1a.jpg"><img class="alignnone size-full wp-image-16474" src="http://mikefrobbins.com/wp-content/uploads/2018/04/improve-private-param-validation1a.jpg" alt="" width="859" height="200" /></a>

This is why you want to attend conferences in person and talk with others while you're there. Communicating with others will make you consider things that you didn't think were possible and the end result is that you're writing better and more understandable code.

I’ve also created a companion <a href="https://youtu.be/PlSNoQrTlVc" target="_blank" rel="noopener">video</a> for this blog article to show what I’m talking about.

https://youtu.be/PlSNoQrTlVc

I’d love to hear your thoughts on this topic. Please post them as a comment to this blog article. Also, let me know what you think about having short companion videos to accompany my blog articles.

µ