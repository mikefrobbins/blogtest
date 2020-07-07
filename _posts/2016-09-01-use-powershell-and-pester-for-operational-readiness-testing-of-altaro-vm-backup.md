---
ID: 14343
post_title: >
  Use PowerShell and Pester for
  Operational Readiness Testing of Altaro
  VM Backup
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2016/09/01/use-powershell-and-pester-for-operational-readiness-testing-of-altaro-vm-backup/
published: true
post_date: 2016-09-01 07:30:37
---
I've recently been working with <a href="http://www.altaro.com/vm-backup/" target="_blank">Altaro VM Backup</a> and I must say that I've been very impressed with the ease and simplicity of the product. The back-end portion of the product can run on a virtual or physical server with or without the GUI (Server Core is supported). It can backup to just about any type of drive (local disk, UNC path, USB drive, etc). It doesn't require SQL Server. In my environment, adding a Hyper-V server (running Windows Server 2012 R2) installed a service on the Hypervisor, but did not require a reboot. There's no agent to install on the VM's themselves. Recovering an entire VM or specific files within the VM is simple enough. There's a mode where automated scheduled restores can be performed to validate the end-to-end backup and restore process. Even their licensing model is straightforward. This is how backups and restores are suppose to work &lt;period&gt;.

One very important thing to keep in mind is that Altaro VM Backup is designed to backup VM's which means that all of the data that you intend on backing up needs to reside in a virtual file (VHD or VHDX for Hyper-V). If some of your VM's have pass-through drives or you've passed through iSCSI network cards and made direct connections to iSCSI targets from VM's, those drives won't been seen or backed up by Altaro VM Backup.

There are a number of reasons why a backup taken with <a href="http://www.altaro.com/vm-backup/" target="_blank">Altaro VM Backup</a> may be crash consistent instead of application consistent so I decided to write an operational readiness test using PowerShell and Pester as shown in the following code example to validate all the items listed in one of their support articles.
<pre class="lang:ps decode:true " title="Test-MrVMBackupRequirement">#Requires -Version 3.0 -Modules Pester
function Test-MrVMBackupRequirement {

&lt;#
.SYNOPSIS
    Tests the requirements for live backups of a Hyper-V Guest VM for use with Altaro VM Backup.
 
.DESCRIPTION
    Test the requirements for live backups of a Hyper-V Guest VM as defined in this Altaro support article:
    http://support.altaro.com/customer/portal/articles/808575-what-are-the-requirements-for-live-backups-of-a-hyper-v-guest-vm-.
 
.PARAMETER ComputerName
    Name of the Hyper-V host virtualization server that the specified VM's are running on.

.PARAMETER VMHost
    Name of the VM (Guest) server to test the requirements for.

.PARAMETER Credential
    Specifies a user account that has permission to perform this action. The default is the current user.
 
.EXAMPLE
     Test-MrVMBackupRequirement -ComputerName HyperVServer01 -VMName VM01, VCM02 -Credential (Get-Credential)

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
        [Parameter(Mandatory)]
        [Alias('VMHost')]
        [string]$ComputerName,

        [Parameter(ValueFromPipeline)]
        [string[]]$VMName,

        [System.Management.Automation.Credential()]$Credential = [System.Management.Automation.PSCredential]::Empty
    )

    BEGIN {
        try {
            $HostSession = New-PSSession -ComputerName $ComputerName -Credential $Credential -ErrorAction Stop
        }
        catch {
            Throw "Unable to connect to Hyper-V host '$ComputerName'. Aborting Pester tests."
        }

        $VMs = (Invoke-Command -Session $HostSession {
            Get-VM | Select-Object -Property Name
        }).Name
        
        if (-not($PSBoundParameters.VMName)) {
            $VMName = $VMs
        }

    }
    
    PROCESS {
        foreach ($VM in $VMName) {
            Describe "Validation of Altaro VM Backup Requirements for Live Backups of Hyper-V Guest VM: '$VM'" {

                if ($VM -notin $VMs) {
                    Write-Warning -Message "The VM: '$VM' does not exist on the Hyper-V host: '$ComputerName'"
                    Continue
                }

                try {
                    $GuestSession = New-PSSession -ComputerName $VM -Credential $Credential -ErrorAction Stop
                }
                catch {
                    Write-Warning -Message "Unable to connect. Aborting Pester tests for computer: '$VM'."
                    Continue
                }
        
                $SupportedGuestOS = '2008 R2', 'Server 2012', 'Server 2012 R2'

                It "Should be running one of the supported guest OS's ($($SupportedGuestOS -join ', '))" {
                    $OS = (Invoke-Command -Session $GuestSession {
                        Get-WmiObject -Class Win32_OperatingSystem -Property Caption
                    }).caption

                    ($SupportedGuestOS | ForEach-Object {$OS -like "*$_*"}) -contains $true |
                    Should Be $true
                }
                
                $VMInfo = Invoke-Command -Session $HostSession {
                    Get-VM -Name $Using:VM | Select-Object -Property IntegrationServicesState, State
                }

                It 'Should have the latest Integration Services version installed' {
                    $VMInfo.IntegrationServicesState -eq 'Up to date' |
                    Should Be $true
                }

                It 'Should have Backup (volume snapshot) enabled in the Hyper-V settings' {
                    (Invoke-Command -Session $HostSession {
                        Get-VM -Name $Using:VM | Get-VMIntegrationService -Name VSS | Select-Object -Property Enabled
                    }).enabled |
                    Should Be $true
                }

                It 'Should be running' {
                    $VMInfo.State.Value |
                    Should Be 'Running'
                }

                $GuestDiskInfo = Invoke-Command -Session $GuestSession {
                    Get-WMIObject -Class Win32_Volume -Filter 'DriveType = 3' -Property Capacity, FileSystem, FreeSpace, Label
                }

                It 'Should have at least 10% free disk space on all disks' {
                    $GuestDiskInfo | ForEach-Object {$_.FreeSpace / $_.Capacity * 100} |
                    Should BeGreaterThan 10
                }
        
                $GuestServiceInfo = Invoke-Command -Session $GuestSession {
                    Get-Service -DisplayName 'Hyper-V Volume Shadow Copy Requestor', 'Volume Shadow Copy', 'COM+ Event System',
                                             'Distributed Transaction Coordinator', 'Remote Procedure Call (RPC)', 'System Event Notification Service'
                }

                It 'Should be running the "Hyper-V Volume Shadow Copy Requestor" service on the guest' {
                    ($GuestServiceInfo |
                     Where-Object DisplayName -eq 'Hyper-V Volume Shadow Copy Requestor'
                    ).status |
                    Should Be 'Running'
                }
        
                It 'Should have snapshot file location for VM set to same location as VM VHD file' {
                    #Hyper-V on Windows Server 2008 R2 and higher: The .AVHD file is always created in the same location as its parent virtual hard disk.
                    $HostOS = (Invoke-Command -Session $HostSession {
                        Get-WmiObject -Class Win32_OperatingSystem -Property Version
                    }).version
            
                    [Version]$HostOS -gt [Version]'6.1.7600' |
                    Should Be $true
                }

                It 'Should be running VSS in the guest OS' {
                    ($GuestServiceInfo |
                     Where-Object Name -eq VSS
                    ).status |
                    Should Be 'Running'
                }
        
                It 'Should have a SCSI controller attached in the VM settings' {
                    Invoke-Command -Session $HostSession {
                        Get-VM -Name $Using:VM | Get-VMScsiController
                    } |
                    Should Be $true
                }
        
                It 'Should not have an explicit shadow storage assignment of a volume other than itself' {
                    Invoke-Command -Session $GuestSession {
                        $Results = vssadmin.exe list shadowstorage | Select-String -SimpleMatch 'For Volume', 'Shadow Copy Storage volume'
                        if ($Results) {
                            for ($i = 0; $i -lt $Results.Count; $i+=2){
                                ($Results[$i] -split 'volume:')[1].trim() -eq ($Results[$i+1] -split 'volume:')[1].trim() 
                            }                                                   
                        }
                        else {
                            $true
                        }                 
                    } |
                    Should Be $true                    
                }

                It 'Should not have any App-V drives installed on the VM' {
                    #App-V drives installed on the VM creates a non-NTFS volume.
                    $GuestDiskInfo.filesystem |
                    Should Be 'NTFS'
                }

                It 'Should have at least 45MB of free space on system reserved partition if one exists in the guest OS' {
                    ($GuestDiskInfo |
                    Where-Object Label -eq 'System Reserved').freespace / 1MB |
                    Should BeGreaterThan 45 
                }
        
                It 'Should have all volumes formated with NTFS in the guest OS' {
                    $GuestDiskInfo.filesystem |
                    Should Be 'NTFS'
                }        

                It 'Should have volume containing VHD files formated with NTFS' {
                    $HostDiskLetter = (Invoke-Command -Session $HostSession {
                        Get-VM -Name $Using:VM | Get-VMHardDiskDrive
                    }).path -replace '\\.*$'

                    $HostDiskInfo = Invoke-Command -Session $HostSession {
                        Get-WMIObject -Class Win32_Volume -Filter 'DriveType = 3' -Property DriveLetter, FileSystem            
                    }

                    ($HostDiskLetter | ForEach-Object {$HostDiskInfo | Where-Object DriveLetter -eq $_}).filesystem |
                    Should Be 'NTFS'
                }

                It 'Should only contain basic and not dynamic disks in the guest OS' {
                    Invoke-Command -Session $GuestSession {
                        $DynamicDisk = 'Logical Disk Manager', 'GPT: Logical Disk Manager Data' 
                        Get-WmiObject -Class Win32_DiskPartition -Property Type |
                        ForEach-Object {$DynamicDisk -contains $_.Type}
                    } |
                    Should Be $false
                }

                It 'Should be running specific services within the VM' {
                    $RunningServices = 'COM+ Event System', 'Distributed Transaction Coordinator', 'Remote Procedure Call (RPC)', 'System Event Notification Service'
                    ($GuestServiceInfo | Where-Object DisplayName -in $RunningServices).status |
                    Should Be 'Running'
                }

                It 'Should have specific services set to manual or automatic within the VM' {
                    $StartMode = (Invoke-Command -Session $GuestSession {
                            Get-WmiObject -Class Win32_Service -Filter "DisplayName = 'COM+ System Application' or DisplayName = 'Microsoft Software Shadow Copy Provider' or DisplayName = 'Volume Shadow Copy'"
                    }).StartMode
                    
                    $StartMode | ForEach-Object {$_ -eq 'Manual' -or $_ -eq 'Automatic'} |
                    Should Be $true
                    
                }

                Remove-PSSession -Session $GuestSession
    
            }
        
        }
            
    }

    END {
        Remove-PSSession -Session $HostSession
    }

}</pre>
To run the operational validation test, point the ComputerName parameter to a Hyper-V host virtualization server and it will automatically determine all of the VM's on the host and run the test on each of the VM's. You can specify specific VM's to test via the VMName parameter. Use the credential parameter to specify a userid and password with admin privileges on the Hyper-V host and VM's if you're running PowerShell as a user who doesn't have sufficient access.

You could use the PowerShell console or ISE (Integrated Scripting Environment) to run the test from, but the syntax highlighting and progress indicator of Visual Studio Code look amazing:

<a href="http://mikefrobbins.com/wp-content/uploads/2016/08/vmbackup-pester1a.png"><img class="alignnone size-full wp-image-14350" src="http://mikefrobbins.com/wp-content/uploads/2016/08/vmbackup-pester1a.png" alt="vmbackup-pester1a" width="859" height="640" /></a>

See <a href="http://mikefrobbins.com/tag/visual-studio-code/" target="_blank">my previous articles on how to setup Visual Studio Code for use with PowerShell</a> if that's something you're interested in.

The <em>Test-MrVMBackupRequirement</em> function shown in this blog article can be downloaded from <a href="https://github.com/mikefrobbins/PowerShell" target="_blank">my PowerShell repository on GitHub</a>. Found a bug or a better way of accomplishing a task shown in the example code? Feel free to contribute by forking the repository and submitting a pull request.

µ