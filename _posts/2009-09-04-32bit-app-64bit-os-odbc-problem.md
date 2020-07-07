---
ID: 10
post_title: '32bit App &#8211; 64bit OS ODBC Problem'
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2009/09/04/32bit-app-64bit-os-odbc-problem/
published: true
post_date: 2009-09-04 20:10:06
---
Problem:
ODBC data sources that are created with the "ODBC Data Source Administrator" located under "Administrative Tools &gt; Data Sources (ODBC)" do not show up in 32 bit (x86) applications. Also, data sources created in 32 bit applications such as Visual Studio do not show up in the "ODBC  Data Source Administrator". This is due to the default shortcut for the "ODBC Data Source Administrator" being for 64 applications only.

Solution:
Create an additional shortcut in Administrative Tools named "Data Sources (ODBC) x86" that points to "C:\Windows\SysWOW64\odbcad32.exe" (the 32bit version of "ODBC Data Source Administrator") for managing data sources for 32 bit applications.

<img class="alignnone size-full wp-image-36" title="ODBCx86" alt="ODBCx86" src="http://mikefrobbins.com/wp-content/uploads/2009/09/odbcx86.jpg" width="377" height="533" />

On a 64 bit OS, the SysWOW64 folder contains 32 bit versions of exe's and dll's.

µ