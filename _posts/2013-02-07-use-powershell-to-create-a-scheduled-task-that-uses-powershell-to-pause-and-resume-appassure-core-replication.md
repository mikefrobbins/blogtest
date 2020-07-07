---
ID: 6646
post_title: >
  Use PowerShell to Create a Scheduled
  Task that Uses PowerShell to Pause and
  Resume AppAssure Core Replication
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2013/02/07/use-powershell-to-create-a-scheduled-task-that-uses-powershell-to-pause-and-resume-appassure-core-replication/
published: true
post_date: 2013-02-07 07:30:50
---
In this scenario there are multiple AppAssure Core servers, one at each physical location that is used to backup the local servers. Each AppAssure Core server is setup to replicate its data to an offsite AppAssure Core server at a separate physical location across a wide area network for disaster recovery purposes. Due to bandwidth constraints, you want to pause the replication between the AppAssure Core servers during normal business hours.

All of the AppAssure Core servers in this scenario are running Windows Server 2008 R2, they all have PowerShell version 3 installed, PowerShell remoting enabled, and AppAssure 5.3.1 or higher installed.

First, we'll create a PSSession to the remote AppAssure Core server that we want to create the scheduled tasks on so we don't have the overhead of setting up and tearing down multiple remote PowerShell sessions to the same server:
<pre class="lang:ps decode:true">$session = New-PSSession -ComputerName AppAssureCoreServerName -Credential (Get-Credential)</pre>
I've specified the <em>-Credential</em> parameter and used the <em>Get-Credential</em> cmdlet so this script will prompt me for credentials since I'm running PowerShell as a user that doesn't have access to the remote AppAssure Core server. These are the credentials that our scripts will run as in this scenario:

<img class="alignnone size-full wp-image-6648" alt="appassure123-1" src="http://mikefrobbins.com/wp-content/uploads/2013/02/appassure123-1.png" width="640" height="247" />

This script will create a scheduled task that will run at 7:30am seven days a week. The scheduled task runs a PowerShell command which pauses the AppAssure replication from the server it is run on to the one that is specified via the <em>-Outgoing</em> parameter of the <em>Suspend-Replication</em> cmdlet. The credentials it is prompting us for are the credentials the scheduled task will run as (they will be saved as part of the scheduled task):

<img class="alignnone size-full wp-image-6649" alt="appassure123-2" src="http://mikefrobbins.com/wp-content/uploads/2013/02/appassure123-2.png" width="640" height="266" />
<pre class="lang:ps decode:true">Invoke-Command -Session $session {
Register-ScheduledJob -Name 'AppAssure - Pause Replication' -Credential (Get-Credential) -ScriptBlock {
Suspend-Replication -Outgoing AppAssureCoreServerNameReplicatedTo
} -Trigger (New-JobTrigger -Daily -At "7:30 AM") -ScheduledJobOption (New-ScheduledJobOption -RunElevated)}</pre>
<img class="alignnone size-full wp-image-6650" alt="appassure123-3" src="http://mikefrobbins.com/wp-content/uploads/2013/02/appassure123-3.png" width="640" height="169" />

Similar to the above script, the following PowerShell script creates a scheduled task on the AppAssure Core server that we created the PSSession to in the first command. The scheduled task runs at 6:30pm seven days a week. This scheduled task runs a PowerShell cmdlet (<em>Resume-Replication</em>) which resumes the AppAssure replication to the server that is specified in the <em>-Outgoing</em> parameter.

<img class="alignnone size-full wp-image-6651" alt="appassure123-4" src="http://mikefrobbins.com/wp-content/uploads/2013/02/appassure123-4.png" width="640" height="266" />
<pre class="lang:ps decode:true">Invoke-Command -Session $session {
Register-ScheduledJob -Name 'AppAssure - Resume Replication' -Credential (Get-Credential) -ScriptBlock {
Resume-Replication -Outgoing AppAssureCoreServerNameReplicatedTo
} -Trigger (New-JobTrigger -Daily -At "6:30 PM") -ScheduledJobOption (New-ScheduledJobOption -RunElevated)}</pre>
<img class="alignnone size-full wp-image-6652" alt="appassure123-5" src="http://mikefrobbins.com/wp-content/uploads/2013/02/appassure123-5.png" width="640" height="171" />

When you're finished, don't forget about removing the PSSession. The following command will remove all PSSessions:

<img class="alignnone size-full wp-image-6653" alt="appassure123-6" src="http://mikefrobbins.com/wp-content/uploads/2013/02/appassure123-6.png" width="404" height="43" />

You can see the scheduled tasks on the AppAssure Core server that the scripts shown in the previous examples were run against:

<img class="alignnone size-full wp-image-6654" alt="appassure123-7" src="http://mikefrobbins.com/wp-content/uploads/2013/02/appassure123-7.png" width="640" height="343" />

By having the scheduled task execute the PowerShell cmdlet directly, the dependency of an external script that could get accidentally moved or modified is eliminated.

µ