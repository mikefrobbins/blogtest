---
ID: 16176
post_title: 'The PowerShell Iron Scripter: My solution to prequel puzzle 1'
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2018/01/20/the-powershell-iron-scripter-my-solution-to-prequel-puzzle-1/
published: true
post_date: 2018-01-20 21:00:25
---
Each week leading up to the <a href="https://powershell.org/summit/" target="_blank" rel="noopener">PowerShell + DevOps Global Summit 2018</a>, <a href="https://powershell.org/" target="_blank" rel="noopener">PowerShell.org</a> will be posting an <a href="https://powershell.org/2018/01/04/iron-scripter-prequel/" target="_blank" rel="noopener">iron scripter prequel puzzle</a> on their website. As their website states, think of the iron scripter as the successor to the scripting games.

<a href="http://ironscripter.us/" target="_blank" rel="noopener"><img class="alignleft size-full wp-image-16207" src="http://mikefrobbins.com/wp-content/uploads/2018/01/iron-scripter175x175.jpg" alt="" width="175" height="175" /></a>I've taken a look at the different factions and it was a difficult choice for me to choose between the Daybreak and Flawless faction. While I try to write code that's flawless, perfection is in the eye of the beholder and it's also a never-ending moving target. Today's perfect code is tomorrow's hot mess because one's knowledge and experience are both constantly increasing, or at least they should be if you want to remain relevant in this industry. I used some of the comments I saw in <a href="https://twitter.com/Jaykul/status/949115863113793536" target="_blank" rel="noopener">a tweet from Joel Bennett</a> to help me choose between the two and I ended up choosing the Daybreak faction.

In <a href="https://powershell.org/2018/01/14/iron-scripter-2018-prequel-puzzle-1/" target="_blank" rel="noopener">the first puzzle</a>, you're given some code that simply does not work due to numerous errors. The instructions even state there are errors in the code. I started out by cleaning up the supplied code a bit to at least make it work so I'd have a better understanding of what it's trying to accomplish.
<pre class="lang:ps decode:true">$Monitor = Get-WmiObject -Class wmiMonitorID -Namespace root\wmi
$Computer = Get-WmiObject -Class Win32_ComputerSystem

$Monitor | ForEach-Object {
     $psObject = New-Object -TypeName PSObject
     $psObject | Add-Member -MemberType NoteProperty -Name ComputerName -Value ''
     $psObject | Add-Member -MemberType NoteProperty -Name ComputerType -Value ''
     $psObject | Add-Member -MemberType NoteProperty -Name ComputerSerial -Value ''
     $psObject | Add-Member -MemberType NoteProperty -Name MonitorSerial -Value ''
     $psObject | Add-Member -MemberType NoteProperty -Name MonitorType -Value ''
     $psObject.ComputerName = $env:COMPUTERNAME
     $psObject.ComputerType = $Computer.Model
     $psObject.ComputerSerial = $Computer.Name
     $psObject.MonitorSerial = ($_.SerialNumberID | ForEach-Object {[char]$_}) -join ''
     $psObject.MonitorType = ($_.UserFriendlyName | ForEach-Object {[char]$_}) -join ''
     $psObject
}</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2018/01/prequel1-ironscripter3a.jpg"><img class="alignnone size-full wp-image-16231" src="http://mikefrobbins.com/wp-content/uploads/2018/01/prequel1-ironscripter3a.jpg" alt="" width="859" height="409" /></a>

If I were part of the Battle faction, I would probably quit here and say it works so that's good enough.

Throwing a prototype function together to query the local system was simple enough. Using the CIM cmdlets instead of the older WMI ones allows it to run on PowerShell Core 6.0 in addition to Windows PowerShell 4.0+. Although the CIM cmdlets were introduced in PowerShell version 3.0, I choose to use the ForEach method which wasn't introduced until PowerShell version 4.0.

In case you didn't already know, <a href="https://github.com/PowerShell/Powershell" target="_blank" rel="noopener">PowerShell Core version 6.0</a> is not an upgrade or replacement to Windows PowerShell version 5.1. It installs side by side on Windows systems. Based on <a href="https://twitter.com/concentrateddon/status/953749696417169408" target="_blank" rel="noopener">the response to a tweet of mine from Don Jones</a>, it appears that I'm not the only one who thought PowerShell Core should have been version 1.0 instead of 6.0 to help differentiate it and eliminate some of the confusion.

Specifying the Property parameter with <em>Get-CimInstance</em> to limit the properties returned makes my <em>Get-MonitorInfo</em> function shown in the following example more efficient. After all, there's no reason whatsoever to retrieve data that's never going to be used. I decided to keep things as simple as possible and write a regular function instead of an advanced one. It could be turned into an advanced function by simply adding cmdlet binding which also requires a param block even if it's empty.
<pre class="lang:ps decode:true " title="Get-MonitorInfo">#Requires -Version 4.0
function Get-MonitorInfo {

&lt;#
.SYNOPSIS
    Retrieves information about the monitors connected to the local system.
 
.DESCRIPTION
    Get-MonitorInfo is a function that retrieves information about the monitors connected to the local system.
 
.EXAMPLE
     Get-MonitorInfo
 
.INPUTS
    None
 
.OUTPUTS
    PSCustomObject
 
.NOTES
    Author:  Mike F Robbins
    Website: http://mikefrobbins.com
    Twitter: @mikefrobbins
#&gt;

    $Computer = Get-CimInstance -ClassName Win32_ComputerSystem -Property Name, Model
    $BIOS = Get-CimInstance -ClassName Win32_BIOS -Property SerialNumber
    $Monitors = Get-CimInstance -ClassName WmiMonitorID -Namespace root/WMI -Property SerialNumberID, UserFriendlyName

    foreach ($Monitor in $Monitors) {
        [pscustomobject]@{
            ComputerName = $Computer.Name
            ComputerType = $Computer.Model
            ComputerSerial = $BIOS.SerialNumber
            MonitorSerial = -join $Monitor.SerialNumberID.ForEach({[char]$_})
            MonitorType = -join $Monitor.UserFriendlyName.ForEach({[char]$_})
        }    
    }

}</pre>
This function works as expected against a local system with multiple monitors, but it's run against a VM with a single monitor in this example.
<pre class="lang:ps decode:true">Get-MonitorInfo</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2018/01/prequel1-ironscripter4a.jpg"><img class="alignnone size-full wp-image-16232" src="http://mikefrobbins.com/wp-content/uploads/2018/01/prequel1-ironscripter4a.jpg" alt="" width="859" height="201" /></a>

While the function seems to meet the requirements of the solution, I wanted to take it a step further and write something that I would use in a production environment. After all, if you're going to go to all of this effort, why not write something useful.

Functions belong in modules so I'll start out by using <a href="http://mikefrobbins.com/2016/06/30/powershell-function-for-creating-a-script-module-template/" target="_blank" rel="noopener">my <em>New-MrScriptModule</em> function</a> from <a href="https://github.com/mikefrobbins/PowerShell" target="_blank" rel="noopener">my MrToolkit module</a> to create a new module named MrIronScripter.
<pre class="lang:ps decode:true">New-MrScriptModule -Name MrIronScripter -Path "$env:ProgramFiles\WindowsPowerShell\Modules" -Author 'Mike F Robbins' -CompanyName mikefrobbins.com -Description 'PowerShell Module containing scripts and functions from the Iron Scripter Competition' -PowerShellVersion 4.0</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2018/01/prequel1-ironscripter1a.jpg"><img class="alignnone size-full wp-image-16199" src="http://mikefrobbins.com/wp-content/uploads/2018/01/prequel1-ironscripter1a.jpg" alt="" width="859" height="89" /></a>

Once the module is created, I use <a href="http://mikefrobbins.com/2016/07/14/powershell-function-for-creating-a-powershell-function-template/" target="_blank" rel="noopener"><em>New-MrFunction</em></a> to create a function named <em>Get-MrMonitorInfo</em>.
<pre class="lang:ps decode:true ">New-MrFunction -Name Get-MrMonitorInfo -Path "$env:ProgramFiles\WindowsPowerShell\Modules\MrIronScripter"</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2018/01/prequel1-ironscripter2a.jpg"><img class="alignnone size-full wp-image-16226" src="http://mikefrobbins.com/wp-content/uploads/2018/01/prequel1-ironscripter2a.jpg" alt="" width="859" height="213" /></a>

I wanted the function to be able to run against remote computers. Since it uses the CIM cmdlets, I decided to rely on CIM sessions for remote connectivity. Replying on them eliminates the need to code remote connectivity and the ability to specify alternate credentials into my function. All of that is taken care of when establishing a CIM session with the <em>New-CimSession</em> cmdlet. <a href="http://mikefrobbins.com/2014/08/28/powershell-function-to-create-cimsessions-to-remote-computers-with-fallback-to-dcom/" target="_blank" rel="noopener">My <em>New-MrCimSession</em> function</a> could also be used to establish a CIM session using WSMAN with automated negotiation down to DCOM depending on which one is available.

Most functions iterate though each remote computer one at a time, but why limit your function to querying one computer at a time? One of my goals was to query all of the remote computers at once which would be much more efficient from a performance standpoint.
<pre class="lang:ps decode:true" title="Get-MrMonitorInfo">#Requires -Version 4.0
function Get-MrMonitorInfo {

&lt;#
.SYNOPSIS
    Retrieves information about the monitors connected to the specified system.
 
.DESCRIPTION
    Get-MrMonitorInfo is an advanced function that retrieves information about the monitors
    connected to the specified system.
 
.PARAMETER CimSession
    Specifies the CIM session to use for this function. Enter a variable that contains the CIM session or a command that
    creates or gets the CIM session, such as the New-CimSession or Get-CimSession cmdlets. For more information, see
    about_CimSessions.
 
.EXAMPLE
     Get-MrMonitorInfo

.EXAMPLE
     Get-MrMonitorInfo -CimSession (New-CimSession -ComputerName Server01, Server02)
 
.INPUTS
    None
 
.OUTPUTS
    Mr.MonitorInfo
 
.NOTES
    Author:  Mike F Robbins
    Website: http://mikefrobbins.com
    Twitter: @mikefrobbins
#&gt;

    [CmdletBinding()]
    [OutputType('Mr.MonitorInfo')]
    param (
        [Microsoft.Management.Infrastructure.CimSession[]]$CimSession
    )

    $Params = @{
        ErrorAction = 'SilentlyContinue'
        ErrorVariable = 'Problem'
    }

    if ($PSBoundParameters.CimSession) {
        $Params.CimSession = $CimSession
    }

    $ComputerInfo = Get-CimInstance @Params -ClassName Win32_ComputerSystem -Property Name, Manufacturer, Model
    $BIOS = Get-CimInstance @Params -ClassName Win32_BIOS -Property SerialNumber
    $Monitors = Get-CimInstance @Params -ClassName WmiMonitorID -Namespace root/WMI -Property ManufacturerName, UserFriendlyName, ProductCodeID, SerialNumberID, WeekOfManufacture, YearOfManufacture

    foreach ($Computer in $ComputerInfo) {
        
        foreach ($Monitor in $Monitors | Where-Object {-not $_.PSComputerName -or $_.PSComputerName -eq $Computer.Name}) {

            if (-not $PSBoundParameters.CimSession) {
                
                Write-Verbose -Message "Running against the local system. Setting value for PSComputerName (a read-only property) to $env:COMPUTERNAME."
                ($BIOS.GetType().GetField('_CimSessionComputerName','static,nonpublic,instance')).SetValue($BIOS,$Computer.Name)

            }

            [pscustomobject]@{
                ComputerName = $Computer.Name
                ComputerManufacturer = $Computer.Manufacturer
                ComputerModel = $Computer.Model
                ComputerSerial = ($BIOS | Where-Object PSComputerName -eq $Computer.Name).SerialNumber
                MonitorManufacturer = -join $Monitor.ManufacturerName.ForEach({[char]$_})
                MonitorModel = -join $Monitor.UserFriendlyName.ForEach({[char]$_})
                ProductCode = -join $Monitor.ProductCodeID.ForEach({[char]$_})
                MonitorSerial = -join $Monitor.SerialNumberID.ForEach({[char]$_})
                MonitorManufactureWeek = $Monitor.WeekOfManufacture
                MonitorManufactureYear = $Monitor.YearOfManufacture
                PSTypeName = 'Mr.MonitorInfo'
            }
                
        }
    
    }
    
    foreach ($p in $Problem) {
        Write-Warning -Message "An error occurred on $($p.OriginInfo). $($p.Exception.Message)"
    }

}</pre>
Can a read-only property be modified in PowerShell? Keep reading while keeping an open mind to find out.

The PSComputerName property isn't populated when running against the local system. That's one of the problems I ran into when trying to put the pieces back together from the different WMI classes to return the results from each individual system. That property is also read-only and generates an error when trying to update the value it contains in the BIOS variable. I found a solution thanks to <a href="https://learn-powershell.net/2016/06/27/quick-hits-writing-to-a-read-only-property/" target="_blank" rel="noopener">a blog article by Boe Prox</a>. Due to differences in the names and where the PSComputerName property is actually pulled from, my example wasn't quite as straight forward as the one in Boe's example, but his article pointed me in the right direction.
<pre class="lang:ps decode:true ">$BIOS = Get-CimInstance -ClassName Win32_BIOS -Property SerialNumber
$BIOS.PSComputerName
$BIOS.PSComputerName = 'FN2187'
($BIOS.GetType().GetField('_CimSessionComputerName','static,nonpublic,instance')).SetValue($BIOS,'FN2187')
$BIOS.PSComputerName</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2018/01/prequel1-ironscripter5a.jpg"><img class="alignnone size-full wp-image-16250" src="http://mikefrobbins.com/wp-content/uploads/2018/01/prequel1-ironscripter5a.jpg" alt="" width="859" height="226" /></a>

I also discovered that I couldn't use traditional error handling because it wouldn't return results for any systems if any single one of them failed. What I ended up doing was to iterate through the error variable and output the errors as warnings if any were generated.

I also decided to return more information than was specified in the Iron Scripter event. I created custom formatting so only the properties specified in their provided code example would be returned by default in either a table or a list view. One change I did make is to use the word "Model" instead of "Type" for the computer and monitor model.
<pre class="lang:ps decode:true " title="MrIronScripter.ps1xml">&lt;?xml version="1.0" encoding="utf-8" ?&gt;
&lt;Configuration&gt;
    &lt;ViewDefinitions&gt;
        &lt;View&gt;
            &lt;Name&gt;Mr.MonitorInfo&lt;/Name&gt;
            &lt;ViewSelectedBy&gt;
                &lt;TypeName&gt;Mr.MonitorInfo&lt;/TypeName&gt;
            &lt;/ViewSelectedBy&gt;
            &lt;TableControl&gt;
                &lt;TableHeaders&gt;
                     &lt;TableColumnHeader&gt;
                        &lt;Width&gt;16&lt;/Width&gt;
                    &lt;/TableColumnHeader&gt;                   
                    &lt;TableColumnHeader&gt;
                        &lt;Width&gt;16&lt;/Width&gt;
                    &lt;/TableColumnHeader&gt;
                    &lt;TableColumnHeader&gt;
                        &lt;Width&gt;33&lt;/Width&gt;
                    &lt;/TableColumnHeader&gt;
                    &lt;TableColumnHeader&gt;
                        &lt;Width&gt;16&lt;/Width&gt;
                    &lt;/TableColumnHeader&gt;
                    &lt;TableColumnHeader&gt;
                        &lt;Width&gt;33&lt;/Width&gt;
                    &lt;/TableColumnHeader&gt;
                &lt;/TableHeaders&gt;
                &lt;TableRowEntries&gt;
                    &lt;TableRowEntry&gt;
                        &lt;TableColumnItems&gt;
                            &lt;TableColumnItem&gt;
                                &lt;PropertyName&gt;ComputerName&lt;/PropertyName&gt;
                            &lt;/TableColumnItem&gt;
                            &lt;TableColumnItem&gt;
                                &lt;PropertyName&gt;ComputerModel&lt;/PropertyName&gt;
                            &lt;/TableColumnItem&gt;
                            &lt;TableColumnItem&gt;
                                &lt;PropertyName&gt;ComputerSerial&lt;/PropertyName&gt;
                            &lt;/TableColumnItem&gt;
                            &lt;TableColumnItem&gt;
                                &lt;PropertyName&gt;MonitorModel&lt;/PropertyName&gt;
                            &lt;/TableColumnItem&gt;
                            &lt;TableColumnItem&gt;
                                &lt;PropertyName&gt;MonitorSerial&lt;/PropertyName&gt;
                            &lt;/TableColumnItem&gt;
                        &lt;/TableColumnItems&gt;
                    &lt;/TableRowEntry&gt;
                 &lt;/TableRowEntries&gt;
            &lt;/TableControl&gt;
        &lt;/View&gt;
        &lt;View&gt;
            &lt;Name&gt;Mr.MonitorInfo&lt;/Name&gt;
            &lt;ViewSelectedBy&gt;
                &lt;TypeName&gt;Mr.MonitorInfo&lt;/TypeName&gt;
            &lt;/ViewSelectedBy&gt;
            &lt;ListControl&gt;
                &lt;ListEntries&gt;
                    &lt;ListEntry&gt;
                        &lt;ListItems&gt;
                            &lt;ListItem&gt;
                                &lt;PropertyName&gt;ComputerName&lt;/PropertyName&gt;
                            &lt;/ListItem&gt;
                            &lt;ListItem&gt;
                                &lt;PropertyName&gt;ComputerModel&lt;/PropertyName&gt;
                            &lt;/ListItem&gt;
                            &lt;ListItem&gt;
                                &lt;PropertyName&gt;ComputerSerial&lt;/PropertyName&gt;
                            &lt;/ListItem&gt;
                            &lt;ListItem&gt;
                                &lt;PropertyName&gt;MonitorModel&lt;/PropertyName&gt;
                            &lt;/ListItem&gt;
                            &lt;ListItem&gt;
                                &lt;PropertyName&gt;MonitorSerial&lt;/PropertyName&gt;
                            &lt;/ListItem&gt;
                        &lt;/ListItems&gt;
                    &lt;/ListEntry&gt;
                &lt;/ListEntries&gt;
            &lt;/ListControl&gt;
        &lt;/View&gt;
    &lt;/ViewDefinitions&gt;
&lt;/Configuration&gt;</pre>
The following example demonstrates how those five properties default to a table and only those five properties are displayed when piping to <em>Format-List</em> unless a wildcard or other properties are specified via the Property parameter.
<pre class="lang:ps decode:true">$CimSession = New-CimSession -ComputerName DC01, SQL17, WEB01
Get-MrMonitorInfo -CimSession $CimSession
Get-MrMonitorInfo -CimSession $CimSession | Format-List
Get-MrMonitorInfo -CimSession $CimSession | Format-List -Property *</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2018/01/prequel1-ironscripter6a.jpg"><img class="alignnone size-large wp-image-16252" src="http://mikefrobbins.com/wp-content/uploads/2018/01/prequel1-ironscripter6a-846x1024.jpg" alt="" width="846" height="1024" /></a>

If you're not already, you should consider competing in <a href="https://powershell.org/2018/01/04/iron-scripter-prequel/" target="_blank" rel="noopener">these Iron Scripter prequel events</a> and <a href="http://ironscripter.us/" target="_blank" rel="noopener">the official Iron Scripter competition</a> if you're attending the <a href="https://powershell.org/summit/" target="_blank" rel="noopener">PowerShell + DevOps Global Summit 2018</a>. Competing isn't about playing a game, winning, or losing. It's about having fun while learning real world skills with a little friendly competition. I've found that I learn something every time I work through a scripting games scenario. The two big takeaways for me during this event are how to update a read-only property and splatting the same command twice or double-splatting as I'll call it as shown in the following example.
<pre class="lang:ps decode:true ">$Params = @{
    ErrorAction = 'SilentlyContinue'
    ErrorVariable = 'Problem'
}

if ($PSBoundParameters.CimSession) {
    $Params.CimSession = $CimSession
}

$CSParams = @{
    ClassName = 'Win32_ComputerSystem'
    Property = @('Name', 'Manufacturer', 'Model')
}

$BIOSParams = @{
    ClassName = 'Win32_BIOS'
    Property = 'SerialNumber'
}

$MonitorParams = @{
    ClassName = 'WmiMonitorID'
    Namespace = 'root/WMI'
    Property = @('ManufacturerName', 'UserFriendlyName', 'ProductCodeID', 'SerialNumberID', 'WeekOfManufacture', 'YearOfManufacture')
}

Get-CimInstance @Params @CSParams
Get-CimInstance @Params @BIOSParams
Get-CimInstance @Params @MonitorParams</pre>
In the end, I decided not to splat these commands twice in my function, but it's nice to know something like that is possible and probably not something I would have thought of if I hadn't been trying to solve this particular iron scripter puzzle.

Last, but not least, your solution isn't complete until you've added it to a source control system such as <a href="https://git-scm.com/" target="_blank" rel="noopener">Git</a> and shared it with the community on a site such as <a href="https://github.com/" target="_blank" rel="noopener">GitHub</a>.
<pre class="lang:ps decode:true ">git init
git add .
git commit -m 'Initial commit of MrIronScripter PowerShell module'
git remote add origin https://github.com/mikefrobbins/IronScripter.git
git remote -v
git push origin master</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2018/01/prequel1-ironscripter7a.jpg"><img class="alignnone size-full wp-image-16259" src="http://mikefrobbins.com/wp-content/uploads/2018/01/prequel1-ironscripter7a.jpg" alt="" width="859" height="426" /></a>

Prior to running the commands shown in the previous example, I created an empty and uninitialized <a href="https://github.com/mikefrobbins/IronScripter" target="_blank" rel="noopener">repository on GitHub named IronScripter</a>.

Publishing it to the <a href="https://www.powershellgallery.com/" target="_blank" rel="noopener">PowerShell Gallery</a> so others can easily install it probably wouldn't be a bad idea either.
<pre class="lang:ps decode:true ">$API = '******My-API-Key******'
Publish-Module -Name MrIronScripter -Repository PSGallery -NuGetApiKey $API
Find-Module -Name MrIronScripter</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2018/01/prequel1-ironscripter8a.jpg"><img class="alignnone size-full wp-image-16261" src="http://mikefrobbins.com/wp-content/uploads/2018/01/prequel1-ironscripter8a.jpg" alt="" width="859" height="140" /></a>

The code in this blog article can be <a href="https://www.powershellgallery.com/packages/MrIronScripter/0.0.1" target="_blank" rel="noopener">installed from the PowerShell Gallery</a> using the <em>Install-Module</em> function which is part of the PowerShellGet module that ships with PowerShell version 5.0 and higher. The PowerShellGet module can be <a href="https://github.com/PowerShell/PowerShellGet" target="_blank" rel="noopener">downloaded</a> and installed on PowerShell 3.0+.
<pre class="lang:ps decode:true ">Install-Module -Name MrIronScripter -Force
Get-Module -Name MrIronScripter -ListAvailable</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2018/01/prequel1-ironscripter9a.jpg"><img class="alignnone size-full wp-image-16263" src="http://mikefrobbins.com/wp-content/uploads/2018/01/prequel1-ironscripter9a.jpg" alt="" width="859" height="186" /></a>

In the future, I plan to start using <a href="https://github.com/PowerShell/Plaster" target="_blank" rel="noopener">Plaster</a> instead of my functions to create script modules and functions. I also plan to look into using <a href="https://github.com/PowerShell/platyPS" target="_blank" rel="noopener">platyPS</a> for creating <a href="https://en.wikipedia.org/wiki/Microsoft_Assistance_Markup_Language" target="_blank" rel="noopener">MAML</a> based help as an alternative to comment based help.

If you want to learn how to write PowerShell functions and script modules from a former winner of the advanced category in the scripting games and a multiyear recipient of both Microsoft’s MVP and SAPIEN Technologies MVP award, then you should definitely consider attending my "<a href="https://powershelldevopsglobalsummit2018.sched.com/event/CnMp/writing-award-winning-powershell-functions-and-script-modules" target="_blank" rel="noopener">Writing award winning PowerShell functions and script modules</a>" session at the <a href="https://powershell.org/summit/" target="_blank" rel="noopener">PowerShell + DevOps Global Summit 2018</a>.

µ