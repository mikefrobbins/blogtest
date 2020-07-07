---
ID: 12193
post_title: 'Change a Machine&#8217;s Subnet Mask with PowerShell Version 2'
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2015/08/13/change-a-machines-subnet-mask-with-powershell-version-2/
published: true
post_date: 2015-08-13 07:30:42
---
I recently worked on a project that required me to change the subnet mask on multiple servers at a remote location. The main problem was that many of the servers were still running PowerShell version 2, but luckily PowerShell remoting was enabled on all of them.
<pre class="lang:ps decode:true ">Invoke-Command -ComputerName (Get-Content -Path C:\MyServers.txt) {
    $NICs = Get-WmiObject -Class Win32_NetworkAdapterConfiguration -Filter "IPEnabled = 'true' and DHCPEnabled = 'False'" |
            Where-Object {$_.ipaddress -like '192.168.0.*'}

    foreach ($NIC in $NICs){
        $ip = $NIC.IPAddress -match '^(?:[0-9]{1,3}\.){3}[0-9]{1,3}$'
        [array]::Reverse($ip)
        $subnet = $NIC.IPSubnet -match '^(?:[0-9]{1,3}\.){3}[0-9]{1,3}$' -replace '255.255.255.0','255.255.0.0'
	    [array]::Reverse($subnet)
        $NIC.EnableStatic($ip, $subnet)
    }

} -Credential (Get-Credential)</pre>
This script takes into account multiple network cards and multiple IP addresses being assigned to the same network card. I discovered that the array needed to be reversed in order to assign the primary IP address back to a network card if more than one IP address was assigned to the same NIC, otherwise a secondary IP would be assigned as the primary.

The previous script filters down to network cards that have a specific range of IP addresses which are the ones connected to the LAN because many of the servers also have additional network cards that are connected to an iSCSI network and those shouldn't be modified.

The following script was used to validate that the settings were indeed changed:
<pre class="lang:ps decode:true ">Invoke-Command -ComputerName (Get-Content -Path C:\MyServers.txt) {
    Get-WmiObject -Class Win32_NetworkAdapterConfiguration -Filter "IPEnabled = 'true' and DHCPEnabled = 'False'" |
    Where-Object {$_.ipaddress -like '192.168.0.*'} |
    Select-Object -Property IPAddress, IPSubnet
} -Credential (Get-Credential)</pre>
In my opinion, having to work with PowerShell version 2 is less than desirable to say the least, but even in the worst case scenario it's better than pointing and clicking in the GUI.

µ