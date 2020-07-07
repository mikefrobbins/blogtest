---
ID: 13026
post_title: >
  Getting Started with the Git Version
  Control System
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2016/01/21/getting-started-with-the-git-version-control-system/
published: true
post_date: 2016-01-21 07:30:13
---
There's a lot to Git and there's tons of information all over the web about it. There's so much information out there that you might feel overwhelmed when you first start trying to learn what Git is and how to use it. The purpose of this blog article is to help you install Git, teach you a few basics, and point you in the right direction to learn more.

I'll start with a little background information: Back in 2009 when I started blogging on this site, I used the TechNet script repository to share my PowerShell code that I wrote about in my blog articles. Then in 2014, I started using GitHub instead of the TechNet script repository to accomplish the same sort of thing. I primarily used both as a way to share my code. I really wasn't taking advantage of the source control features in GitHub other than using it as a way to get back to a previous major version. I would develop my scripts outside of what GitHub was aware of, placing the finished product in the default master branch that GitHub created, create a commit, and sync those changes to the online repository. Needless to say it was a haphazard process especially when trying to collaborate with others.

So I decided to start learning Git which is what GitHub is built on. I know there are PowerShell cmdlets for Git, but I felt that learning pure Git would give me a better understanding and then I could learn the PowerShell cmdlets for it.

The first resource that you need to know about is <a href="https://git-scm.com/" target="_blank">the home page for Git</a>. From there you can download <a href="https://git-for-windows.github.io/" target="_blank">Git for Windows</a> or for other operating systems that you plan to run it on. The install is a clicker-size (even the click-next admin can install it). You're safe taking all the default options during the install.

While I won't get bogged down in the details, these particular options have to do with sharing your code with people running different operating systems. Take the defaults and move on:

<a href="http://mikefrobbins.com/wp-content/uploads/2016/01/git101-1a.png"><img class="alignnone wp-image-13030 size-full" src="http://mikefrobbins.com/wp-content/uploads/2016/01/git101-1a.png" alt="git101-1a" width="499" height="387" /></a>

I've launched Git Bash. There's also Git CMD if you feel more comfortable with it, although all of the examples I'll show in this blog article use Git Bash (on Windows).

Coming from a PowerShell background, the first thing I wanted to know was how to use Git's help system. Typing <em>git help</em> shows some basic help information. Typing <em>git help help</em> opens more detailed help information in a webpage.
<pre class="lang:sh decode:true">git help</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2016/01/git101-3a.jpg" rel="attachment wp-att-13040"><img class="alignnone size-full wp-image-13040" src="http://mikefrobbins.com/wp-content/uploads/2016/01/git101-3a.jpg" alt="git101-3a" width="821" height="418" /></a>

If you're looking for help on a specific command, type <em>git help &lt;command_name&gt;</em> and the help page for that command will be opened as a webpage. The following example opens the help information for the <em>git init</em> command:
<pre class="lang:sh decode:true">git help init</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2016/01/git101-4a.jpg" rel="attachment wp-att-13042"><img class="alignnone size-full wp-image-13042" src="http://mikefrobbins.com/wp-content/uploads/2016/01/git101-4a.jpg" alt="git101-4a" width="821" height="145" /></a>

The following command shows the version of Git that you're currently running. Notice that version begins with two dashes and that's something you'll find common to all parameters that are spelled out (if the parameter is a single letter, it only uses a single dash):
<pre class="lang:sh decode:true">git --version</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2016/01/git101-2a.jpg" rel="attachment wp-att-13039"><img class="alignnone size-full wp-image-13039" src="http://mikefrobbins.com/wp-content/uploads/2016/01/git101-2a.jpg" alt="git101-2a" width="821" height="147" /></a>

One of the first things you'll want to do is configure Git. At a minimum, set the user name and email address since this information will be used in all of your commits. Remember that if you're sharing any of your Git repositories publicly online, this information (specifically your email address) will be made publicly available. For this reason, I've specified my <a href="https://help.github.com/articles/keeping-your-email-address-private/" target="_blank">private GitHub email address</a> instead of my real email address.
<pre class="lang:sh decode:true">git config --global user.name "your name"
git config --global user.email "your email address"</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2016/01/git101-5a.jpg" rel="attachment wp-att-13044"><img class="alignnone size-full wp-image-13044" src="http://mikefrobbins.com/wp-content/uploads/2016/01/git101-5a.jpg" alt="git101-5a" width="821" height="179" /></a>

The previous commands set those configuration options globally for your user. It's also possible to set these options at the machine and/or repository level. To learn more run <em>git help config</em>.

You can verify this information was indeed set as shown in the following example:
<pre class="lang:sh decode:true">git config user.name
git config user.email</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2016/01/git101-6a.jpg" rel="attachment wp-att-13045"><img class="alignnone size-full wp-image-13045" src="http://mikefrobbins.com/wp-content/uploads/2016/01/git101-6a.jpg" alt="git101-6a" width="821" height="209" /></a>

You could also list all of the contents of the global config file by running the following command:
<pre class="lang:sh decode:true">git config --list</pre>
I want to create a new repository on my machine to keep track of the changes I make to my MrSQL PowerShell script module. I create a directory named "MrSQL" which will be used as the working folder for this Git repository. I change into that directory and then run the <em>git init</em> command to initialize a new git repository:
<pre class="lang:sh decode:true">mkdir MrSQL
cd MrSQL
git init</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2016/01/git101-7a.jpg" rel="attachment wp-att-13048"><img class="alignnone size-full wp-image-13048" src="http://mikefrobbins.com/wp-content/uploads/2016/01/git101-7a.jpg" alt="git101-7a" width="821" height="243" /></a>

That creates a hidden .git folder which is what keeps track of your changes (don't mess with this folder if you value the data in your repository):
<pre class="lang:sh decode:true">ls -a</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2016/01/git101-8a.jpg" rel="attachment wp-att-13050"><img class="alignnone size-full wp-image-13050" src="http://mikefrobbins.com/wp-content/uploads/2016/01/git101-8a.jpg" alt="git101-8a" width="821" height="148" /></a>

I've also placed a PowerShell script module (.psm1) and manifest (.psd1) in the folder shown in the previous example that I want Git to keep track of the changes for.

The <em>git status</em> command shows the current status of the working folder:
<pre class="lang:sh decode:true">git status</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2016/01/git101-9a.jpg" rel="attachment wp-att-13052"><img class="alignnone size-full wp-image-13052" src="http://mikefrobbins.com/wp-content/uploads/2016/01/git101-9a.jpg" alt="git101-9a" width="821" height="304" /></a>

Notice in the previous example, there are two untracked files that Git is aware of but not currently keeping track of.

You have to tell Git what files to keep track of. I want to track all files that are currently in the working folder so I'll run "<em>git add .</em>" to add all of them to the staging area which is sometimes referred to as an index. Then when I run <em>git status</em> again, you see that both files are staged but not committed:
<pre class="lang:sh decode:true">git add .
git status</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2016/01/git101-10a.jpg" rel="attachment wp-att-13054"><img class="alignnone size-full wp-image-13054" src="http://mikefrobbins.com/wp-content/uploads/2016/01/git101-10a.jpg" alt="git101-10a" width="821" height="371" /></a>

As you can see in the previous example, many commands give you tips when they're run. You can learn a lot by reading through these tips.

To commit these changes to the repository, use the <em>git commit</em> command:
<pre class="lang:sh decode:true">git commit -m "Added MrSQL script module and manifest files."</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2016/01/git101-11a.jpg" rel="attachment wp-att-13056"><img class="alignnone size-full wp-image-13056" src="http://mikefrobbins.com/wp-content/uploads/2016/01/git101-11a.jpg" alt="git101-11a" width="821" height="225" /></a>

Notice that the repository is now up to date with what's in the working folder:
<pre class="lang:sh decode:true">git status</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2016/01/git101-12a.jpg" rel="attachment wp-att-13058"><img class="alignnone size-full wp-image-13058" src="http://mikefrobbins.com/wp-content/uploads/2016/01/git101-12a.jpg" alt="git101-12a" width="821" height="161" /></a>

The <em>git log</em> command shows the history of the repository:
<pre class="lang:sh decode:true">git log</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2016/01/git101-13a.jpg" rel="attachment wp-att-13059"><img class="alignnone size-full wp-image-13059" src="http://mikefrobbins.com/wp-content/uploads/2016/01/git101-13a.jpg" alt="git101-13a" width="821" height="212" /></a>

Notice that the commit is referenced by a SHA1 hash (the 40 character hexadecimal number in yellow next to the word commit).

Now I'm going to tag that commit with a human readable name so I'll know that it's version 1.0 of my MrSQL module:
<pre class="lang:sh decode:true">git tag v1.0 -m "MrSQL PowerShell Module Version 1.0" 2ae04e06d07fe8b21be7167a0cb099a42741ad8b</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2016/01/git101-14a.jpg" rel="attachment wp-att-13061"><img class="alignnone size-full wp-image-13061" src="http://mikefrobbins.com/wp-content/uploads/2016/01/git101-14a.jpg" alt="git101-14a" width="821" height="144" /></a>

I'm going to be working on a new version of my MrSQL module. I'll create a development branch so that my changes don't accidentally get deployed before they're ready. By default, the <em>git branch</em> command creates a new branch that's based off of the code that's committed to whatever branch is currently checked out. I think of a new branch as an isolated environment.

The <em>git checkout</em> command places the code that's in the specified branch into the working folder. If the working folder currently has any unsaved changes, you'll get a warning and you'll have a chance to either commit or stash the changes because checking out another branch removes whatever is currently in the working folder.
<pre class="lang:sh decode:true">git branch dev
git checkout dev
ls</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2016/01/git101-15a.jpg" rel="attachment wp-att-13063"><img class="alignnone size-full wp-image-13063" src="http://mikefrobbins.com/wp-content/uploads/2016/01/git101-15a.jpg" alt="git101-15a" width="821" height="260" /></a>

I've now completed my changes to my MrSQL module and I'm ready to commit the changes to the repository. Remember to commit and commit often. At this point the changes are not saved except to the files that exist in the working folder.

Notice what the file system currently knows about versus what Git knows about for the working folder:
<pre class="lang:sh decode:true">ls
git ls-files</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2016/01/git101-16a.jpg" rel="attachment wp-att-13064"><img class="alignnone size-full wp-image-13064" src="http://mikefrobbins.com/wp-content/uploads/2016/01/git101-16a.jpg" alt="git101-16a" width="821" height="302" /></a>

The <em>git status</em> command shows that two files have been modified and several files have been added that are not currently tracked:
<pre class="lang:sh decode:true">git status</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2016/01/git101-17a.jpg" rel="attachment wp-att-13065"><img class="alignnone size-full wp-image-13065" src="http://mikefrobbins.com/wp-content/uploads/2016/01/git101-17a.jpg" alt="git101-17a" width="821" height="500" /></a>

The <em>git diff</em> command can be used to view the specific details of what's different between the files that are currently in the working folder versus what the staging area is aware of. This information is displayed a page at a time. Press the spacebar to page down to the next page or q to quit.
<pre class="lang:sh decode:true">git diff</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2016/01/git101-25a.jpg" rel="attachment wp-att-13126"><img class="alignnone size-full wp-image-13126" src="http://mikefrobbins.com/wp-content/uploads/2016/01/git101-25a.jpg" alt="git101-25a" width="821" height="271" /></a>

Although the two modified files were previously added to the staging area to be tracked, they still have to be added again. Any time a file in the working folder is changed whether it's already being tracked or not, it must be added to the staging area first before it can be commited to the repository. What I could do since the two modified files were already being tracked is to specify the -a parameter when performing a commit to automatically add and commit them with a single command. I'll show you the problem this creates if you have both tracked and untracked files that you want in the same commit:
<pre class="lang:sh decode:true">git commit -a -m "Moved functions from PSM1 file to PS1 files."
git status</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2016/01/git101-18a.jpg" rel="attachment wp-att-13066"><img class="alignnone size-full wp-image-13066" src="http://mikefrobbins.com/wp-content/uploads/2016/01/git101-18a.jpg" alt="git101-18a" width="821" height="483" /></a>

As you can see, the shortcut used in the previous example to add and commit with a single command only works for files that are already being tracked. What I should have done in this scenario is to add all of the files to the staging area with the <em>git add</em> command and then commit them so they would all be in the same commit. The problem now is that I committed those two files to the repository and I meant to commit all of the changes. I want to fix this without having to performing another separate commit and I also don't want to loose any of the history of what's been done.

Here's what the <em>git log</em> command currently shows:
<pre class="lang:sh decode:true">git log --oneline</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2016/01/git101-19a.jpg" rel="attachment wp-att-13071"><img class="alignnone size-full wp-image-13071" src="http://mikefrobbins.com/wp-content/uploads/2016/01/git101-19a.jpg" alt="git101-19a" width="821" height="161" /></a>

Instead of trying to use the <em>git reset</em> command, I'll go ahead and add all of those untracked files in the working folder to the staging area:
<pre class="lang:sh decode:true">git add .
git status</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2016/01/git101-20a.jpg" rel="attachment wp-att-13072"><img class="alignnone size-full wp-image-13072" src="http://mikefrobbins.com/wp-content/uploads/2016/01/git101-20a.jpg" alt="git101-20a" width="821" height="415" /></a>

Use the <em>git diff</em> command except with the <em>staged</em> parameter to see the detailed differences between the files in the staging area versus the ones that were previously committed to the repository:
<pre class="lang:sh decode:true">git diff --staged</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2016/01/git101-26a.jpg" rel="attachment wp-att-13128"><img class="alignnone size-full wp-image-13128" src="http://mikefrobbins.com/wp-content/uploads/2016/01/git101-26a.jpg" alt="git101-26a" width="821" height="222" /></a>

Now I'll amend the previous commit, adding whatever changes exist in the staging area to it:
<pre class="lang:sh decode:true">git commit --amend</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2016/01/git101-21a.jpg" rel="attachment wp-att-13073"><img class="alignnone size-full wp-image-13073" src="http://mikefrobbins.com/wp-content/uploads/2016/01/git101-21a.jpg" alt="git101-21a" width="821" height="514" /></a>

The previous command opened Vim since I didn't specify the -m parameter along with a commit message, but since I didn't want to change the message, I simply pressed :q to exit. You can change the default editor to Notepad using:
<pre class="lang:sh decode:true">git config --global core.editor "notepad"</pre>
Now I'll change back to the master branch. Notice what files are in the working folder while I have the files from the dev branch checked out. It's important to note that checking out a branch changes the actually files that are located in the working folder. The physical location on disk does not change. After checking out the master branch, all of the files that were in the working folder are removed and replaced by the ones from the most recent commit of the master branch. I could also checkout a previous commit of a branch by specifying a tag name or SHA1 hash of a commit to check out an older version of the files.
<pre class="lang:sh decode:true">ls
git checkout master
ls</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2016/01/git101-22a.jpg" rel="attachment wp-att-13074"><img class="alignnone size-full wp-image-13074" src="http://mikefrobbins.com/wp-content/uploads/2016/01/git101-22a.jpg" alt="git101-22a" width="821" height="350" /></a>

Since I've completed my changes to the MrSQL PowerShell module and I'm ready for those changes to go into production, I now need to merge those changes from the dev branch into master so they're placed in the production code base which is what I'm using the master branch for. All I have to do is run the <em>git merge</em> command, specifying the branch to merge from and it will be merged into the checked out branch (which is master). Since no commits have occurred in the master branch since the dev branch was created, a fast-forward merge is performed:
<pre class="lang:sh decode:true">git merge dev</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2016/01/git101-23a.jpg" rel="attachment wp-att-13075"><img class="alignnone size-full wp-image-13075" src="http://mikefrobbins.com/wp-content/uploads/2016/01/git101-23a.jpg" alt="git101-23a" width="821" height="514" /></a>

With fast-forward merges, a commit isn't performed after the merge occurs, but it doesn't need to since it only fast-forwards up to the most recent commit from the dev branch:
<pre class="lang:sh decode:true">ls
git log --oneline</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2016/01/git101-24a.jpg" rel="attachment wp-att-13076"><img class="alignnone size-full wp-image-13076" src="http://mikefrobbins.com/wp-content/uploads/2016/01/git101-24a.jpg" alt="git101-24a" width="821" height="304" /></a>

I'll create another tag since this new version is going to be version 2.0 of my MrSQL PowerShell module:
<pre class="lang:sh decode:true ">git tag v2.0 -m "MrSQL PowerShell Module Version 2.0" 5717744
git tag</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2016/01/git101-27b.jpg" rel="attachment wp-att-13130"><img class="alignnone size-full wp-image-13130" src="http://mikefrobbins.com/wp-content/uploads/2016/01/git101-27b.jpg" alt="git101-27b" width="821" height="208" /></a>

Tags remind you which commit is what release version because there may be hundreds of commits between versions. I can also checkout a specific version of the code that's stored in a branch based on the tag name if I ever need to fix a bug in a previously released version.

Want to learn more? Keep an eye on this blog site.

You can download the eBook version of <a href="https://git-scm.com/book/en/v2" target="_blank">Pro Git 2nd Edition</a> for free (it's open source). You can also try out Git from <a href="http://try.github.com/" target="_blank">code school</a> without having to install anything. Both of these resources can also be found on <a href="https://git-scm.com/" target="_blank">the home page for Git</a>.

<span style="text-decoration: underline;">Update 3/28/16 - Additional related blog articles on this site:</span>
<a href="http://mikefrobbins.com/2016/01/28/getting-started-with-the-git-version-control-system-part-2/" target="_blank">Getting Started with the Git Version Control System – Part 2</a>
<a href="http://mikefrobbins.com/2016/02/09/configuring-the-powershell-ise-for-use-with-git-and-github/" target="_blank">Configuring the PowerShell ISE for use with Git and GitHub</a>
<a href="http://mikefrobbins.com/2016/02/18/git-status-doesnt-know-if-your-local-repository-is-out-of-date/" target="_blank">Git status doesn’t know if your local repository is out of date</a><a href="http://mikefrobbins.com/2016/02/18/git-status-doesnt-know-if-your-local-repository-is-out-of-date/" target="_blank">
</a>

µ