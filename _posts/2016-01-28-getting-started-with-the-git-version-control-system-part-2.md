---
ID: 13180
post_title: 'Getting Started with the Git Version Control System &#8211; Part 2'
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2016/01/28/getting-started-with-the-git-version-control-system-part-2/
published: true
post_date: 2016-01-28 07:30:52
---
In <a href="http://mikefrobbins.com/2016/01/21/getting-started-with-the-git-version-control-system/" target="_blank">my last blog article</a>, I demonstrated a few Git basics which were all performed on a local repository. Today I'll pick up where I left off in that blog article and clone my local MrSQL repository to a file server so others can clone it to their machine from there. Git is a distributed version control system and others could simply clone the repository to their machine directly from mine but placing it on a server will give us a more centralized location that's backed up and one that will always be available to other users. All changes are still performed against your local copy of the repository and changes are simply pushed or pulled from this designated central location that my team will use.

Cloning a repository to a network location is just like cloning it to another local directory. I'll specify the <em>bare</em> parameter since I don't won't the repository that's stored on the server to have a working folder:
<pre class="lang:sh decode:true">git clone MrSQL //dc01/Git/MrSQL.git --bare</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2016/01/git102-1a.png" rel="attachment wp-att-13182"><img class="alignnone size-full wp-image-13182" src="http://mikefrobbins.com/wp-content/uploads/2016/01/git102-1a.png" alt="git102-1a" width="821" height="160" /></a>

I'll add the cloned copy of the repository from the previous example (the one on the server) as a remote named origin to my local MrSQL repository:
<pre class="lang:sh decode:true">git remote
git remote add origin //dc01/Git/MrSQL.git
git remote
git remote -v show</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2016/01/git102-2a.jpg" rel="attachment wp-att-13184"><img class="alignnone size-full wp-image-13184" src="http://mikefrobbins.com/wp-content/uploads/2016/01/git102-2a.jpg" alt="git102-2a" width="821" height="321" /></a>

Even though I've added the repository on the server as a remote, changes still aren't being tracked between the two. I'll push my changes (even though there aren't currently any differences) to the remote and set it as the upstream copy. Notice the differences in the statuses before and after running that command:
<pre class="lang:sh decode:true">git status
git push --set-upstream origin master
git status</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2016/01/git102-3a.png" rel="attachment wp-att-13186"><img class="alignnone size-full wp-image-13186" src="http://mikefrobbins.com/wp-content/uploads/2016/01/git102-3a.png" alt="git102-3a" width="821" height="338" /></a>

Another member of my team who I'll be collaborating with on this repository can now pull down a clone of it:
<pre class="lang:sh decode:true ">git clone //dc01/Git/MrSQL.git MrSQL</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2016/01/git102-4a.jpg" rel="attachment wp-att-13188"><img class="alignnone size-full wp-image-13188" src="http://mikefrobbins.com/wp-content/uploads/2016/01/git102-4a.jpg" alt="git102-4a" width="821" height="159" /></a>

When you start out by cloning from a remote, everything is already setup automatically for your local copy of the repository to keep track of differences between it and the remote one. It's also already setup to know where to push its changes to by default:
<pre class="lang:sh decode:true ">git remote -v show
git status
git push</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2016/01/git102-7a.jpg" rel="attachment wp-att-13196"><img class="alignnone size-full wp-image-13196" src="http://mikefrobbins.com/wp-content/uploads/2016/01/git102-7a.jpg" alt="git102-7a" width="821" height="319" /></a>

Now we can push our changes to this repository and pull one another's changes from it. I'll cover those topics in a future blog article.

µ