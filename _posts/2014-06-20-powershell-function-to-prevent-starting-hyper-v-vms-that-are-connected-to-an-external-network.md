---
ID: 10081
post_title: 'PowerShell Function to Prevent Starting Hyper-V VM&#8217;s that are Connected to an External Network'
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2014/06/20/powershell-function-to-prevent-starting-hyper-v-vms-that-are-connected-to-an-external-network/
published: true
post_date: 2014-06-20 07:30:13
---
Beginning with Windows 8 (Professional and Enterprise Edition), Hyper-V is available on workstations that have a processor that supports SLAT (<a href="http://en.wikipedia.org/wiki/Second_Level_Address_Translation" target="_blank">Second Level Address Translation</a>). For specifics about the requirements, see the <a href="http://technet.microsoft.com/en-us/library/hh857623.aspx" target="_blank">Client Hyper-V</a> blog article on Microsoft TechNet. That means you have a <a href="http://en.wikipedia.org/wiki/Hypervisor" target="_blank">Hypervisor</a> running right on your desktop or laptop computer for free. With the price of hardware these days, your regular everyday computer can be spec'd out with an i7 processor, 16 gigabytes of memory, and one or more solid state drives and in addition to performing your everyday work on it, you have an awesome test environment without the need for additional physical computers.

Thanks to the server core (no-GUI) installation of newer versions of Windows Server, you can run a number of VM's on the previously referenced hardware with no issues. I have a domain controller, web server, and a couple of SQL servers all running on Windows Server 2012 R2 with the server core installation. I also have more than a half dozen test Windows 8.1 machines on that same hardware, all of which can be running at the same time if needed thanks to Hyper-V's dynamic memory feature.

All of that is well and fine until you decided to run something like the DHCP Server role on one of the servers in that test lab environment. Accidentally connect it to an external network and it could reap havoc on your production network to say the least.

That's why I created the following function to only boot VM's that are connected to internal or private network. It will also start VM's that are not connected to a network.
<pre class="lang:ps decode:true" title="Start-MrVM">#Requires -Version 3.0
#Requires -Modules Hyper-V
function Start-MrVM {

&lt;#
.SYNOPSIS
    Starts Hyper-V VM's that are valid, not already running, and not connected
    to an external network. 
 
.DESCRIPTION
    Start-MrVM is a function that is designed to start a test lab environment of
    VM's running on Hyper-V on a Windoes 8.1 machine. It is designed specifically
    to prevent VM's that are connected to an external switch from being started
    since this could interfere with a production environment because of the
    potiential of things like a DHCP server running in the test VM environment.
 
.PARAMETER VMName
    The name of the VM(s) to start.
 
.EXAMPLE
     Start-MrVM -VMName DC01, SQL01, PC01
 
.EXAMPLE
     'DC01', 'SQL01', 'PC01' | Start-MrVM

.EXAMPLE
     Get-VM | Start-MrVM
 
.INPUTS
    String
 
.OUTPUTS
    None
 
.NOTES
    Author:  Mike F Robbins
    Website: http://mikefrobbins.com
    Twitter: @mikefrobbins
#&gt;

    [CmdletBinding()]
    param (        
        [Parameter(Mandatory,
                   ValueFromPipeline,
                   ValueFromPipelineByPropertyName)]
        [Alias('Name')]
        [string[]]$VMName
    )

    BEGIN {
        try {
            $VMs = Get-VM -ErrorAction Stop
            $VMSwitches = Get-VMSwitch -SwitchType Internal, Private -ErrorAction Stop |
                          Select-Object -ExpandProperty Name
        }
        catch {
            Write-Warning -Message "An error has occured.  Error details: $_.Exception.Message"
        }
    }

    PROCESS {
        foreach ($v in $VMName) {

            if ($VMs.Name -contains $v) {
                
                $VM = $VMs | Where-Object Name -eq $v

                if ($VM.State -ne 'Running') {

                    $VMSwitch = $VM | Get-VMNetworkAdapter | Select-Object -ExpandProperty SwitchName

                    if ($VMSwitches -contains $VMSwitch -or (-not($VMSwitch))) {
            
                        Write-Verbose -Message "Starting VM: $($VM.Name)"
                
                        try {
                            Start-VM -Name $VM.VMName -ErrorAction Stop
                        }
                        catch {
                            Write-Warning -Message "An error has occured.  Error details: $_.Exception.Message"
                        }
                
                    }
                    else {
                        Write-Warning -Message "VM: $($VM.Name) was not started because it's network is set to External!"
                    }                
                }
                else {
                    Write-Warning -Message "VM: $($VM.Name) is Already Running!"
                }
            }
            else {                
                Write-Warning -Message "VM: $v is invalid or was not found!"
            }
        }
    }
}</pre>
In the following example, an attempt is being made to start the VM's named DC01, SQL01, PC01, and PC02. The VM's are being specified via parameter input. DC01 is connected to an internal virtual switch, PC01 is set to "Not Connected" on its network adapter, PC02 is set to a private virtual switch, and SQL01 is set to an external virtual switch.
<pre class="lang:ps decode:true">. .\Start-MrVM.ps1
Start-MrVM -VMName DC01, SQL01, PC01, PC02</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/06/start-mrvm1.jpg"><img class="alignnone size-full wp-image-10082" src="http://mikefrobbins.com/wp-content/uploads/2014/06/start-mrvm1.jpg" alt="start-mrvm1" width="877" height="83" /></a>

Same network settings in the following example. This time DC01 is already running. The same computers are specified in this example except via pipeline input this time:
<pre class="lang:ps decode:true">'DC01', 'SQL01', 'PC01', 'PC02' | Start-MrVM</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/06/start-mrvm2.jpg"><img class="alignnone size-full wp-image-10083" src="http://mikefrobbins.com/wp-content/uploads/2014/06/start-mrvm2.jpg" alt="start-mrvm2" width="877" height="84" /></a>

And this time, the <em>Get-VM</em> cmdlet which is part of the Hyper-V PowerShell module is being used to specify the VM's and the output of that cmdlet is being piped to my function.
<pre class="lang:ps decode:true">Get-VM -Name DC01, SQL01, PC01, PC02 | Start-MrVM -Verbose</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/06/start-mrvm3.jpg"><img class="alignnone size-full wp-image-10084" src="http://mikefrobbins.com/wp-content/uploads/2014/06/start-mrvm3.jpg" alt="start-mrvm3" width="877" height="106" /></a>

Notice in the previous example, the verbose parameter was specified so you could see what VM's are attempting to be started.

You may be wondering how to get external network access so you can access the Internet on your VM's without connecting them directly to an external network? I use Internet connection sharing as referenced in <a href="http://blogs.msdn.com/b/virtual_pc_guy/archive/2008/01/09/using-hyper-v-with-a-wireless-network-adapter.aspx" target="_blank">this blog article on MSDN</a>. Even though that blog references a wireless adapter, the same procedure can be used for a wired adapter as well.

I would also like to thank <a href="http://twitter.com/MSH_Dave" target="_blank">Dave Wyatt</a> for <a href="http://powershell.org/wp/forums/topic/redundancy-of-code-in-advanced-function/" target="_blank">the feedback he provided on the PowerShell.org forums</a> for my <em>Start-MrVM</em> function.

µ