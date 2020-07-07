---
ID: 17017
post_title: >
  Indentation and Formatting Style for
  PowerShell Code
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2018/09/06/indentation-and-formatting-style-for-powershell-code/
published: true
post_date: 2018-09-06 07:30:55
---
My preferred <a href="https://en.wikipedia.org/wiki/Indentation_style" target="_blank" rel="noopener">indentation style</a> when writing PowerShell code is <a href="https://en.wikipedia.org/wiki/Indentation_style#Variant:_Stroustrup" target="_blank" rel="noopener">Stroustrup style</a> because I don't like my code to cuddle (there's no cuddled else in Stroustrup style). I occasionally hear from others that they don't like this style because it doesn't work from the PowerShell console.
<pre class="lang:ps decode:true">if ($plasterParams.Git) {
    $ModulePath = "$($plasterParams.DestinationPath)\$($plasterParams.GitRepoName)\$($plasterParams.Name)"
}
else {
    $ModulePath = "$($plasterParams.DestinationPath)\$($plasterParams.Name)"
}</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2018/08/no-asterisks2a.png"><img class="alignnone size-full wp-image-17016" src="https://mikefrobbins.com/wp-content/uploads/2018/08/no-asterisks2a.png" alt="" width="859" height="214" /></a>

While it doesn't work by default, there's a trick to making that style work from the PowerShell console. Simply press <em>Shift+Enter</em> instead of just <em>Enter</em> at the end of the line before the else.

<a href="https://mikefrobbins.com/wp-content/uploads/2018/08/no-asterisks3a-1.png"><img class="alignnone size-full wp-image-17033" src="https://mikefrobbins.com/wp-content/uploads/2018/08/no-asterisks3a-1.png" alt="" width="859" height="121" /></a>

I also prefer to write my code with the curly brace on the same line.
<pre class="lang:ps decode:true">function Get-Something {
    #Does Something
}</pre>
Why? Because I like consistency. Writing code with the curly brace on the next line, like in the following example, doesn't work every time. You end up having to modify how things are done on a case by case basis. For example, you can't place the curly brace for <em>Where-Object</em> on the next line. I even tried the <em>Shift+Enter</em> trick with no luck.
<pre class="lang:ps decode:true">function Get-Something
{
    #Does Something
{</pre>
According to <a href="https://github.com/PoshCode/PowerShellPracticeAndStyle/issues/81#issuecomment-285871306" target="_blank" rel="noopener">a discussion on the Unofficial PowerShell Best Practices and Style Guide</a>, using both of these styles is technically a combination of Stroustrup style and the One True Brace Style variant (OTBS) variant of K&amp;R. There was also another interesting discussion about "<em><a href="https://github.com/PoshCode/PowerShellPracticeAndStyle/issues/24" target="_blank" rel="noopener">Where to put braces</a></em>" in the same style guide a while back.

What's your preferred code writing style for PowerShell and why? Whatever style you choose, stick with it and be consistent. Formatting your code is a form of art in itself. If you're contributing to someone else's code such as an open-source project on GitHub, be sure to follow their formatting style as refactoring all of their code to your style won't be well received to say the least.

<hr />

Update 9/6/18

<a href="https://twitter.com/Jaykul/status/1037769322314297344" target="_blank" rel="noopener">Joel Bennett mention on Twitter that "<em>Shift + Enter</em>" requires PSReadline</a>. I did some testing and indeed it does, but what I also found out is that <em>Else</em> can be on a separate line and it works by default without having to use "<em>Shift + Enter</em>" as long as the PSReadline module isn't loaded.

µ