---
ID: 11078
post_title: 'Creating Hyper-V VM&#8217;s with Desired State Configuration'
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2015/01/22/creating-hyper-v-vms-with-desired-state-configuration/
published: true
post_date: 2015-01-22 07:30:10
---
I'm looking to automate the build of my test environment that runs as Hyper-V virtual machines on my Windows 8.1 Laptop computer. To get started, I thought I would take a look at the xHyper-V DSC resource to create the actual VM's. There's also no reason this shouldn't work on a Windows Server that's running the Hyper-V role.

The Hyper-V role has already been added to my Windows 8.1 computer. I also have a previously created virtual hard drive (vhdx) file that has been loaded with the Windows Server 2012 R2 operating system and all of the available Windows updates. That OS has been syspreped, an unattend.xml file has been added to it to set the local administrator password when a new VM is created using a differencing disk based on this parent vhdx, and the vhdx has been marked as read only.

I've downloaded the <a href="https://gallery.technet.microsoft.com/scriptcenter/DSC-Resource-Kit-All-c449312d" target="_blank">DSC Resource Kit Wave 9</a> which contains the most recent versions of all of the Microsoft created DSC resources to include the xHyper-V DSC resource. What's the "x" stand for? Experimental.

The xHyper-V resource has been extracted from the downloaded DSC Resource Kit Wave 9 zip file and added to the "C:\Program Files\WindowsPowerShell\Modules" folder on my Windows 8.1 computer that I want to create the VM's on.

I've created a configuration as shown in the following example:
<pre class="lang:ps decode:true">Configuration MrHyperV_VM { 
 
    param ( 
        [Parameter(Mandatory)] 
        [string]$VMName,
        
        [Parameter(Mandatory)] 
        [string]$baseVhdPath,

        [Parameter(Mandatory)] 
        [string]$ParentPath,

        [Parameter(Mandatory)] 
        [string]$VMSwitchName
    ) 
 
    Import-DscResource -module xHyper-V
 
    xVMSwitch switch { 
        Name = $VMSwitchName
        Ensure = 'Present'         
        Type = 'Internal' 
    }
 
    xVHD DiffVHD { 
        Ensure = 'Present' 
        Name = $VMName 
        Path = $baseVhdPath
        ParentPath = $ParentPath
        Generation = 'vhdx' 
    } 
 
    xVMHyperV CreateVM { 
        Name = $VMName 
        SwitchName = $VMSwitchName
        VhdPath = Join-Path -Path $baseVhdPath -ChildPath "$VMName.vhdx" 
        ProcessorCount = 1
        MaximumMemory = 2GB
        MinimumMemory = 512MB
        RestartIfNeeded = 'True' 
        DependsOn = '[xVHD]DiffVHD', '[xVMSwitch]switch' 
        State = 'Running'
        Generation = 'vhdx'
    } 
}</pre>
I've run this configuration so it's loaded in memory in my current PowerShell session:
<pre class="lang:ps decode:true">Get-Command -CommandType Configuration</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/01/dsc-xhyperv1b.jpg"><img class="alignnone size-full wp-image-11094" src="http://mikefrobbins.com/wp-content/uploads/2015/01/dsc-xhyperv1b.jpg" alt="dsc-xhyperv1b" width="877" height="129" /></a>

I'll now call the configuration along with the necessary parameters and values which creates the MOF file:
<pre class="lang:ps decode:true">MrHyperV_VM -VMName Test01 -baseVhdPath 'D:\Hyper-V\Virtual Hard Disks' -ParentPath 'D:\Hyper-V\Virtual Hard Disks\Template\Server2012R2Core-Template_011915.vhdx' -VMSwitchName 'Internal Virtual Switch'</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/01/dsc-xhyperv2b.jpg"><img class="alignnone size-full wp-image-11095" src="http://mikefrobbins.com/wp-content/uploads/2015/01/dsc-xhyperv2b.jpg" alt="dsc-xhyperv2b" width="877" height="189" /></a>

The MOF file is applied to my local computer using the <em>Start-DscConfiguration</em> cmdlet:
<pre class="lang:ps decode:true">Start-DscConfiguration -Wait -Path .\MrHyperV_VM -Verbose</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/01/dsc-xhyperv3b.jpg"><img class="alignnone size-full wp-image-11096" src="http://mikefrobbins.com/wp-content/uploads/2015/01/dsc-xhyperv3b.jpg" alt="dsc-xhyperv3b" width="877" height="515" /></a>

A new virtual machine named Test01 has been created as specified in the configuration:
<pre class="lang:ps decode:true">Get-VM -Name Test01</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/01/dsc-xhyperv4b.jpg"><img class="alignnone size-full wp-image-11097" src="http://mikefrobbins.com/wp-content/uploads/2015/01/dsc-xhyperv4b.jpg" alt="dsc-xhyperv4b" width="877" height="132" /></a>

Piping a few cmdlets together, allows you to see the virtual hard drive information for the newly created VM to include its location, type (differencing), and its parent:
<pre class="lang:ps decode:true">Get-VM -Name Test01 | Get-VMHardDiskDrive | Get-VHD</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/01/dsc-xhyperv5b.jpg"><img class="alignnone size-full wp-image-11098" src="http://mikefrobbins.com/wp-content/uploads/2015/01/dsc-xhyperv5b.jpg" alt="dsc-xhyperv5b" width="877" height="347" /></a>

As shown here, the IP address of the VM can easily be obtained from the Hyper-V host:
<pre class="lang:ps decode:true ">Get-VM -Name Test01 | Get-VMNetworkAdapter</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/01/dsc-xhyperv6b.jpg"><img class="alignnone size-full wp-image-11099" src="http://mikefrobbins.com/wp-content/uploads/2015/01/dsc-xhyperv6b.jpg" alt="dsc-xhyperv6b" width="877" height="131" /></a>

Now to add the IPv4 address to the trusted host list on my pc so I can run commands on the VM via PowerShell remoting. This is necessary because of Kerberos mutual authentication not being available due to the new VM being in a workgroup by default. For security reasons, the IP should be removed from the trusted host list once the new VM is added to the domain. You should NEVER add "*" to the trusted host list &lt;period&gt;.
<pre class="lang:ps decode:true ">Set-Item -Path WSMan:\localhost\Client\TrustedHosts -Value "$((Get-VM -Name Test01 | Get-VMNetworkAdapter).ipaddresses -match '\d{1,3}(\.\d{1,3}){3}')" -Concatenate -Force</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/01/dsc-xhyperv7b.jpg"><img class="alignnone size-full wp-image-11100" src="http://mikefrobbins.com/wp-content/uploads/2015/01/dsc-xhyperv7b.jpg" alt="dsc-xhyperv7b" width="877" height="72" /></a>

You can see that it has indeed been added:
<pre class="lang:ps decode:true">Get-Item -Path WSMan:\localhost\Client\TrustedHosts</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/01/dsc-xhyperv8b.jpg"><img class="alignnone size-full wp-image-11101" src="http://mikefrobbins.com/wp-content/uploads/2015/01/dsc-xhyperv8b.jpg" alt="dsc-xhyperv8b" width="877" height="167" /></a>

Now to test running a command on the VM from my pc with PowerShell remoting:
<pre class="lang:ps decode:true ">Invoke-Command -ComputerName 192.168.29.108 {
  $PSVersionTable.PSVersion
} -Credential (Get-Credential)</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/01/dsc-xhyperv9b.jpg"><img class="alignnone size-full wp-image-11102" src="http://mikefrobbins.com/wp-content/uploads/2015/01/dsc-xhyperv9b.jpg" alt="dsc-xhyperv9b" width="877" height="219" /></a>

One thing to note is that PowerShell remoting is enabled by default on Windows Server 2012 and higher which is why no changes had to be made on the VM to accomplish this.

At this point, I should be able to create a DSC configuration to add the VM to the domain and make all of the configuration changes I want to it. I wrote about that process in a previous blog article on "<a href="http://mikefrobbins.com/2014/12/04/use-a-certificate-with-powershell-dsc-to-add-a-server-to-active-directory-without-hard-coding-a-password/" target="_blank">Use a certificate with PowerShell DSC to add a server to Active Directory without hard coding a password</a>".

If Desired State Configuration is something that you're interested in, you should definitely take a look at the <a href="http://mspsug.com/2015/01/20/video-from-the-january-2015-mspsug-meeting-dsc-in-practice/" target="_blank">video from the January Mississippi PowerShell User Group meeting</a>.

µ