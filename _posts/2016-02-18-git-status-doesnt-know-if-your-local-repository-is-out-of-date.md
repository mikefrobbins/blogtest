---
ID: 13363
post_title: 'Git status doesn&#8217;t know if your local repository is out of date'
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2016/02/18/git-status-doesnt-know-if-your-local-repository-is-out-of-date/
published: true
post_date: 2016-02-18 07:30:57
---
To setup the scenario that will be demonstrated in this blog article, a new commit has been pushed to the dev branch of my PowerShell repository on GitHub from a computer named PC01. Then I switched over to using to an alternate computer named PC02 that was up to date prior to that latest commit being pushed to GitHub from PC01. This means that the dev branch of the PowerShell repository on PC02 is one commit behind the remote origin on GitHub.

You would think that checking the status of the dev branch for the PowerShell repository on PC02 would show it as being out of date by one commit, but it doesn't. It says "<em>Your branch is up-to-date with 'origin/dev'</em>":
<pre class="lang:ps decode:true ">git status</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2016/02/git-outofdate1a.png" rel="attachment wp-att-13364"><img class="alignnone size-full wp-image-13364" src="http://mikefrobbins.com/wp-content/uploads/2016/02/git-outofdate1a.png" alt="git-outofdate1a" width="859" height="119" /></a>

Everything looks correct when double checking the settings for the remote origin:
<pre class="lang:ps decode:true">git remote -v</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2016/02/git-outofdate2a.png" rel="attachment wp-att-13365"><img class="alignnone size-full wp-image-13365" src="http://mikefrobbins.com/wp-content/uploads/2016/02/git-outofdate2a.png" alt="git-outofdate2a" width="859" height="105" /></a>

The simplest way to know if the branch is out of date is to run the following command:
<pre class="lang:ps decode:true ">git remote show origin</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2016/02/git-outofdate3a.png" rel="attachment wp-att-13366"><img class="alignnone size-full wp-image-13366" src="http://mikefrobbins.com/wp-content/uploads/2016/02/git-outofdate3a.png" alt="git-outofdate3a" width="859" height="214" /></a>

You could also use the fetch command with the dry run option, but I prefer the previous command.
<pre class="lang:ps decode:true">git fetch -v --dry-run</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2016/02/git-outofdate4a.png" rel="attachment wp-att-13367"><img class="alignnone size-full wp-image-13367" src="http://mikefrobbins.com/wp-content/uploads/2016/02/git-outofdate4a.png" alt="git-outofdate4a" width="859" height="177" /></a>

I'm going to disable the network card on PC02 to show you a secret about checking the status of your Git repository:
<pre class="lang:ps decode:true">Get-NetAdapter -Name vEthernet* | Disable-NetAdapter -PassThru -Confirm:$false</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2016/02/git-outofdate21a.png" rel="attachment wp-att-13390"><img class="alignnone size-full wp-image-13390" src="http://mikefrobbins.com/wp-content/uploads/2016/02/git-outofdate21a.png" alt="git-outofdate21a" width="857" height="152" /></a>

Most Git commands run against your local repository which is one of the reasons that Git is so fast and it's also one of the strengths of Git due to its ability to allow you to work while being disconnected from the network. The problem this creates though, is that checking the status of your Git repository doesn't actually communicate with the remote origin to tell you what the current status is. The status is based off of the last time a fetch or pull was performed which is why checking the status doesn't necessarily provide accurate information.

Notice that no errors are generated when checking the status of my PowerShell repository while being disconnected from the network:
<pre class="lang:ps decode:true">git status</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2016/02/git-outofdate1a.png" rel="attachment wp-att-13364"><img class="alignnone size-full wp-image-13364" src="http://mikefrobbins.com/wp-content/uploads/2016/02/git-outofdate1a.png" alt="git-outofdate1a" width="859" height="119" /></a>

An error is generated when I run the command that I previously recommended that actually reports the correct status of whether or not my local repository is out of date with the remote origin one:
<pre class="lang:ps decode:true ">git remote show origin</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2016/02/git-outofdate22a.png" rel="attachment wp-att-13391"><img class="alignnone size-full wp-image-13391" src="http://mikefrobbins.com/wp-content/uploads/2016/02/git-outofdate22a.png" alt="git-outofdate22a" width="859" height="95" /></a>

If you're like me, you want to know a little more about what's going on under the hood. I fired up <a href="https://technet.microsoft.com/en-us/sysinternals/processmonitor.aspx" target="_blank" rel="noopener noreferrer">process monitor</a> and used the file monitor portion of it to determine that Git status uses the SHA1 hash that's stored in the following file to know what the latest commit is for the dev branch of my PowerShell repository on the remote origin:
<pre class="lang:ps decode:true ">Get-Content -Path .\.git\refs\remotes\origin\dev</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2016/02/git-outofdate23a.png" rel="attachment wp-att-13394"><img class="alignnone size-full wp-image-13394" src="http://mikefrobbins.com/wp-content/uploads/2016/02/git-outofdate23a.png" alt="git-outofdate23a" width="859" height="92" /></a>

That file is only updated during a fetch or pull which explains why Git status has no idea that a more recent commit has been added to the remote origin.

My point about most git commands running locally has been made so I'll re-enable the network card on PC02:
<pre class="lang:ps decode:true">Get-NetAdapter -Name vEthernet* | Enable-NetAdapter -PassThru</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2016/02/git-outofdate29a.png" rel="attachment wp-att-13434"><img class="alignnone size-full wp-image-13434" src="http://mikefrobbins.com/wp-content/uploads/2016/02/git-outofdate29a.png" alt="git-outofdate29a" width="859" height="132" /></a>

Now to leverage my existing PowerShell skills along with some Git commands to work a little magic. First, I'll return the SHA1 hash of the most recent dev branch commit for my local PowerShell repository and store it in a variable named localCommit. Then I'll return the same thing for the remote origin and store it in a variable named remoteCommit. I'll compare the two to see if they're equal. Next I'll run a command so if they're not equal, run git fetch. Lastly, I'll run Git status which now shows that my local repository is indeed out of date by one commit:
<pre class="lang:ps decode:true ">($localCommit = git rev-list --all -n1)
($remoteCommit = (git ls-remote origin dev) -replace '\s.*$')
$localCommit -eq $remoteCommit
if ($localCommit -ne $remoteCommit) {git fetch}
git status</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2016/02/git-outofdate24a.png" rel="attachment wp-att-13396"><img class="alignnone size-full wp-image-13396" src="http://mikefrobbins.com/wp-content/uploads/2016/02/git-outofdate24a.png" alt="git-outofdate24a" width="859" height="285" /></a>

The previous example could easily be turned into a function that would not only check the status, but fetch the changes if your local repository is indeed out of date:
<pre class="lang:ps decode:true " title="Update-MrGitRepository">function Update-MrGitRepository {

    [CmdletBinding(SupportsShouldProcess,
                   ConfirmImpact='Medium')]
    param ()

    $Location = Get-Location

    if (Get-GitDirectory) {
        $Repository = Split-Path -Path (git.exe rev-parse --show-toplevel) -Leaf
    }
    else {
        throw "$Location is not part of a Git repsoitory."
    }

    if ((git.exe remote) -contains 'origin') {        
        $originURL = (git.exe remote -v) -match '^origin.*fetch\)$' -replace '^origin\s*|\s*\(fetch\)$'
    }
    else {
        throw "Origin not setup for Git '$Repository' repository"
    }    
    
    if ((Invoke-WebRequest -Uri $($originURL) -TimeoutSec 15).StatusCode -ne 200) {     
        Write-Warning -Message "Unable to communicate with remote origin '$originURL'"
    }
    else {        
        $currentBranch = git.exe symbolic-ref --short HEAD
        $localCommit = git.exe rev-list --all -n1
        $remoteCommit = (git.exe ls-remote origin $currentBranch) -replace '\s.*$'
        
        if ($localCommit -ne $remoteCommit){

            if ($PSCmdlet.ShouldProcess($currentBranch,'Fetch')) {
                git.exe fetch
            }

        }
        else {
            "Local '$currentBranch' branch of the '$Repository' repository is already up-to-date with '$originURL'."
        }

    }

}</pre>
The current version of the <em>Update-MrGitRepository</em> function can be found in <a href="https://github.com/mikefrobbins/Git" target="_blank" rel="noopener noreferrer">my Git repository on GitHub</a>.

Now you can see that the SHA1 hash stored in the file that I referenced earlier is the same as the latest commit for both the local and remote repositories:
<pre class="lang:ps decode:true">Get-Content -Path .\.git\refs\remotes\origin\dev
($localCommit = git rev-list --all -n1)
($remoteCommit = (git ls-remote origin dev) -replace '\s.*$')
$localCommit -eq $remoteCommit</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2016/02/git-outofdate25a.png" rel="attachment wp-att-13400"><img class="alignnone size-full wp-image-13400" src="http://mikefrobbins.com/wp-content/uploads/2016/02/git-outofdate25a.png" alt="git-outofdate25a" width="859" height="200" /></a>

You might be wondering why my local repository is still one commit behind? That's because although I fetched the changes, I didn't merge them. Now that I have the changes downloaded, you can see the commit that the local dev branch is pointing to along with the one that the remote one points to:
<pre class="lang:ps decode:true">git status
git log --decorate --oneline --all -n6</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2016/02/git-outofdate26a.png" rel="attachment wp-att-13401"><img class="alignnone size-full wp-image-13401" src="http://mikefrobbins.com/wp-content/uploads/2016/02/git-outofdate26a.png" alt="git-outofdate26a" width="859" height="223" /></a>

I can simply merge those changes in and now my local dev branch points to the same commit as the remote one:
<pre class="lang:ps decode:true">git merge
git status</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2016/02/git-outofdate27a.png" rel="attachment wp-att-13402"><img class="alignnone size-full wp-image-13402" src="http://mikefrobbins.com/wp-content/uploads/2016/02/git-outofdate27a.png" alt="git-outofdate27a" width="859" height="200" /></a>

Let's take one last look at the log to make sure that both the local and remote repositories point to the same commit:
<pre class="lang:ps decode:true">git log --decorate --oneline --all -n6</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2016/02/git-outofdate28a.png" rel="attachment wp-att-13404"><img class="alignnone size-full wp-image-13404" src="http://mikefrobbins.com/wp-content/uploads/2016/02/git-outofdate28a.png" alt="git-outofdate28a" width="859" height="152" /></a>

As you can see, they do. I could have also used git pull to run both git fetch and git merge with one command.

µ