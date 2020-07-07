---
ID: 15814
post_title: >
  Safety to prevent entire script from
  running in the PowerShell ISE
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2017/11/02/safety-to-prevent-entire-script-from-running-in-the-powershell-ise/
published: true
post_date: 2017-11-02 07:30:39
---
I recently watched fellow Microsoft MVP <a href="https://twitter.com/MrThomasRayner" target="_blank" rel="noopener">Thomas Rayner</a> present a session on regular expressions for the <a href="https://twitter.com/MVPDays" target="_blank" rel="noopener">MVP Days</a> virtual conference. Sometimes it's the little things that are the biggest takeaways for me when I'm watching others present. One thing that wasn't related to regular expressions that I noticed was Thomas's way of preventing his entire script from running if F5 was accidentally pressed instead of F8 in the PowerShell ISE (Integrated Scripting Environment). If you're not aware, F5 runs the entire script (the demo would be over) and F8 runs the current selection. If F8 is pressed without having anything selected, the current line is run.

The code that Thomas used at the top of his demonstration script generates a terminating error if the entire script is run.
<pre class="lang:ps decode:true ">#region Come on man
throw "You're not supposed to hit F5"
#endregion"</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2017/10/ise-f5-safety1a.jpg"><img class="alignnone size-full wp-image-15815" src="http://mikefrobbins.com/wp-content/uploads/2017/10/ise-f5-safety1a.jpg" alt="" width="859" height="108" /></a>

I've used <em><a href="https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_break?view=powershell-5.1" target="_blank" rel="noopener">Break</a></em> in the past, but I've run into some scenarios where break didn't work since it's designed to break out of a loop. Once I ran into problems with <em>Break</em>, I started using <a href="https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.utility/start-sleep?view=powershell-5.1" target="_blank" rel="noopener"><em>Start-Sleep</em></a> to effectively pause the script long enough for me to figure out what I'd done. Thomas's example of using <a href="https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_throw?view=powershell-5.1" target="_blank" rel="noopener"><em>Throw</em></a> to generate a terminating error is better than either of those options though.

I'm totally going to steal (I mean borrow) Thomas's code and start using it in my demos when I present.

I posted this tip on Twitter during the virtual conference, but the problem is I won't be able to find <a href="https://twitter.com/mikefrobbins/status/923227990493990913" target="_blank" rel="noopener">that tweet</a> later since it isn't indexed for easy searchability.

<a href="https://twitter.com/mikefrobbins/status/923227990493990913" target="_blank" rel="noopener"><img class="alignnone wp-image-15819 size-full" src="http://mikefrobbins.com/wp-content/uploads/2017/10/ise-f5-safety2a.jpg" alt="" width="589" height="198" /></a>

According to Thomas, there are <a href="https://www.facebook.com/mvpdays/videos/1610860425640356" target="_blank" rel="noopener">videos</a> of the sessions from the MVPDays virtual conference and the code from his session can be found in his <a href="https://github.com/ThmsRynr/Presentation-Files/tree/master/Regex%20for%20Noobs" target="_blank" rel="noopener">Presentation-Files</a> repository on GitHub.

Did I mention that <a href="https://www.eventbrite.com/e/powershell-devops-global-summit-2018-registration-32452427083" target="_blank" rel="noopener">registration</a> for the <a href="https://powershell.org/summit/" target="_blank" rel="noopener">PowerShell &amp; DevOps Global Summit 2018</a> is now open? I'll be presenting there as well as Thomas Rayner who is referenced in this blog article.

µ