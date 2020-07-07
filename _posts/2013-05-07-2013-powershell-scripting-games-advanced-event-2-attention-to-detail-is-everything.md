---
ID: 7230
post_title: '2013 PowerShell Scripting Games Advanced Event 2 &#8211; Attention to Detail is Everything'
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2013/05/07/2013-powershell-scripting-games-advanced-event-2-attention-to-detail-is-everything/
published: true
post_date: 2013-05-07 18:00:31
---
Here's my approach to the 2013 PowerShell Scripting Games Advanced Event 2:

When I start one of the Scripting Games events, I read and re-read the scenario because if you don't understand the requirements, you can't write an effect script, function, command, tool, etc. It's not a bad idea to print out the event scenario and highlight the high-points.

Here's the scenario for Advanced Event 2 -An Inventory Intervention, I'll place the items in bold that I would normally highlight on my printout:

<span style="color: #0000ff;"><em>Dr. Scripto finally has the budget to buy a few new virtualization host servers, but he needs to make some room in the data center to accommodate them. He thinks it makes sense to get rid of his lowest-powered old servers first... but he needs to figure out which ones those are.</em></span>

<span style="color: #0000ff;"><em>This is just the first wave, too - there's more budget on the horizon so it's possible he'll need to run this little report a few times. <strong>Better make a reusable tool</strong>.</em></span>

The phrase in bold in the previous paragraph means it needs to be a script, function, or maybe even part of a module that has error checking to prevent others you may hand this 'tool" off to from having issues with it (not a one-liner).

<span style="color: #0000ff;"><em>All of the virtualization hosts run Windows Server, but <strong>some of them don't have Windows PowerShell installed</strong>, and <strong>they're all running different OS versions</strong>. <strong>The oldest OS version is Windows 2000 Server</strong> (he knows, and he's embarrassed  but he's just been so darn busy). The good news is that <strong>they all belong to the same domain</strong>, and that <strong>you can rely on having a Domain Admin account to work with</strong>.</em></span>

<span style="line-height: 1.5;">The important points are support OS's as old as Windows 2000, some without PowerShell, they're all in the same domain and we have a domain admin account to use for access. It's best practice to run PowerShell as a standard user and specify alternate credentials when running tools such as this need additional rights instead of running PowerShell as a domain admin so we'll need to add a credential parameter to our tool.</span>

<span style="color: #0000ff;"><em>The good Doctor has asked you to write a PowerShell tool that can show him <strong>each server's name, installed version of Windows, amount of installed physical memory,</strong> and <strong>number of installed processors</strong>. <strong><span style="color: #ff0000;">For processors, he'll be happy getting a count of cores, or sockets, or even both - whatever you can reliably provide across all these different versions of Windows.</span> He has a few text files with computer names - he'd like to pipe the computer names, as strings, to you tool, and have your tool query those computers.</strong></em></span>

That's a lot of information. It's a good thing I have a few highlighters that are different colors. My first thought: "I'll need to use WMI to retrieve this information". The great thing about WMI is that it's been around forever and you could even find a VBScript that uses WMI to accomplish what you want and pull the WMI class out of it and use it in PowerShell.

Now to determine what WMI classes we need to use:

<span style="line-height: 1.5;">Windows Version: Win32_OperatingSystem. This one was too easy. You can search WMI using the Get-WMIObject cmdlet to find classes that may contain the data you're looking for. The class you want is going to begin with win32_ and normally your not going to want the classes that start with win32_perf so filter those out like so:</span>

<a href="http://mikefrobbins.com/wp-content/uploads/2013/05/event2blog1a1.png"><img class="alignnone size-large wp-image-7315" alt="event2blog1a1" src="http://mikefrobbins.com/wp-content/uploads/2013/05/event2blog1a1.png?w=640" width="640" height="199" /></a>

Based on these results, there's only two classes that might contain the data we're looking for: Win32_OperatingSystem or Win32_SystemOperatingSystem

I'll switch to the <em>Get-CimInstance</em> cmdlet at this point since I'm running PowerShell version 3 and it tab expands namespaces and classes so there's less typing. I'll pipe to "<em>Format-List -Property * -Force</em>" because by default only a fraction of the properties are displayed. I added the <em>-Force</em> switch parameter because I want to see the properties where whoever wrote this is trying to protect me from myself. I can see the Caption is the OS name, and the CSName is the ComputerName:

<a href="http://mikefrobbins.com/wp-content/uploads/2013/05/event2blog1b.png"><img class="alignnone size-large wp-image-7234" alt="event2blog1b" src="http://mikefrobbins.com/wp-content/uploads/2013/05/event2blog1b.png?w=640" width="640" height="164" /></a>

Towards the bottom of the results, the Version property is the OS Version:

<a href="http://mikefrobbins.com/wp-content/uploads/2013/05/event2blog1c.png"><img class="alignnone size-full wp-image-7235" alt="event2blog1c" src="http://mikefrobbins.com/wp-content/uploads/2013/05/event2blog1c.png" width="490" height="79" /></a>

Test against all the different OS's we need to support and validate that class exists and returns the correct data for each OS starting with Windows 2000. I personally ran my commands against over 30 machines so I would receive different results from a wide variety of different hardware and operating systems. Don't run your commands in your company's production environment though.

Ok, that one class gives us the server name, and OS version which are two of the items we need. I'll also include the OS Name since OS's like Windows 8 and Server 2012 or Windows Server 2003 and XP have the same "version" numbers. That way if this tool is ever run on workstations and servers, we'll be able to differentiate between the two.

I've found in the scripting games it's generally ok to include a little more information as long as it still meets the requirements and works, but with each item you add that's not required, you open up another chance for problems. Add something that's not required and if it doesn't work, it's much worse that not adding that item or feature at all.

Now we need the memory and processor information. I'll do another WMI search to see what classes exist that might contain that information just like before:

<a href="http://mikefrobbins.com/wp-content/uploads/2013/05/event2blog1d.png"><img class="alignnone size-large wp-image-7238" alt="event2blog1d" src="http://mikefrobbins.com/wp-content/uploads/2013/05/event2blog1d.png?w=640" width="640" height="187" /></a>

The Win32_ComputerSystem class looks promising.

<a href="http://mikefrobbins.com/wp-content/uploads/2013/05/event2blog1e.png"><img class="alignnone size-full wp-image-7239" alt="event2blog1e" src="http://mikefrobbins.com/wp-content/uploads/2013/05/event2blog1e.png" width="614" height="32" /></a>

It's also a good idea to test the classes as you go to make sure they do indeed retrieve the data you want. While the Win32_ComputerSystem appeared to return the correct information on multiple modern operating systems, on Windows 2003 it doesn't provide the correct information as shown below. I know that this Dell PE 1950 Server has two physical processors with four cores each so the NumberOfProcessors property can't be used to retrieve the number of physical CPU sockets since this should be 2:

<a href="http://mikefrobbins.com/wp-content/uploads/2013/05/event2blog1f.png"><img class="alignnone size-full wp-image-7240" alt="event2blog1f" src="http://mikefrobbins.com/wp-content/uploads/2013/05/event2blog1f.png" width="453" height="210" /></a>

I also have a question for you, first read this again: <em><strong>For processors, he'll be happy getting a count of cores, or sockets, or even both - <span style="color: #ff0000;">whatever you can reliably provide across all these different versions of Windows</span></strong>.</em>

What does reliably mean? The online dictionary I used says "giving the same result on successive trials". Can you provide a count of processor cores RELIABLY across all the different versions of Windows beginning with Windows 2000? My answer is no so I did not provide this information. Same question for processor sockets: Can you RELIABLY provide sockets across all the different versions? Yes, so I provided that information.

It's also a good idea to lookup the WMI classes you've chosen to use on MSDN to see if there are any notes about the properties that you're planning to use. Doing this for the Win32_ComputerSystem class and viewing the NumberOfProcessors property would have allowed you to discover that it wouldn't display the processor sockets correctly for Windows 2000 and Windows 2003 machines:

<a href="http://mikefrobbins.com/wp-content/uploads/2013/05/event2blog1j.png"><img class="alignnone size-large wp-image-7254" alt="event2blog1j" src="http://mikefrobbins.com/wp-content/uploads/2013/05/event2blog1j.png?w=640" width="640" height="345" /></a>

Image Source: <a href="http://msdn.microsoft.com/en-us/library/windows/desktop/aa394102(v=vs.85).aspx">http://msdn.microsoft.com/en-us/library/windows/desktop/aa394102(v=vs.85).aspx</a>

There's a hotfix to correct this problem on Windows 2003, but not on 2000: <a href="http://support.microsoft.com/kb/932370">http://support.microsoft.com/kb/932370</a>

<span style="line-height: 1.5;">Here's a trick to accurately retrieve the correct number of physical processors from any of the systems this tool is suppose to run against:</span>

<a href="http://mikefrobbins.com/wp-content/uploads/2013/05/event2blog1h.png"><img class="alignnone size-large wp-image-7246" alt="event2blog1h" src="http://mikefrobbins.com/wp-content/uploads/2013/05/event2blog1h.png?w=640" width="640" height="244" /></a>

The TotalPhysicalMemory property in Win32_ComputerSystem is also incorrect. It shows what's available to Windows after things such as memory for video has been taken out or if the OS doesn't support the amount of physical memory in the server, it would only show what the OS is able to access.

Using this class and property would only return 4091MB of RAM on the same Dell PE 1950 as shown in the following image. I didn't know they made physcial memory chips that would add up to that amount (4091MB)? That's because they don't. Notice in the second example, it's actually very easy to determine the true amount of physical memory using the Win32_PhysicalMemory class. You'll need to Sum or add up all of the memory chips to retrieve this information, but it's not that difficult:

<a href="http://mikefrobbins.com/wp-content/uploads/2013/05/event2blog1g1.png"><img alt="event2blog1g1" src="http://mikefrobbins.com/wp-content/uploads/2013/05/event2blog1g1.png?w=640" width="640" height="87" /></a>

<span style="color: #0000ff;">Oh, and in case he forgets how to use it - make sure this tool of yours has a full <strong>help</strong> display available.</span>

The help is self explanatory. Run "help about_Comment_Based_Help" in PowerShell to see examples.

Are you casting the amount of memory as an integer in gigabytes? Guess what? It's not uncommon for a Windows 2000 Server to have less than half a gigabyte of RAM. Cast 256MB as an integer in gigabytes and you'll end up with zero. Is zero useful information? It's not. You could either leave a couple of decimal places (don't cast it as an integer), or cast it to megabytes as I did.

<a href="http://mikefrobbins.com/wp-content/uploads/2013/05/event2blog1k.png"><img class="alignnone size-full wp-image-7264" alt="event2blog1k" src="http://mikefrobbins.com/wp-content/uploads/2013/05/event2blog1k.png" width="339" height="106" /></a>

Here's the process I use to write my PowerShell scripts, functions, commands, tools, etc:

#1 Write pieces of the script, function, command, etc that pulls the necessary data and verify accuracy. If the data isn't accurate it doesn't matter how fancy your PowerShell code is (period).

#2 Tune your command for efficiency. Don't make multiple calls across the network to the same machine unless absolutely necessary. Only go to the well once and get all you need. Need all you get as well (don't pull data from a remote machine that you don't need - filter at the source, not the destination). Store the data in a variable and work with the variable instead of constantly creating network traffic back and forth. Same goes for calls to disk. They're expensive. Get what you need and store it in a variable. Be sure to only retrieve what you need. A wise man once told me don't buy the whole box of Whitman's Samplers if all you like is chocolate covered peanuts.

#3 Do step #1 again and make sure you're data is still accurate (Accuracy counts!)

#4 Apply best practices once you've completed #1, #2, and #3. Add error checking etc.

Want to see my solution for Scripting Games advanced event 2? Click this link: Advanced 2: An Inventory Intervention: http://scriptinggames.org/entrylist.php?entryid=552. You’ll have to be signed in to see the solution. If you like my script, please vote for it! Click on the star all the way to the right:

<img class="alignnone size-full wp-image-7276" alt="5stars" src="http://mikefrobbins.com/wp-content/uploads/2013/05/5stars.png" width="592" height="147" />

Want to know more or talk about the Scripting Games event #2? Join us for an unofficial meeting of the Mississippi PowerShell User Group tonight at 8:30pm CDT. Use <a href="https://meet.lync.com/flpowershellug/mrobbins/87M20VRB" target="_blank">this link</a> to join the Lync meeting. See our <a href="http://mspsug.com/attendee-info/" target="_blank">Attendee Info</a> page for the requirements if needed.

<span style="text-decoration: underline;">Update 02/09/14</span>
The link to my solution is no longer valid so I’m posting it here since I’ve received some requests for it:
<pre class="lang:ps decode:true crayon-selected" title="Advanced Category Event #2 of the 2013 Scripting Games">#Requires -Version 3.0

function Get-SystemInventory {

&lt;#
.SYNOPSIS
Retrieves hardware and operating system information for systems running Windows 2000 and higher. 
.DESCRIPTION
Get-SystemInventory is a function that retrieves the computer name, operating system version, amount of physical memory
(RAM) in megabytes, and number of processors (CPU's) sockets from one or more hosts specified via the ComputerName parameter
or via pipeline input. The TotalPhysicalMemory property of Win32_ComputerSystem was initially used, but that property is not
the total amount of physical system memory. It is the amount of system memory available to Windows after the OS takes some
out for video if necessary or if the OS doesn't support the amount of RAM in the machine which is common when running an older
32 bit operating system on hardware with a lot of physical memory. Displaying the amount of memory in megabytes was chosen
because this function can be run against older hosts where less than half a gigabyte of memory is common and casting 256MB for
example to an integer in gigabytes would display zero which is not useful information. The amount of processor sockets was
chosen for similar reasons because older operating systems aren't aware of CPU cores and that information wouldn't be reliably
provided across all operating systems this function could be run against. Initially, the NumberOfProcessors property in the
Win32_ComputerSystem class was used, but it does not provide an accurate count of CPU sockets on older operating systems with
multi-core processors per this MSDN article: http://msdn.microsoft.com/en-us/library/windows/desktop/aa394102(v=vs.85).aspx
The Cim cmdlets have been used to gain maximum efficiency so only one connection will be made to each computer. This tool has
also been future proofed so as more hosts are upgraded to newer Windows operating systems, they will be able to take advantage
of the WSMAN protocol because DCOM is blocked by default in the firewall of newer operating systems and possibly on firewalls
between the computer running this tool and the destination host. A ShowProtocol switch parameter has been provided so the
results can be filtered (Where-Object) or sorted (Sort-Object) to determine which computers are not being communicated with
using WSMAN which is another means of finding older hosts. A Credential parameter has been provided because it's best practice
to run PowerShell as a non-domain admin and provide the domain admin credentials specified in the scenario on an as needed per
individual command basis. This function requires PowerShell version 3 on the computer it is being run from, but PowerShell is
not required to be installed or enabled on the remote computers that it is being run against.
.PARAMETER ComputerName
Specifies the name of a target computer(s). The local computer is the default. You can also pipe this parameter value.
.PARAMETER Credential
Specifies a user account that has permission to perform this action. The default is the current user.
.EXAMPLE
Get-SystemInventory
.EXAMPLE
Get-SystemInventory -ComputerName 'Server1'
.EXAMPLE
Get-SystemInventory -ComputerName 'Server1', 'Server2' -Credential 'Domain\UserName'
.EXAMPLE
'Server1', 'Server2' | Get-SystemInventory
.EXAMPLE
(Get-Content c:\ComputerNames.txt) | Get-SystemInventory
.EXAMPLE
(Get-Content c:\ComputerNames.txt, c:\ServerNames.txt) | Get-SystemInventory -Credential (Get-Credential) -ShowProtocol
.INPUTS
System.String
.OUTPUTS
System.Management.Automation.PSCustomObject
#&gt;

    param(
        [Parameter(ValueFromPipeline=$True)]
        [ValidateNotNullorEmpty()]
        [string[]]$ComputerName = $env:COMPUTERNAME,

        [System.Management.Automation.Credential()]$Credential = [System.Management.Automation.PSCredential]::Empty,

        [switch]$ShowProtocol
    )

    BEGIN {
        $Opt = New-CimSessionOption -Protocol Dcom
    }

    PROCESS {
        foreach ($Computer in $ComputerName) {

            Write-Verbose "Attempting to Query $Computer"
            $Problem = $false
            $SessionParams = @{
                ComputerName  = $Computer
                ErrorAction = 'Stop'
            }

            If ($PSBoundParameters['Credential']) {
               $SessionParams.credential = $Credential
            }

            if ((Test-WSMan -ComputerName $Computer -ErrorAction SilentlyContinue).productversion -match 'Stack: 3.0') {
                try {
                    $CimSession = New-CimSession @SessionParams
                    $CimProtocol = $CimSession.protocol
                    Write-Verbose "Successfully created a CimSession to $Computer using the $CimProtocol protocol."
                }
                catch {
                    $Problem = $True
                    Write-Verbose "Unable to connect to $Computer using the WSMAN protocol. Verify your credentials and try again."
                }
            }

            elseif (Test-Connection -ComputerName $Computer -Count 1 -Quiet -ErrorAction SilentlyContinue) {
                $SessionParams.SessionOption = $Opt
                try {
                    $CimSession = New-CimSession @SessionParams
                    $CimProtocol = $CimSession.protocol
                    Write-Verbose "Successfully created a CimSession to $Computer using the $CimProtocol protocol."
                }
                catch {
                    $Problem = $True
                    Write-Verbose  "Unable to connect to $Computer using the DCOM protocol. Verify your credenatials and that DCOM is allowed in the firewall on the remote host."
                }
            }

            else {
                $Problem = $True
                Write-Verbose "Unable to connect to $Computer using the WSMAN or DCOM protocol. Verify $Computer is online and try again."
            }

            if (-not($Problem)) {
                $OperatingSystem = Get-CimInstance -CimSession $CimSession -Namespace root/CIMV2 -ClassName Win32_OperatingSystem -Property CSName, Caption, Version
                $PhysicalMemory = Get-CimInstance -CimSession $CimSession -Namespace root/CIMV2 -ClassName Win32_PhysicalMemory -Property Capacity |
                                  Measure-Object -Property Capacity -Sum
                $Processor = Get-CimInstance -CimSession $CimSession -Namespace root/CIMV2 -ClassName Win32_Processor -Property SocketDesignation |
                             Select-Object -Property SocketDesignation -Unique
                Remove-CimSession -CimSession $CimSession -ErrorAction SilentlyContinue
            }

            else {
                $OperatingSystem = @{
                    CSName= $Computer
                    Caption = 'Failed to connect to computer'
                    Version = 'Unknown'
                }
                $PhysicalMemory = @{
                    Sum = 0
                }
                $Processor = @{
                }
                $CimProtocol = 'NA'
            }

            $SystemInfo = [ordered]@{
                ComputerName = $OperatingSystem.CSName
                'OS Name' = $OperatingSystem.Caption
                'OS Version' = $OperatingSystem.Version
                'Memory(MB)' =  $PhysicalMemory.Sum/1MB -as [int]
                'CPU Sockets' = $Processor.SocketDesignation.Count
            }

            If ($PSBoundParameters['ShowProtocol']) {
               $SystemInfo.'Connection Protocol' = $CimProtocol
            }

            New-Object PSObject -Property $SystemInfo
        }
    }
}</pre>
This PowerShell script can also be downloaded from the TechNet <a href="http://gallery.technet.microsoft.com/scriptcenter/2013-Scripting-Games-beddb0fb" target="_blank">script repository</a>.

µ