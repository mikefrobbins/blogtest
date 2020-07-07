---
ID: 6
post_title: Vista and 7 x64 Audio Codec Problem
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2009/09/04/vista-and-7-x64-audio-codec-problem/
published: true
post_date: 2009-09-04 20:07:53
---
Problem:
An important audio codec is missing from 64bit versions of Windows Vista and Windows 7. Some media files generate an error when using Windows Media Player. Third party media players seem to be affected as well, since some will play the video portion with no audio, and others won't play the media file at all. This problem even occurs with media files downloaded from Microsoft. The Windows Media Player specific error is: "Windows Media Player cannot play, burn, rip, or sync the file because a required audio codec is not installed on your computer."

<img class="alignnone size-full wp-image-33" title="AudioCodecProblem" src="http://mikefrobbins.com/wp-content/uploads/2009/09/audiocodecproblem.jpg" alt="AudioCodecProblem" width="457" height="158" />

Clicking on the "Web Help" leads you to the following page which isn't much help: <a href="http://www.microsoft.com/windows/windowsmedia/player/webhelp/default.aspx?&amp;mpver=12.0.7600.16385&amp;id=C00D11C7&amp;contextid=68&amp;originalid=C00D0BC2" target="_blank" rel="noopener">http://www.microsoft.com/windows/windowsmedia/player/webhelp/default.aspx?&amp;mpver=12.0.7600.16385&amp;id=C00D11C7&amp;contextid=68&amp;originalid=C00D0BC2</a> .

Solution:
Download the ACELP.net Codec by VoiceAge from<a href=" http://www.acelp.net/acelp_eval.php" target="_blank" rel="noopener"> </a><del>http://www.acelp.net/acelp_eval.php</del> and install it. The codec listed for Windows Vista x64 appears to work fine on Windows 7 x64.

Update:
The codec is no longer available from the referenced URL. Search for "ACELP.net Codec by VoiceAge" using your favorite search engine to try to locate it.

µ