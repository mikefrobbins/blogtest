---
ID: 3933
post_title: >
  Interesting How People are Claiming to
  Have Same Beginner 1 Solution as the
  Expert Solution but Still Did Not Get 5
  Stars
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2012/04/23/interesting-how-people-are-claiming-to-have-same-beginner-1-solution-as-the-expert-solution-but-still-did-not-get-5-stars/
published: true
post_date: 2012-04-23 07:30:37
---
This past weekend I posted a <a href="http://mikefrobbins.com/2012/04/21/search-event-log-2012-powershell-scripting-games-beginner-event-9/" target="_blank">blog</a> about Beginner Event #9 of the 2012 PowerShell Scripting Games and I received a comment on it from <a href="http://twitter.com/ruddawg26" target="_blank">@ruddawg26</a>. I took a look at his profile and tweets. I found a tweet that was tweeted by him "<a href="https://twitter.com/#!/ruddawg26/status/192043193624297473" target="_blank">Interesting how people are claiming to have same beginner 1 solution as the expert solution but still did not get 5 stars #2012SG</a>" so I decided to investigate further which is how this blog came to be.

There's a blog article on the Hey, Scripting Guy! blog: "<a href="http://blogs.technet.com/b/heyscriptingguy/archive/2012/04/16/expert-commentary-2012-scripting-games-beginner-event-1.aspx" target="_blank">Expert Commentary: 2012 Scripting Games Beginner Event 1</a>" that contains the expert solution to <a href="http://blogs.technet.com/b/heyscriptingguy/archive/2012/04/02/the-2012-scripting-games-beginner-event-1-use-windows-powershell-to-identify-a-working-set-of-processes.aspx" target="_blank">Beginner Event 1</a>. I also took a look at <a href="https://2012sg.poshcode.org/2162" target="_blank">the script I submitted</a> and scripts from several other contestants.

No disrespect is meant by this blog and it is strictly my opinion that the expert solution doesn't meet the requirements of the scenario which is why the people who submitted the exact same solution as it to this event didn't receive 5 stars. Here's why:

This is the last sentence of the Scenario: <em>"To better get a handle on what is going on with the desktop computers, your boss has directed you to ascertain the top ten processes that are consuming memory resources on each computer"</em>.

When I run the expert solution against two computers, here's what I receive:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/04/2012ec-be1-1.png"><img class="alignnone size-full wp-image-3935" title="2012ec-be1-1" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/04/2012ec-be1-1.png" width="503" height="226" /></a>

As shown in the image above, that command shows the top 10 processes from <span style="text-decoration:underline;">all</span> computers sorted by memory working set, not from <span style="text-decoration:underline;">each</span> computer.

Now here's the solution I submitted which is run against the same two computers:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/04/2012ec-be1-2.png"><img class="alignnone size-full wp-image-3936" title="2012ec-be1-2" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/04/2012ec-be1-2.png" width="640" height="360" /></a>

As shown above, my solution displays the top 10 processes sorted by memory working set for <span style="text-decoration:underline;">each</span> computer as shown in the PSComputerName column which is what was specified in the scenario requirements. There's no reason not to use PowerShell remoting since the scenario states the following: <em>"Your desktops are all running Windows 7, and your servers are all running Windows Server 2008 R2. Your company has a single domain, and Windows PowerShell remoting is enabled on all computers—both servers and desktops"</em>.

Design point (2) <em>"The command you use should be CAPABLE of running remotely against other computers in the domain, but you are not required to have multiple computers available for this scenario"</em>. The same results as shown above can be produced by just using the local computer and specifying it twice as shown below to see how the results from multiple computers would be displayed:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/04/2012ec-be1-3.png"><img class="alignnone size-full wp-image-3941" title="2012ec-be1-3" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/04/2012ec-be1-3.png" width="640" height="365" /></a>

In the example below, I've piped the expert solution to <em>Format-Table</em> so we can see the computer name (MachineName) which makes the issue with it self explanatory:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/04/2012ec-be1-4.png"><img class="alignnone size-full wp-image-3951" title="2012ec-be1-4" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/04/2012ec-be1-4.png" width="489" height="335" /></a>

Running the expert solution against 1, 10, or 100 computers produces a total of 10 results, running my solution against 1 computer produces 10 results, 10 computers produces 100 results, and 100 computers produces 1000 results (the top 10 for <span style="text-decoration:underline;">each</span> computer).

There's a couple of other things that seem to be common among scripts that were submitted for beginner event #1.  These involve the last two design points: (4) <em>"Your command should return an object that could be piped to additional commands"</em> and (5) <em>"You should be able to write the results of your command to a file if required (but writing to a file is not a requirement for this scenario)"</em>.

If you added the code to output this command to a file as specified in design point 5, then you did not meet the requirement in design point 4 since outputting the command to a file does not produce an object and it can no longer be piped to additional commands. If you met design point 4 (which means you can't use a Format cmdlet such as <em>Format-Table</em>), then design point 5 was a given. I referenced that I could easily save the output of my command to a file in the description of my code entry on the <a href="https://2012sg.poshcode.org/2162" target="_blank">PowerShell Code Repository</a> for this event.

If my command is saved to a script, you can easily call the script as shown below and pipe the output to a file via <em>Out-File</em> or to <em>Format-Table</em> for custom formating, but if you add those options to the actual script it's removes the ability of being able to dynamically adjust the output as needed.

<a href="http://mikefrobbins.com/wp-content/uploads/2012/04/2012ec-be1-5.png"><img class="alignnone size-full wp-image-3962" title="2012ec-be1-5" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/04/2012ec-be1-5.png" width="640" height="212" /></a>

The one thing I did learn is that it's preferable to use $Env:ComputerName (which I hadn't heard of prior to the Scripting Games) for the local computer name instead of localhost since it resolves to an actual computer name otherwise the output will only display localhost as shown in the screenshot above. I learned this through research and viewing other contestants scripts (I didn't receive any comments on my beginner event #1 script).

µ