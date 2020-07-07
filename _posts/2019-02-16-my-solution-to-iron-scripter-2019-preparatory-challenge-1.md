---
ID: 17571
post_title: >
  My Solution to Iron Scripter 2019
  Preparatory Challenge 1
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2019/02/16/my-solution-to-iron-scripter-2019-preparatory-challenge-1/
published: true
post_date: 2019-02-16 07:30:27
---
Anyone who has competed in the scripting games before knows that I'm always looking for a challenge when it comes to writing PowerShell code. While the scripting games haven't been held in the last several years, they've somewhat been replaced by the Iron Scripter competition at the <a href="https://powershell.org/summit/" target="_blank" rel="noopener">PowerShell + DevOps Global Summit</a> and 2019 is shaping up to be no different. Think you've got skills? Bring them on! and Get-Involved.

Whether you've previously competed or not, you should definitely head over to <a href="https://ironscripter.us/iron-scripter-2019-is-coming/" target="_blank" rel="noopener">IronScripter.us</a> to see what it's all about. I'm not sure if there will be more than one practice scenario or not since I'm in no way officially affiliated with the competition other than being a participant. This particular scenario deals with the <em>Get-Counter</em> cmdlet which I used in my chapter on <em>Finding Performance Bottlenecks with PowerShell</em> in <a href="https://leanpub.com/powershell-conference-book" target="_blank" rel="noopener">The PowerShell Conference Book</a> (you can read my chapter in its entirety by downloading the free sample).

The challenge is to create a PowerShell function that <em>Get-Counter</em> can be piped to which produces friendlier output and returns specific properties. I used a regular expression in my chapter from the previously referenced book. I simply reused it for this portion of the challenge.
<pre class="lang:ps decode:true " title="Format-MrCounter">function Format-MrCounter {

    [CmdletBinding()]
    param (
        [Parameter(Mandatory, 
                    ValueFromPipeline)]
        [Microsoft.PowerShell.Commands.GetCounter.PerformanceCounterSampleSet]$CounterSampleSet
    )

    foreach ($Counter in $CounterSampleSet.CounterSamples){

        $ComputerName = $Counter.Path -replace '^\\\\|\\.*$'

        [pscustomobject]@{
            DateTime = $Counter.Timestamp
            ComputerName = $ComputerName
            CounterSet = $Counter.Path -replace "^\\\\$ComputerName\\|\\.*$"
            Counter = $Counter.Path -replace '^.*\\'
            Value = $Counter.CookedValue
        }

    }
      
}</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2019/02/ironscripter2019prep1a.jpg"><img class="alignnone size-full wp-image-17579" src="https://mikefrobbins.com/wp-content/uploads/2019/02/ironscripter2019prep1a.jpg" alt="" width="859" height="486" /></a>

I try to avoid the <em>ComputerName</em> parameter of commands when targeting remote systems because they're usually implemented via older DCOM protocols which are typically blocked by firewalls on modern systems. I simply use <em>Invoke-Command</em> to run commands locally on remote systems via PowerShell remoting since WinRM is much more firewall friendly. Because of this design philosophy, I never tested my regular expressions when targeting remote systems with <em>Get-Counter</em>'s <em>ComputerName</em> parameter. Well, they don't work because of an extra slash that exists after the computer name in the results when targeting a remote system.
<pre class="lang:ps decode:true ">(Get-Counter).CounterSamples.Path
(Get-Counter -ComputerName DC01).CounterSamples.Path</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2019/02/ironscripter2019prep2a.jpg"><img class="alignnone size-full wp-image-17580" src="https://mikefrobbins.com/wp-content/uploads/2019/02/ironscripter2019prep2a.jpg" alt="" width="859" height="255" /></a>

While I could rework the regex to take that into account, I've been there and done that so I thought I would take a different approach and eliminate the regular expressions altogether. While I'm at it, I'll also tackle the advanced portion of the challenge which wants a custom type and view defined to return four specific properties by default in a table.

I'm writing my code in a way that requires PowerShell version 3.0 or higher and since <em>Get-Counte</em>r only exists in Windows PowerShell, I'll define both of those requirements in a <a href="https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_requires" target="_blank" rel="noopener">requires statement</a>. I'm using <a href="https://docs.microsoft.com/en-us/powershell/developer/cmdlet/approved-verbs-for-windows-powershell-commands" target="_blank" rel="noopener">an approved verb</a> along with a singular noun and the command name is in Pascal case. This is followed by comment based help. <em>CmdletBinding</em> is declared which makes it an advanced function, the output type is declared, and then the single <em>InputObject</em> parameter which only accepts the type of object that <em>Get-Counter</em> produces. The parameter is mandatory and accepts input via the pipeline by value (what I call by type).
<pre class="lang:ps decode:true" title="Format-MrCounter">#Requires -Version 3.0 -PSEdition Desktop
function Format-MrCounter {

&lt;#
.SYNOPSIS
    Formats the output of the Get-Counter cmdlet into a friendlier format.
 
.DESCRIPTION
    The Format-MrCounter function accepts input from the Get-Counter function
    and formats it into a much more useable and more object oriented format.
 
.PARAMETER InputObject
    Accepts the output of the results from the Get-Counter cmdlet. It expects a
    Microsoft.PowerShell.Commands.GetCounter.PerformanceCounterSampleSet object type.
 
.EXAMPLE
    Get-Counter | Format-MrCounter

.EXAMPLE
    Get-Counter -ComputerName Server01, Server02 | Format-MrCounter

.INPUTS
    Microsoft.PowerShell.Commands.GetCounter.PerformanceCounterSampleSet
 
.OUTPUTS
    Mr.CounterInfo
 
.NOTES
    Author:  Mike F Robbins
    Website: http://mikefrobbins.com
    Twitter: @mikefrobbins
#&gt;

    [CmdletBinding()]
    [OutputType('Mr.CounterInfo')]
    param (
        [Parameter(Mandatory, 
                   ValueFromPipeline)]
        [Microsoft.PowerShell.Commands.GetCounter.PerformanceCounterSampleSet[]]$InputObject
    )

    PROCESS {

        foreach ($Counter in $InputObject.CounterSamples){

            $CounterInfo = $Counter.Path.Split('\\', [System.StringSplitOptions]::RemoveEmptyEntries)

            for ($i = 0; $i -lt $CounterInfo.Count; $i += 3){
                [pscustomobject]@{
                    DateTime = $Counter.Timestamp
                    ComputerName = $CounterInfo[$i]
                    CounterSet = $CounterInfo[$i+1]
                    Counter = $CounterInfo[$i+2]
                    Value = $Counter.CookedValue
                    PSTypeName = 'Mr.CounterInfo'
                } |
                Add-Member -MemberType MemberSet -Name PSStandardMembers -Value (
                    [System.Management.Automation.PSMemberInfo[]]@(
                        New-Object -TypeName System.Management.Automation.PSPropertySet(
                            'DefaultDisplayPropertySet',[string[]]@(
                                'ComputerName', 'CounterSet', 'Counter', 'Value'
                            )
                        )
                    )
                ) -PassThru
            }
        
        }

    }
      
}</pre>
I'm splitting the <em>Path</em> property on backslashes, but since there are some double backslashes, I'm using the <em>Split</em> method instead of the <em>Split</em> operator so I can take advantage of the .NET option to remove empty entries. A <em>For</em> loop is used to iterate through the entries. <em>PSCustomObject</em> is used to create a custom object and a type name is specified within the <em>PSCustomObject</em> to define a custom object type. Finally, since only four properties are required in the default output and four or fewer properties are displayed in a table by default, a <em>Default Display Property Set</em> is defined instead of going through the trouble of creating a module, manifest, and custom format file in XML.

By default, the output is the four properties required by the advanced challenge specifications. All of the properties can always be viewed in either a list (or table) when piping to either <em>Format-List</em>, <em>Format-Table</em>, or <em>Select-Object</em> and specifying the asterisk wildcard (*) as the value for the <em>Property</em> parameter.
<pre class="lang:ps decode:true ">Get-Counter -ComputerName DC01 | Format-MrCounter
Get-Counter -ComputerName DC01 | Format-MrCounter | Format-List -Property *</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2019/02/ironscripter2019prep3a.jpg"><img class="alignnone size-full wp-image-17586" src="https://mikefrobbins.com/wp-content/uploads/2019/02/ironscripter2019prep3a.jpg" alt="" width="859" height="404" /></a>

The <em>Format-MrCounter</em> function shown in this blog article can be downloaded from <a href="https://github.com/mikefrobbins/IronScripter" target="_blank" rel="noopener">my IronScripter repository</a> on GitHub. I'd love to hear your thoughts. Please post your questions, comments, and/or suggestions as a comment to this blog article.

If you enjoyed this blog article and the techniques I used in my solution, then I hope to see you in my <a href="https://app.socio.events/MjQ4Nw/agenda/14445/session/61456" target="_blank" rel="noopener"><em>Finding Performance Bottlenecks with PowerShell</em></a> session at the PowerShell + DevOps Global Summit 2019.

µ