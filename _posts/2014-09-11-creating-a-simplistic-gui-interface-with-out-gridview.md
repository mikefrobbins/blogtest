---
ID: 10349
post_title: >
  Creating a Simplistic GUI Interface with
  Out-GridView
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2014/09/11/creating-a-simplistic-gui-interface-with-out-gridview/
published: true
post_date: 2014-09-11 07:30:08
---
A couple of weeks ago I noticed <a href="http://powershell.org/wp/forums/topic/ps-in-server-manager-2012/" target="_blank">a post in the forums at PowerShell.org</a> where a question was asked about selecting a group of servers in Server Manager and running a PowerShell script against that selection of machines. While my solution doesn't accomplish exactly what I think they were looking for, it does the next best thing.

First, I added a menu item to Server Manager called MrToolkit (short for Mike Robbins Toolkit) based on a "<a href="http://blogs.technet.com/b/servermanager/archive/2012/07/09/customize-tools-menu-in-server-manager.aspx" target="_blank">Customize Tools menu in Server Manager</a>" blog article on the TechNet Server Manager Blog:

<a href="http://mikefrobbins.com/wp-content/uploads/2014/08/gridview-gui1.png"><img class="alignnone size-full wp-image-10351" src="http://mikefrobbins.com/wp-content/uploads/2014/08/gridview-gui1.png" alt="gridview-gui1" width="1010" height="736" /></a>

The item I added is a .bat file that contains the following code:
<pre class="wrap:true lang:batch decode:true">powershell.exe -ExecutionPolicy Bypass -NoProfile -WindowStyle Hidden -Command "Invoke-Command -ComputerName (Get-ADComputer -Filter {OperatingSystem -like 'Windows Server*' -and enabled -eq $true} | Out-GridView -OutputMode Multiple -Title 'Select Servers to Query:' | Select-Object -ExpandProperty DNSHostName) -FilePath (Get-ChildItem -Path C:\Scripts\*.ps1 | Out-GridView -OutputMode Single -Title 'Select PowerShell Script to Run:' | Select-Object -ExpandProperty FullName) | Out-GridView -Title 'Results' -PassThru"</pre>
Notice the tweaks in the previous code for bypassing the execution policy and running it with no profile. I also had to add the <em>PassThru</em> parameter to the last <em>Out-GridView</em> otherwise the command finished and exited without the user being able to see the results.

Those tweaks aren't necessary if you just want to run the actual PowerShell code:
<pre class="lang:ps decode:true ">Invoke-Command -ComputerName (
    Get-ADComputer -Filter {OperatingSystem -like 'Windows Server*' -and enabled -eq $true} |
    Out-GridView -OutputMode Multiple -Title 'Select Servers to Query:' |
    Select-Object -ExpandProperty DNSHostName
) -FilePath (
    Get-ChildItem -Path C:\Scripts\*.ps1 |
    Out-GridView -OutputMode Single -Title 'Select PowerShell Script to Run:' |
    Select-Object -ExpandProperty FullName
) | Out-GridView -Title 'Results'</pre>
One thing to note is using the <em>Out-GridView</em> cmdlet requires the PowerShell ISE to be installed and <em>Out-GridView</em> being able to send its results to the pipeline was a feature introduced in PowerShell version 3 so those are the minimum requirements.

This command first returns a list of all the servers in the domain that are enabled:

<a href="http://mikefrobbins.com/wp-content/uploads/2014/08/gridview-gui2.png"><img class="alignnone size-full wp-image-10353" src="http://mikefrobbins.com/wp-content/uploads/2014/08/gridview-gui2.png" alt="gridview-gui2" width="847" height="305" /></a>

After selecting the Servers you want to run a script against and clicking &lt;OK&gt; as shown in the previous example, the user is prompted to select one of the scripts that exists in the specified directory:

<a href="http://mikefrobbins.com/wp-content/uploads/2014/08/gridview-gui3.png"><img class="alignnone size-full wp-image-10354" src="http://mikefrobbins.com/wp-content/uploads/2014/08/gridview-gui3.png" alt="gridview-gui3" width="596" height="234" /></a>

The user is limited to selecting one script, after selecting it and clicking &lt;OK&gt;, the selected script is run on the remote machines and the results are displayed:

<a href="http://mikefrobbins.com/wp-content/uploads/2014/08/gridview-gui4.png"><img class="alignnone size-full wp-image-10355" src="http://mikefrobbins.com/wp-content/uploads/2014/08/gridview-gui4.png" alt="gridview-gui4" width="708" height="278" /></a>

When the user clicks &lt;OK&gt;, the <em>Out-GridView</em> window and all traces of the command disappear. How's that for a simplistic GUI interface for your PowerShell scripts?

µ