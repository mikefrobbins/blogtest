---
ID: 8
post_title: 'Hyperlink Issues &#8211; MS Office'
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2009/09/04/hyperlink-issues-ms-office/
published: true
post_date: 2009-09-04 20:09:13
---
Problem:
You receive the error message "This operation has been cancelled due to restrictions in effect on this computer. Please contact your system administrator" when clicking on a hyperlink from any office application after updating Internet Explorer to version 7 or 8. The specific error I received was:<img class="alignnone size-full wp-image-35" title="IELinkProblem" alt="IELinkProblem" src="http://mikefrobbins.com/wp-content/uploads/2009/09/ielinkproblem.jpg" width="657" height="119" />

Solution:
Change the registry value in [HKEY_CLASSES_ROOThtmlfileshellopencommand] from "C:\Program Files\Internet Explorer\IEXPLORE.EXE" -nohome to "C:\Program Files\Internet Explorer\iexplore.exe" -nohome . The update of Internet Explorer apparently changes the executable name in the registry to upper case. Simply changing the name of the executable in the registry back to lower case resolves this problem.

You can also copy the text below, paste it into notepad, and save it as "html_fix.reg". Make sure the file extension is ".reg" and not ".txt". Double click the .reg file to fix the issue:
<em><strong> Windows Registry Editor Version 5.00</strong></em>

<em><strong>[HKEY_CLASSES_ROOThtmlfileshellopencommand]
@=""C:\Program Files\Internet Explorer\iexplore.exe" -nohome"</strong></em>

<em>Before:
<img class="alignnone size-full wp-image-109" title="ie_reg_problem" alt="ie_reg_problem" src="http://mikefrobbins.com/wp-content/uploads/2009/09/ie_reg_problem.jpg" width="634" height="45" /></em>

<em>After:
<img class="alignnone size-full wp-image-110" title="ie_reg_fix" alt="ie_reg_fix" src="http://mikefrobbins.com/wp-content/uploads/2009/09/ie_reg_fix.jpg" width="629" height="43" /> </em>
<div id="_mcePaste" style="position: absolute; left: -10000px; top: 205px; width: 1px; height: 1px;">Windows Registry Editor Version 5.00</div>
<div id="_mcePaste" style="position: absolute; left: -10000px; top: 205px; width: 1px; height: 1px;">[HKEY_CLASSES_ROOThtmlfileshellopencommand]</div>
<div id="_mcePaste" style="position: absolute; left: -10000px; top: 205px; width: 1px; height: 1px;">@=""C:\Program Files\Internet Explorer\iexplore.exe" -nohome"</div>
µ