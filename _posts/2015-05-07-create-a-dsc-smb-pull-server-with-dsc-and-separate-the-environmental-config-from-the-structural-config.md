---
ID: 11648
post_title: >
  Create a DSC SMB Pull Server with DSC
  and separate the Environmental Config
  from the Structural Config
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2015/05/07/create-a-dsc-smb-pull-server-with-dsc-and-separate-the-environmental-config-from-the-structural-config/
published: true
post_date: 2015-05-07 07:30:40
---
On Saturday May 16th, I'll be presenting a session titled "<a href="http://www.sqlsaturday.com/392/Sessions/Details.aspx?sid=20709" target="_blank">PS C:\&gt; Get-Started -With 'PowerShell Desired State Configuration'</a>" at <a href="http://www.sqlsaturday.com/392/EventHome.aspx" target="_blank">SQL Saturday #392 in Atlanta</a>. One of the things I'll be demonstrating is a SMB based DSC Pull Server and I figured since it's a DSC presentation, why not create it with DSC, right?

The machines used in this blog article are running Windows 8.1 and Windows Server 2012 R2. Both have the default version of PowerShell installed that shipped with those operating systems which is PowerShell version 4.

The following configuration uses the <a href="https://github.com/PowerShell/xSmbShare" target="_blank">xSmbShare</a> and the <a href="https://github.com/rohnedwards/PowerShellAccessControl" target="_blank">PowerShellAccessControl</a> DSC resources which can be downloaded from <a href="https://github.com/" target="_blank">GitHub</a>. These resources must exist on the machine where you're authoring the DSC configuration and on the node that the configuration is being applied to. They should be placed in the following path:
<pre class="lang:ps decode:true ">"$env:ProgramFiles\WindowsPowerShell\Modules"</pre>
Keep in mind that any DSC Resources that do not reside in the following path have to be imported using <em>Import-DscResource</em> in your DSC configuration.
<pre class="lang:ps decode:true ">"$PSHOME\Modules"</pre>
Don't even think about placing your DSC resources in the <em>$PSHOME</em> path shown in the previous example. I'm my opinion, only Microsoft should be placing things in that path.

The configuration shown in the following example is very static and both the environmental config and the structural config are specified together:
<pre class="lang:ps decode:true ">configuration DSCSMB {

    Import-DscResource -Module xSmbShare, PowerShellAccessControl

    Node dc01 {

        File CreateFolder {

            DestinationPath = 'C:\DSCSMB'
            Type = 'Directory'
            Ensure = 'Present'

        }

        xSMBShare CreateShare {

            Name = 'DSCSMB'
            Path = 'C:\DSCSMB'
            FullAccess = 'mikefrobbins\domain admins'
            ReadAccess = 'mikefrobbins\domain computers'
            FolderEnumerationMode = 'AccessBased'
            Ensure = 'Present'
            DependsOn = '[File]CreateFolder'

        }

        cAccessControlEntry AssignPermissions {

            Path = 'C:\DSCSMB'
            ObjectType = 'Directory'
            AceType = 'AccessAllowed'
            Principal = 'mikefrobbins\domain computers'
            AccessMask = [System.Security.AccessControl.FileSystemRights]::ReadAndExecute
            DependsOn = '[File]CreateFolder'

        }

    }

}</pre>
The DSCSMB configuration shown in the previous code example has been loaded into memory and when it's called, a MOF file is created which will be used to configure DC01:
<pre class="lang:ps decode:true ">DSCSMB</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/05/dscsmb1a.jpg"><img class="alignnone size-full wp-image-11663" src="http://mikefrobbins.com/wp-content/uploads/2015/05/dscsmb1a.jpg" alt="dscsmb1a" width="877" height="181" /></a>

The MOF file is applied to DC01 using the DSC push configuration mode:
<pre class="lang:ps decode:true ">Start-DscConfiguration -Wait -Path .\DSCSMB -Verbose</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/05/dscsmb2a.jpg"><img class="alignnone size-full wp-image-11664" src="http://mikefrobbins.com/wp-content/uploads/2015/05/dscsmb2a.jpg" alt="dscsmb2a" width="877" height="480" /></a>

The following example only contains the structural config and the environmental config has been removed:
<pre class="lang:ps decode:true">configuration DSCSMB {

    Import-DscResource -Module xSmbShare, PowerShellAccessControl

    Node $AllNodes.NodeName {

        File CreateFolder {

            DestinationPath = $Node.Path
            Type = 'Directory'
            Ensure = 'Present'

        }

        xSMBShare CreateShare {

            Name = $Node.ShareName
            Path = $Node.Path
            FullAccess = $node.FullAccess
            ReadAccess = $node.ReadAccess
            FolderEnumerationMode = 'AccessBased'
            Ensure = 'Present'
            DependsOn = '[File]CreateFolder'

        }

        cAccessControlEntry AssignPermissions {

            Path = $Node.Path
            ObjectType = 'Directory'
            AceType = 'AccessAllowed'
            Principal = $Node.ReadAccess
            AccessMask = [System.Security.AccessControl.FileSystemRights]::ReadAndExecute
            DependsOn = '[File]CreateFolder'

        }

    }

}</pre>
The environmental config is now defined in a hash table:
<pre class="lang:ps decode:true ">$ConfigData = @{
    AllNodes = @(
        @{
            NodeName = 'DC01'
            ShareName = 'DSCSMB'
            Path = 'C:\DSCSMB'
            FullAccess = 'mikefrobbins\domain admins'
            ReadAccess = 'mikefrobbins\domain computers'
        }
    )
}</pre>
The variable that contains the hash table has to be specified via the <em>ConfigurationData</em> parameter when calling the configuration otherwise the MOF file won't be created:
<pre class="lang:ps decode:true">DSCSMB -ConfigurationData $ConfigData</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/05/dscsmb3a.jpg"><img class="alignnone size-full wp-image-11666" src="http://mikefrobbins.com/wp-content/uploads/2015/05/dscsmb3a.jpg" alt="dscsmb3a" width="877" height="179" /></a>

There are no changes as far as how the MOF file is applied to DC01:
<pre class="lang:ps decode:true ">DSCSMB -ConfigurationData $ConfigData</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/05/dscsmb4a.jpg"><img class="alignnone size-full wp-image-11667" src="http://mikefrobbins.com/wp-content/uploads/2015/05/dscsmb4a.jpg" alt="dscsmb4a" width="877" height="479" /></a>

Instead of storing the environmental data in a variable, you could also store it in a file:
<pre class="lang:ps decode:true ">@{
    AllNodes = @(
        @{
            NodeName = 'DC01'
            ShareName = 'DSCSMB'
            Path = 'C:\DSCSMB'
            FullAccess = 'mikefrobbins\domain admins'
            ReadAccess = 'mikefrobbins\domain computers'
        }
    )
}</pre>
It must be a file with a PSD1 extension otherwise the MOF file will fail to be created:
<pre class="lang:ps decode:true ">DSCSMB -ConfigurationData .\configdata-dscsmb.txt -Verbose</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/05/dscsmb5a.jpg"><img class="alignnone size-full wp-image-11668" src="http://mikefrobbins.com/wp-content/uploads/2015/05/dscsmb5a.jpg" alt="dscsmb5a" width="877" height="156" /></a>
<h5><span style="color: #ff0000;">DSCSMB : Cannot process argument transformation on parameter 'ConfigurationData'. Cannot resolve the path</span>
<span style="color: #ff0000;">'.\configdata-dscsmb.txt' to a single .psd1 file.</span>
<span style="color: #ff0000;">At line:1 char:27</span>
<span style="color: #ff0000;">+ DSCSMB -ConfigurationData .\configdata-dscsmb.txt -Verbose</span>
<span style="color: #ff0000;">+ ~~~~~~~~~~~~~~~~~~~~~~~</span>
<span style="color: #ff0000;"> + CategoryInfo : InvalidData: (:) [DSCSMB], ParameterBindingArgumentTransformationException</span>
<span style="color: #ff0000;"> + FullyQualifiedErrorId : ParameterArgumentTransformationError,DSCSMB</span></h5>
Saving the file with a PSD1 extension allows the MOF file to be created successfully:
<pre class="lang:ps decode:true ">DSCSMB -ConfigurationData .\configdata-dscsmb.psd1 -Verbose</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/05/dscsmb6a.jpg"><img class="alignnone size-full wp-image-11669" src="http://mikefrobbins.com/wp-content/uploads/2015/05/dscsmb6a.jpg" alt="dscsmb6a" width="877" height="181" /></a>

Once again, there are no changes as far as how the MOF file is applied to DC01:

<a href="http://mikefrobbins.com/wp-content/uploads/2015/05/dscsmb7a.jpg"><img class="alignnone size-full wp-image-11670" src="http://mikefrobbins.com/wp-content/uploads/2015/05/dscsmb7a.jpg" alt="dscsmb7a" width="877" height="480" /></a>

You can run commands to dynamically generate the configuration data hash table when it's stored in a variable, but if you store the configuration data in a PSD1 file, it has to contain static values otherwise an error will be generated when you try to create the MOF file. I'll elaborate on this topic more and provide some examples in a future blog article.

Did you notice that domain computers was assigned access to the SMB file share as well as NTFS permissions to the folder that was created? This is because the DSC LCM (Local Configuration Manager) runs as the system account by default and once the LCM on the target nodes is configured to pull their configuration from this SMB file share, they will have access, that is if they're members of the domain computers group. Did you know that not all computers that are a member of a domain are a member of the domain computers group? Domain controllers are not members of the domain computers group.

Additional information about separating the environment config from the structural config can be found in the Windows PowerShell Team blog titled <a href="http://blogs.msdn.com/b/powershell/archive/2014/01/09/continuous-deployment-using-dsc-with-minimal-change.aspx" target="_blank">Separating "What" from "Where" in PowerShell DSC</a>.

If you've enjoyed this blog article, you should definitely consider attending my presentation at <a href="http://www.sqlsaturday.com/392/EventHome.aspx">SQL Saturday Atlanta</a> that I referenced at the beginning of this blog article.

µ