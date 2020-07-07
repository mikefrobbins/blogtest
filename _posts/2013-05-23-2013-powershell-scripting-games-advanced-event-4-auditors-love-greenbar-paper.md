---
ID: 7371
post_title: '2013 PowerShell Scripting Games Advanced Event 4 &#8211; Auditors Love Greenbar Paper'
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2013/05/23/2013-powershell-scripting-games-advanced-event-4-auditors-love-greenbar-paper/
published: true
post_date: 2013-05-23 07:30:34
---
The requirements for the 2013 Scripting Games Advanced Event 4 can be found <a href="http://scriptinggames.org/5d14fbd92df9e7409ba6.pdf" target="_blank">here</a>. For this event I created multiple functions and I'm going to quote chapter 6, section 1 of the "<a href="http://www.manning.com/jones4/" target="_blank">Learn PowerShell Toolmaking in a Month of Lunches</a>" book written by Don Jones and Jeffery Hicks, published by Manning:

<strong><em><span style="text-decoration: underline;">A function should do one and only one of these things:</span></em></strong>
<em> Retrieve data from someplace</em>
<em> Process data</em>
<em> Output data to some place</em>
<em> Put data into some visual format meant for human consumption</em>

I started out by naming my function to create the audit report properly (New-UserAuditReport). If you having trouble picking a name for your function, it's probably because you've broken the previous rule and your function does more than one thing.

Of course, all of the functions (4 of them) all have their own comment based help.

Here's the validation that I performed on the filename and extension via a regular expression. This not only enforces the htm and html file extension specified in the requirements, but also ensures that the file name is valid on a Windows system. There's no sense in allowing the script to process all the way to the end only to have it fail because the file name is not valid on a Windows system. Be proactive, not reactive. This <a href="http://msdn.microsoft.com/en-us/library/windows/desktop/aa365247(v=vs.85).aspx" target="_blank">MSDN article</a> explains what is not valid in a Windows filename.

<a href="http://mikefrobbins.com/wp-content/uploads/2013/05/sq2013-event4a.png"><img class="alignnone size-large wp-image-7372" alt="sq2013-event4a" src="http://mikefrobbins.com/wp-content/uploads/2013/05/sq2013-event4a.png?w=640" width="640" height="47" /></a>

<span style="line-height: 1.5;">I couldn't decide whether to use the cmdlets that are part of the Active Directory module or not because they may not be present on the system this is being run from and they only work on Windows Server 2008 R2 or higher unless you have a list of </span>ingredients<span style="line-height: 1.5;"> to include a Leprechaun as discussed in </span><a style="line-height: 1.5;" href="http://blogs.technet.com/b/ashleymcglone/archive/2011/03/17/step-by-step-how-to-use-active-directory-powershell-cmdlets-against-2003-domain-controllers.aspx" target="_blank">this TechNet blog</a> to make them work with <span style="line-height: 1.5;">Windows Server 2003 domain controllers. Here's a "Hey, Scripting Guy! blog" on the same subject that refers to 2008 non-R2 domain controllers as well. The problem is that since the scenario didn't specify what OS the domain controllers are running, these cmdlets may not work.</span>

<span style="line-height: 1.5;">Using ADSI (Active Directory Service Interfaces) on the other hand would be something that would work on systems that didn't have the AD PowerShell module installed and it would also work against older domain controllers.</span>

What I decided is why make a decision? Why not have the best of both worlds? Try the AD cmdlets and I mean literally try the AD cmdlets as shown in the first try catch block in the code below and then if there's an issue, try ADSI as shown in the second (nested) try catch block in the code shown below as well:

<a href="http://mikefrobbins.com/wp-content/uploads/2013/05/sq2013-event4b.png"><img class="alignnone size-full wp-image-7373" alt="sq2013-event4b" src="http://mikefrobbins.com/wp-content/uploads/2013/05/sq2013-event4b.png" width="630" height="248" /></a>

<span style="line-height: 1.5;">If neither one works then the script exits with a warning instead of continuing to only error out somewhere further along in the code. That's what the problem variable is for.</span>

<a href="http://mikefrobbins.com/wp-content/uploads/2013/05/sq2013-event4c.png"><img class="alignnone size-full wp-image-7374" alt="sq2013-event4c" src="http://mikefrobbins.com/wp-content/uploads/2013/05/sq2013-event4c.png" width="418" height="342" /></a>

<strong><span style="color: #ff0000;">There is an issue on the Scripting Games website where the "Plain" tab doesn't show the inline CSS so be sure to use the "Code" tab when copying and pasting the code to use on your own machine.</span></strong>

I figured those auditors would love some old school Greenbar paper. Here's an example of six results showing a "Greenbar" style webpage (aka virtual Greenbar paper):

<a href="http://mikefrobbins.com/wp-content/uploads/2013/05/sq2013-event4h.png"><img class="alignnone size-large wp-image-7379" alt="sq2013-event4h" src="http://mikefrobbins.com/wp-content/uploads/2013/05/sq2013-event4h.png?w=640" width="640" height="201" /></a>

You may be wondering why I would chose to use the "LastLogonTimestamp" property when there are other properties such as "LastLogon" that already have human readable dates to start with? That's because the LastLogon property is not replicated to all domain controllers in the domain and LastLogonTimestamp is beginning with Windows Server 2003. So it's either query all the domain controllers in the domain or convert the date. From an efficiency standpoint it's better to convert the date rather than possibly querying hundreds of domain controllers. See this <a href="http://blogs.technet.com/b/askds/archive/2009/04/15/the-lastlogontimestamp-attribute-what-it-was-designed-for-and-how-it-works.aspx" target="_blank">TechNet blog article</a> for more information about those two properties.

<a href="http://mikefrobbins.com/wp-content/uploads/2013/05/sq2013-event4d.png"><img class="alignnone size-large wp-image-7375" alt="sq2013-event4d" src="http://mikefrobbins.com/wp-content/uploads/2013/05/sq2013-event4d.png?w=640" width="640" height="251" /></a>

Now for the two functions that "Get" the data. The first is "Get-ADRandomUser":

<a href="http://mikefrobbins.com/wp-content/uploads/2013/05/sq2013-event4e.png"><img class="alignnone size-large wp-image-7376" alt="sq2013-event4e" src="http://mikefrobbins.com/wp-content/uploads/2013/05/sq2013-event4e.png?w=640" width="640" height="378" /></a>

As you can see, this is a reusable function. Here's the output that it creates (which is objects):

<a href="http://mikefrobbins.com/wp-content/uploads/2013/05/sq2013-event4i.png"><img class="alignnone size-large wp-image-7385" alt="sq2013-event4i" src="http://mikefrobbins.com/wp-content/uploads/2013/05/sq2013-event4i.png?w=640" width="640" height="170" /></a>

The ADSI one was a little trickier because I needed it to return the same data as the AD one. This one is called "Get-ADSIRandomUser":

<a href="http://mikefrobbins.com/wp-content/uploads/2013/05/sq2013-event4f.png"><img class="alignnone size-large wp-image-7377" alt="sq2013-event4f" src="http://mikefrobbins.com/wp-content/uploads/2013/05/sq2013-event4f.png?w=640" width="640" height="476" /></a>

Although the type of object that this function produces is different, it's still objects and it produces the same output from a properties and data standpoint as the previous function:

<a href="http://mikefrobbins.com/wp-content/uploads/2013/05/sq2013-event4j.png"><img class="alignnone size-large wp-image-7386" alt="sq2013-event4j" src="http://mikefrobbins.com/wp-content/uploads/2013/05/sq2013-event4j.png?w=640" width="640" height="171" /></a>

And finally, there is the "Set-AlternatingCSS" function which is a modified version of a function from Don Jones's "Creating HTML Reports in PowerShell" book. Visit <a href="http://powershellbooks.com" target="_blank">PowerShellBooks.com</a> for details about this free ebook (Thank You Don!)

<a href="http://mikefrobbins.com/wp-content/uploads/2013/05/sq2013-event4g.png"><img class="alignnone size-full wp-image-7378" alt="sq2013-event4g" src="http://mikefrobbins.com/wp-content/uploads/2013/05/sq2013-event4g.png" width="327" height="382" /></a>

<span style="text-decoration: underline;">Update 02/09/14</span>
The link to my solution is no longer valid so I’m posting it here since I’ve received some requests for it:
<pre class="lang:ps decode:true crayon-selected" title="Advanced Category Event #4 of the 2013 Scripting Games">#Requires -Version 3.0
function New-UserAuditReport {

&lt;#
.SYNOPSIS
Creates a report of specific properties for random Active Directory users in HTM or HTML format to be used for
auditing purposes. 
.DESCRIPTION
    New-UserAuditReport is a function that creates a htm or html report for a random number of active directory
user accounts. The specific information that is on the report is: user name, department, title, date
and time for the last interactive, network, or service logon, date and time of last password change, and
whether or not the account is disabled or locked out. The ActiveDirectory PowerShell module is not required,
but it is attempted first via an external reusable function and then ADSI is attempted via another reusable
external function if this function experiences an issue with the Active Directory PowerShell module.
    The LastLogonTimestamp property was chosen because the other possible attributes are bot replicated to all
the domain controllers in the domain and each one would need to be queried as discussed in this blog article:
http://blogs.technet.com/b/askds/archive/2009/04/15/the-lastlogontimestamp-attribute-what-it-was-designed-for-and-how-it-works.aspx
    The pwdLastSet property was chosen to provide consistency on what is returned by Get-ADUser and ADSI. 
.PARAMETER Records
The number of random Active Directory user records to retrieve.
.PARAMETER Path
The folder or directory to place the htm or html file in. The default is the current user's temporary directory.
.PARAMETER FileName
The file name that will be used to save the report as. Only valid file names with a .htm or .html file extension
are accepted.
.PARAMETER Force
Switch parameter that when specified overwrites the destination file if it already exists.
.EXAMPLE
New-UserAuditReport -FileName AuditReport.htm
.EXAMPLE
New-UserAuditReport -Records 10 -FileName AuditReport.html
.EXAMPLE
New-UserAuditReport -Records 15 -Path c:\tmp -FileName MyAuditReport.htm
.EXAMPLE
New-UserAuditReport -Records 25 -Path c:\tmp -FileName MyAuditReport.htm -Force
.INPUTS
None
.OUTPUTS
None
#&gt;

    [CmdletBinding()]
    param(
        [ValidateNotNullorEmpty()]
        [int]$Records = 20,

        [ValidateNotNullorEmpty()]
        [ValidateScript({Test-Path $_ -PathType 'Container'})]
        [string]$Path = $Env:TEMP,

        [Parameter(Mandatory=$True)]
        [ValidatePattern("^(?!^(PRN|AUX|CLOCK\$|NUL|CON|COM\d|LPT\d|\..*)(\..+)?$)[^\x00-\x1f\\?*:\"";|/]+\.html?$")]
        [string]$FileName,

        [switch]$Force
    )

    $Params = @{
        Records = $Records
        ErrorAction = 'Stop'
        ErrorVariable = 'Issue'
    }

    $Problem = $false

    Try {
        Write-Verbose -Message "Attempting to use the Active Directory PowerShell Module"
        $Users = Get-ADRandomUser @Params
    }
    catch {

        try {
            Write-Verbose -Message "Failure when attempting to use the Active Directory PowerShell Module, now attempting to use ADSI"
            $Users = Get-ADSIRandomUser @Params
        }
        catch {
            $Problem = $True
            Write-Warning -Message "$Issue.Exception.Message"
        }

    }

    If (-not($Problem)) {

Write-Verbose -Message "Defining GreenBar CSS Style"
$GreenBarStyle = @"
    &lt;style&gt;
    body {
        color:#333333;
        font-family:"Lucida Grande", verdana, sans-serif;
        font-size: 10pt;
    }
    h1 {
        text-align:center;
    }
    h2 {
        border-top:2px solid #4e9a06;
    }

    th {
        font-weight:bold;
        color:#eeeeee;
        background-color:#4e9a06;
    }
    .odd  { background-color:#ffffff; }
    .even { background-color:#e4ffc7; }
    &lt;/style&gt;
"@

        Write-Verbose -Message "Defining auditor friendly dates, property names, converting to HTMl fragment, and applying CSS."
        $UsersHTML = $Users | Select-Object -Property @{
                                 Label='UserName';Expression ={$_.samaccountname}},
                                 Department,
                                 Title,
                                 @{Label='LastLogin';Expression ={([datetime]::fromfiletime([int64]::Parse($_.lastlogontimestamp)))}},
                                 @{Label='PasswordLastChanged';Expression ={([datetime]::fromfiletime([int64]::Parse($_.pwdlastset)))}},
                                 @{Label='IsDisabled';Expression ={(-not($_.enabled))}},
                                 @{Label='IsLockedOut';Expression ={$_.lockedout}} |
                              ConvertTo-Html -Fragment |
                              Out-String |
                              Set-AlternatingCSS -CSSEvenClass 'even' -CssOddClass 'odd'

        $HTMLParams = @{
            'Head'="&lt;title&gt;Random User Audit Report&lt;/title&gt;$GreenBarStyle"
            'PreContent'="&lt;H2&gt;Random User Audit Report for $Records Users&lt;/H2&gt;"
            'PostContent'= "$UsersHTML &lt;HR&gt; $(Get-Date)"
        }

        $Params.Remove("Records")

        Try {
            Write-Verbose -Message "Attempting to build filepath. The regular expression in the params block validated that the file name is valid on a windows system."
            $FilePath = Join-Path -Path $Path -ChildPath "$($FileName.ToLower())" @Params

            Write-Verbose -Message "Converting to HTML and creating the file."
            ConvertTo-Html @HTMLParams @Params |
            Out-File -FilePath $FilePath -NoClobber:(-not($Force)) @Params
        }
        catch {
            Write-Warning -Message "Use the -Force parameter to overwrite the existing file. Error details: $Issue.Message.Exception"      
        }
    }

}

function Get-ADRandomUser {
#Requires -Modules ActiveDirectory

&lt;#
.SYNOPSIS
Returns a list of specific properties for random Active Directory users using the Active Directory PowerShell Module. 
.DESCRIPTION
Get-ADRandomUser is a function that retrieves a list of random active directory users using the Active Directory
PowerShell module. The results are returned as objects for reusability.
.PARAMETER Records
The number of random Active Directory user records to retrieve.
.EXAMPLE
Get-ADRandomUser -Records 20
.INPUTS
None
.OUTPUTS
Selected.Microsoft.ActiveDirectory.Management.ADUser
#&gt;

    [CmdletBinding()]
    param(
        [Parameter(Mandatory=$True)]
        [int]$Records
    )

    $Params = @{
        Filter = '*'
        Properties = 'SamAccountName',
                     'Department',
                     'Title',
                     'LastLogonTimestamp',
                     'pwdLastSet',
                     'Enabled',
                     'LockedOut'
        ErrorAction = 'Stop'
        ErrorVariable = 'Issue'
    }

    try {
        Write-Verbose -Message "Attempting to import the Active Directory Get-ADUser cmdlet."
        Import-Module -Name ActiveDirectory -Cmdlet Get-ADUser -ErrorAction Stop -ErrorVariable Issue

        Write-Verbose -Message "Attempting to query Active Directory using the Get-ADUser cmdlet"
        $RandomUsers = Get-ADUser @Params |
                       Get-Random -Count $Records

        $Params.Property = $Params.Properties
        $Params.Remove("Properties")
        $Params.Remove("Filter")

        $RandomUsers | Select-Object @Params

    }
    catch {
        Write-Warning -Message "$Issue.Message.Exception"
    }

}

function Get-ADSIRandomUser {
#Requires -Version 3.0

&lt;#
.SYNOPSIS
Returns a list of specific properties for random Active Directory users using ADSI. 
.DESCRIPTION
Get-ADSIRandomUser is a function that retrieves a list of random active directory users using ADSI (Active Directory
Service Inferfaces). The results are returned as objects for reusability. This function does not depend on the
Active Directory PowerShell module.
.PARAMETER Records
The number of random Active Directory user records to retrieve.
.EXAMPLE
Get-ADSIRandomUser -Records 20
.INPUTS
None
.OUTPUTS
System.Management.Automation.PSCustomObject
#&gt;

    [CmdletBinding()]
    param(
        [Parameter(Mandatory=$True)]
        [int]$Records
    )

    try {
        $searcher=[adsisearcher]'(&amp;(objectCategory=user)(objectClass=user))'

        $props=@(
            'samaccountname',
            'department',
            'title',
            'lastlogontimestamp',
            'pwdlastset',
            'useraccountcontrol',
            'islockedout'
        )

        $searcher.PropertiesToLoad.AddRange($props)
        $RandomUsers = $searcher.FindAll() |
                       Get-Random -Count $Records
    }
    catch {
        Write-Warning -Message "Error retrieving active directory user information using ADSI"
    }

    foreach ($user in $RandomUsers) {

        [pscustomobject][ordered]@{
            SamAccountName = $($user.Properties.samaccountname)
            Department = $($user.Properties.department)
            Title=$($user.Properties.title)
            LastLogonTimestamp=$($user.Properties.lastlogontimestamp)
            pwdLastSet=$($user.Properties.pwdlastset)
            Enabled = (-not($($user.GetDirectoryEntry().InvokeGet('AccountDisabled'))))
            LockedOut = $($user.GetDirectoryEntry().InvokeGet('IsAccountLocked'))
        }

    }

}

function Set-AlternatingCSS {

&lt;#
.SYNOPSIS
Setup an alternating cascading style sheet. 
.DESCRIPTION
The Set-AlternatingCSS function is a modified version of a function from Don Jones's "Creating HTML Reports in PowerShell"
book. Visit http://powershellbooks.com for details about this free ebook (Thank You Don!).
.PARAMETER HTMLFragment
The HTML fragment created with the ConvertTo-Html cmdlet that you wish to apply the alternating CSS to.
.PARAMETER CSSEvenClass
The CSS to apply to the even rows within the table.
.PARAMETER CssOddClass
The CSS to apply to the odd rows within the table.
.EXAMPLE
Set-AlternatingCSS -HTMLFragment ('My HTML' | ConvertTo-Html -Fragment | Out-String) -CSSEvenClass 'even' -CssOddClass 'odd'
.EXAMPLE
'My HTML' | ConvertTo-Html -Fragment | Out-String |Set-AlternatingCSS -CSSEvenClass 'even' -CssOddClass 'odd'
.INPUTS
String
.OUTPUTS
String
#&gt;

    [CmdletBinding()]
    param(
        [Parameter(Mandatory=$True,
                   ValueFromPipeline=$True)]
        [string]$HTMLFragment,

        [Parameter(Mandatory=$True)]
        [string]$CSSEvenClass,

        [Parameter(Mandatory=$True)]
        [string]$CssOddClass
    )

    [xml]$xml = $HTMLFragment
    $Table = $xml.SelectSingleNode('table')
    $Classname = $CSSOddClass

    foreach ($tr in $Table.tr) {
        if ($Classname -eq $CSSEvenClass) {
            $Classname = $CssOddClass
        } else {
            $Classname = $CSSEvenClass
        }
        $Class = $xml.CreateAttribute('class')
        $Class.value = $Classname
        $tr.attributes.append($Class) | Out-Null
    }

    $xml.innerxml | Out-String
}</pre>
This PowerShell script can also be downloaded from the TechNet <a href="http://gallery.technet.microsoft.com/scriptcenter/2013-Scripting-Games-d33e4af3" target="_blank">script repository</a>.

µ