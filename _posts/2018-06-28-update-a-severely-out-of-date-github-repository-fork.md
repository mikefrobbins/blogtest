---
ID: 16646
post_title: >
  Update a Severely Out of Date GitHub
  Repository Fork
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2018/06/28/update-a-severely-out-of-date-github-repository-fork/
published: true
post_date: 2018-06-28 07:30:54
---
Recently, I received <a href="https://github.com/planetpowershell/planetpowershell/issues/110" target="_blank" rel="noopener">notification</a> from <a href="https://www.planetpowershell.com/" target="_blank" rel="noopener">Planet PowerShell</a> that they will be removing blogs that do not support HTTPS as of August 1st. I had forked their repo on GitHub over a year ago, added my blog, and submitted a pull request. I hadn't touched my fork of their repo since then. It was severely out of date and in an unknown state.

Taking a look at it on my system showed my local copy only had one remote which was my fork on GitHub.
<pre class="lang:ps decode:true ">git remote show
git remote show origin</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2018/06/git-update-fork1a.jpg"><img class="alignnone size-full wp-image-16647" src="https://mikefrobbins.com/wp-content/uploads/2018/06/git-update-fork1a.jpg" alt="" width="859" height="247" /></a>

I could see that one of the files had been updated in my local copy, but not committed. I didn't have a clue about what changes had been made or why they were made.
<pre class="lang:ps decode:true">git status</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2018/06/git-update-fork2a.jpg"><img class="alignnone size-full wp-image-16648" src="https://mikefrobbins.com/wp-content/uploads/2018/06/git-update-fork2a.jpg" alt="" width="859" height="157" /></a>

The git diff command can be used to show what's been changed as shown in the following example.
<pre class="lang:ps decode:true ">git diff src/Firehose.Web/Authors/MikeRobbins.cs</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2018/06/git-update-fork3a.jpg"><img class="alignnone size-full wp-image-16649" src="https://mikefrobbins.com/wp-content/uploads/2018/06/git-update-fork3a.jpg" alt="" width="859" height="378" /></a>

I don't care about those changes. I'll simply check that file out again to discard those changes.
<pre class="lang:ps decode:true">git checkout src/Firehose.Web/Authors/MikeRobbins.cs</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2018/06/git-update-fork4a.jpg"><img class="alignnone size-full wp-image-16650" src="https://mikefrobbins.com/wp-content/uploads/2018/06/git-update-fork4a.jpg" alt="" width="859" height="78" /></a>

Now I'll add the upstream remote and fetch any updates, or a copy of it in this scenario.
<pre class="lang:ps decode:true ">git remote add upstream https://github.com/planetpowershell/planetpowershell.git
git fetch upstream</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2018/06/git-update-fork5a.jpg"><img class="alignnone size-full wp-image-16651" src="https://mikefrobbins.com/wp-content/uploads/2018/06/git-update-fork5a.jpg" alt="" width="859" height="186" /></a>

All that needs to be done now is to merge the changes from upstream into my current branch.
<pre class="lang:ps decode:true">git merge upstream/master</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2018/06/git-update-fork6a.jpg"><img class="alignnone size-full wp-image-16652" src="https://mikefrobbins.com/wp-content/uploads/2018/06/git-update-fork6a.jpg" alt="" width="859" height="210" /></a>
<blockquote>I know why they call this a GitHub fork. Stick a fork in it, this repo is done!</blockquote>
That was wishful thinking. While the previous command would work in many cases, this repo is too far out of date. I could simply delete my local copy and my forked copy of it on GitHub and then fork it again along with cloning it locally again. That's a lot of trouble and work. I'll simple do a hard reset on my local copy and then force those changes into my forked copy on GitHub.
<blockquote>Warning: Use the following commands at your own risk!</blockquote>
Per <a href="https://git-scm.com/docs/git-reset" target="_blank" rel="noopener">the Git documentation</a>, the following command <em>"Resets the index and working tree. Any changes to tracked files in the working tree are discarded"</em>. You will loose any changes you've made using these commands.

<strong>Make sure you understand what is happening before proceeding.</strong>
<pre class="lang:ps decode:true ">git reset --hard upstream/master
git push --force</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2018/06/git-update-fork7a.jpg"><img class="alignnone size-full wp-image-16653" src="https://mikefrobbins.com/wp-content/uploads/2018/06/git-update-fork7a.jpg" alt="" width="859" height="211" /></a>

My local copy and my forked copy on GitHub are now in sync with the Planet PowerShell repo on GitHub.
<pre class="lang:ps decode:true ">git status
git remote show origin
git remote show upstream</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2018/06/git-update-fork8a.jpg"><img class="alignnone size-full wp-image-16654" src="https://mikefrobbins.com/wp-content/uploads/2018/06/git-update-fork8a.jpg" alt="" width="859" height="404" /></a>

Now I can make the necessary changes to my profile file to direct readers on Planet PowerShell to my blog site via HTTPS instead of HTTP and everyone will be happy!

µ