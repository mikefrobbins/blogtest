---
ID: 7420
post_title: >
  2013 PowerShell Scripting Games Advanced
  Event 6 – The Grand Finale
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2013/06/08/2013-powershell-scripting-games-advanced-event-6-the-grand-finale/
published: true
post_date: 2013-06-08 10:00:34
---
For me, the Scripting Games have been a great learning experience this year. I've used many PowerShell features that I hadn't used before such as splatting, ADSI, Workflows, and producing html webpages with PowerShell. I plan to write detailed followup blog articles on each of these topics over the next few months.

Event 6 was definitely challenging since I hadn't used workflows before but I also knew that's what was really needed to accomplish the given task properly (In my opinion). While you could accomplish the task without a workflow, the scripting games are about learning. I decided that I could maximize the return of investment on my time that I would invest in event 6 by using a workflow since I knew very little about them.

I start out by specifying the version of PowerShell and the module that is required. The "<em>#Requires -Version 3.0</em>" statement will prevent this function from being dot-sourced on a machine with anything less than PowerShell version 3. Using a requires version statement is going to be even more important in the future as additional versions of PowerShell are released in the future. PowerShell version 4 was announced at Microsoft TechEd North America 2013 this week.

Specifying that the DHCP module is required prevents the function from being dot-sourced on a machine that does not have the DHCPServer module installed which is part of the <a href="http://www.microsoft.com/en-us/download/details.aspx?id=28972" target="_blank">Remote Server Administration Tools</a>. The additional benefit to this is that the module will be automatically imported when the function is dot-sourced which keeps you from having to manually import it if the $PSModuleAutoLoadingPreference happens to be set to none. Some people dislike the module auto-load feature and set this preference variable to none in their profile so you can't assume that modules auto-load when sharing tools such as this one with others.

The help is collapsed in the image of the script, but providing comment based help is extremely important. For more information on this topic, run "help about_Comment_Based_Help" from within PowerShell:

<a href="http://mikefrobbins.com/wp-content/uploads/2013/06/2013sg-e6h-png.png"><img class="alignnone size-large wp-image-7478" alt="2013sg-e6h-png" src="http://mikefrobbins.com/wp-content/uploads/2013/06/2013sg-e6h-png.png?w=640" width="640" height="122" /></a>

I validate the format of the values provided for the MAC address parameter and return a meaningful error message if they are not provided in the proper format.

I don't validate the computers with the <em>Test-Connection</em> cmdlet because a Windows Server 2012 machine blocks ICMP (ping) requests by default which means it would fail to validate all of the computers we are trying to rename and add to the domain.

I provide parameters and defaults for all of the different items specified in the scenario requirements which can be found <a href="http://scriptinggames.org/019a63360a2f27f09f45.pdf" target="_blank">here</a>.

I obfuscate the passwords so they're not just in plain text in the script for anyone to see, although this is not a secure method of storing a password, and if someone runs that portion of the code, they will see the password in clear text.

<a href="http://mikefrobbins.com/wp-content/uploads/2013/06/2013sg-e6a-png.png"><img class="alignnone size-large wp-image-7422" alt="2013sg-e6a-png" src="http://mikefrobbins.com/wp-content/uploads/2013/06/2013sg-e6a-png.png?w=640" width="640" height="544" /></a>

The BEGIN Block:

The first thing I do in the begin block is to check to see if PowerShell is running as an administrator. If not, the function ends and the user receives a message with instructions on how to run PowerShell as an administrator since PowerShell is unable to participate in <a href="https://en.wikipedia.org/wiki/User_Account_Control" target="_blank">User Access Control</a> (UAC) and ask for elevated privileges.

You may ask why do I need to run PowerShell as an administrator? Well, to start with you shouldn't be managing servers by logging into the console or remote desktoping into them. That means that this tool will be run from a client computer and by default on Windows 8, the WinRM service is not running. In order to access the trusted host list, WinRM has to be running. I retrieve what's in the trusted host list and store it in a variable for use later. Specifying the <em>-Force</em> parameter of the <em>Get-Item</em> cmdlet starts the WinRM service without prompting the user if it's not already running.

Another best practice is that you should never be logged into your computer or running PowerShell as a domain administrator. In order to run PowerShell as an admin, you'll have to specify admin credentials when running it, but those should have local admin rights and be a domain user, not a domain admin. Specify elevated credentials on an as needed, per command basis. Remember, use the <a href="http://en.wikipedia.org/wiki/Principle_of_least_privilege" target="_blank">principal of least privilege</a>.

This means the user you're running PowerShell as won't have access to the DHCP server and in order to specify a credential parameter with the <em>Get-DHCPServerv4Lease</em> cmdlet, you'll need to create a CimSession. The other benefit to creating a CimSession is when the MAC addresses are piped in, we can retrieve all of the IP Addresses over the single connnection instead of having the overhead of setting up and tearing down ten different connections in this scenario.

I use a nested Try/Catch which attempts to create the CimSession using WSMAN and then attempts to use DCOM if it is unable to connect with WSMAN.

<a href="http://mikefrobbins.com/wp-content/uploads/2013/06/2013sg-e6b-png.png"><img class="alignnone size-large wp-image-7423" alt="2013sg-e6b-png" src="http://mikefrobbins.com/wp-content/uploads/2013/06/2013sg-e6b-png.png?w=640" width="640" height="181" /></a>

The Workflow:

I nested the actual workflow into my begin block since it only needs to run once to load it into memory and then it can be called as many times as necessary, almost like dot-sourcing a function.

Comment based help is not supported in a workflow so I've added some inline help where applicable. I didn't use a workflow instead of a function for this entire process because the way I understood the scenario, you need to accept the MAC addresses via pipeline input and pipeline input is not supported in workflows. Advanced parameter validation is also not supported per the information I found on the <a href="http://blogs.technet.com/b/heyscriptingguy/archive/2013/01/02/powershell-workflows-restrictions.aspx" target="_blank">Hey, Scripting Guy Blog!</a> article that I read, and per what I read in the <a href="http://www.manning.com/jones2/" target="_blank">PowerShell in Depth</a> book.

A brand new machine can be added to the domain and renamed in one line using the <em>Add-Computer</em> cmdlet, but there's an issue where if you revert the VM to a snapshot and delete the computer account from Active Directory, the rename portion fails with a "Directory service is busy" error, although it is added to the domain with the existing computer name:

<a href="http://mikefrobbins.com/wp-content/uploads/2013/06/2013sg-e6g-png.png"><img class="alignnone size-large wp-image-7468" alt="2013sg-e6g-png" src="http://mikefrobbins.com/wp-content/uploads/2013/06/2013sg-e6g-png.png?w=640" width="640" height="87" /></a>

For this reason, I decided to rename the computer in one step, reboot, and then add it to the domain and reboot again because that process works reliably every time and I'll take reliability over a little performance any day. I see having to reboot twice as a non-issue since the entire process is automated.

I was at Microsoft TechEd this week and I actually had a chance to speak to members of the PowerShell team about the "Directory service is busy" issue. They confirmed that it is an issue and said they had previously run into the same problem. They stated that they had spoken to the Active Directory team about the issue and were told to do the rename and the add to the domain in two steps as I had done in my workflow.

I started out with a sequence block in my workflow and had a difficult time finding the definitive answer on whether it was needed or not, but I finally found the answer on page 818 of Lee Holmes's new <a href="http://shop.oreilly.com/product/0636920024132.do" target="_blank">Windows PowerShell Cookbook</a>, 3rd edition book:

<em>"Unlike the parallel statement, the lines within the braces of the parallel -foreach statement are treated as a unit and are invoked sequentially."</em>

This means the sequence statement was not needed. While it wouldn't have necessarily hurt anything to have it in there, I don't like having unnecessary items in my code.

<a href="http://mikefrobbins.com/wp-content/uploads/2013/06/2013sg-e6c-png.png"><img class="alignnone size-large wp-image-7424" alt="2013sg-e6c-png" src="http://mikefrobbins.com/wp-content/uploads/2013/06/2013sg-e6c-png.png?w=640" width="640" height="242" /></a>

Here's the part of the script I discussed earlier where it gives you a warning if you're not running PowerShell as an administrator:

<a href="http://mikefrobbins.com/wp-content/uploads/2013/06/2013sg-e6d-png.png"><img class="alignnone size-large wp-image-7425" alt="2013sg-e6d-png" src="http://mikefrobbins.com/wp-content/uploads/2013/06/2013sg-e6d-png.png?w=640" width="640" height="70" /></a>

The PROCESS Block:

The objective of the process block in my function is to translate the MAC addresses to IP addresses by querying the DHCP server. If the MAC addresses are provided via parameter input, it only runs once and the command can translate all of them in one shot so a foreach loop is not needed, but if the MAC addresses are specified via pipeline input, the process block will run once per MAC address and retrieve them one at a time which is not a problem either based on the way the command in the process block is written:

<a href="http://mikefrobbins.com/wp-content/uploads/2013/06/2013sg-e6e-png.png"><img class="alignnone size-large wp-image-7426" alt="2013sg-e6e-png" src="http://mikefrobbins.com/wp-content/uploads/2013/06/2013sg-e6e-png.png?w=640" width="640" height="106" /></a>

The END Block:

One unique thing about my function is it performs a good portion of the work in the End block. This is because I need these items to run one time and only one time regardless of whether the MAC addresses are provided via parameter or pipeline input and that wouldn't be the case if this portion of the function was added to the PROCESS block.

It starts out with a foreach loop to build a hash table of server names and IP addresses. While it's looping through each individual IP address, it also adds just the specific IP addresses to the trusted host list along with not overwriting what's already in it to prevent issues with other applications that may have already modified this setting. The individual IP's are added to maximize security and I think of it like adding holes in a firewall, you want the holes to be as small as possible to reduce the risk on a security incident. You should <strong>NEVER</strong> add "<strong>*</strong>" to the trusted host list. For more information about the trusted host list see the free "Secrets of PowerShell Remoting" ebook on <a href="http://PowerShellBooks.com" target="_blank">PowerShellBooks.com</a>.

It's also not possible to modify the trusted hosts list unless PowerShell is running as an administrator. This is just one more reason your tool needs to validate that PowerShell is running as an administrator, otherwise you're only wasting time by processing the entire function to only fail in the workflow because the computers aren't trusted:

<a href="http://mikefrobbins.com/wp-content/uploads/2013/06/2013sg-e6i-png.png"><img class="alignnone size-large wp-image-7495" alt="2013sg-e6i-png" src="http://mikefrobbins.com/wp-content/uploads/2013/06/2013sg-e6i-png.png?w=640" width="640" height="156" /></a>

At the very end, I put the trusted host back the way it was to start with as I don't want to leave unnecessary items in the trusted host list because it reduces security. We do however, want to put the trusted host list back the way it was to start with to prevent any issues with applications that may have previously modified this setting.

<a style="line-height: 1.5;" href="http://mikefrobbins.com/wp-content/uploads/2013/06/2013sg-e6f-png.png"><img class="alignnone size-large wp-image-7427" alt="2013sg-e6f-png" src="http://mikefrobbins.com/wp-content/uploads/2013/06/2013sg-e6f-png.png?w=640" width="640" height="336" /></a>

<span style="text-decoration: underline;">Update 02/09/14
</span>Posting my solution here as well since I've received some requests for it:
<pre class="lang:ps decode:true" title="Advanced Category Event #6 of the 2013 Scripting Games">#Requires -Version 3.0
#Requires -Modules DhcpServer
function Add-DomainComputer {

&lt;#
.SYNOPSIS
    Adds computers running Windows Server 2012 to a domain based on MAC Address for computers that receive their IP
address via DHCP.

.DESCRIPTION
    Add-DomainComputer is a function that adds computers running Windows Server 2012 to a domain. PowerShell 3.0 is
required to run this tool.The Windows Server 2012 computer that you want to add to the domain must receive its IP
address via DHCP. This tool accepts the MAC address of the computer(s) you wish to add to the domain via pipeline
or paramter input. Those MAC addresses are translated to IP addresses using the Get-DhcpServerv4Lease function that
is part of the DhcpServer module which is installed as part of the Remote Server Administration Tools (RSAT):
http://www.microsoft.com/en-us/download/details.aspx?id=28972.
    The current list of trusted hosts for the machine running this tool is captured, and then all of the IP addresses
of the machines you are adding to the domain are added to the trustedhosts list. The last step once this tool
completes is to restore the trusted host list to the state it was in prior to this tool being run to prevent any
issues with machines that may have already existed in the trusted host list and to prevent the permenant reduction
of security for the machine running this tool.
    A workflow is used to add all of the machines to the domain in parallel.    

.PARAMETER MACAddress
    The Media Access Control Address of the network card in the Windows Server 2012 computer that is receiving its
IP address via a DHCP server that you're attempting to add to the domain. The value(s) for this parameter must be
specified in MAC-48 format which is six groups of two hexadecimal digits separated by dashes: 01-23-45-67-89-AB.

.PARAMETER NewNameBase
    The base name for the new name to be used for the Servers. Numbers starting with 1 are appended to this base
name and used to rename the server when adding them to the domain. The default is "SERVER" which will cause the
servers to be named "SERVER1", "SERVER2", etc.

.PARAMETER DHCPServer
    The name of the DHCP Server that is providing IP addresses to the servers you wish to add to the domain. The
default attempts to obtain the DHCP information from a server named "DHCP1".

.PARAMETER DHCPScopeId
    The name of the DHCP Scope on the server specified via the DHCPServer parameter. The default attempts to obtain
DHCP information from a scope named "10.0.0.0".

.PARAMETER DomainName
    The name of the domain that you are attempting to add the new servers to. The default is to attempt to add the
new servers to a domain named "Company.local".

.PARAMETER LocalAdminCredential
    A local account on the servers that you're attempting to add to the domain that has local admin privileges. The
default obfuscates the password provided in the requirements for this scenario to prevent it from being displayed in
clear text, but this is not a secure method of storing a password. 

.PARAMETER DomainAdminCredential
    A domain account in the domain that you're attempting to add to the servers to that has domain admin privileges (As
specified in the scenario requirements). The default obfuscates the password provided in the requirements for this
scenario to prevent it from being displayed in clear text, but this is not a secure method of storing a password.

.EXAMPLE
    Add-DomainComputer -MACAddress '01-23-45-67-89-AB', '01-23-45-67-89-A1'

.EXAMPLE
    '01-23-45-67-89-AB', '01-23-45-67-89-A1' | Add-DomainComputer

.EXAMPLE
    Get-Content .\server-macs.txt | Add-DomainComputer

.EXAMPLE
    Get-Content .\server-macs.txt | Add-DomainComputer -BaseName 'SERVER'

.EXAMPLE
    Get-Content .\server-macs.txt | Add-DomainComputer -BaseName 'SERVER' -DHCPServer 'DHCP1'

.EXAMPLE
    Get-Content .\server-macs.txt | Add-DomainComputer -BaseName 'SERVER' -DHCPServer 'DHCP1' -DHCPScopeId '10.0.0.0'

.EXAMPLE
    Get-Content .\server-macs.txt | Add-DomainComputer -BaseName 'SERVER' -DHCPServer 'DHCP1' -DHCPScopeId '10.0.0.0' -DomainName 'Company.local'

.EXAMPLE
    Get-Content .\server-macs.txt | Add-DomainComputer -LocalAdminCredential (Get-Credential Administrator) -DomainAdminCredential (Get-Credential company\Admin)

.INPUTS
    String

.OUTPUTS
    None
#&gt;

    [CmdletBinding()]
    param (
        [Parameter(Mandatory,ValueFromPipeline)]
        [ValidateScript({
            If ($_ -match '^([0-9a-fA-F]{2}[:-]{0,1}){5}[0-9a-fA-F]{2}$') {
                $True
            }
            else {
                Throw "$_ is either not a valid MAC Address or was not specified in the required format. Please specify one or more MAC addresses in the follwing format: '01-23-45-67-89-AB'"
            }
        })]
        [string[]]$MACAddress,

        [ValidateNotNullorEmpty()]
        [string]$NewNameBase = 'SERVER',

        [ValidateNotNullorEmpty()]
        [string]$DHCPServer = 'DHCP1',

        [ValidateNotNullorEmpty()]
        [string]$DHCPScopeId = '10.0.0.0',

        [ValidateNotNullorEmpty()]
        [string]$DomainName = 'Company.local',

        [pscredential]$LocalAdminCredential = (
            New-Object System.Management.Automation.PSCredential -ArgumentList administrator, (
            -join ("5040737377307264" -split "(?&lt;=\G.{2})",19 |
            ForEach-Object {if ($_) {[char][int]"0x$_"}}) |
            ConvertTo-SecureString -AsPlainText -Force)
        ),

        [pscredential]$DomainAdminCredential = (
            New-Object System.Management.Automation.PSCredential -ArgumentList company\admin, (
            -join ("5040737377307264" -split "(?&lt;=\G.{2})",19 |
            ForEach-Object {if ($_) {[char][int]"0x$_"}}) |
            ConvertTo-SecureString -AsPlainText -Force)
        )
    )

    BEGIN {
        Write-Verbose -Message "Determining if PowerShell is running as an Admin. Terminating the tool execution and returning a message to the user if not."
        if (([Security.Principal.WindowsPrincipal] [Security.Principal.WindowsIdentity]::GetCurrent()).IsInRole([Security.Principal.WindowsBuiltInRole]::Administrator)) {

            $IPNameMatrix = @{}

            Write-Verbose -Message "Obtaining the list of currently trusted hosts, -Force will start the WinRM service if it in not already running without prompting."
            $TrustedHost = Get-Item -Path WSMan:\localhost\Client\TrustedHosts -Force | Select-Object -ExpandProperty Value
            Write-Verbose -Message "The Trusted Host list currently contains: $TrustedHost"

            Write-Verbose -Message "Creating CimSession to DHCP Server using Domain Admin credential. Current user may not have access to the DHCP Server."
            try {
                Write-Verbose -Message "Attempting to create CimSession to the DHCP Server: $DHCPServer using WSMAN."
                $CimSession = New-CimSession -ComputerName $DHCPServer -Credential $DomainAdminCredential -ErrorAction Stop
            }
            catch {
                try {
                    Write-Verbose -Message "WSMAN failed, attempting to create CimSession to the DHCP Server: $DHCPServer using DCOM."
                    $Opt = New-CimSessionOption -Protocol Dcom
                    $CimSession = New-CimSession -ComputerName $DHCPServer -SessionOption $Opt -Credential $DomainAdminCredential -ErrorAction Stop
                }
                catch {
                    $Problem = $true
                    Write-Warning -Message "Error creating CimSession to DHCP Server: $DHCPServer. Error Details: $_.Exception.Message"                   
                }
            }

            Workflow Join-Domain {
             #Comment based help and advanced parameter validation are not supported in workflows: http://blogs.technet.com/b/heyscriptingguy/archive/2013/01/02/powershell-workflows-restrictions.aspx
                param(
                    [hashtable]$IPNameMatrix,
                    [string]$DomainName,
                    [pscredential]$DomainAdminCredential
                )
                    foreach -parallel ($Computer in $PSComputerName) {

                        #This could be done in a single command with a single reboot, but if the machine has ever been in the domain, even if it has been reverted and the computer account was removed
                        #from AD, it will cause directory service busy errors on the rename so I chose to rename the computer in one command and add it to the domain in another which requires two
                        #reboots. Here's the single command to do this: Add-Computer -DomainName $DomainName -NewName $IPNameMatrix[$Computer] -Credential $DomainAdminCredential -PSActionRetryCount 3

                        Rename-Computer -NewName $IPNameMatrix[$Computer] -Force
                        Restart-Computer -Wait -For WinRM -Protocol WSMan -Force

                        Add-Computer -DomainName $DomainName -Credential $DomainAdminCredential -PSActionRetryCount 3
                        Restart-Computer -Wait -For WinRM -Protocol WSMan -Force -PSCredential $DomainAdminCredential

                        #Design Note: Unlike the parallel statement, the lines within the braces of the parallel -foreach statement are treated as a unit and are invoked sequentially
                    }
            }

        }
        else {
            $Problem = $true
            Write-Warning -Message "PowerShell must be run as an Administrator in order to use this tool. Right click PowerShell and select 'Run as Administrator' and then try again."
        }
    }

    PROCESS {
        if (-not($Problem)) {
                try {
                    Write-Verbose -Message "Attempting to translate the MAC addresses to IP addresses via the DHCP Server: $DHCPServer"               
                    [array]$IPAddress+= (Get-DhcpServerv4Lease -CimSession $CimSession -ScopeId $DHCPScopeId -ClientId $MACAddress -ErrorAction Stop |
                    Select-Object -ExpandProperty IPAddress).IPAddressToString
                }
                catch {
                    $Problem = $true
                    Write-Warning -Message "Error translating MAC Addresses to IP Addresses. Error Details: $_.Exception.Message"
                }
        }
    }

    END {
        if (-not($Problem)) {
            foreach ($IP in $IPAddress) {
                $i++

                Write-Verbose -Message "Adding IPAddress: $IP and ServerName: $NewNameBase$i to the IPNameMatrix HashTable."
                $IPNameMatrix.Add($IP, "$NewNameBase$i")
                Write-Verbose -Message "IPNameMatrix now contains: $($IPNameMatrix.Count) items"

                Write-Verbose -Message "Adding the IP Addresses to the local computers trusted hosts list."
                Set-Item -Path WSMan:\localhost\Client\TrustedHosts -Value $IP.ToString() -Concatenate -Force
            }

            $Params = @{
                PSComputerName = $($IPNameMatrix.Keys)
                IPNameMatrix = $IPNameMatrix
                DomainName = $DomainName
                DomainAdminCredential = $DomainAdminCredential
                PSCredential = $LocalAdminCredential
            }

            try {
                Write-Verbose -Message "Calling the Join-Domain Workflow."
                Join-Domain -PSParameterCollection $Params

                Write-Verbose -Message "Restoring the list of trusted hosts to the state that it was in prior to running this tool to prevent a reduction in system security."
                $TrustedHost | ForEach-Object {Set-Item -Path WSMan:\localhost\Client\TrustedHosts -Value $_.ToString() -Force}
            }
            catch {
                Write-Warning -Message "Please contact your system administrator. Reference error: $_.Exception.Message"
            }

            Write-Verbose -Message "Cleanup: Removing the single CimSession that was created by this tool since it is no longer needed."
            Remove-CimSession -CimSession $CimSession -ErrorAction SilentlyContinue
        }
    }
}</pre>
This PowerShell script can also be downloaded from the TechNet <a href="http://gallery.technet.microsoft.com/scriptcenter/2013-Scripting-Games-e079e02a" target="_blank">script repository</a>.

µ