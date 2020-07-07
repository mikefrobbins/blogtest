---
ID: 7729
post_title: 'Use PowerShell to Determine the Chain of VHD&#8217;s for a Virtual Machine on Hyper-V'
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2013/07/18/use-powershell-to-determine-the-chain-of-vhds-for-a-virtual-machine-on-hyper-v/
published: true
post_date: 2013-07-18 07:30:19
---
You want to determine what VHD's exist for the virtual machines (VM's) that are virtualized on your Windows 8 or Windows Server 2012 Hyper-V virtualization host.

This may sound simple, but what if you have a base image or template that has been configured and then multiple VM's have been created using differencing disks that reference that single base image? If that weren't difficult enough to keep track of, you may also have snapshots of those VM's which are also classified as differencing disks in HyperV.

How do you easily figure out what the chain is for all of the VHD's or VHDX files that are necessary for a virtual machine? I couldn't find a way to determine this easily so I decided to write a PowerShell function to figure this out.

I recently added a second hard drive (an SSD) to my laptop computer and I want to move some of the virtual hard drive files to this drive.
<pre class="lang:ps decode:true crayon-selected" title="Get-VHDChain">#Requires -Version 3.0
#Requires -Modules Hyper-V
function Get-VHDChain {

    [CmdletBinding()]
    param(
        [string]$ComputerName = $env:COMPUTERNAME,
        [string[]]$Name = '*'
    )

    try {
        $VMs = Get-VM -ComputerName $ComputerName -Name $Name -ErrorAction Stop
    }
    catch {
        Write-Warning $_.Exception.Message
    }

    foreach ($vm in $VMs){
        $VHDs = ($vm).harddrives.path

        foreach ($vhd in $VHDs){
            Clear-Variable VHDType -ErrorAction SilentlyContinue

            try {
                $VHDInfo = $vhd | Get-VHD -ComputerName $ComputerName -ErrorAction Stop
            }
            catch {
                $VHDType = 'Error'
                $VHDPath = $vhd
                Write-Verbose $_.Exception.Message
            }

            $i = 1
            $problem = $false

            while (($VHDInfo.parentpath -or $i -eq 1) -and (-not($problem))){
                If ($VHDType -ne 'Error' -and $i -gt 1){
                    try {
                        $VHDInfo = $VHDInfo.ParentPath | Get-VHD -ComputerName $ComputerName -ErrorAction Stop
                    }
                    catch {
                        $VHDType = 'Error'
                        $VHDPath = $VHDInfo.parentpath
                        Write-Verbose $_.Exception.Message
                    }
                }

                if ($VHDType -ne 'Error'){
                    $VHDType = $VHDInfo.VhdType
                    $VHDPath = $VHDInfo.path
                }
                else {
                    $problem = $true
                }

                [pscustomobject]@{
                    Name = $vm.name
                    VHDNumber = $i
                    VHDType = $VHDType
                    VHD = $VHDPath
                }

                $i++
            }
        }
    }
}</pre>
I've already moved a couple of the VHDX files as you can see in the results where the VHDType is "Error":

<a href="http://mikefrobbins.com/wp-content/uploads/2013/07/vhdchain-1.png"><img class="alignnone size-full wp-image-7732" alt="vhdchain-1" src="http://mikefrobbins.com/wp-content/uploads/2013/07/vhdchain-1.png" width="640" height="537" /></a>

Click on the previous image for a more readable view. Let me know what you think of this function and if you find any issues with it.

The function shown in this blog can be downloaded from the TechNet <a href="http://gallery.technet.microsoft.com/scriptcenter/Determine-the-Chain-of-776cd2d6" target="_blank">script repository</a>.

<span style="line-height: 1.5;">µ</span>