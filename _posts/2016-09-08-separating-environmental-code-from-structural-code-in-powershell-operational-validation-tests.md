---
ID: 14461
post_title: >
  Separating Environmental Code from
  Structural Code in PowerShell
  Operational Validation Tests
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2016/09/08/separating-environmental-code-from-structural-code-in-powershell-operational-validation-tests/
published: true
post_date: 2016-09-08 07:30:14
---
Do you ever feel like you're writing the same operational validation or readiness test over and over again? I'm not sure about you, but I don't like repeating myself by rewriting the same code because it creates a lot of technical debt. There has to be a better way &lt;period&gt;. Why not take the same thought process from DSC (Desired State Configuration) and separate the environmental portion of the code from the structural portion and apply it to operational tests so the same or similar code isn't repeated over and over again?

An article that I wrote for PowerShell Magazine on "<a href="http://www.powershellmagazine.com/2015/07/07/eliminating-redundant-code-by-writing-reusable-dsc-configurations/" target="_blank">Eliminating Redundant Code by Writing Reusable DSC Configurations</a>" provides a good overview of the thought process that I'm going to apply to operational tests.

The following example shows a function that is used to perform validation of multiple environments for a specific application on one or more servers:
<pre class="lang:ps decode:true" title="Test-MrAppServer">#Requires -Version 3.0 -Modules Pester, MrToolkit
function Test-MrAppServer {
    [CmdletBinding()]
    param (
        [Parameter(Mandatory,
                   ValueFromPipeline)]
        [string[]]$ComputerName,

        [System.Management.Automation.Credential()]$Credential = [System.Management.Automation.PSCredential]::Empty
    )

    PROCESS {
        foreach ($Computer in $ComputerName) {

            Describe "Validation of application server: $Computer" {
                try {
                    $Session = New-PSSession -ComputerName $Computer -Credential $Credential -ErrorAction Stop
                }
                catch {
                    Write-Warning -Message "Unable to connect. Aborting Pester tests for computer: '$Computer'."
                    Continue
                }                

                Context 'Validating the Training environment' {
                    It "The 'Training Runtime Server' service should be running" {
                        (Invoke-Command -Session $Session {Get-Service -Name '*Runtime Server - Train'}).status |
                        Should be 'Running'
                    }

                    It "The 'Training System Admin Server' service should be running" {
                        (Invoke-Command -Session $Session {Get-Service -Name '*System Admin Server - Train'}).status |
                        Should be 'Running'
                    }

                    It "The 'Training Runtime Server' service should be listening on port 9850" {
                        (Test-Port -Computer $Computer -Port 9850).Open |
                        Should be 'True'
                    }

                    It "The 'Training (SSL) Runtime Server' service should be listening on port 9860" {
                        (Test-Port -Computer $Computer -Port 9860).Open |
                        Should be 'True'
                    }

                    It "The 'Training System Admin Server' service should be listening on port 4234" {
                        (Test-Port -Computer $Computer -Port 4234).Open |
                        Should be 'True'
                    }

                    It "The 'Training (SSL) System Admin Server' service should be listening on port 4235" {
                        (Test-Port -Computer $Computer -Port 4235).Open |
                        Should be 'True'
                    }

                }
    
                Context 'Validating the Test environment' {
                    It "The 'Test Runtime Server' service should be running" {
                        (Invoke-Command -Session $Session {Get-Service -Name '*Runtime Server - Test'}).status |
                        Should be 'Running'
                    }

                    It "The 'Test System Admin Server' service should be running" {
                        (Invoke-Command -Session $Session {Get-Service -Name '*System Admin Server - Test'}).status |
                        Should be 'Running'
                    }

                    It "The 'Test Runtime Server' service should be listening on port 9950" {
                        (Test-Port -Computer $Computer -Port 9950).Open |
                        Should be 'True'
                    }

                    It "The 'Test (SSL) Runtime Server' service should be listening on port 9960" {
                        (Test-Port -Computer $Computer -Port 9960).Open |
                        Should be 'True'
                    }

                    It "The 'Testing System Admin Server' service should be listening on port 3234" {
                        (Test-Port -Computer $Computer -Port 3234).Open |
                        Should be 'True'
                    }

                    It "The 'Test (SSL) System Admin Server' service should be listening on port 3235" {
                        (Test-Port -Computer $Computer -Port 3235).Open |
                        Should be 'True'
                    }

                }
    
                Context 'Validating the Production environment' {
                    It "The 'Production Runtime Server' service should be running" {
                        (Invoke-Command -Session $Session {Get-Service -Name '*Runtime Server'}).status |
                        Should be 'Running'
                    }

                    It "The 'Production System Admin Server' service should be running" {
                        (Invoke-Command -Session $Session {Get-Service -Name '*System Admin Server'}).status |
                        Should be 'Running'
                    }

                    It "The 'Production Runtime Server' service should be listening on port 1234" {
                        (Test-Port -Computer $Computer -Port 1234).Open |
                        Should be 'True'
                    }

                    It "The 'Production (SSL) Runtime Server' service should be listening on port 1235" {
                        (Test-Port -Computer $Computer -Port 1235).Open |
                        Should be 'True'
                    }

                    It "The 'Production System Admin Server' service should be listening on port 9750" {
                        (Test-Port -Computer $Computer -Port 9750).Open |
                        Should be 'True'
                    }

                    It "The 'Production (SSL) System Admin Server' service should be listening on port 9760" {
                        (Test-Port -Computer $Computer -Port 9760).Open |
                        Should be 'True'
                    }

                }
    
                Context 'Validating the Report environment' {
                    It "The 'Report Runtime Server' service should be running" {
                        (Invoke-Command -Session $Session {Get-Service -Name '*Runtime Server - Report'}).status |
                        Should be 'Running'
                    }

                    It "The 'Report System Admin Server' service should be running" {
                        (Invoke-Command -Session $Session {Get-Service -Name '*System Admin Server - Report'}).status |
                        Should be 'Running'
                    }

                    It "The 'Report Runtime Server' service should be listening on port 9650" {
                        (Test-Port -Computer $Computer -Port 9650).Open |
                        Should be 'True'
                    }

                    It "The 'Report (SSL) Runtime Server' service should be listening on port 9660" {
                        (Test-Port -Computer $Computer -Port 9660).Open |
                        Should be 'True'
                    }

                    It "The 'Report System Admin Server' service should be listening on port 2234" {
                        (Test-Port -Computer $Computer -Port 2234).Open |
                        Should be 'True'
                    }

                    It "The 'Report (SSL) System Admin Server' service should be listening on port 2235" {
                        (Test-Port -Computer $Computer -Port 2235).Open |
                        Should be 'True'
                    }

                }
                
                Remove-PSSession -Session $Session    

            }
        
        }

    }

}</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2016/09/ovf-splitconfig1a.png"><img class="alignnone size-full wp-image-14464" src="http://mikefrobbins.com/wp-content/uploads/2016/09/ovf-splitconfig1a.png" alt="ovf-splitconfig1a" width="859" height="632" /></a>

Separating the environmental portion of the test is simple enough and finding items that might need to be changed is now much easier than having all of the environmental specific items buried in one huge test.
<pre class="lang:ps decode:true ">$ConfigData = @(

        @{
            Environment = 'Production'
            RtServiceName = '*Runtime Server'
            RtPort = 9750
            RtSSLPort = 9760
            AdmServiceName = '*System Admin Server'
            AdmPort = 1234
            AdmSSLPort = 1235
        }
        @{
            Environment = 'Training'
            RtServiceName = '*Runtime Server - Train'
            RtPort = 9850
            RtSSLPort = 9860
            AdmServiceName = '*System Admin Server - Train'
            AdmPort = 4234
            AdmSSLPort = 4235
        }
        @{
            Environment = 'Test'
            RtServiceName = '*Runtime Server - Test'
            RtPort = 9950
            RtSSLPort = 9960
            AdmServiceName = '*System Admin Server - Test'
            AdmPort = 3234
            AdmSSLPort = 3235
        }
        @{
            Environment = 'Report'
            RtServiceName = '*Runtime Server - Report'
            RtPort = 9650
            RtSSLPort = 9660
            AdmServiceName = '*System Admin Server - Report'
            AdmPort = 2234
            AdmSSLPort = 2235
        }

)</pre>
The structural portion of the test itself is now much more condensed and concise along with being reusable:
<pre class="lang:ps decode:true " title="Test-MrAppServer">#Requires -Version 3.0 -Modules Pester, MrToolkit
function Test-MrAppServer {
    [CmdletBinding()]
    param (
        [Parameter(Mandatory,
                   ValueFromPipeline)]
        [string[]]$ComputerName,

        [hashtable[]]$ConfigurationData,

        [System.Management.Automation.Credential()]$Credential = [System.Management.Automation.PSCredential]::Empty
    )

    PROCESS {
        foreach ($Computer in $ComputerName) {

            Describe "Validation of application server: $Computer" {
                try {
                    $Session = New-PSSession -ComputerName $Computer -Credential $Credential -ErrorAction Stop
                }
                catch {
                    Write-Warning -Message "Unable to connect. Aborting Pester tests for computer: '$Computer'."
                    Continue
                }                

                foreach ($Config in $ConfigurationData) {

                    Context "Validating $($Config.Environment) environment" {

                        It "The '$($Config.RtServiceName)' service should be running" {
                            (Invoke-Command -Session $Session {Get-Service -Name $Using:Config.RtServiceName}).status |
                            Should be 'Running'
                        }

                        It "The '$($Config.RtServiceName)' service should be listening on port $($Config.RtPort)" {
                            (Test-Port -Computer $Computer -Port $Config.RtPort).Open |
                            Should be 'True'
                        }

                        It "The '$($Config.RtServiceName) (SSL)' service should be listening on port $($Config.RtSSLPort)" {
                            (Test-Port -Computer $Computer -Port $Config.RtSSLPort).Open |
                            Should be 'True'
                        }

                        It "The '$($Config.AdmServiceName)' service should be running" {
                            (Invoke-Command -Session $Session {Get-Service -Name $Using:Config.AdmServiceName}).status |
                            Should be 'Running'
                        }

                        It "The '$($Config.AdmServiceName)' service should be listening on port $($Config.AdmPort)" {
                            (Test-Port -Computer $Computer -Port $Config.AdmPort).Open |
                            Should be 'True'
                        }

                        It "The '$($Config.AdmServiceName) (SSL)' service should be listening on port $($Config.AdmSSLPort)" {
                            (Test-Port -Computer $Computer -Port $Config.AdmSSLPort).Open |
                            Should be 'True'
                        }

                    }

                }

                Remove-PSSession -Session $Session
        
            }

        }

    }

}</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2016/09/ovf-splitconfig2a.png"><img class="alignnone size-full wp-image-14466" src="http://mikefrobbins.com/wp-content/uploads/2016/09/ovf-splitconfig2a.png" alt="ovf-splitconfig2a" width="859" height="632" /></a>

Specific modifications have been made to the code shown in these examples so that the actual application and service names aren't disclosed.

Since I haven't seen anyone else separate environmental code from structural code for operational testing before, I would like to know what your thoughts are on this subject and if you can think of any problems that it may create that I may not have considered.

µ