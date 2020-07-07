---
ID: 11119
post_title: >
  Automate the installation of DSC
  Resource Kit Wave 9 resources with
  PowerShell Desired State Configuration
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2015/01/29/automate-the-installation-of-dsc-resource-kit-wave-9-resources-with-powershell-desired-state-configuration/
published: true
post_date: 2015-01-29 07:30:52
---
Last week, in my blog article titled "<a href="http://mikefrobbins.com/2015/01/22/creating-hyper-v-vms-with-desired-state-configuration/" target="_blank">Creating Hyper-V VM’s with Desired State Configuration</a>" I left off with a newly created Hyper-V VM named Test01 that was created with DSC and the specific IP address of that VM was added to my trusted host list. For more details on the current state of this test environment, see that previous blog article.

Today I'll begin configuring the Test01 VM with DSC. This virtual machine will become the first Active Directory domain controller in my test environment. I want to statically assign the IP address information and I need to rename the server before I turn it into a domain controller. In order to accomplish those tasks, a few DSC resources need to be downloaded and added to the VM.

I would like to fully automate the entire build of my test environment with DSC and that task may seem overwhelming to some. Breaking it down into smaller steps makes the overall task seem much more manageable.

In this blog article, I'll focus on automating the download of the DSC Resource Kit Wave 9 resources, extract all of those resources to a temporary folder, and copy only the ones I plan to use to the appropriate location. That way I'll have all of the resources downloaded in case I need to use any of them for the other test VM's that I plan to build.

I previously talked about using an initialization script in another blog article titled "<a href="http://mikefrobbins.com/2014/12/04/use-a-certificate-with-powershell-dsc-to-add-a-server-to-active-directory-without-hard-coding-a-password/" target="_blank">Use a certificate with PowerShell DSC to add a server to Active Directory without hard coding a password</a>" and I'll take advantage of that technique here as well:
<pre class="lang:ps decode:true">$ConfigData = @{
    AllNodes = @(
        @{
            NodeName = '192.168.29.108'
            DSCResources =  @(
                'xActiveDirectory', 'xComputerManagement', 'xNetworking'
            )
            Uri = 'https://gallery.technet.microsoft.com/scriptcenter/DSC-Resource-Kit-All-c449312d/file/131371/1/DSC%20Resource%20Kit%20Wave%209%2012172014.zip'
            Destination = 'C:\tmp\DSC'
            OutFile = 'C:\tmp\DSC\DSC Resource Kit Wave 9 12172014.zip'
        }
    )
}</pre>
One thing that was definitely a challenge was using variables with the DSC script resource as shown in the following example. I used PowerShell here-strings to make them work.
<pre class="lang:ps decode:true ">configuration AddResource {
    Node $AllNodes.NodeName {

        Script DownloadResource {
            GetScript = @"
                @{
                    Result = (Test-Path -Path '$($Node.OutFile)' -PathType Leaf)
                }
"@
            SetScript = @"
                If (-not(Test-Path -Path '$($Node.Destination)' -PathType Container)) {
                    New-Item -Path '$($Node.Destination)' -ItemType Directory
                }
                Invoke-WebRequest -Uri '$($Node.Uri)' -OutFile '$($Node.OutFile)'
                Unblock-File -Path '$($Node.OutFile)'
"@
            TestScript = @"
                Test-Path -Path '$($Node.OutFile)' -PathType Leaf
"@
        }

        Archive UnzipResource {
            Path = $Node.OutFile
            Destination = $Node.Destination
            Ensure = 'Present'
            DependsOn = '[Script]DownloadResource'
        }

        $Node.DSCResources.ForEach({
            File $_ {
                SourcePath = "$($Node.Destination)\All Resources\$_"
                DestinationPath = "$env:ProgramFiles\WindowsPowerShell\Modules\$_"
                Recurse = $true
                Type = 'Directory'
                Ensure = 'Present'
                DependsOn = '[Archive]UnzipResource'
            }
        })

    }
}</pre>
The configuration is called which creates the MOF file:
<pre class="lang:ps decode:true ">AddResource -ConfigurationData $ConfigData</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/01/dsc-resources2a.jpg"><img class="alignnone size-full wp-image-11122" src="http://mikefrobbins.com/wp-content/uploads/2015/01/dsc-resources2a.jpg" alt="dsc-resources2a" width="877" height="181" /></a>

Needless to say that there is a lot of "Verbose" output so I've omitted a large portion of it, but I think you'll get the idea of what's going on:
<pre class="lang:ps decode:true">Start-DscConfiguration -Wait -Path .\AddResource -Credential (Get-Credential) -Verbose</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/01/dsc-resources3a.jpg"><img class="alignnone size-full wp-image-11123" src="http://mikefrobbins.com/wp-content/uploads/2015/01/dsc-resources3a.jpg" alt="dsc-resources3a" width="876" height="915" /></a>

Applying the configuration a second time shows that everything is already done (note that all of the sets are skipped):
<pre class="lang:ps decode:true  ">Start-DscConfiguration -Wait -Path .\AddResource -Credential (Get-Credential) -Verbose</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/01/dsc-resources5a.jpg"><img class="alignnone size-full wp-image-11143" src="http://mikefrobbins.com/wp-content/uploads/2015/01/dsc-resources5a.jpg" alt="dsc-resources5a" width="877" height="619" /></a>

First, the DSC script resource is used to check to see if the DSC Resouce Kit Wave 9 zip file already exists in the "C:\tmp\DSC" folder which of course will return false if it doesn't, it will also return false if the directory it's looking for it in doesn't exist either. If the "C:\tmp\DSC" folder doesn't exist, it's created, then the DSC Resouce Kit Wave 9 zip file is downloaded from the TechNet script repository.

Next the DSC archive resource is used to extract the contents of the zip file to the temporary folder which ends up being "C:\tmp\DSC\All Resources" due to the zip file creating the "All Resources" folder when its contents are extracted.

Then the DSC file resource is used to copy the DSC resources that I plan to use to further configure the Test01 VM to the appropriate location ("C:\Program Files\WindowsPowerShell\Modules"). This portion of the code is written using the ForEach method to eliminate redundant code in the actual DSC configuration.

Last but not least, dependencies are set using "DependsOn" to make sure the execution occurs in the proper sequence and to prevent the configuration from continuing if a prerequisite step fails that is required by a later step in the configuration.

µ