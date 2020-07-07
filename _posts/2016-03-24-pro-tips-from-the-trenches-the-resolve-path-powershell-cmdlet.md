---
ID: 13690
post_title: 'Pro Tips from the Trenches: The Resolve-Path PowerShell cmdlet'
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2016/03/24/pro-tips-from-the-trenches-the-resolve-path-powershell-cmdlet/
published: true
post_date: 2016-03-24 07:30:44
---
You're designing a reusable tool in PowerShell and decide a function is the most appropriate type of command to accomplish the task at hand. It will accept a <em>FilePath</em> parameter which will be used to specify both the path and filename to a destination file. The path should already exist, but the file may or may not already exist.

For simplicity, I've stripped away the complexity so the problem is more apparent. This function validates that the parent path exists and then returns the absolute path where the output file would be created once the remainder of the function is written:
<pre class="lang:ps decode:true " title="Out-MrFile">function Out-MrFile {
    [CmdletBinding()]
    param (
        [ValidateScript({
          if (Test-Path -Path (Split-Path -Parent $_ -OutVariable Parent) -PathType Container) {
            $True
          }
          else {
            Throw "'$Parent' is not a valid directory."
          }
        })]
        [string]$FilePath
    )

    Join-Path -Path (Resolve-Path -Path (Split-Path -Parent $FilePath)) -ChildPath (Split-Path -Leaf $FilePath)

}</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2016/03/resolve-path1b.png" rel="attachment wp-att-13713"><img class="alignnone size-full wp-image-13713" src="http://mikefrobbins.com/wp-content/uploads/2016/03/resolve-path1b.png" alt="resolve-path1b" width="859" height="190" /></a>

It works great based on the previous results, but when specifying a UNC path, it doesn't return a usable path:

<a href="http://mikefrobbins.com/wp-content/uploads/2016/03/resolve-path2b.png" rel="attachment wp-att-13695"><img class="alignnone size-full wp-image-13695" src="http://mikefrobbins.com/wp-content/uploads/2016/03/resolve-path2b.png" alt="resolve-path2b" width="859" height="71" /></a>

In this scenario, the problem is immediately identifiable, but if the path itself wasn't displayed in the previous set of results and an attempt was made to write to a file in that location, a cryptic error message would be returned.

The solution to this problem is to use the <em>ProviderPath</em> property of the <em>Resolve-Path</em> cmdlet:
<pre class="lang:ps decode:true " title="Out-MrFile">function Out-MrFile {
    [CmdletBinding()]
    param (
        [ValidateScript({
          if (Test-Path -Path (Split-Path -Parent $_ -OutVariable Parent) -PathType Container) {
            $True
          }
          else {
            Throw "'$Parent' is not a valid directory."
          }
        })]
        [string]$FilePath
    )

    Join-Path -Path (Resolve-Path -Path (Split-Path -Parent $FilePath)).ProviderPath -ChildPath (Split-Path -Leaf $FilePath)

}</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2016/03/resolve-path3a.png" rel="attachment wp-att-13696"><img class="alignnone size-full wp-image-13696" src="http://mikefrobbins.com/wp-content/uploads/2016/03/resolve-path3a.png" alt="resolve-path3a" width="859" height="71" /></a>

Hours could be spent troubleshooting a problem such as the one demonstrated in this scenario, but the fix for this particular problem is simply choosing the specific property that not only works with local file paths, but also with UNC paths.

A bonus tip which was shown in the first example of this blog article is the <em>Resolve-Path</em> cmdlet can be used to generate an absolute path from a relative one. It can also be used to generate an absolute path from a relative one that contains wildcards as shown in the following example:
<pre class="lang:ps decode:true ">Resolve-Path -Path .\test\file.txt
Resolve-Path -Path .\te*\file.txt</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2016/03/resolve-path4a.png" rel="attachment wp-att-13721"><img class="alignnone size-full wp-image-13721" src="http://mikefrobbins.com/wp-content/uploads/2016/03/resolve-path4a.png" alt="resolve-path4a" width="859" height="214" /></a>

If you're attending the <a href="http://powershellsummit.org/" target="_blank">PowerShell and DevOps Global Summit 2016</a>, I'm presenting two sessions and if you've enjoyed the blog articles on this site, then you'll definitely enjoy my presentations! I have some awesome content that I haven't previously blogged or spoken about nor have I seen anyone else blog or speak on anything like it so I can't wait to share the information.

Regardless if you're attending that particular event and/or my sessions or not, if you happen to see me at a technology event, I'd love to chat with you and I'm always interested in hearing about the challenges you're trying to solve in your environment.

µ