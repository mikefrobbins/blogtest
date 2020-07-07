---
ID: 9931
post_title: >
  Helping Others at Microsoft TechEd with
  PowerShell 911
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2014/05/22/helping-others-at-microsoft-teched-with-powershell-911/
published: true
post_date: 2014-05-22 07:30:52
---
While at Microsoft TechEd last week, I met a gentleman from Europe who was experiencing a particular issue with the <em>Get-ADUser</em> PowerShell cmdlet.

When <em>Get-ADUser</em> is used with a hard coded value such as name as shown in the following example, it returns the expected information without issue:
<pre class="lang:ps decode:true">Get-ADUser -Filter {Name -eq 'Administrator'}</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/05/052214-1.jpg"><img class="alignnone size-full wp-image-9932" src="http://mikefrobbins.com/wp-content/uploads/2014/05/052214-1.jpg" alt="052214-1" width="877" height="241" /></a>

The issue is that when the name, for example, is stored in a variable and double quotes are used to try to expand the variable, nothing is returned:
<pre class="lang:ps decode:true">$User = 'Administrator'
Get-ADUser -Filter {Name -eq "$user"}</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/05/052214-2.jpg"><img class="alignnone size-full wp-image-9933" src="http://mikefrobbins.com/wp-content/uploads/2014/05/052214-2.jpg" alt="052214-2" width="877" height="76" /></a>

For the life of us, we couldn't lay our hands on a test system with the Active Directory cmdlets on it at TechEd and neither of us had a computer with us that particular day. Luckily I had a test PowerShell Web Access system exposed to the Internet that I was able to log into from my phone over 3G, replicate the problem, and come up with a solution:

<a href="http://mikefrobbins.com/wp-content/uploads/2014/05/052214-3.jpg"><img class="alignnone size-full wp-image-9934" src="http://mikefrobbins.com/wp-content/uploads/2014/05/052214-3.jpg" alt="052214-3" width="600" height="337" /></a>

I brought this up at the PowerShell dinner at TechEd and ended up speaking to the person who is in charge of the team that came up with PowerShell Web Access. He actually had me bring it up on my phone at the dinner and took a picture of me holding my phone up while logged into PSWA to take back to his team. A bit of trivia that he told me about was that PowerShell Web Access was originally called "PowerShell 911".

The solution I gave was to use double quotes instead of curly braces and single quotes around the variable since the quote marks on the outside always win:
<pre class="lang:ps decode:true">Get-ADUser -Filter "Name -eq '$user'"</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/05/052214-4.jpg"><img class="alignnone size-full wp-image-9935" src="http://mikefrobbins.com/wp-content/uploads/2014/05/052214-4.jpg" alt="052214-4" width="877" height="239" /></a>

I saw the same gentleman at the closing party and mentioned that dropping the quotes altogether would probably allow it to work inside the curly braces, and indeed it does:
<pre class="lang:ps decode:true">Get-ADUser -Filter {Name -eq $user}</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/05/052214-5.jpg"><img class="alignnone size-full wp-image-9936" src="http://mikefrobbins.com/wp-content/uploads/2014/05/052214-5.jpg" alt="052214-5" width="877" height="242" /></a>

µ