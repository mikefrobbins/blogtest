---
ID: 15402
post_title: >
  Remove all user defined variables
  without restarting the PowerShell
  Console or ISE
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2017/07/20/remove-all-user-defined-variables-without-restarting-the-powershell-console-or-ise/
published: true
post_date: 2017-07-20 07:30:55
---
Recently, fellow Microsoft MVP <a href="https://twitter.com/mickey_gousset" target="_blank" rel="noopener">Mickey Gousset</a> asked me how to remove existing user defined variables from the PowerShell ISE (Integrated Scripting Environment) before running a script a second time without having to restart the ISE.

While you could keep track of the variables you've used within your script to remove them once the script completes with the <em>Remove-Variable</em> cmdlet or by deleting them from the variable PSDrive, that can be a less than desirable solution for long and complicated scripts that define lots of variables. You could also save your script as a PS1 file and run the PS1 file instead of running it interactively in the ISE. If the PS1 file is run, the variables will be defined in the script scope by default and when the script completes, the script scope is removed along with the variables as long as you haven't coerced them into the global scope.

A better solution is to use a variable to either store a list of the default variables when PowerShell starts or before your script runs. Be sure you understand the precedence of the different PowerShell profiles since you maybe defining variables in some of your profiles that you don't want to remove. See my blog article "<a href="http://mikefrobbins.com/2015/07/02/powershell-profile-the-six-options-and-their-precedence/" target="_blank" rel="noopener">PowerShell $Profile: The six options and their precedence</a>".

I'll place the following code in my profile for the ISE ($profile.AllUsersCurrentHost or $profile.CurrentUserCurrentHost from within the ISE):
<pre class="lang:ps decode:true ">$StartupVars = @()
$StartupVars = Get-Variable | Select-Object -ExpandProperty Name</pre>
Not sure where the profile is or how to add this code to it? The following code can be used to automatically add the necessary items to the all users ISE profile:
<pre class="lang:ps decode:true ">if ($Host.Name -eq 'Windows PowerShell ISE Host') {
    if (-not(Test-Path -Path $profile.AllUsersCurrentHost)) {
        New-Item -Path $profile.AllUsersCurrentHost -ItemType File
    }
    if (-not(Get-Content -Path $profile.AllUsersCurrentHost |
             Select-String -SimpleMatch '$StartupVars = Get-Variable | Select-Object -ExpandProperty Name')) {
        Add-Content -Path $profile.AllUsersCurrentHost -Value '
$StartupVars = @()
$StartupVars = Get-Variable | Select-Object -ExpandProperty Name'
    }
}
else {
    Write-Warning -Message 'This code can only be run from within the PowerShell ISE'
}</pre>
This could even be taken a step further and turned unto a function to add those two lines of code to any of the available profiles. I've removed the check to make sure it's run in the ISE since the same process could be used to remove user defined variables in the console.
<pre class="lang:ps decode:true">#Requires -Version 3.0
function Add-MrStartupVariable {

&lt;#
.SYNOPSIS
    Add variable for list of startup variables.
 
.DESCRIPTION
    Add variable for list of startup variables to the specified PowerShell profile. Create the specified PowerShell
    profile if it does not exist. Only adds the variable and code to populate the variable if it does not already exist.
    Designed to be used in conjunction with Remove-MrUserVariable.
 
.PARAMETER Location
    Location of the PowerShell profile to add the startup variable to.
 
.EXAMPLE
     Add-MrStartupVariable -Location AllUsersCurrentHost

.INPUTS
    None
 
.OUTPUTS
    None
 
.NOTES
    Author:  Mike F Robbins
    Website: http://mikefrobbins.com
    Twitter: @mikefrobbins
#&gt;

    [CmdletBinding(SupportsShouldProcess)]
    param (
        [Parameter(Mandatory)]
        [ValidateSet('AllUsersAllHosts', 'AllUsersCurrentHost', 'CurrentUserAllHosts', 'CurrentUserCurrentHost')]
        $Location
    )

    $Content = @'
$StartupVars = @()
$StartupVars = Get-Variable | Select-Object -ExpandProperty Name
'@

    if (-not(Test-Path -Path $profile.$Location)) {
        New-Item -Path $profile.$Location -ItemType File |
        Set-Content -Value $Content
    }
    elseif (-not(Get-Content -Path $profile.$Location |
             Select-String -SimpleMatch '$StartupVars = Get-Variable | Select-Object -ExpandProperty Name')) {
        Add-Content -Path $profile.$Location -Value "`r`n$Content"
    }
    else {
        Write-Verbose -Message "`$StartupVars already exists in '$($profile.$Location)'"
    }

}</pre>
Write a script defining whatever variables you want. Run the script interactively by pressing F5 from within the ISE. I'll use the following code for simplicity:
<pre class="lang:ps decode:true ">$Processes = Get-Process
$Services = Get-Service</pre>
My first thought was to use the <em>Compare-Object</em> cmdlet, retrieve a new list of all the variables, compare that list to the original, and remove the results. That's a little more complicated than simply getting a list of variables while excluding the original ones and then simply piping the results to <em>Remove-Variable</em>:
<pre class="lang:ps decode:true ">Get-Variable -Exclude $StartupVars |
Remove-Variable -Force</pre>
I'll turn that code into a function so I can simply call it without having to remember any syntax or the variable name:
<pre class="lang:ps decode:true">#Requires -Version 3.0
function Remove-MrUserVariable {

&lt;#
.SYNOPSIS
    Removes user defined variables.
 
.DESCRIPTION
    Removes user defined variables from the PowerShell ISE or console. $StartupVars must be defined prior to running
    this function, preferably in a profile script. Populate $StartUpVars with 'Get-Variable | Select-Object -ExpandProperty
    Name'. All variables added after populating $StartupVars will be removed when this function is run.
 
.EXAMPLE
     Remove-MrUserVariable

.INPUTS
    None
 
.OUTPUTS
    None
 
.NOTES
    Author:  Mike F Robbins
    Website: http://mikefrobbins.com
    Twitter: @mikefrobbins
#&gt;

    [CmdletBinding(SupportsShouldProcess)]
    param ()

    if ($StartupVars) {
        $UserVars = Get-Variable -Exclude $StartupVars -Scope Global
        
        foreach ($var in $UserVars){
            try {
                Remove-Variable -Name $var.Name -Force -Scope Global -ErrorAction Stop
                Write-Verbose -Message "Variable '$($var.Name)' has been successfully removed."
            }
            catch {
                Write-Warning -Message "An error has occured. Error Details: $($_.Exception.Message)"
            }            
            
        }
        
    }
    else {
        Write-Warning -Message '$StartupVars has not been added to your PowerShell profile'
    }    

}</pre>
Running the function with the <em>Verbose</em> parameter shows that the two previously defined variables have been successfully removed:
<pre class="lang:ps decode:true ">Remove-MrUserVariable -Verbose</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2017/07/remove-vars1a.jpg"><img class="alignnone size-full wp-image-15406" src="http://mikefrobbins.com/wp-content/uploads/2017/07/remove-vars1a.jpg" alt="" width="858" height="199" /></a>

The functions shown in this blog article can be installed from the PowerShell Gallery as part of <a href="https://www.powershellgallery.com/packages/MrToolkit/1.3" target="_blank" rel="noopener">my MrToolkit PowerShell module</a> (version 1.3 and higher) with the <em>Install-Module</em> cmdlet that is part of the PowerShellGet module. PowerShellGet exists by default on PowerShell version 5.0 and higher and can be <a href="https://github.com/PowerShell/PowerShellGet" target="_blank" rel="noopener">downloaded</a> for PowerShell version 3.0 and higher.
<pre class="lang:ps decode:true ">Find-Module -Name MrToolKit
Find-Module -Name MrToolKit | Install-Module -Force</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2017/07/install-mrtoolkit1a.jpg"><img class="alignnone size-full wp-image-15411" src="http://mikefrobbins.com/wp-content/uploads/2017/07/install-mrtoolkit1a.jpg" alt="" width="859" height="140" /></a>

The functions shown in this blog article have also been added to <a href="https://github.com/mikefrobbins/PowerShell" target="_blank" rel="noopener">my PowerShell repository on GitHub.</a>

µ