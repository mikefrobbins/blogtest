---
ID: 15730
post_title: >
  Regions now available in VSCode Insiders
  Build version 1.17.0
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2017/10/02/regions-now-available-in-vscode-insiders-build-version-1-17-0/
published: true
post_date: 2017-10-02 17:39:49
---
Today, <a href="https://twitter.com/daviwil" target="_blank" rel="noopener">David Wilson</a> from the PowerShell team at Microsoft, <a href="https://twitter.com/daviwil/status/914925309220106241" target="_blank" rel="noopener">announced that Regions are now available in VSCode</a> (Visual Studio Code) as of insiders build version 1.17.0.

I was hoping the region and endregion keywords would be case-insensitive unlike in the PowerShell ISE where they're case-sensitive. It looks like that's indeed what they intended since the region keyword is case-insensitive in VSCode, but unfortunately the endregion keyword is case-sensitive as shown in the following example.

<a href="http://mikefrobbins.com/wp-content/uploads/2017/10/vscode-endregion-casesensitive1c.jpg"><img class="alignnone size-full wp-image-15739" src="http://mikefrobbins.com/wp-content/uploads/2017/10/vscode-endregion-casesensitive1c.jpg" alt="" width="859" height="688" /></a>

There's definitely a bug with either the region or endregion keyword since they should either both be case sensitive or both be case insensitive. If you want to make your code compatible with the PowerShell ISE, you'll want to specify the region and endregion keywords in lower case since that's the only way it will work in the ISE.

I've logged <a href="https://github.com/Microsoft/vscode/issues/35506" target="_blank" rel="noopener">an issue on GitHub about this problem</a>.

I didn't realize that regions were such a controversial topic as seen in <a href="https://twitter.com/Jaykul/status/914940837909483520" target="_blank" rel="noopener">this thread on Twitter</a>. I use them all the time in my demonstration scripts to organize my content when presenting.

<hr />

Update
Maybe this is working as designed? If region is specified in lower case, then endregion must be specified in lower case, but if region is specified in mixed or upper case, then endregion can be specified in any case.

µ