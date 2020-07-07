---
ID: 17988
post_title: 'What&#8217;s in your PowerShell $PSDefaultParameterValues Preference Variable?'
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2019/08/01/whats-in-your-powershell-psdefaultparametervalues-preference-variable/
published: true
post_date: 2019-08-01 07:30:36
---
The <em>$PSDefaultParameterValues</em> preference variable, which was introduced in Windows PowerShell version 3.0, provides a mechanism for specifying default values for parameters. I thought I would share what I've added to mine and ask the community to share theirs.
<pre class="lang:ps decode:true">$PSDefaultParameterValues = @{
    'Out-Default:OutVariable' = 'LastResult'
    'Out-File:Encoding' = 'utf8'
    'Export-Csv:NoTypeInformation' = $true
    'ConvertTo-Csv:NoTypeInformation' = $true
    'Receive-Job:Keep' = $true
    'Install-Module:AllowClobber' = $true
    'Install-Module:Force' = $true
    'Install-Module:SkipPublisherCheck' = $true
}</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2019/08/psdefaultparametervalues1a.jpg"><img class="alignnone size-full wp-image-17996" src="https://mikefrobbins.com/wp-content/uploads/2019/08/psdefaultparametervalues1a.jpg" alt="" width="859" height="381" /></a>

The first one in the list (<em>'Out-Default:OutVariable' = 'LastResult'</em>) is <a href="https://twitter.com/Jaykul/status/783051182805815296" target="_blank" rel="noopener noreferrer">one I picked up from Joel Bennett</a> to store the results of the last command in a variable named <em>$LastResult</em>. Since then, I've seen others with similar options except using double underscores for the variable name. What would be really nice is to have the option to save a specific number of the last results in different elements of a variable similarly to the way the <em>$Error</em> automatic variable works.

Many of the other options I've specified are self-explanatory. One of the areas where <em>$PSDefaultParameterValues</em> really shines is for commands that you always forget to set some option for that probably should be the default out of the box. When running ad-hoc commands and piping the results to <em>Export-Csv</em> or <em>ConvertTo-Csv</em>, I almost always forget to specify the <em>NoTypeInformation</em> parameter. That means having to re-run the command again, specifying that option. I use jobs in PowerShell so infrequently that the same is true for specifying the <em>Keep</em> parameter for <em>Receive-Job</em>. Never again though, now that I've specified those options in my <em>$PSDefaultParameterValues</em> preference variable.

The last three options have to do with installing modules from the PowerShell Gallery or other NuGet repositories. I consider those protections to be in place because of the same reasons the execution policy exists in PowerShell. To prevent users from unknowingly doing something. These three options take the safety off because I know what I'm doing and I'm willing to take the associated risks, although I definitely wouldn't add those options to a normal user's system.

The options specified for <em>$PSDefaultParameterValues</em> don't persist between PowerShell sessions so I've added them to my <a href="https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_profiles" target="_blank" rel="noopener noreferrer">PowerShell profile</a>. They can be cleared for the current session using the following command.
<pre class="lang:ps decode:true ">$PSDefaultParameterValues.Clear()</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2019/08/psdefaultparametervalues2a.jpg"><img class="alignnone size-full wp-image-17997" src="https://mikefrobbins.com/wp-content/uploads/2019/08/psdefaultparametervalues2a.jpg" alt="" width="859" height="272" /></a>

Although they can be cleared altogether as shown in the previous example, maybe you only want to disable them while running a command or two. There's an option to disable the specified values without clearing them.
<pre class="lang:ps decode:true">$PSDefaultParameterValues.Add('Disabled', $true)</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2019/08/psdefaultparametervalues3a.jpg"><img class="alignnone size-full wp-image-17998" src="https://mikefrobbins.com/wp-content/uploads/2019/08/psdefaultparametervalues3a.jpg" alt="" width="859" height="274" /></a>

To re-enable them, simply remove the <em>Disabled</em> option.
<pre class="lang:ps decode:true ">$PSDefaultParameterValues.Remove('Disabled')</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2019/08/psdefaultparametervalues4a.jpg"><img class="alignnone size-full wp-image-18001" src="https://mikefrobbins.com/wp-content/uploads/2019/08/psdefaultparametervalues4a.jpg" alt="" width="859" height="465" /></a>

What's in your <em>$PSDefaultParameterValues</em> preference variable?

For more information about <em>$PSDefaultParameterValues</em>, see the <a href="https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_parameters_default_values" target="_blank" rel="noopener noreferrer">About Parameters Default Values help topic</a>.

Thoughts, questions, comments? Please post them as a comment to this blog article.

µ