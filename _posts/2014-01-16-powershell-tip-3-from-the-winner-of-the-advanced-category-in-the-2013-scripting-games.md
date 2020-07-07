---
ID: 8874
post_title: 'PowerShell Tip #3 from the Winner of the Advanced Category in the 2013 Scripting Games'
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2014/01/16/powershell-tip-3-from-the-winner-of-the-advanced-category-in-the-2013-scripting-games/
published: true
post_date: 2014-01-16 07:30:57
---
In my previous blog article (<a href="http://powershell.org/wp/2014/01/09/powershell-tip-2-from-the-winner-of-the-advanced-category-in-the-2013-scripting-games/" target="_blank">PowerShell Tip #2</a>), I left off with the subject of inline help and stated there was a better way. I'm fast-forwarding through lots of concepts and jumping right into "Advanced Functions and Scripts" with this tip because they are where you'll find the answer to a "better way" to add inline help. The inline comments we saw in the previous tip looked like this:
<pre class="lang:ps decode:true">function Get-BiosInfo {

    # Attempting to retrieve the BIOS information from the local computer
    Get-WmiObject -Class Win32_BIOS

}</pre>
When looking at the syntax for this function, you can see that it has no parameters:

<a href="http://mikefrobbins.com/wp-content/uploads/2014/01/posh-tip3a.png"><img class="alignnone size-full wp-image-8878" alt="posh-tip3a" src="http://mikefrobbins.com/wp-content/uploads/2014/01/posh-tip3a.png" width="561" height="100" /></a>

<em>CmdletBinding</em> is an attribute that makes a function or script work like a compiled cmdlet. You can't use the <em>CmdletBinding</em> attribute without also using the <em>Parameter</em> (Param) attribute, although you don't necessarily have to define any parameters even if you do specify the <em>Parameter</em> attribute:
<pre class="lang:ps decode:true">function Get-BiosInfo {

    [CmdletBinding()]
    param()

    # Attempting to retrieve the BIOS information from the local computer
    Get-WmiObject -Class Win32_BIOS

}</pre>
Among other things, the <em>CmdletBinding</em> attribute gives your function the ability to use "common parameters" without having to do anything else to it:

<a href="http://mikefrobbins.com/wp-content/uploads/2014/01/posh-tip3b.png"><img class="alignnone size-full wp-image-8877" alt="posh-tip3b" src="http://mikefrobbins.com/wp-content/uploads/2014/01/posh-tip3b.png" width="560" height="101" /></a>

For more information about the CmdletBinding attribute, read the <a href="http://technet.microsoft.com/en-us/library/hh847872.aspx" target="_blank">about_Functions_CmdletBindingAttribute</a> help topic:
<pre class="lang:ps decode:true">help about_Functions_CmdletBindingAttribute</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/01/posh-tip3i.png"><img class="alignnone size-full wp-image-8890" alt="posh-tip3i" src="http://mikefrobbins.com/wp-content/uploads/2014/01/posh-tip3i.png" width="562" height="367" /></a>

You might ask, what are these common parameters you speak of?

They are several parameters that are available to any cmdlet. The actual number of common parameters will vary depending on what version of PowerShell you're using.

You can see these common parameter names along with any other parameters that you have added to your function by using the <em>Get-Command</em> cmdlet:
<pre class="lang:ps decode:true">Get-Command -Name Get-BiosInfo | Select-Object -ExpandProperty Parameters</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/01/posh-tip3f.png"><img class="alignnone size-full wp-image-8883" alt="posh-tip3f" src="http://mikefrobbins.com/wp-content/uploads/2014/01/posh-tip3f.png" width="560" height="257" /></a>

To learn more about the "common parameters", read the <a href="http://technet.microsoft.com/en-us/library/hh847884.aspx" target="_blank">about_CommonParameters</a> help topic:
<pre>help about_CommonParameters</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/01/posh-tip3e.png"><img alt="posh-tip3e" src="http://mikefrobbins.com/wp-content/uploads/2014/01/posh-tip3e.png" width="563" height="348" /></a>

The common parameter that you're interested in as far as using it as a "better way" to provide inline help is the <em>Verbose</em> parameter. You can use the <em>Write-Verbose</em> cmdlet in your function with the same message you previously used as inline help:
<pre class="wrap:false lang:ps decode:true">function Get-BiosInfo {

    [CmdletBinding()]
    param()

    Write-Verbose -Message "Attempting to retrieve the BIOS information from the local computer"
    Get-WmiObject -Class Win32_BIOS

}</pre>
When you want to see the message, you can simply specify the <em>Verbose</em> parameter when calling your function to display it. <em>Verbose</em>, in addition to being one of the common parameters is also a switch parameter because it doesn't require a value:

<a href="http://mikefrobbins.com/wp-content/uploads/2014/01/posh-tip3g.png"><img class="alignnone size-full wp-image-8885" alt="posh-tip3g" src="http://mikefrobbins.com/wp-content/uploads/2014/01/posh-tip3g.png" width="560" height="196" /></a>

There's one last thing I want to cover as far as adding help to your functions and scripts goes and that's the parameter help message:
<pre class="wrap:false lang:ps decode:true">function Get-BiosInfo {

    [CmdletBinding()]
    param(
        [Parameter(Mandatory,
                   HelpMessage="Enter a Computer Name")]
        $ComputerName
    )

    Write-Verbose -Message "Attempting to retrieve the BIOS information from $ComputerName"
    Get-WmiObject -Class Win32_BIOS -ComputerName $ComputerName

}</pre>
This allows the user of your function to see help by specifying "!?" for a mandatory parameter when they don't initially specify a value for it. My recommendation is to not use this sort of help:

<a href="http://mikefrobbins.com/wp-content/uploads/2014/01/posh-tip3h.png"><img class="alignnone size-full wp-image-8887" alt="posh-tip3h" src="http://mikefrobbins.com/wp-content/uploads/2014/01/posh-tip3h.png" width="560" height="133" /></a>

If you're using standardized parameter names (and you should be) then what's needed should be self-explanatory. If it's not, then the user of this function needs to read the comment based help for the function because they clearly don't understand how to use it and continuing may lead to undesired results to say the least. The comment based help for the function should include information about each parameter. For more information about comment based help, see my <a href="http://powershell.org/wp/2014/01/09/powershell-tip-2-from-the-winner-of-the-advanced-category-in-the-2013-scripting-games/" target="_blank">previous blog article</a>.

There's a <a href="http://powershell.org/wp/2013/05/06/a-helpful-message-about-helpmessage/" target="_blank">blog article on PowerShell.org</a> that June Blender wrote during the 2013 scripting games that provides some additional insight as to why parameter help messages don't provide any value.

µ