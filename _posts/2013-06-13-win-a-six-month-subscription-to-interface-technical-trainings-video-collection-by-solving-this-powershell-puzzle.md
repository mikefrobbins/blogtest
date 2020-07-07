---
ID: 7519
post_title: 'Win a Six Month Subscription to Interface Technical Training&#8217;s Video Collection by Solving this PowerShell Puzzle!'
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2013/06/13/win-a-six-month-subscription-to-interface-technical-trainings-video-collection-by-solving-this-powershell-puzzle/
published: true
post_date: 2013-06-13 07:30:40
---
During the 2013 Scripting Games, I won three subscriptions, each good for six months of <a href="http://videotraining.interfacett.com/" target="_blank">video training</a> at <a href="http://www.interfacett.com/" target="_blank">Interface Technical Training</a>. I have a subscription already so I asked Don Jones about re-gifting my prizes since I don't need more than one subscription so here's your chance to win one.

<span style="line-height:1.5;">The first video training subscription that I won was given to Rohn Edwards who is the co-founder of the Mississippi PowerShell User Group. We exclude ourselves from the normal swag giveaways we have during our official meetings each month so I wanted Rohn to have one of these. Speaking of the </span><a style="line-height:1.5;" href="http://mspsug.com/" target="_blank">Mississippi PowerShell User Group</a><span style="line-height:1.5;">, our meetings are virtual so anyone from anywhere can attend. Where else can you find a PowerShell User Group whose founders are the 2012 Scripting Games advanced track winner (Rohn Edwards) and the 2013 Scripting Games advanced track winner (Mike F Robbins)?</span>

I tweeted out a mini-competition for the second video training subscription that I won which Rob Campbell won for being the first person to find my script in one of the events where our names were no longer listed on our entries and no one had voted on that particular entry of mine yet.

That leaves me with one voucher good for a six month subscription to <a href="http://videotraining.interfacett.com/" target="_blank">Interface Technical Training's video training collection</a>. Here's the puzzle you need to solve with PowerShell in order to win:

Your task is to write a short PowerShell one-liner that returns a list of PowerShell cmdlet names that do not have repeating characters in them:
<ul>
	<li>Only PowerShell commands that are classified as “Cmdlets” should be returned.</li>
	<li>Return only the name of the cmdlets.</li>
	<li>Non-repeating characters means the cmdlet name doesn't contain two or more of the same characters, for example: <em>Get-Help</em> would not be in the results because it contains two of the letter “E” and <em>Copy-Item</em> would be in the results because it doesn't contain any repeating characters.</li>
	<li>For the purposes of this contest, case does not matter. "E" and "e" are considered to be the same character. (Thanks to Rob Campbell for bringing this to my attention).</li>
</ul>
The shortest answer wins! Be aware, a space is a character too. In case of a tie, the person who submits a solution first wins.

Use the "Leave a Reply" box at the bottom of this blog article and submit your solution via a comment to this blog article by 5am (GMT) on Tuesday, June 25th. The winner will be announced on Thursday, June 27th on this blog and the prize will be awarded at that time.

Note: Entries (comments) will not be made public until after the submission deadline.

<span style="text-decoration:underline;"><strong>Update: 6/26/13</strong></span>
The winner is Carlo with the following entry which was 24 characters in length and produced the desired results:

[sourcecode language="powershell" autolinks="false"]
gcm -c c|sls '(.).*1'-N
[/sourcecode]

<a href="http://mikefrobbins.com/wp-content/uploads/2013/06/itt-winner.png"><img class="alignnone size-full wp-image-7636" alt="itt-winner" src="http://mikefrobbins.com/wp-content/uploads/2013/06/itt-winner.png" width="223" height="117" /></a>

Thank you to everyone who competed in this contest!

µ