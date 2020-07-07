---
ID: 15689
post_title: >
  Not Specifying a Verb in PowerShell is
  an Expensive Shortcut
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2017/09/27/not-specifying-a-verb-in-powershell-is-an-expensive-shortcut/
published: true
post_date: 2017-09-27 17:25:53
---
When working with PowerShell, there are lots of shortcuts that can be taken and while some of them may seem like a good idea at first, they usually end up costing you more trouble than they're worth in the long run.

I recently saw <a href="https://twitter.com/TroyMartinNet/status/913115013627486208" target="_blank" rel="noopener">a Tweet</a> that a fellow PowerShell enthusiast <a href="https://twitter.com/ToreGroneng" target="_blank" rel="noopener">Tore Groneng</a>‏ responded to which referenced running commands in PowerShell without specifying a verb. Such as "Service" for Get-Service.
<pre class="lang:ps decode:true">Service</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2017/09/no-verb1a.jpg"><img class="alignnone size-full wp-image-15691" src="http://mikefrobbins.com/wp-content/uploads/2017/09/no-verb1a.jpg" alt="" width="859" height="125" /></a>

That works great, right? Not so fast. It might appear to be a great shortcut on the surface, but what's occurring under the covers?

I originally got involved with that Tweet because one of the referenced nouns (Process) didn't work by itself.
<pre class="lang:ps decode:true">Process</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2017/09/no-verb2a.jpg"><img class="alignnone size-full wp-image-15693" src="http://mikefrobbins.com/wp-content/uploads/2017/09/no-verb2a.jpg" alt="" width="615" height="154" /></a>

My guess was that this was due to "Process" being a reserved word because it's used with Pipeline input as are Begin and End. <a href="https://twitter.com/IISResetMe" target="_blank" rel="noopener">Mathias Jessen</a> confirmed this and said it's because the parser gets in the way. He also stated that using the call operator with "Process" works fine.
<pre class="lang:ps decode:true ">&amp; Process</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2017/09/no-verb3a.jpg"><img class="alignnone size-full wp-image-15694" src="http://mikefrobbins.com/wp-content/uploads/2017/09/no-verb3a.jpg" alt="" width="859" height="128" /></a>

If you are going to use a shortcut like this, treat it like an alias and only use it interactively and never in code that you save whether it's in scripts, functions, or modules. This shortcut, however, is much, much worse from a performance standpoint than using the full command name or even its alias.

Be sure to <a href="https://github.com/PowerShell/PowerShell/issues/3987" target="_blank" rel="noopener">read Jason Shirk's comment to this GitHub issue</a> (Jason is a member of the PowerShell team at Microsoft). Here's the relevant portion of Jason's comment:

<em>"It is also very expensive - we first search normally (including the PATH), and if that fails, we repeat the search prepending Get-. Personally, I'd rather remove this misfeature than formalize it."</em>

You can see the problem that Jason referenced in his comment when viewing the output of the command that Mathias Jessen provided.
<pre class="lang:ps decode:true ">Trace-Command -Expression {Service} -Name CommandDiscovery -PSHost | Out-Null</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2017/09/no-verb4a.jpg"><img class="alignnone size-full wp-image-15695" src="http://mikefrobbins.com/wp-content/uploads/2017/09/no-verb4a.jpg" alt="" width="859" height="515" /></a>

Compare that output to when the full command name is used.
<pre class="lang:ps decode:true">Trace-Command -Expression {Get-Service} -Name CommandDiscovery -PSHost | Out-Null</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2017/09/no-verb5a.jpg"><img class="alignnone size-full wp-image-15696" src="http://mikefrobbins.com/wp-content/uploads/2017/09/no-verb5a.jpg" alt="" width="859" height="79" /></a>

Using an alias adds one additional step, but is still way more efficient than using just a noun.
<pre class="lang:ps decode:true ">Trace-Command -Expression {gsv} -Name CommandDiscovery -PSHost | Out-Null</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2017/09/no-verb6a.jpg"><img class="alignnone size-full wp-image-15698" src="http://mikefrobbins.com/wp-content/uploads/2017/09/no-verb6a.jpg" alt="" width="859" height="91" /></a>

<a href="https://twitter.com/devblackops" target="_blank" rel="noopener">Brandin Olin</a> responded to one of the original Tweets in the thread with some words of wisdom <em>"I’ll take verbose and explicit over clever and obtuse. Especially if I’m that “other guy” :)"</em> The other guy he was referencing was in response to Tore Groneng‏'s comment <em>"use it only interactively, never in scripts/modules. Think of the other guy and the performance impact :-)"</em>.

Saving those few characters of typing cost a lot of time in the long run. It's like taking a shortcut when driving and then getting lost.

If you're not following the PowerShell enthusiasts referenced in this blog article on Twitter, you should be. I definitely recommend reading through the comments to <a href="https://twitter.com/TroyMartinNet/status/913115013627486208" target="_blank" rel="noopener">the Tweet referenced in this blog article</a> and I've written this blog article because it will be much easier to locate and reference six months from now.

<hr />

Update
Mathias Jessen provided the following image with his annotations.

According to the <a href="https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_command_precedence?view=powershell-5.1" target="_blank" rel="noopener">about_Command_Precedence</a> help topic, an implied <em>Get-</em> causes 1, 2, 3, 4, and then 1 again.
<pre class="lang:ps decode:true">Get-Help -Name about_Command_Precedence</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2017/09/no-verb7a.jpg"><img class="alignnone size-full wp-image-15707" src="http://mikefrobbins.com/wp-content/uploads/2017/09/no-verb7a.jpg" alt="" width="779" height="198" /></a>

"Incorrect" in the previous image refers to the fact that <em>Help</em> is a function, not a cmdlet and that it resolves to the <em>Help</em> function so it never has to look for a cmdlet by that name.

Jason Shirk replied stating that 1, 2, and 3 could also be slow with deep call stacks and that there was a lot of room for improvement.

One of the things I didn't originally mention in this blog article is that the cost of not specifying a verb is always expensive, but it could be even worse if you have lots of items in your path since it searches through the path looking for a command by that name and if it finds one, guess what? It didn't run the command you intended.

If the PowerShell team meant for this to be a valid feature, it looks like they would have just added aliases to the cmdlets similarly to how <em>List</em> is an alias for <em>Format-List</em>.

µ