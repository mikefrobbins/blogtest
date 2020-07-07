---
ID: 15677
post_title: >
  An Uncommon Cause for IIS 503 Service
  Unavailable Errors
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2017/11/16/an-uncommon-cause-for-iis-503-service-unavailable-errors/
published: true
post_date: 2017-11-16 07:30:46
---
Recently, while migrating IIS websites to a new server, I encountered "<em>Service Unavailable HTTP Error 503. The service is unavailable.</em>" errors, but only for HTTPS, while HTTP worked fine. Depending on the scenario, the problem could have just as easily impacted HTTP.

<a href="http://mikefrobbins.com/wp-content/uploads/2017/09/503error1a.jpg"><img class="alignnone size-full wp-image-15679" src="http://mikefrobbins.com/wp-content/uploads/2017/09/503error1a.jpg" alt="" width="859" height="164" /></a>

The server was listening on port 443:
<pre class="lang:ps decode:true">Get-NetTCPConnection -LocalPort 443 -State Listen</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2017/09/503error2a.jpg"><img class="alignnone size-full wp-image-15680" src="http://mikefrobbins.com/wp-content/uploads/2017/09/503error2a.jpg" alt="" width="859" height="129" /></a>

If you ever encounter a problem like this, stop the web publishing service:
<pre class="lang:ps decode:true">Stop-Service -Name w3svc -PassThru</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2017/09/503error3a.jpg"><img class="alignnone size-full wp-image-15681" src="http://mikefrobbins.com/wp-content/uploads/2017/09/503error3a.jpg" alt="" width="859" height="129" /></a>

Then check to see if the server is still listening on port 443:
<pre class="lang:ps decode:true">Get-NetTCPConnection -LocalPort 443 -State Listen</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2017/09/503error2a.jpg"><img class="alignnone size-full wp-image-15680" src="http://mikefrobbins.com/wp-content/uploads/2017/09/503error2a.jpg" alt="" width="859" height="129" /></a>

If it is, then something else is running that's listening on port 443. If this is a server that you've inherited, it may not be easy to determine what's listening on that particular port.

I'll install a PowerShell module named <a href="https://www.powershellgallery.com/packages/Carbon/" target="_blank" rel="noopener">Carbon</a> from the PowerShell Gallery to help determine what's going on.

<a href="http://mikefrobbins.com/wp-content/uploads/2017/09/503error4a.jpg"><img class="alignnone size-full wp-image-15682" src="http://mikefrobbins.com/wp-content/uploads/2017/09/503error4a.jpg" alt="" width="859" height="56" /></a>

When a non-IIS web server is installed on Windows, it sets an ACL on the port it will listen on. You can see the ACL which references port 443 that's shown in the following image.
<pre class="lang:ps decode:true">Get-HttpUrlAcl</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2017/11/503error5b.jpg"><img class="alignnone size-full wp-image-15903" src="http://mikefrobbins.com/wp-content/uploads/2017/11/503error5b.jpg" alt="" width="859" height="250" /></a>

Actually, there are two entries listed for port 443 in the previous image. The one with the GUID after it is fine since it's only for a particular application via a host header and not the entire namespace.

Although I wasn't sure, I was fairly certain the Dell EqualLogic SAN Headquarters application that was installed on this particular server was causing the problem so I decided to deinstall it since it was no longer needed.

<a href="http://mikefrobbins.com/wp-content/uploads/2017/09/503error6a.jpg"><img class="alignnone size-full wp-image-15684" src="http://mikefrobbins.com/wp-content/uploads/2017/09/503error6a.jpg" alt="" width="859" height="244" /></a>

While the deinstall seemed to work fine and the application was removed from the server, it did not remove the ACL assigned to port 443.
<pre class="lang:ps decode:true">Get-HttpUrlAcl -Url https://+443/</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2017/09/503error7a.jpg"><img class="alignnone size-full wp-image-15685" src="http://mikefrobbins.com/wp-content/uploads/2017/09/503error7a.jpg" alt="" width="859" height="129" /></a>

Since deinstalling the application did not resolve the problem, I decided to forcibly remove the ACL. Forcibly deleting ACL's is NOT something I recommend, but I was to the point where I had nothing else to lose because the alternative was to simply spin up another server.
<pre class="lang:ps decode:true">Revoke-HttpUrlPermission -Url https://+443/ -Principal 'NT AUTHORITY\SYSTEM'</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2017/09/503error8a.jpg"><img class="alignnone size-full wp-image-15686" src="http://mikefrobbins.com/wp-content/uploads/2017/09/503error8a.jpg" alt="" width="859" height="56" /></a>

Once that entry was removed and the web service was restarted, the problems with IIS listening on port 443 were resolved.

<a href="http://mikefrobbins.com/wp-content/uploads/2017/09/503error9a.jpg"><img class="alignnone size-full wp-image-15687" src="http://mikefrobbins.com/wp-content/uploads/2017/09/503error9a.jpg" alt="" width="859" height="196" /></a>

In addition to the steps previously listed, I went through all of the normal items you would check in this scenario. Most of them were ruled out because the website was working on port 80.

I decided to turn my checklist of items for 503 service unavailable errors into a Pester test to make it easier to know where to start troubleshooting.
<pre class="lang:ps decode:true ">Describe 'HTTP Error 503' {
    $SiteName = 'Default Web Site'

    It 'Should have a W3SVC service that is running' {
        (Get-Service -Name W3SVC).Status |
        Should -Be 'Running'
    }
    It "Should have an IIS website named '$SiteName' that is started" {
        (Get-Website -Name $SiteName).State |
        Should -Be 'Started'
    }
    It 'Should have an IIS application pool that is started' {
        (Get-WebAppPoolState -Name ((Get-Website -Name $SiteName).applicationPool)).value |
        Should -Be 'Started'
    }
    It 'Should have a process named w3wp.exe that is running' {
        Get-Process -Name w3wp -ErrorAction SilentlyContinue |
        Should -Not -BeNullOrEmpty
    }
    It 'Should not have any WAS warnings or errors in the system event log' {
        2,3 | ForEach-Object {
            Get-WinEvent -FilterHashtable @{LogName='System';Data='WAS';Level=$_} -ErrorAction SilentlyContinue |
            Should -BeNullOrEmpty
        }
    }
    It 'Should not have any WAS warnings or errors in the application event log' {
        2,3 | ForEach-Object {
            Get-WinEvent -FilterHashtable @{LogName='Application';Data='WAS';Level=$_} -ErrorAction SilentlyContinue |
            Should -BeNullOrEmpty
        }
    }
    It 'Should not have any W3SVC warnings or errors in the system event log' {
        2,3 | ForEach-Object {
            Get-WinEvent -FilterHashtable @{LogName='System';Data='W3SVC';Level=$_} -ErrorAction SilentlyContinue |
            Should -BeNullOrEmpty
        }
    }
    It 'Should not have any W3SVC warnings or errors in the application event log' {
        2,3 | ForEach-Object {
            Get-WinEvent -FilterHashtable @{LogName='Application';Data='W3SVC';Level=$_} -ErrorAction SilentlyContinue |
            Should -BeNullOrEmpty
        }
    }
    It 'Should not have any 503 errors in the IIS log for the website' {
        Get-ChildItem -Path 'C:\inetpub\logs\LogFiles' -Include *.log -Recurse |
        Where-Object LastWriteTime -gt (Get-Date).AddDays(-1) |
        Select-String -SimpleMatch '503' |
        Should -BeNullOrEmpty
    }
    It 'Should not have any HTTP entries in the system event log' {
        (Get-WinEvent -FilterHashtable @{LogName='System';ProviderName='Microsoft-Windows-HttpEvent'}).Count |
        Should -Be 0
    }
}</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2017/11/503error10b.jpg"><img class="alignnone size-full wp-image-15898" src="http://mikefrobbins.com/wp-content/uploads/2017/11/503error10b.jpg" alt="" width="859" height="250" /></a>

The way I've written this test, the last entry is almost always going to fail because there will most likely be at least some informational entries in that particular event log. Starting from the top, check the first item that fails.

One informational entry in the event log stood out that appears to have occurred when the ACL on port 443 was set, although it still didn't specify which application had set the ACL.
<pre class="lang:ps decode:true ">Get-WinEvent -FilterHashtable @{LogName='System';ProviderName='Microsoft-Windows-HttpEvent';Level=4} -MaxEvents 1 |
Select-Object -Property Message
</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2017/11/503error11a.jpg"><img class="alignnone size-full wp-image-15892" src="http://mikefrobbins.com/wp-content/uploads/2017/11/503error11a.jpg" alt="" width="859" height="139" /></a>

Information about this particular problem seems to be non-existent, although I did come across a couple of really old articles that had some information about it.

<a href="https://blogs.msdn.microsoft.com/webtopics/2010/02/17/a-not-so-common-root-cause-for-503-service-unavailable/" target="_blank" rel="noopener">A Not So Common Root Cause for 503 Service Unavailable</a>
<a href="https://blogs.msdn.microsoft.com/drnick/2006/10/16/configuring-http-for-windows-vista/" target="_blank" rel="noopener">Configuring HTTP for Windows Vista</a>

µ