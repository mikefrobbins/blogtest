---
ID: 481
post_title: >
  Migrate Active Directory from 2003 R2 to
  2008 R2 Server Core
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2010/02/03/migrate-active-directory-from-2003-r2-to-2008-r2-server-core/
published: true
post_date: 2010-02-03 16:54:28
---
This blog will step you through the process of migrating your Active Directory domain controllers from Microsoft Windows Server 2003 R2 to Windows Server 2008 R2 Server Core. Server Core is an excellent choice for dedicated domain controllers since it requires less maintenance, has a reduced attack surface, requires less management, and will run on less hardware. Lots of people are scared off by Server Core because there's no GUI. To be honest with you, it's a blessing in disguise since you shouldn't be managing your production Active Directory environment directly on your domain controllers anyway. You can remotely manage AD, DNS, DHCP, etc from your Windows 7 pc with a GUI interface by using the "<a href="http://www.microsoft.com/downloads/details.aspx?FamilyID=7d2f6ad7-656b-4313-a005-4e344e43997d&amp;displaylang=en" target="_blank">Remote Server Administration Tools for Windows 7</a>". I'm sure the tools probably exist for Windows Vista also. See my blog on "<a href="http://mikefrobbins.com/2010/02/01/how-to-create-an-administrative-shortcut/" target="_blank">How to create an Administrative shortcut</a>" which will make your life a lot easier since the best practice is to log into your pc as a normal user (not as a user with elevated domain privileges).

Prerequisites:
(1) All of your existing active directory domain controllers need to be running Windows 2000 Service Pack 4 or higher.
(2) Your forest must be in at least Windows 2000 native mode.
(3) Verify your Antivirus and Backup agents will run on Windows Server 2008 R2 Server Core.

Copy the contents of the supportadprep folder from the Windows Server 2008 R2 DVD to a location that is accessible by the schema master for your forest and the Infrastructure Master for each of your domains that you plan to update. The Schema Master is a forest level FSMO role (one per forest). <a href="http://technet.microsoft.com/en-us/library/cc755631(WS.10).aspx" target="_blank">How to identify the schema master article on TechNet</a>. The Infrastructure Master is a domain level FSMO role (one per domain). <a href="http://technet.microsoft.com/en-us/library/cc757276(WS.10).aspx" target="_blank">How to identify the Infrastructure Master article on TechNet</a>.

Log into the schema master as a user who is a member of the Enterprise Admins group and Schema Admins group. Open a command prompt and navigate to the folder where you copied the adprep utility. Run adprep32 /forestprep if the schema master is using a 32 bit version of Windows Server 2003 R2. Run adprep /forestprep if it is a 64 bit version. When the forestprep completes, you will receive the following message:
<a href="http://mikefrobbins.com/wp-content/uploads/2010/02/forestprep.jpg"><img class="alignnone size-full wp-image-482" title="forestprep" alt="" src="http://mikefrobbins.com/wp-content/uploads/2010/02/forestprep.jpg" width="600" height="413" /></a>

If all of your domain controllers are running Windows Server 2003 and your forest level is at the Windows 2003 level, then I recommend going ahead and preparing the forest for Read Only Domain Controllers by running adprep32 /rodcprep . When rodcprep completes you will receive the following:
<a href="http://mikefrobbins.com/wp-content/uploads/2010/02/rodcprep.jpg"><img class="alignnone size-full wp-image-483" title="rodcprep" alt="" src="http://mikefrobbins.com/wp-content/uploads/2010/02/rodcprep.jpg" width="600" height="316" /></a>

Allow for the changes from the forestprep and rodcprep commands to propagate out to all of the domain controllers in your forest. You can use RepAdmin.exe to verify that the replication is complete. Run:
repadmin /replsum /bysrc /bydest /sort:delta and then repadmin /showrepl

Once the replication of the forest schema updates have completed to all domain controllers in your forest, login to the infrastructure master of each domain in the forest that will be updated to the Windows 2008 R2 level as a member of the domain admins group. Open a command prompt and navigate to the folder where you copied the adprep utility. Run:
adprep32 /domainprep /gpprep

<a href="http://mikefrobbins.com/wp-content/uploads/2010/02/domainprep.jpg"><img class="alignnone size-full wp-image-484" title="domainprep" alt="" src="http://mikefrobbins.com/wp-content/uploads/2010/02/domainprep.jpg" width="569" height="160" /></a>

If your domain is not at least in Windows 2000 native mode, you will receive the following error message:
<a href="http://mikefrobbins.com/wp-content/uploads/2010/02/nativemode_error.jpg"><img class="alignnone size-full wp-image-485" title="nativemode_error" alt="" src="http://mikefrobbins.com/wp-content/uploads/2010/02/nativemode_error.jpg" width="524" height="120" /></a>

Your forest and domains have now been updated so that you can introduce Windows Server 2008 R2 domain controllers. Since this blog focuses on migrating your active directory environment from Windows Server 2003 R2 to Windows Server 2008 R2 Core, you need to start with a fresh installation of Windows Server 2008 R2 on a new server. One thing to remember is that the R2 version of Windows Server 2008 is 64 bit only so you’ll need hardware capable of running a 64 bit operating system. Any server purchased in the last three years should be fine for the core edition since it has reduced hardware requirements, and in this example, I’m virtualizing the new domain controller using Hyper-V Server 2008 R2.

During the installation of the operating system, select one of the Server Core versions:
<a href="http://mikefrobbins.com/wp-content/uploads/2010/02/install2008core.jpg"><img class="alignnone size-full wp-image-486" title="install2008core" alt="" src="http://mikefrobbins.com/wp-content/uploads/2010/02/install2008core.jpg" width="600" height="450" /></a>

Once the installation of the operating system is complete, you are asked to change the password since the initial one is blank. Once this is complete and you log into the server, you notice the huge difference between the normal installation and core which only has a command prompt:
<a href="http://mikefrobbins.com/wp-content/uploads/2010/02/core_cmd.jpg"><img class="alignnone size-full wp-image-487" title="core_cmd" alt="" src="http://mikefrobbins.com/wp-content/uploads/2010/02/core_cmd.jpg" width="600" height="449" /></a>

Run sconfig.cmd (which is only available on R2) from this command prompt to start the Server Configuration:
<a href="http://mikefrobbins.com/wp-content/uploads/2010/02/sconfig.jpg"><img class="alignnone size-full wp-image-488" title="sconfig" alt="" src="http://mikefrobbins.com/wp-content/uploads/2010/02/sconfig.jpg" width="600" height="447" /></a>

Set the computer name, configure the network settings, install windows updates, and add it to the domain. This process is much easier in the R2 version with sconfig instead of having to manually do everything from the command prompt.

To make this server a domain controller, Run:
dcpromo /unattend /InstallDNS:Yes /ReplicaOrNewDomain:Replica /ReplicaDomainDNSName:mikefrobbins.com /ConfirmGc:Yes  /UserName:mikefrobbinsadministrator /Password:* /SafeModeAdminPassword:password /RebootOnCompletion:No
<a href="http://mikefrobbins.com/wp-content/uploads/2010/02/dcpromo_unattend.jpg"><img class="alignnone size-full wp-image-489" title="dcpromo_unattend" alt="" src="http://mikefrobbins.com/wp-content/uploads/2010/02/dcpromo_unattend.jpg" width="600" height="782" /></a>

There are many more options for dcpromo /unattend. <a href="http://technet.microsoft.com/en-us/library/cc732887(WS.10).aspx" target="_blank">A list of these options can be found on TechNet</a>. The following command will remove active directory services and revert the server back to a member server if needed.
dcpromo /unattend /AdministratorPassword:password

If you revert the domain controller back to a member server as referenced above, you'll probably also want to remove the DNS Server role. To remove the DNSServer role, run:
Start /w ocsetup DNS-Server-Core-Role /uninstall

The oclist command will show you a list of roles that are currently installed.

Warning: You cannot manage a Windows Server 2008 R2 DNS Server from Windows Server 2003 R2. You will receive this error even though the DNS Server is operating properly:
<a href="http://mikefrobbins.com/wp-content/uploads/2010/02/dns_error.jpg"><img class="alignnone size-full wp-image-490" title="dns_error" alt="" src="http://mikefrobbins.com/wp-content/uploads/2010/02/dns_error.jpg" width="357" height="119" /></a>

To install the DHCP Server role on your core domain controller, execute the following command:
Start /w ocsetup DHCPServerCore

If you don’t use the start /w part of the command, it will still work, but it immediately returns you to a command prompt and you won’t know when the installation of the role has completed.

Set the DHCPServer service to start automatically:  sc config dhcpserver start= auto
Start the DHCPServer Service:  net start dhcpserver
Authorize the DHCPServer:  netsh dhcp add server dc102.mikefrobbins.com 10.0.0.2

Configure the remainder of the dhcp server options from another machine that has the GUI tools installed. Managing DHCP from a Windows Server 2003 R2 machine seems to work fine. Transfer the FSMO roles from your Windows Server 2003 R2 domain controllers before decommisioning them.

Enter the software license key:  slmgr /ipk xxxxx-xxxxx-xxxxx-xxxxx-xxxxx
Activate the operating system:  cscript C:\windows\system32\slmgr.vbs -ato

µ