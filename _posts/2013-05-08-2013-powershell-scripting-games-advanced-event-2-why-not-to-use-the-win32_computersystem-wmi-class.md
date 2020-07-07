---
ID: 7288
post_title: >
  2013 PowerShell Scripting Games Advanced
  Event 2 – Why NOT to use the
  Win32_ComputerSystem WMI Class
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2013/05/08/2013-powershell-scripting-games-advanced-event-2-why-not-to-use-the-win32_computersystem-wmi-class/
published: true
post_date: 2013-05-08 07:30:19
---
The majority of the entries I've seen for the Scripting Games event 2 are using the TotalPhysicalMemory property from the Win32_ComputerSystem WMI class to determine the total amount of physical memory in a server. The property name is "TotalPhysicalMemory" after all so that's what it should contain, right? Not so fast, keep reading to discover why.

Your manager needs an inventory of all of your company's physical servers that are located in three different data centers which are in different parts of the country. He has assigned you the task of inventorying those servers and wants you to physically log into each server or travel to the location where the servers reside to retrieve the information using the GUI.

You've asked your manager if you could try retrieving this information with PowerShell first. He is skeptical and thinks PowerShell is some kind of Witchcraft Voodoo stuff that must be like EverQuest or WarCraft because people stay up late at night playing some sort of scripting games with it. Reluctantly, he gives you the go ahead to proceed with the use of PowerShell for this project. This is your chance to sell your manager on using PowerShell at your company!

The requirements that your manager wants just happens to be the same as the Scripting Games event 2 requirements. Since you're new to PowerShell, you decide to look at the scripts that were submitted in the advanced event #2 and use the method to retrieve the information that most others used, after all, if most people used it and received good scores, then it must be right? You think "This will be too easy, I can copy one of those 5 star scripts and I'll be the PowerShell hero at my company!"

Well, it just so happens that your company got a good deal on some servers several years ago, before you started with the company, that had 8 gigabytes of RAM in them, but due to application compatibility requirements the prior IT engineers who are no longer with the company had to load an x86 (32 bit) version Windows Server 2003 R2. The company already owned Standard Edition of that OS and they didn't need more than 4 gigabytes of RAM so they loaded that operating system to keep the project within budget.

The awesome 5 star script that you downloaded used what most others in the competition used to retrieve the total amount of physical memory in the servers (TotalPhysicalMemory property from the Win32_ComputerSystem WMI class).

Here are the requirements your manager provided which just happen to be exactly the same as the requirements in the Scripting Games advanced event #2. As you can see, in black and white, he wants the "<strong>Amount of Installed Physical Memory</strong>" from the servers:

<a href="http://mikefrobbins.com/wp-content/uploads/2013/05/event2blog2b.png"><img class="alignnone size-full wp-image-7289" alt="event2blog2b" src="http://mikefrobbins.com/wp-content/uploads/2013/05/event2blog2b.png" width="484" height="111" /></a>

You've completed the task, turned in your amazing results to your manager and saved the company lots of money on labor and travel in the process. Now your manager wants to see you in his office. It must to be because he wants to give you a raise or promotion for doing such an awesome job!

Your manager has checked the manufacturers website for some of the servers you provided the inventory for and he wants to know who stole memory out of the servers to put in their gaming machines at home to play these scripting games because the report shows 4GB of RAM and the manufacturers website shows they shipped with 8GB.

Well, it just so happens that the 5 star script you copied as well as what most of the others are using for the Scripting Games in event #2 to retrieve the <strong>Amount of Installed Physical Memory</strong> is incorrect! What? Sorry, but it's true.

As you can see in the image below, the TotalPhysicalMemory property of Win32_ComputerSystem WMI class only shows the amount of memory that the OS has available to it and not the amount of physical memory in the servers:

<a href="http://mikefrobbins.com/wp-content/uploads/2013/05/event2blog2a.png"><img class="alignnone size-large wp-image-7290" alt="event2blog2a" src="http://mikefrobbins.com/wp-content/uploads/2013/05/event2blog2a.png?w=640" width="640" height="340" /></a>

Since the Windows Server 2003 R2 Standard Edition x86 (32 bit) operating system is only able to access 4 gigabytes of RAM, that's what the report you turned into your manager had on it, where as you can see using the Win32_PhysicalMemory WMI class and adding up or summing the capacity for all of the memory chips did indeed retrieve the actual amount of physical memory in the server.

Want to know why not to use the Win32_ComputerSystem WMI class for the number of processor sockets? See my blog titled "<a href="http://mikefrobbins.com/2013/05/07/2013-powershell-scripting-games-advanced-event-2-attention-to-detail-is-everything/" target="_blank">2013 PowerShell Scripting Games Advanced Event 2 – Attention to Detail is Everything</a>"

Want to see my solution for Scripting Games advanced event 2? Click this link: Advanced 2: An Inventory Intervention: http://scriptinggames.org/entrylist.php?entryid=552. You’ll have to be signed in to see the solution. If you like my script, please vote for it! Click on the star all the way to the right:

<img alt="5stars" src="http://mikefrobbins.com/wp-content/uploads/2013/05/5stars.png" width="592" height="147" />

<span style="text-decoration: underline;">Update 02/09/14</span>
The link to my solution is no longer valid so I'm posting it here since I've received some requests for it:
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