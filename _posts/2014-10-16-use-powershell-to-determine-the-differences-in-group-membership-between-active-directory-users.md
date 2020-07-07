---
ID: 10671
post_title: >
  Use PowerShell to Determine the
  Differences in Group Membership between
  Active Directory Users
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2014/10/16/use-powershell-to-determine-the-differences-in-group-membership-between-active-directory-users/
published: true
post_date: 2014-10-16 07:30:36
---
I recently saw a <a href="http://www.reddit.com/r/PowerShell/comments/2imlso/compare_array_values_for_outliers_or/" target="_blank" rel="noopener noreferrer">post on Reddit</a> where someone was trying to create a function that takes an Active Directory user name as input for a manager who has direct reports (subordinates) specified in Active Directory. They wanted to determine if the Active Directory group membership of any of those subordinates is different than the others.

There are two different parts to this scenario. Returning a list of the manager's direct reports by querying that property from the manager's user account in Active Directory:
<pre class="lang:ps decode:true">Get-ADUser -Identity sbuchanan -Properties directReports |
Select-Object -ExpandProperty directReports</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/10/adgroup-diff1.jpg"><img class="alignnone size-full wp-image-10672" src="http://mikefrobbins.com/wp-content/uploads/2014/10/adgroup-diff1.jpg" alt="adgroup-diff1" width="877" height="155" /></a>

I decided to keep that portion separate since it would be easy enough to accomplish that part of the task and hard coding that functionality would limit the re-usability of the group comparison portion of the tool. I wanted the users id's (input for my tool) to be able to come from a query against Active Directory, a list of user id's stored in a text file, or a CSV file (maybe an auditor supplies a list of user id's to compare that he emails to you).

The following PowerShell function compares the Active Directory user groups of one or more users. The function gets a combined list of all groups that the specified users are in. It then determines what are considered to be common groups between the users by determining which of those groups have 50% or more of the specified users in them. Finally, it iterates through each user comparing their group membership to the common group list and returns the user's group membership where it differentiates from the  list.
<pre class="lang:ps decode:true " title="Compare-MrADGroup">#Requires -Version 3.0
#Requires -Modules ActiveDirectory
function Compare-MrADGroup {

&lt;#
.SYNOPSIS
    Compares the groups of a the specified Active Directory users.

.DESCRIPTION
    Compare-MrADGroup is a function that retrieves a list of all the Active
    Directory groups that the specified Active Directory users are a member
    of. It determines what groups are common between the users based on
    membership of 50% or more of the specified users. It then compares the
    specified users group membership to the list of common groups and returns
    a list of users whose group membership differentiates from that list. A
    minus (-) in the status column means the user is not a member of a common
    group and a plus (+) means the user is a member of an additional group.

.PARAMETER UserName
    The Active Directory user(s) account object to compare. Can be specified
    in the form or SamAccountName, Distinguished Name, or GUID. This parameter
    is mandatory.

.PARAMETER IncludeEqual
    Switch parameter to include common groups that the specified user is a
    member of. An equals (=) sign means the user is a member of a common group.

.EXAMPLE
     Compare-MrADGroup -UserName 'jleverling', 'lcallahan', 'mpeacock'

.EXAMPLE
     'jleverling', 'lcallahan', 'mpeacock' | Compare-MrADGroup -IncludeEqual

.EXAMPLE
     Get-ADUser -Filter {Department -eq 'Sales' -and Enabled -eq 'True'} |
     Compare-MrADGroup

.INPUTS
    String

.OUTPUTS
    PSCustomObject

.NOTES
    Author:  Mike F Robbins
    Website: http://mikefrobbins.com
    Twitter: @mikefrobbins
#&gt;

    [CmdletBinding()]
    param (
        [Parameter(Mandatory,
                   ValueFromPipeline)]
        [string[]]$UserName,

        [switch]$IncludeEqual
    )

    BEGIN {
        $Params = @{}

        If ($PSBoundParameters['IncludeEqual']) {
            $Params.IncludeEqual = $true
        }
    }

    PROCESS {
        foreach ($name in $UserName) {
            try {
                Write-Verbose -Message "Attempting to query Active Directory of user: '$name'."
                [array]$users += Get-ADUser -Identity $name -Properties MemberOf -ErrorAction Stop
            }
            catch {
                Write-Warning -Message "An error occured. Error Details: $_.Exception.Message"
            }
        }
    }

    END {
        Write-Verbose -Message "The `$users variable currently contains $($users.Count) items."

        $commongroups = ($groups = $users |
        Select-Object -ExpandProperty MemberOf |
        Group-Object) |
        Where-Object Count -ge ($users.Count / 2) |
        Select-Object -ExpandProperty Name
        
        Write-Verbose -Message "There are $($commongroups.Count) groups with 50% or more of the specified users in them."
        
        foreach ($user in $users) {
            Write-Verbose -Message "Checking user: '$($user.SamAccountName)' for group differences."

            $differences = Compare-Object -ReferenceObject $commongroups -DifferenceObject $user.MemberOf @Params

            foreach ($difference in $differences) {
                [PSCustomObject]@{
                    UserName = $user.SamAccountName
                    GroupName = $difference.InputObject -replace '^CN=|,.*$'
                    Status = switch ($difference.SideIndicator){'&lt;='{'-';break}'=&gt;'{'+';break}'=='{'=';break}}
                    'RatioOfUsersInGroup(%)' = ($groups | Where-Object name -eq $difference.InputObject).Count / $users.Count * 100 -as [int]
                }
            }
        }
    }
}</pre>
Now to use PowerShell to query the "Direct reports" of a manager in Active Directory and return those users as input for our group comparison tool:

<a href="http://mikefrobbins.com/wp-content/uploads/2014/10/adgroup-diff4.jpg"><img class="alignnone size-full wp-image-10718" src="http://mikefrobbins.com/wp-content/uploads/2014/10/adgroup-diff4.jpg" alt="adgroup-diff4" width="417" height="498" /></a>

That task can be performed with this simple PowerShell one-liner:
<pre class="lang:ps decode:true">Get-ADUser -Identity sbuchanan -Properties directReports |
Select-Object -ExpandProperty directReports |
Compare-MrADGroup</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/10/adgroup-diff2a.jpg"><img class="alignnone size-full wp-image-10710" src="http://mikefrobbins.com/wp-content/uploads/2014/10/adgroup-diff2a.jpg" alt="adgroup-diff2a" width="877" height="239" /></a>

As shown in the previous set of results, a minus in the status column means the user is not a member of a common group and a plus means they are a member of an extra group other than the common ones. The "RatioOfUsersInGroup(%)" column returns a percentage value of how many users are in the specified group, for example 50% (3 of the 6 users) are in both the Faculty and Staff groups and only 17% (1 of the users) is in the Test01 group.
<pre class="lang:ps decode:true">Compare-MrADGroup -UserName 'jleverling', 'lcallahan', 'mpeacock' -IncludeEqual</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/10/adgroup-diff3b.jpg"><img class="alignnone size-full wp-image-10711" src="http://mikefrobbins.com/wp-content/uploads/2014/10/adgroup-diff3b.jpg" alt="adgroup-diff3b" width="877" height="206" /></a>

An equal sign will only show up in the status column when the <em>-IncludeEqual</em> parameter is specified. It means that the users are included in the common group(s) as shown in the previous example.

The <em>Compare-MrADGroup</em> PowerShell function shown in this blog article can also be downloaded from the <a href="https://gallery.technet.microsoft.com/scriptcenter/Determine-the-Differences-7f82e8c2" target="_blank" rel="noopener noreferrer">TechNet script repository</a>.

<hr />

Update: The most recent version of the <em>Compare-MrADGroup</em> function shown in this blog article can be downloaded from <a href="https://github.com/mikefrobbins/ActiveDirectory" target="_blank" rel="noopener noreferrer">my ActiveDirectory repository on GitHub</a>.

µ