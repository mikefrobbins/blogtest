---
ID: 7182
post_title: >
  PowerShell Function to Determine the
  Active Directory FSMO Role Holders via
  the .NET Framework
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2013/04/18/powershell-function-to-determine-the-active-directory-fsmo-role-holders-via-the-net-framework/
published: true
post_date: 2013-04-18 07:30:46
---
<a href="http://mikefrobbins.com/2013/04/11/use-powershell-to-find-where-the-current-fsmo-roles-are-assigned-in-active-directory/" target="_blank">Last week I posted a PowerShell function</a> to determine what Active Directory domain controllers held the <a href="http://en.wikipedia.org/wiki/Flexible_single_master_operation" target="_blank">FSMO roles</a> for one or more domains and forests. That particular function used the <em>Get-ADDomain</em> and <em>Get-ADForest</em> cmdlets which are part of the Active Directory PowerShell module. As it so happens, a friend of mine, Shay Levy who is a PowerShell MVP posted <a href="http://www.powershellmagazine.com/2013/04/11/pstip-discovering-active-directory-fsmo-role-holders-using-powershell/" target="_blank">an article on PowerShell Magazine</a> that uses a couple of one liners that use the .NET Framework to return the FSMO role holders.

I'm not a .NET guy, but this started me thinking that there was probably a way with the .NET Framework to figure out where the FSMO roles were based on a given domain instead of the current one.

I decided to retro-fit my function to use the .NET Framework Class that Shay was using, but I figured out a different static method (I think that's what it's called, but correct me if I'm wrong). This other static method would indeed return the FSMO role holders based on a given domain name.
<pre class="lang:ps decode:true">function Get-FSMORole {
&lt;#
.SYNOPSIS
Retrieves the FSMO role holders from one or more Active Directory domains and forests.
.DESCRIPTION
Get-FSMORole uses the .NET Framework to determine which domain controller currently holds each
of the Active Directory FSMO roles. The Active Directory PowerShell module is not required.
.PARAMETER DomainName
One or more Active Directory domain names.
.EXAMPLE
Get-Content domainnames.txt | Get-FSMORole
.EXAMPLE
Get-FSMORole -DomainName domain1, domain2
#&gt;
    [CmdletBinding()]
    param(
        [Parameter(ValueFromPipeline=$True)]
        [string[]]$DomainName = $env:USERDOMAIN
    )
    PROCESS {
        foreach ($domain in $DomainName) {
            Write-Verbose "Querying $domain"
            Try {
            $problem = $false
            $addomain = [System.DirectoryServices.ActiveDirectory.Domain]::GetDomain(
                (New-Object System.DirectoryServices.ActiveDirectory.DirectoryContext('Domain', $domain)))
            } Catch { $problem = $true
                Write-Warning $_.Exception.Message
              }
            if (-not $problem) {
                $adforest = [System.DirectoryServices.ActiveDirectory.Forest]::GetForest(
                    (New-Object System.DirectoryServices.ActiveDirectory.DirectoryContext('Forest', (($addomain).forest))))

                New-Object PSObject -Property @{
                    InfrastructureMaster = $addomain.InfrastructureRoleOwner
                    PDCEmulator = $addomain.PdcRoleOwner
                    RIDMaster = $addomain.RidRoleOwner
                    DomainNamingMaster = $adforest.NamingRoleOwner
                    SchemaMaster = $adforest.SchemaRoleOwner
                }
            }
        }
    }
}</pre>
<span style="line-height: 1.5;">This script can be downloaded from the <a href="http://gallery.technet.microsoft.com/scriptcenter/PowerShell-Function-to-331f41d6" target="_blank">TechNet Script Repository</a>.</span>

<a href="http://mikefrobbins.com/wp-content/uploads/2013/04/fsmo-netframework.png"><img class="alignnone size-large wp-image-7189" alt="fsmo-netframework" src="http://mikefrobbins.com/wp-content/uploads/2013/04/fsmo-netframework.png?w=640" width="640" height="479" /></a>

I'll be at the <a href="http://powershell.org/summit" target="_blank">PowerShell Summit North America 2013</a> next week! Don't forget about the <a href="http://powershell.org/games" target="_blank">Scripting Games</a> that also begin next week.

<span style="line-height: 1.5;">µ</span>