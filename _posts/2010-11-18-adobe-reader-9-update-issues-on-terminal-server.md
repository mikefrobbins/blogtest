---
ID: 1379
post_title: >
  Adobe Reader 9 update issues on Terminal
  Server
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2010/11/18/adobe-reader-9-update-issues-on-terminal-server/
published: true
post_date: 2010-11-18 05:30:25
---
You’re using group policy to enforce the “Run only approved Windows applications” for a specific group of computers such as terminal servers. After updating Adobe Reader from version 9.0 to 9.2, users receive the message: “This operation has been cancelled due to restrictions in effect on this computer. Please contact your system administrator.”
<a href="http://mikefrobbins.com/wp-content/uploads/2009/09/ielinkproblem.jpg"><img class="alignnone size-full wp-image-35" title="IELinkProblem" src="http://mikefrobbins.com/wp-content/uploads/2009/09/ielinkproblem.jpg" alt="" width="640" height="115" /></a>

My first thought was "The name of the executable must have changed". A quick check disproved that. It is the same name with both versions: “AcroRd32.exe” which is in the list of approved applications. I took a look at the executables in the Adobe Reader program files folder and guessed that it wanted each user to accept the EULA. I added Eula.exe to the list of approved applications, ran gpupdate.exe on the terminal server that was experiencing the problem and the next time the user who experienced the issue ran Adobe Reader, they were prompted to accept the EULA:
<a href="http://mikefrobbins.com/wp-content/uploads/2010/11/adobe-issue1.jpg"><img class="alignnone size-medium wp-image-1381" title="adobe-issue1" src="http://mikefrobbins.com/wp-content/uploads/2010/11/adobe-issue1.jpg?w=300" alt="" width="300" height="240" /></a>

Accepting the EULA resolved the issue for that particular user. This meant that each user would have to accept this EULA one time.

This is a good solution to this problem, but not good enough in my opinion since it could generate unnecessary help desk calls. While the above fix will resolve your problem, it doesn't explain why this happened and that there’s an easier way to resolve this problem. The more I looked into this issue, the more I discovered why this happened and it’s not as simple as it seems. There is a global registry setting for accepting the EULA located in HKLM&gt;Software&gt;Adobe&gt;Adobe Reader&gt;9.0&gt;AdobeViewer&gt;EULA:
<a href="http://mikefrobbins.com/wp-content/uploads/2010/11/adobe-issue2.jpg"><img class="alignnone size-medium wp-image-1382" title="adobe-issue2" src="http://mikefrobbins.com/wp-content/uploads/2010/11/adobe-issue2.jpg?w=300" alt="" width="300" height="113" /></a>

When you update to Adobe 9.2, the “AdobeViewer” registry key is deleted from HKLM, but not from HKCU:
<a href="http://mikefrobbins.com/wp-content/uploads/2010/11/adobe-issue3.jpg"><img class="alignnone size-medium wp-image-1383" title="adobe-issue3" src="http://mikefrobbins.com/wp-content/uploads/2010/11/adobe-issue3.jpg?w=300" alt="" width="300" height="131" /></a>

This causes Adobe Reader to work properly for the original administrator who installed it since they still have a value of 1 in HKCU&gt;Software&gt;Adobe&gt;Adobe Reader&gt;9.0&gt;AdobeViewer&gt;EULA which means this particular user has accepted the EULA. If you run Eula.exe from the Adobe Reader program files folder as an administrator and accept it, the “AdobeViewer” HKLM registry key is recreated along with setting the EULA value to 1. This will resolve the problem for all users without having to add Eula.exe to the list of approved applications and without requiring each user to accept the EULA. If a normal user runs Eula.exe and accepts it, they do not have the necessary permission to recreate the HKLM registry key or update its value which is why it is created in HKCU for them. When this happens, it only affects their particular user and each user will have to accept the EULA.

Sounds complicated, but just run Eula.exe from the Adobe Reader program files folder as an administrator, accept it, and the problem will be resolved for everyone who uses the terminal server.

µ