---
ID: 11805
post_title: >
  Using a .NET Enumeration for PowerShell
  Parameter Validation
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2015/06/15/using-a-net-enumeration-for-powershell-parameter-validation/
published: true
post_date: 2015-06-15 07:30:36
---
I recently ran into an issue where I wanted to use the values from a .NET enumeration as the default values for a parameter. That was easy enough:
<pre class="lang:ps decode:true ">function Test-ConsoleColorValidation {
    [CmdletBinding()]
    param (
        [ValidateNotNullOrEmpty()]
        [string[]]$Color = [System.Enum]::GetValues([System.ConsoleColor])
    )
    $Color
}</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/06/enum-param-validation1a.jpg"><img class="alignnone size-full wp-image-11808" src="http://mikefrobbins.com/wp-content/uploads/2015/06/enum-param-validation1a.jpg" alt="enum-param-validation1a" width="877" height="251" /></a>

Although the previous code met my initial requirements, I decided that I also wanted the user to be able to tab expand the values and to validate the values based on the list of colors found in the enumeration without a requirement of having to hard code the values.

My first thought was to use the <em>ValidateSet</em> parameter validation attribute, but it appears that would require static values to be used for the valid colors. Next, I tried using a dynamic parameter which added too much complexity for what I was trying to accomplish:
<pre class="lang:ps decode:true ">function Test-ConsoleColorValidation {

    [CmdletBinding()]
    param ()

    DynamicParam {
        $Values = [System.Enum]::GetValues([System.ConsoleColor])
        $DynParamDictionary = New-Object -TypeName System.Management.Automation.RuntimeDefinedParameterDictionary
        $ParamAttributes = New-Object -TypeName System.Management.Automation.ParameterAttribute
        $AttribColl = New-Object -TypeName System.Collections.ObjectModel.Collection[System.Attribute]
        $AttribColl.Add($ParamAttributes)
        $AttribColl.Add((New-Object -TypeName System.Management.Automation.ValidateSetAttribute($Values)))
        $DynamicParameter = New-Object -TypeName System.Management.Automation.RuntimeDefinedParameter(
            'Color',
            [string[]],
            $AttribColl
        )
        $DynParamDictionary.Add('Color', $DynamicParameter)
        $DynParamDictionary
	}

    BEGIN {
        if ($PSBoundParameters -notcontains 'Color') {
            $PSBoundParameters.Add('Color', $Values)
        }

		$PSBoundParameters.GetEnumerator() |
        ForEach-Object {New-Variable -Name $_.Key -Value $_.Value -ErrorAction SilentlyContinue}
    }

	PROCESS {
        $Color
    }
}</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/06/enum-param-validation2a.jpg"><img class="alignnone size-full wp-image-11809" src="http://mikefrobbins.com/wp-content/uploads/2015/06/enum-param-validation2a.jpg" alt="enum-param-validation2a" width="877" height="297" /></a>

I discovered that there's actually an easy way to accomplish this task while eliminating all that unnecessary complexity.

Simply use the class ([System.ConsoleColor] in this example) that describes the .NET enumeration as the parameter data type:
<pre class="lang:ps decode:true">function Test-ConsoleColorValidation {
    [CmdletBinding()]
    param (
        [ValidateNotNullOrEmpty()]
        [System.ConsoleColor[]]$Color = [System.Enum]::GetValues([System.ConsoleColor])
    )
    $Color
}</pre>
The solution shown in the previous example meets all of my requirements.

It allows all colors in the enumeration to be used as the default value when the Color parameter is not specified without having to hard code the colors:

<a href="http://mikefrobbins.com/wp-content/uploads/2015/06/enum-param-validation1a.jpg"><img class="alignnone size-full wp-image-11808" src="http://mikefrobbins.com/wp-content/uploads/2015/06/enum-param-validation1a.jpg" alt="enum-param-validation1a" width="877" height="251" /></a>

The valid values (colors in this scenario) which are part of the enumeration can be tab expanded and they show up in intellisense:

<a href="http://mikefrobbins.com/wp-content/uploads/2015/06/enum-param-validation2a.jpg"><img class="alignnone size-full wp-image-11809" src="http://mikefrobbins.com/wp-content/uploads/2015/06/enum-param-validation2a.jpg" alt="enum-param-validation2a" width="877" height="297" /></a>

If an invalid color which is not a valid color as far as the enumeration is concerned happens to be specified, a meaningful error message is generated which provides the user of the function with a list of valid values:

<a href="http://mikefrobbins.com/wp-content/uploads/2015/06/enum-param-validation3a.jpg"><img class="alignnone size-full wp-image-11810" src="http://mikefrobbins.com/wp-content/uploads/2015/06/enum-param-validation3a.jpg" alt="enum-param-validation3a" width="877" height="215" /></a>

Reference: <a href="https://msdn.microsoft.com/en-us/library/system.console(v=vs.110).aspx" target="_blank">Console Class</a> and <a href="https://msdn.microsoft.com/en-us/library/system.consolecolor(v=vs.110).aspx" target="_blank">ConsoleColor Enumeration</a> topics on MSDN.

µ