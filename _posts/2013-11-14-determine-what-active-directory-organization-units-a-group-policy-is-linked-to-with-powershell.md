---
ID: 8464
post_title: >
  Determine What Active Directory
  Organization Units a Group Policy is
  Linked to with PowerShell
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2013/11/14/determine-what-active-directory-organization-units-a-group-policy-is-linked-to-with-powershell/
published: true
post_date: 2013-11-14 07:30:46
---
Have you ever noticed that there's not many GPO related PowerShell cmdlets? I started out wanting to know what group policies existed that weren't linked to OU's and added a few other properties to return additional useful information for the ones that were:
<pre class="wrap:false lang:ps decode:true" title="Get-GPOLink">#Requires -Version 3.0
#Requires -Modules GroupPolicy 

function Get-GPOLink {

&lt;#
.SYNOPSIS
    Returns the Active Directory (AD) Organization Units (OU's) that a Group Policy Object (GPO) is linked to.

.DESCRIPTION
    Get-GPOLink is a function that returns the Active Directory Organization Units (OU's) that a Group Policy
Object (GPO) is linked to.

.PARAMETER Name
    The Name of the Group Policy Object.

.EXAMPLE
    Get-GPOLink -Name 'Default Domain Policy'

.EXAMPLE
    Get-GPOLink -Name 'Default Domain Policy', 'Default Domain Controllers Policy'

.EXAMPLE
    'Default Domain Policy' | Get-GPOLink

.EXAMPLE
    'Default Domain Policy', 'Default Domain Controllers Policy' | Get-GPOLink

.EXAMPLE
    Get-GPO -All | Get-GPO-Link

.INPUTS
    System.String, Microsoft.GroupPolicy.Gpo

.OUTPUTS
    PSCustomObject
#&gt;

    [CmdletBinding()]
    param (
        [Parameter(Mandatory,
                   ValueFromPipeline,
                   ValueFromPipelineByPropertyName)]
        [Alias('DisplayName')]
        [string[]]$Name
    )

    PROCESS {

        foreach ($n in $Name) {            
            $problem = $false

            try {
                Write-Verbose -Message "Attempting to produce XML report for GPO: $n"

                [xml]$report = Get-GPOReport -Name $n -ReportType Xml -ErrorAction Stop
            }
            catch {
                $problem = $true
                Write-Warning -Message "An error occured while attempting to query GPO: $n"
            }

            if (-not($problem)) {
                Write-Verbose -Message "Returning results for GPO: $n"

                [PSCustomObject]@{
                    'GPOName' = $report.GPO.Name
                    'LinksTo' = $report.GPO.LinksTo.SOMName
                    'Enabled' = $report.GPO.LinksTo.Enabled
                    'NoOverride' = $report.GPO.LinksTo.NoOverride
                    'CreatedDate' = ([datetime]$report.GPO.CreatedTime).ToShortDateString()
                    'ModifiedDate' = ([datetime]$report.GPO.ModifiedTime).ToShortDateString()
                }

            }

        }

    }

}</pre>
One of more group policy names can be piped into this function or provided via parameter input. If you want to query all group policy objects, simply pipe <em>Get-GPO -All</em> to it:

<a href="http://mikefrobbins.com/wp-content/uploads/2013/11/gpolink1.png"><img class="alignnone size-full wp-image-8467" alt="gpolink1" src="http://mikefrobbins.com/wp-content/uploads/2013/11/gpolink1.png" width="877" height="179" /></a>

What's neat to me is being able to create an XML report on the fly, store it in a variable, and query that variable to return the desired output. Cool huh?

The function shown in this blog can be downloaded from the TechNet <a href="http://gallery.technet.microsoft.com/scriptcenter/Determine-What-Active-3a37b0c5" target="_blank">script repository</a>.

µ