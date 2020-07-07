---
ID: 16162
post_title: >
  Adding Multiple Parameter Sets to a
  PowerShell Function
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2018/02/22/adding-multiple-parameter-sets-to-a-powershell-function/
published: true
post_date: 2018-02-22 07:30:03
---
Sometimes you need to add more than one parameter set to a function you're creating. If that's not something you're familiar with, it can be a little confusing at first. In the following example, I want to either specify the Name or Module parameter, but not both at the same time. I also want the Path parameter to be available when using either of the parameter sets.
<pre class="lang:ps decode:true ">function Test-MrMultiParamSet {
    [CmdletBinding(DefaultParameterSetName='Name')]
    param (
        [Parameter(Mandatory,
                   ParameterSetName='Name')]
        [string[]]$Name,

        [Parameter(Mandatory,
                   ParameterSetName='Module')]
        [string[]]$Module,

        [string]$Path
    )
    $PSCmdlet.ParameterSetName
}</pre>
Taking a look at the syntax shows the function shown in the previous example does indeed have two different parameter sets and the Path parameter exists in both of them. The only problem is both the Name and Module parameters are mandatory and it would be nice to have Name available positionally.
<pre class="lang:ps decode:true">Get-Command -Name Test-MrMultiParamSet -Syntax
Test-MrMultiParamSet -Name 'Testing Name Parameter Set' -Path C:\Demo\
Test-MrMultiParamSet -Module 'Testing Name Parameter Set' -Path C:\Demo\
Test-MrMultiParamSet 'Testing Name Parameter Set' -Path C:\Demo\</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2018/01/multi-param-sets1a.png"><img class="alignnone size-full wp-image-16406" src="http://mikefrobbins.com/wp-content/uploads/2018/01/multi-param-sets1a.png" alt="" width="859" height="299" /></a>

Simply specifying Name as being in position zero solves that problem.
<pre class="lang:ps decode:true ">function Test-MrMultiParamSet {
    [CmdletBinding(DefaultParameterSetName = 'Name')]
    param (
        [Parameter(Mandatory,
            ParameterSetName = 'Name',
            Position = 0)]
        [string[]]$Name,

        [Parameter(Mandatory,
            ParameterSetName = 'Module')]
        [string[]]$Module,

        [string]$Path
    )
    $PSCmdlet.ParameterSetName
}</pre>
Notice that "Name" is now enclosed in square brackets when viewing the syntax for the function. This means that it's a positional parameter and specifying the parameter name is not required as long as its value is specified in the correct position. Keep in mind that you should always use full command and parameter names in any code that you share.
<pre class="lang:ps decode:true">Get-Command -Name Test-MrMultiParamSet -Syntax
Test-MrMultiParamSet 'Testing Name Parameter Set' -Path C:\Demo\</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2018/02/multi-param-sets2a.png"><img class="alignnone size-full wp-image-16408" src="http://mikefrobbins.com/wp-content/uploads/2018/02/multi-param-sets2a.png" alt="" width="859" height="158" /></a>

While continuing to work on the parameters for this function, I decided to make the Path parameter available positionally as well as adding pipeline input support for it. I've seen others add those requirements similar to what's shown in the following example.
<pre class="lang:ps decode:true ">function Test-MrMultiParamSet {
    [CmdletBinding(DefaultParameterSetName = 'Name')]
    param (
        [Parameter(Mandatory,
            ParameterSetName = 'Name',
            Position = 0)]
        [string[]]$Name,

        [Parameter(Mandatory,
            ParameterSetName = 'Module')]
        [string[]]$Module,

        [Parameter(ParameterSetName = 'Name')]
        [Parameter(ParameterSetName = 'Module')]
        [Parameter(Mandatory,
                   ValueFromPipeline,
                   ValueFromPipelineByPropertyName,
                   Position = 1)]
        [string]$Path        
    )
    $PSCmdlet.ParameterSetName
}</pre>
This might initially seem to work, but what appears to happen is that it ignores the Parameter blocks for both the Name and Module parameter set names for the Path parameter because they are effectively blank. This is because another totally separate parameter block is specified for the Path parameter. Looking at the help for the Path parameter shows that it accepts pipeline input, but looking at the individual parameter sets seems to suggest that it doesn't. It's confused to say the least.
<pre class="lang:ps decode:true">'C:\Demo' | Test-MrMultiParamSet Test01
help Test-MrMultiParamSet -Parameter Path
(Get-Command -Name Test-MrMultiParamSet).ParameterSets.Parameters.Where({$_.Name -eq 'Path'})</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2018/02/multi-param-sets3a.png"><img class="alignnone size-full wp-image-16410" src="http://mikefrobbins.com/wp-content/uploads/2018/02/multi-param-sets3a.png" alt="" width="859" height="719" /></a>

There's honestly no reason to specify the individual parameter sets for the Path parameter if all of the options are going to be the same for all of the parameter sets.
<pre class="lang:ps decode:true ">function Test-MrMultiParamSet {
    [CmdletBinding(DefaultParameterSetName = 'Name')]
    param (
        [Parameter(Mandatory,
            ParameterSetName = 'Name',
            Position = 0)]
        [string[]]$Name,

        [Parameter(Mandatory,
            ParameterSetName = 'Module')]
        [string[]]$Module,

        [Parameter(Mandatory,
                   ValueFromPipeline,
                   ValueFromPipelineByPropertyName,
                   Position = 1)]
        [string]$Path        
    )
    $PSCmdlet.ParameterSetName
}</pre>
Removing those two empty parameter declarations above the Path parameter that reference the individual parameter sets clears up the problems.
<pre class="lang:ps decode:true">'C:\Demo' | Test-MrMultiParamSet Test01
help Test-MrMultiParamSet -Parameter Path
(Get-Command -Name Test-MrMultiParamSet).ParameterSets.Parameters.Where({$_.Name -eq 'Path'})</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2018/02/multi-param-sets5a.png"><img class="alignnone size-full wp-image-16413" src="http://mikefrobbins.com/wp-content/uploads/2018/02/multi-param-sets5a.png" alt="" width="859" height="690" /></a>

If you want to specify different options for the Path parameter to be used in different parameter sets, then you would need to explicitly specify those options as shown in the following example. To demonstrate this, I've omitted pipeline input by property name when the Module parameter set is used.
<pre class="lang:ps decode:true ">function Test-MrMultiParamSet {
    [CmdletBinding(DefaultParameterSetName = 'Name')]
    param (
        [Parameter(Mandatory,
            ParameterSetName = 'Name',
            Position = 0)]
        [string[]]$Name,

        [Parameter(Mandatory,
            ParameterSetName = 'Module')]
        [string[]]$Module,

        [Parameter(ParameterSetName = 'Name',
                   Mandatory,
                   ValueFromPipeline,
                   ValueFromPipelineByPropertyName,
                   Position = 1)]
        [Parameter(ParameterSetName = 'Module',
                   Mandatory,
                   ValueFromPipeline,
                   Position = 1)]
        [string]$Path        
    )
    $PSCmdlet.ParameterSetName
}</pre>
Now everything looks correct.
<pre class="lang:ps decode:true">'C:\Demo' | Test-MrMultiParamSet Test01
help Test-MrMultiParamSet -Parameter Path
(Get-Command -Name Test-MrMultiParamSet).ParameterSets.Parameters.Where({$_.Name -eq 'Path'})</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2018/02/multi-param-sets4a.png"><img class="alignnone size-full wp-image-16411" src="http://mikefrobbins.com/wp-content/uploads/2018/02/multi-param-sets4a.png" alt="" width="859" height="689" /></a>

For more information about using multiple parameter sets in your functions, see the <a href="https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_functions_advanced_parameters" target="_blank" rel="noopener">about_Functions_Advanced_Parameters</a> help topic.

µ