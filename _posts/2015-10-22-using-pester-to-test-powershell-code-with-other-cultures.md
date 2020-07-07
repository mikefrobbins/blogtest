---
ID: 12591
post_title: >
  Using Pester to Test PowerShell Code
  with Other Cultures
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2015/10/22/using-pester-to-test-powershell-code-with-other-cultures/
published: true
post_date: 2015-10-22 07:30:01
---
I recently published a blog article on <a href="http://mikefrobbins.com/2015/10/15/some-cases-of-unexpected-case-sensitivity-in-powershell/" target="_blank">unexpected case sensitivity in PowerShell</a>. An example in that blog article uses the contains method which is indeed case sensitive. One of the workarounds that I demonstrated was to convert whatever the user entered to upper case using the <em>ToUpper()</em> method.

One of the reasons I like blogging is that many times there are things that I may not have considered and sometimes things that I wasn't even aware of so in addition to sharing my knowledge with others, I often times learn in the process based on the feedback I receive about my blog articles and this was one of those times.

I received a response to my tweet about that blog article from <a href="http://twitter.com/oleschri" target="_blank">@Oleschri</a> on Twitter:

<a href="http://mikefrobbins.com/wp-content/uploads/2015/10/turkey-test1a.jpg"><img class="alignnone size-full wp-image-12593" src="http://mikefrobbins.com/wp-content/uploads/2015/10/turkey-test1a.jpg" alt="turkey-test1a" width="589" height="107" /></a>

<em>"You know that using conversions like .ToUpper() to do case insensitive comparisons can be problematic?"</em>

<a href="http://mikefrobbins.com/wp-content/uploads/2015/10/turkey-test2a.jpg"><img class="alignnone size-full wp-image-12594" src="http://mikefrobbins.com/wp-content/uploads/2015/10/turkey-test2a.jpg" alt="turkey-test2a" width="589" height="107" /></a>

No one knows everything so always be willing to listen to others. My response: <em>"I haven't had any problems with .ToUpper(), but if it can be problematic, I would like to hear more about that &amp; see some examples"</em>

<a href="http://mikefrobbins.com/wp-content/uploads/2015/10/turkey-test3a.jpg"><img class="alignnone size-full wp-image-12596" src="http://mikefrobbins.com/wp-content/uploads/2015/10/turkey-test3a.jpg" alt="turkey-test3a" width="589" height="125" /></a>

<em>"Sure. I present to you the The Turkey Test. Yeah, really, they call it that. <a href="http://www.moserware.com/2008/02/does-your-code-pass-turkey-test.html" target="_blank">http://www.moserware.com/2008/02/does-your-code-pass-turkey-test.html</a>"</em>

<a href="http://mikefrobbins.com/wp-content/uploads/2015/10/turkey-test4a.jpg"><img class="alignnone size-full wp-image-12597" src="http://mikefrobbins.com/wp-content/uploads/2015/10/turkey-test4a.jpg" alt="turkey-test4a" width="589" height="107" /></a>

<em>"I admin, nobody should expect i18n problems in service names. But I never give advice to use such workarounds for that reason."</em>

More info about i18n: <a href="http://www.w3.org/International/questions/qa-i18n" target="_blank">http://www.w3.org/International/questions/qa-i18n</a>

After reading the previously referenced "<a href="http://www.moserware.com/2008/02/does-your-code-pass-turkey-test.html" target="_blank">Turkey Test</a>" blog article, I created a simple <em>Test-ToUpper</em> function in PowerShell to test with:
<pre class="lang:ps decode:true ">function Test-ToUpper {
    [CmdletBinding()]
    param (
        [string]$Text
    )
    $Text.ToUpper()
}</pre>
I created a Pester test to not only test using my culture, but also with the Turkish culture via the <em>Use-Culture</em> function that's part of the <a href="https://www.powershellgallery.com/packages/PowerShellCookbook/" target="_blank">PowerShellCookbook module</a> (the PowerShell Cookbook and module was written by <a href="http://twitter.com/Lee_Holmes" target="_blank">Lee Holmes</a>):
<pre class="lang:ps decode:true ">$here = Split-Path -Parent $MyInvocation.MyCommand.Path
$sut = (Split-Path -Leaf $MyInvocation.MyCommand.Path).Replace('.Tests.', '.')
. "$here\$sut"

Describe 'Test-ToUpper' {
    It 'Converts to Upper Case' {
        Test-ToUpper -Text 'SQLEngine' | Should BeExactly 'SQLENGINE'
    }
    It 'Converts to Upper Case using Turkish Culture' {
        Use-Culture -Culture tr-TR -ScriptBlock {
            Test-ToUpper -Text 'SQLEngine'
        } | Should BeExactly 'SQLENGINE'
    }
}</pre>
The results show that using the <em>ToUpper()</em> method does indeed have the "Turkish I" problem as it's referred to:

<a href="http://mikefrobbins.com/wp-content/uploads/2015/10/turkey-test6a.jpg"><img class="alignnone size-full wp-image-12600" src="http://mikefrobbins.com/wp-content/uploads/2015/10/turkey-test6a.jpg" alt="turkey-test6a" width="859" height="191" /></a>

I'm sure you can see how simple it really is to test your code and make sure it works internationally and not just with your localization.

<a href="http://mikefrobbins.com/wp-content/uploads/2015/10/turkey-test5a.jpg"><img class="alignnone size-full wp-image-12598" src="http://mikefrobbins.com/wp-content/uploads/2015/10/turkey-test5a.jpg" alt="turkey-test5a" width="589" height="107" /></a>

Give credit to others and thank them for their assistance. This will make them more likely to provide feedback to you and to others in the future. My response: <em>"You're right. It has the "Turkish I" problem. Thanks for the info. That gives me some additional tests to perform on my code."</em>

µ