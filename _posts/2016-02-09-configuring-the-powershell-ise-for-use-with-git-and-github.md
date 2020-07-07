---
ID: 13247
post_title: >
  Configuring the PowerShell ISE for use
  with Git and GitHub
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2016/02/09/configuring-the-powershell-ise-for-use-with-git-and-github/
published: true
post_date: 2016-02-09 07:30:43
---
The goal of this blog article is to configure the PowerShell ISE (Integrated Scripting Environment) so the management of Git version control can be performed from it. Most tutorials you'll find will attempt to lead you down the path of using SSH instead of HTTPS to synchronize your repositories to GitHub from the command-line but that's really over-complicated and unnecessary if you're using a Windows based machine.

The client machine used in this blog article runs the 64 bit version of Windows 10 Enterprise Edition. Windows 10 has PowerShell version 5 installed by default. <a href="https://git-for-windows.github.io/" target="_blank">Git for Windows</a> version 2.7.0 and <a href="https://www.powershellgallery.com/packages/posh-git/0.5.0.2015" target="_blank">Posh-Git</a> version 0.5.0.2015 are the third party software versions used in this blog article.

The first question you may ask is why not just use the Git Shell that's installed as part of <a href="https://desktop.github.com/" target="_blank">GitHub Desktop</a>? While it's true that you can launch Git Shell and then the ISE from it, the problem is that Git Shell uses the x86 (32 bit) version of PowerShell on a machine with a 64 bit operating system which means you won't have all of your PowerShell modules available and that's a solution that ends up being a sub-par experience. What I want to accomplish is simple, to launch a PowerShell ISE session as I always do and then to be able to manage both local Git repositories as well as remote ones on GitHub.

If you've installed GitHub Desktop, then you already have a copy of the Posh-Git PowerShell module and a portable version of Git installed which are located in subfolders of "<em>$env:LOCALAPPDATA\GitHub</em>" in your user's profile. I chose not to use those versions and to go ahead and install them for all users in the program files folder since I'm running PowerShell elevated as an alternate user (I login to Windows as a standard user and run PowerShell elevated as a local admin). You could also manage your GitHub repositories using this process without ever having to install GitHub Desktop.

First, install and configure Git for Windows. I previously covered this topic in <a href="http://mikefrobbins.com/2016/01/21/getting-started-with-the-git-version-control-system/" target="_blank">another blog article</a>. In this scenario, I ran the Git installer elevated so I could install it in the program files folder and I took the option to add the path for Git to the system environment variable path:

<a href="http://mikefrobbins.com/wp-content/uploads/2016/02/posh-git0a.png" rel="attachment wp-att-13277"><img class="alignnone size-full wp-image-13277" src="http://mikefrobbins.com/wp-content/uploads/2016/02/posh-git0a.png" alt="posh-git0a" width="499" height="387" /></a>

Make sure that you've configured Git as the user who is running PowerShell (I ran these commands from within my elevated PowerShell session):
<pre class="lang:ps decode:true">git config --global user.name 'your name'
git config --global user.email 'your email address'
git config --global push.default simple</pre>
I used my private GitHub email address instead of my personal public email address to eliminate the possibility of spam. For more information on this topic, see the GitHub help article on "<a href="https://help.github.com/articles/keeping-your-email-address-private/" target="_blank">Keeping your email address private</a>".

Install the Posh-Git PowerShell module from the PowerShell Gallery:
<pre class="lang:ps decode:true ">Install-Module -Name posh-git -Force
Get-Module -Name posh-git -ListAvailable</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2016/02/posh-git1a.png" rel="attachment wp-att-13248"><img class="alignnone size-full wp-image-13248" src="http://mikefrobbins.com/wp-content/uploads/2016/02/posh-git1a.png" alt="posh-git1a" width="859" height="191" /></a>

I created a custom profile script that changes my PowerShell prompt depending on whether or not I'm in a directory that's part of a Git repository. There is a default profile located in the Posh-Git module folder, but I chose not to use it for a number of reasons, one of which is that I wanted to retain the ability to show a nested prompt and I also wanted the prompt to look similar to Git Bash when working with a Git repository. The one in the Posh-Git folder also generates an error because it attempts to setup an SSH agent which is not available in the version of Posh-Git that's installed from the PowerShell Gallery. See <a href="https://github.com/dahlbyk/posh-git" target="_blank">the version on GitHub</a> if that's something you're interested in, but as you'll learn in this blog article, the SSH agent is unnecessary.

I setup this profile to run for all users and all hosts on my machine since I often run PowerShell as an alternate user and I want it to be available from the PowerShell console and ISE:
<pre class="lang:ps decode:true " title="profile.ps1">Set-Location -Path $env:SystemDrive\
Clear-Host

$Error.Clear()
Import-Module -Name posh-git -ErrorAction SilentlyContinue

if (-not($Error[0])) {
    $DefaultTitle = $Host.UI.RawUI.WindowTitle
    $GitPromptSettings.BeforeText = '('
    $GitPromptSettings.BeforeForegroundColor = [ConsoleColor]::Cyan
    $GitPromptSettings.AfterText = ')'
    $GitPromptSettings.AfterForegroundColor = [ConsoleColor]::Cyan

    function prompt {

        if (-not(Get-GitDirectory)) {
            $Host.UI.RawUI.WindowTitle = $DefaultTitle
            "PS $($executionContext.SessionState.Path.CurrentLocation)$('&gt;' * ($nestedPromptLevel + 1)) "   
        }
        else {
            $realLASTEXITCODE = $LASTEXITCODE

            Write-Host 'PS ' -ForegroundColor Green -NoNewline
            Write-Host "$($executionContext.SessionState.Path.CurrentLocation) " -ForegroundColor Yellow -NoNewline

            Write-VcsStatus

            $LASTEXITCODE = $realLASTEXITCODE
            return "`n$('$' * ($nestedPromptLevel + 1)) "   
        }

    }

}
else {
    Write-Warning -Message 'Unable to load the Posh-Git PowerShell Module'
}</pre>
The latest version of my profile script can be found in <a href="https://github.com/mikefrobbins/PowerShell" target="_blank">my PowerShell repository</a> on GitHub. That repository is where I keep my PowerShell code I want to share publicly that doesn't fit into more specific categories. I have dedicated repositories for <a href="https://github.com/mikefrobbins/ActiveDirectory" target="_blank">Active Directory</a>, <a href="https://github.com/mikefrobbins/SQL" target="_blank">SQL</a>, etc. If you're not placing your profile script in source control, why not?

I'll clone my PowerShell repository from GitHub to my local computer:
<pre class="lang:ps decode:true">git clone https://github.com/mikefrobbins/PowerShell.git PowerShell -q
Set-Location -Path .\PowerShell</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2016/02/posh-git2a.png" rel="attachment wp-att-13251"><img class="alignnone size-full wp-image-13251" src="http://mikefrobbins.com/wp-content/uploads/2016/02/posh-git2a.png" alt="posh-git2a" width="859" height="190" /></a>

Notice in the previous example that the prompt changed once I entered the PowerShell folder. That's because of the custom profile script and this folder being part of a Git repository. If I were to change to another folder that's not part of a Git repository, my prompt would change back to the default PowerShell one.

Getting the status of this repository shows that it's up to date with the remote and you can also see the details of where the remote is located on Github:
<pre class="lang:ps decode:true">git status
git remote -v</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2016/02/posh-git3a.png" rel="attachment wp-att-13253"><img class="alignnone size-full wp-image-13253" src="http://mikefrobbins.com/wp-content/uploads/2016/02/posh-git3a.png" alt="posh-git3a" width="859" height="279" /></a>

The problem at this point is the ISE generates some weird error message that you'll find all sorts of suggestions when searching on the Internet for solutions, but unfortunately all of the recommended fixes are a dead-end.
<pre class="lang:ps decode:true ">git push</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2016/02/posh-git4a.png" rel="attachment wp-att-13254"><img class="alignnone size-full wp-image-13254" src="http://mikefrobbins.com/wp-content/uploads/2016/02/posh-git4a.png" alt="posh-git4a" width="859" height="292" /></a>

<em><span style="color: #ff0000;">git : bash: /dev/tty: No such device or address</span></em>
<em><span style="color: #ff0000;">At line:1 char:1</span></em>
<em><span style="color: #ff0000;">+ git push</span></em>
<em><span style="color: #ff0000;">+ ~~~~~~~~</span></em>
<em><span style="color: #ff0000;"> + CategoryInfo : NotSpecified: (bash: /dev/tty:...vice or address:String) [], RemoteException</span></em>
<em><span style="color: #ff0000;"> + FullyQualifiedErrorId : NativeCommandError</span></em>
<em><span style="color: #ff0000;">error: failed to execute prompt script (exit code 1)</span></em>
<em><span style="color: #ff0000;">fatal: could not read Username for 'https://github.com': Invalid argument</span></em>

At this point, you need to punt and go back to the PowerShell console to perform a few tasks that will end up resolving the problem that you're experiencing in the ISE.

Running the same command from the console appears to work, but due to having two-factor authentication enabled on your account, the authentication will fail. Notice that I didn't say if you have two-factor authentication enabled on your account because in this day and age you better have two-factor authentication enabled or you're asking to have your account hacked.
<pre class="lang:ps decode:true">git push</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2016/02/posh-git5a.png" rel="attachment wp-att-13255"><img class="alignnone size-full wp-image-13255" src="http://mikefrobbins.com/wp-content/uploads/2016/02/posh-git5a.png" alt="posh-git5a" width="859" height="133" /></a>

See this <a href="https://github.com/blog/1614-two-factor-authentication" target="_blank">blog article on GitHub</a> to learn more about setting up two-factor authentication on your GitHub account.

Create a "Personal access token" in your GitHub account that will be used for authentication at the command-line:

<a href="http://mikefrobbins.com/wp-content/uploads/2016/02/posh-git6a.png" rel="attachment wp-att-13256"><img class="alignnone size-full wp-image-13256" src="http://mikefrobbins.com/wp-content/uploads/2016/02/posh-git6a.png" alt="posh-git6a" width="859" height="389" /></a>

See <a href="https://help.github.com/articles/creating-an-access-token-for-command-line-use/" target="_blank">this GitHub help topic</a> to learn more about setting up a Personal access token for command-line use. Make sure you read about limiting the scope of this token before continuing. See the <a href="https://developer.github.com/v3/oauth/#scopes" target="_blank">GitHub Developer OAuth API documentation</a> to learn more about what each scope option authorizes.

Using the token as the password works without issue except it has to be entered each time a command is run that requires authentication to GitHub:
<pre class="lang:ps decode:true">git push</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2016/02/posh-git7a.png" rel="attachment wp-att-13257"><img class="alignnone size-full wp-image-13257" src="http://mikefrobbins.com/wp-content/uploads/2016/02/posh-git7a.png" alt="posh-git7a" width="859" height="119" /></a>

To prevent having to enter the token each time, enable the credentials to be saved to Windows credential manager:
<pre class="lang:ps decode:true">git config --global credential.helper wincred</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2016/02/posh-git8a.png" rel="attachment wp-att-13258"><img class="alignnone size-full wp-image-13258" src="http://mikefrobbins.com/wp-content/uploads/2016/02/posh-git8a.png" alt="posh-git8a" width="859" height="85" /></a>

For more information about the command used in the previous example, see the "<a href="https://help.github.com/articles/caching-your-github-password-in-git/" target="_blank">Caching your GitHub password in Git</a>" help topic on GitHub.

The next time the token is entered as the password, it will be saved to Windows credential manager and you'll no longer be asked for a userid / password combination when running a command that requires authentication to GitHub.
<pre class="lang:ps decode:true">git push
git push</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2016/02/posh-git9a.png" rel="attachment wp-att-13259"><img class="alignnone size-full wp-image-13259" src="http://mikefrobbins.com/wp-content/uploads/2016/02/posh-git9a.png" alt="posh-git9a" width="859" height="154" /></a>

Switch back to the ISE. Running the same commands that previously generated the cryptic error message now run without issue. It appears that they generate errors but these are due to Git sending much of its output to StdErr instead of StdOut so they can safely be ignored:
<pre class="lang:ps decode:true">git status
git push
Set-Location -Path ..\AWS
git status
git push</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2016/02/posh-git10a.png" rel="attachment wp-att-13260"><img class="alignnone size-large wp-image-13260" src="http://mikefrobbins.com/wp-content/uploads/2016/02/posh-git10a.png" alt="posh-git10a" width="859" height="645" /></a>

<em><span style="color: #ff0000;">git : Everything up-to-date</span></em>
<em><span style="color: #ff0000;">At line:1 char:1</span></em>
<em><span style="color: #ff0000;">+ git push</span></em>
<em><span style="color: #ff0000;">+ ~~~~~~~~</span></em>
<em><span style="color: #ff0000;"> + CategoryInfo : NotSpecified: (Everything up-to-date:String) [], RemoteException</span></em>
<em><span style="color: #ff0000;"> + FullyQualifiedErrorId : NativeCommandError</span></em>

Notice that in the previous example, you can now push to any of your GitHub repositories without being prompted for authentication. You might be wondering where the saved credentials are stored at? They're stored securely in Windows credential manager:
<pre class="lang:ps decode:true ">cmdkey /list</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2016/02/posh-git11a.png" rel="attachment wp-att-13262"><img class="alignnone size-full wp-image-13262" src="http://mikefrobbins.com/wp-content/uploads/2016/02/posh-git11a.png" alt="posh-git11a" width="859" height="280" /></a>

Use caution when changing branches and having files from a Git repository working folder open in the ISE. You could unknowingly overwrite files in one branch with files from a different branch. My recommendation is to close any open ISE tabs containing files from a Git repository working folder before switching branches.

You'll find that you'll be much more likely to place your PowerShell scripts, functions, and modules in source control now that you can manage the version control system right from the same interface that you're writing your code in.

Additional GitHub help topics worth mentioning: "<a href="https://help.github.com/articles/git-automation-with-oauth-tokens/" target="_blank">Git automation with OAuth tokens</a>" and "<a href="https://github.com/blog/1509-personal-api-tokens" target="_blank">Personal API tokens</a>".

<span style="text-decoration: underline;">Update 2/20/16</span>
Based on a number of<a href="https://twitter.com/MSH_Dave/status/701135110737780736" target="_blank"> responses I received on Twitter</a>, there are several reasons you might want to use SSH instead of HTTPS. Most of these use cases fall outside of the scope that my blog article was originally intended for, but I still wanted to make you aware of them: (1) You hop between different operating systems, (2) you shuffle between different services such as GitHub, GitLab, and Gogs, (3) you're using on-premises SSL: "<a href="http://blogs.msdn.com/b/phkelley/archive/2014/01/20/adding-a-corporate-or-self-signed-certificate-authority-to-git-exe-s-store.aspx" target="_blank">Adding a corporate (or self-signed) certificate authority to git.exe’s store</a>".

<span style="text-decoration: underline;">Update 2/21/16</span>
I decided to do a little more research on the subject of which protocol to use for accessing GitHub from Git based on my previous update since those tips came from people in the industry that I have a great deal of respect for. My research lead me to a help page on GitHub on how to "<a href="https://help.github.com/articles/set-up-git/" target="_blank">Set Up Git</a>" which is self explanatory:

<a href="https://help.github.com/articles/set-up-git/" target="_blank" rel="attachment wp-att-13448"><img class="alignnone size-full wp-image-13448" src="http://mikefrobbins.com/wp-content/uploads/2016/02/posh-git13a.png" alt="posh-git13a" width="712" height="289" /></a>

There are definitely some considerations to keep in mind and reasons you would want to use SSH over HTTPS which is probably why they make it available, but I have a difficult time going against the recommendation to use HTTPS from the software manufacturer considering this blog article focuses specifically on accessing GitHub from Git.

µ