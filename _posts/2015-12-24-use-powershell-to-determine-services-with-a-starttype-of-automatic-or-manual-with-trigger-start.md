---
ID: 12892
post_title: >
  Use PowerShell to Determine Services
  with a StartType of Automatic or Manual
  with Trigger Start
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2015/12/24/use-powershell-to-determine-services-with-a-starttype-of-automatic-or-manual-with-trigger-start/
published: true
post_date: 2015-12-24 07:30:10
---
Newer Windows operating systems have numerous services that are set to start automatically or manually with a triggered start. The following image is from a machine that's running Windows 10 Enterprise Edition (version 1511):

<img class="alignnone size-full wp-image-12895" src="http://mikefrobbins.com/wp-content/uploads/2015/12/trigger-start-service1a.jpg" alt="trigger-start-service1a" width="638" height="233" />

The problem is when you're trying to determine whether or not all of the services that are set to start automatically are running or not. These trigger start services can't be filtered out using the <em>Get-Service</em> cmdlet or with WMI and they aren't necessarily suppose to be running. The only way to filter them out is to query the registry.

Trigger start services have a "TriggerInfo" key under their registry key with one or more triggers defined beginning with "0". The "DNS Client" service is one of these "Trigger Start" services on Windows 10:

<img class="alignnone size-full wp-image-12896" src="http://mikefrobbins.com/wp-content/uploads/2015/12/trigger-start-service2a.jpg" alt="trigger-start-service2a" width="731" height="225" />

The simplest way to check for the existence of a registry key is with the <em>Test-Path</em> cmdlet:
<pre class="lang:ps decode:true">Test-Path -Path 'HKLM:\SYSTEM\CurrentControlSet\Services\dnscache\TriggerInfo\'</pre>
<img class="alignnone size-full wp-image-12898" src="http://mikefrobbins.com/wp-content/uploads/2015/12/trigger-start-service3a.jpg" alt="trigger-start-service3a" width="859" height="94" />

I decided to create a reusable tool that returns a list of trigger start services. This function is written so that it's compatible with PowerShell version 2.0 and higher:
<pre class="lang:ps decode:true ">function Get-MrTriggerStartService {

    [CmdletBinding(DefaultParameterSetName='Name')]
    param (
        [Parameter(ParameterSetName='Name',
                   Position=0)]
        [ValidateNotNullOrEmpty()]
        [string]$Name = '*',

        [Parameter(ParameterSetName='DisplayName')]
        [ValidateNotNullOrEmpty()]
        [string]$DisplayName = '*'
    )

    $Services = Get-WmiObject -Class Win32_Service -Filter "StartMode != 'Disabled' and Name like '$($Name -replace '\*', '%')' and DisplayName like '$($DisplayName -replace '\*', '%')'"

    foreach ($Service in $Services) {
        
        if (Test-Path -Path "HKLM:\SYSTEM\CurrentControlSet\Services\$($Service.Name)\TriggerInfo\") {
        
            New-Object -TypeName PSObject -Property @{
                Status = $Service.State
                Name = $Service.Name
                DisplayName = $Service.DisplayName
                StartMode = "$($Service.StartMode) (Trigger Start)"
            }

        }
    
    }

}</pre>
Just running this function without specifying any parameters gives you a list of both the automatic and manual trigger start services:
<pre class="lang:ps decode:true">Get-MrTriggerStartService</pre>
<img class="alignnone size-full wp-image-12901" src="http://mikefrobbins.com/wp-content/uploads/2015/12/trigger-start-service4a.jpg" alt="trigger-start-service4a" width="859" height="333" />

The name of a specific service can be specified:
<pre class="lang:ps decode:true">Get-MrTriggerStartService -Name w32time</pre>
<img class="alignnone size-full wp-image-12903" src="http://mikefrobbins.com/wp-content/uploads/2015/12/trigger-start-service6a.jpg" alt="trigger-start-service6a" width="859" height="132" />

Wildcards are permitted as well as positional parameters:
<pre class="lang:ps decode:true">Get-MrTriggerStartService w*</pre>
<img class="alignnone size-full wp-image-12902" src="http://mikefrobbins.com/wp-content/uploads/2015/12/trigger-start-service5a.jpg" alt="trigger-start-service5a" width="859" height="252" />

The display name of a service can be specified instead of the service name by using the other parameter set:
<pre class="lang:ps decode:true">Get-MrTriggerStartService -DisplayName 'Dns Client'</pre>
<img class="alignnone size-full wp-image-12904" src="http://mikefrobbins.com/wp-content/uploads/2015/12/trigger-start-service7a.jpg" alt="trigger-start-service7a" width="859" height="130" />

The DisplayName parameter also accepts wildcards:
<pre class="lang:ps decode:true ">Get-MrTriggerStartService -DisplayName 'Device *'</pre>
<img class="alignnone size-full wp-image-12905" src="http://mikefrobbins.com/wp-content/uploads/2015/12/trigger-start-service8a.jpg" alt="trigger-start-service8a" width="859" height="157" />

Hopefully Microsoft will consider adding this sort of functionality to either the <em>Get-Service</em> cmdlet or WMI as they have with delayed start services in the RTM version of Windows 10. You can find my blog on that topic <a href="http://mikefrobbins.com/2015/09/04/powershell-delayedautostart-property-added-to-the-win32_service-wmi-class-in-windows-10-rtm/" target="_blank">here</a>.

µ