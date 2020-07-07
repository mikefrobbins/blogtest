---
ID: 7131
post_title: >
  Use PowerShell to Find Where the Current
  FSMO Roles are Assigned in Active
  Directory
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2013/04/11/use-powershell-to-find-where-the-current-fsmo-roles-are-assigned-in-active-directory/
published: true
post_date: 2013-04-11 07:30:11
---
A while back, I had a need to figure out with PowerShell what server in an Active Directory domain held the PDC Emulator FSMO Role. I found a script on a very popular blog site that figured it out by using a command similar to this:
<pre class="lang:ps decode:true">Get-ADDomainController -Filter * |
  where OperationMasterRoles -contains PDCEmulator |
  select Name</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2013/04/fsmo-roles1.png"><img class="alignnone size-full wp-image-7132" alt="fsmo-roles1" src="http://mikefrobbins.com/wp-content/uploads/2013/04/fsmo-roles1.png" width="423" height="147" /></a>

While it accomplished what was necessary, I immediately thought "I can do better" and improved the one liner so it filtered left:
<pre class="lang:ps decode:true">Get-ADDomainController -Filter {
  OperationMasterRoles -like 'PDCEmulator'
} | select name</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2013/04/fsmo-roles2.png"><img class="alignnone size-full wp-image-7133" alt="fsmo-roles2" src="http://mikefrobbins.com/wp-content/uploads/2013/04/fsmo-roles2.png" width="406" height="147" /></a>

At the April <a href="http://phillyposh.org/" target="_blank">Philadelphia PowerShell User Group</a> meeting, I won a copy of <a href="http://www.sapien.com/books/Managing-Active-Directory" target="_blank">Managing Active Directory with Windows PowerShell</a> written by <a href="http://jdhitsolutions.com/blog/" target="_blank">Jeffery Hicks</a> and published by <a href="http://www.sapien.com/software/powershell_studio" target="_blank">SAPIEN Technologies</a>.

On page 90, I noticed one of the examples used the <em>Get-ADDomain</em> cmdlet to retrieve which server held the PDC Emulator role so it was time to investigate that cmdlet.

What I discovered is all of the domain level FSMO roles could be retrieved very easily using that cmdlet:
<pre class="lang:ps decode:true">Get-ADDomain |
  select InfrastructureMaster, PDCEmulator, RIDMaster</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2013/04/fsmo-roles3.png"><img class="alignnone size-full wp-image-7134" alt="fsmo-roles3" src="http://mikefrobbins.com/wp-content/uploads/2013/04/fsmo-roles3.png" width="636" height="133" /></a>

A little more research and I found that the <em>Get-ADForest</em> cmdlet could be used to obtain the server names of the forest level FSMO role holders:
<pre class="lang:ps decode:true">Get-ADForest |
  select DomainNamingMaster, SchemaMaster</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2013/04/fsmo-roles4.png"><img class="alignnone size-full wp-image-7135" alt="fsmo-roles4" src="http://mikefrobbins.com/wp-content/uploads/2013/04/fsmo-roles4.png" width="524" height="133" /></a>

I wrote a function named Get-FSMORole that will retrieve the FSMO roles holders from the domain of the current user that this function is being run by, or from domains that are provided via pipeline or parameter input as shown in the following examples:
<pre class="lang:ps decode:true">function Get-FSMORole {
  [CmdletBinding()]
  param(
    [Parameter(ValueFromPipeline=$True)]
    [string[]]$DomainName = $env:USERDOMAIN
  )
  BEGIN {
    Import-Module ActiveDirectory -Cmdlet Get-ADDomain, Get-ADForest -ErrorAction SilentlyContinue
  }
  PROCESS {
    foreach ($domain in $DomainName) {
      Write-Verbose "Querying $domain"
      Try {
      $problem = $false
      $addomain = Get-ADDomain -Identity $domain -ErrorAction Stop
      } Catch { $problem = $true
      Write-Warning $_.Exception.Message
      }
      if (-not $problem) {
        $adforest = Get-ADForest -Identity (($addomain).forest)

        New-Object PSObject -Property @{
          InfrastructureMaster = $addomain.InfrastructureMaster
          PDCEmulator = $addomain.PDCEmulator
          RIDMaster = $addomain.RIDMaster
          DomainNamingMaster = $adforest.DomainNamingMaster
          SchemaMaster = $adforest.SchemaMaster
        }
      }
    }
  }
}</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2013/04/fsmo-roles5a.png"><img class="alignnone size-full wp-image-7161" alt="fsmo-roles5a" src="http://mikefrobbins.com/wp-content/uploads/2013/04/fsmo-roles5a.png" width="613" height="562" /></a>

This function depends on either having the Remote Server Administration Tools installed or importing the Active Directory module locally via Implicit Remoting before running the function. In the previous example, the machine the function is being run on is running Windows 8 and the domain controllers are running Windows Server 2012, although I've tested it on a multiple Windows Server 2008 R2 forests where there's a trust between the forests and the function worked without issue.

This function can be downloaded from the <a href="http://gallery.technet.microsoft.com/scriptcenter/PowerShell-Function-to-bec6c607" target="_blank">TechNet Script Repository</a>.

<span style="line-height: 1.5;">µ</span>