---
ID: 17846
post_title: >
  Using the AST to Find Module
  Dependencies in PowerShell Functions and
  Scripts
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2019/05/17/using-the-ast-to-find-module-dependencies-in-powershell-functions-and-scripts/
published: true
post_date: 2019-05-17 07:30:32
---
Earlier this week, <a href="https://twitter.com/HalbaradKenafin" target="_blank" rel="noopener noreferrer">Chris Gardner</a> presented a session on "<a href="https://mspsug.com/2019/05/09/mspsug-may-2019-virtual-meeting-lean-on-me-managing-dependencies-in-powershell/" target="_blank" rel="noopener noreferrer">Managing dependencies in PowerShell</a>" for the <a href="https://mspsug.com/" target="_blank" rel="noopener noreferrer">Mississippi PowerShell User Group</a>. I mentioned that I had written a function to retrieve PowerShell module dependencies that's part of <a href="https://github.com/mikefrobbins/ModuleBuildTools" target="_blank" rel="noopener noreferrer">my ModuleBuildTools module</a>.

<em>Get-MrAST</em> is one of the primary functions that numerous other functions in the module are built on.
<pre class="lang:ps decode:true " title="Get-MrAst">#Requires -Version 3.0
function Get-MrAst {

&lt;#
.SYNOPSIS
    Explores the Abstract Syntax Tree (AST).
 
.DESCRIPTION
    Get-MrAST is an advanced function that provides a mechanism for exploring the Abstract Syntax Tree (AST).
 
 .PARAMETER Path
    Specifies a path to one or more locations. Wildcards are permitted. The default location is the current directory.

.PARAMETER Code
    The code to view the AST for. If Get-Content is being used to obtain the code, use its -Raw parameter otherwise
    the formating of the code will be lost.

.PARAMETER ScriptBlock
    An instance of System.Management.Automation.ScriptBlock Microsoft .NET Framework type to view the AST for.

.PARAMETER AstType
    The type of object to view the AST for. If this parameter is ommited, only the top level ScriptBlockAst is returned.
 
.EXAMPLE
     Get-MrAST -Path 'C:\Scripts' -AstType FunctionDefinition

.EXAMPLE
     Get-MrAST -Code 'function Get-PowerShellProcess {Get-Process -Name PowerShell}'

.EXAMPLE
     Get-MrAST -ScriptBlock ([scriptblock]::Create('function Get-PowerShellProcess {Get-Process -Name PowerShell}'))
 
.NOTES
    Author:  Mike F Robbins
    Website: http://mikefrobbins.com
    Twitter: @mikefrobbins
#&gt;

    [CmdletBinding(DefaultParameterSetName='Path')]
    param(
        [Parameter(ValueFromPipeline,
                   ValueFromPipelineByPropertyName,
                   ValueFromRemainingArguments,
                   ParameterSetName = 'Path',
                   Position = 1)]
        [ValidateNotNull()]
        [Alias('FilePath')]
        [string[]]$Path = ('.\*.ps1', '.\*.psm1'),

        [Parameter(Mandatory,
                   ValueFromPipelineByPropertyName,
                   ValueFromRemainingArguments,
                   ParameterSetName = 'Code')]
        [string[]]$Code,

        [Parameter(Mandatory,
                   ValueFromPipelineByPropertyName,
                   ValueFromRemainingArguments,
                   ParameterSetName = 'ScriptBlock')]
        [scriptblock[]]$ScriptBlock
    )
 
    DynamicParam {
        $ParameterAttribute = New-Object -TypeName System.Management.Automation.ParameterAttribute
        $ParameterAttribute.Position = 0

        $ValidationValues = Get-MrAstType
        $ValidateSetAttribute = New-Object -TypeName System.Management.Automation.ValidateSetAttribute($ValidationValues)

        $AttributeCollection = New-Object -TypeName System.Collections.ObjectModel.Collection[System.Attribute]
        $AttributeCollection.Add($ParameterAttribute)
        $AttributeCollection.Add($ValidateSetAttribute)

        $ParameterName = 'AstType'
        $RuntimeParameter = New-Object -TypeName System.Management.Automation.RuntimeDefinedParameter($ParameterName, [string], $AttributeCollection)            
            
        $RuntimeParameterDictionary = New-Object -TypeName System.Management.Automation.RuntimeDefinedParameterDictionary            
        $RuntimeParameterDictionary.Add($ParameterName, $RuntimeParameter)
        $RuntimeParameterDictionary
    }

    BEGIN {
        $AstType = $PsBoundParameters[$ParameterName]
    }

    PROCESS {
        switch ($PSCmdlet.ParameterSetName) {
            'Path' {
                Write-Verbose -Message 'Path Parameter Set Selected'
                Write-Verbose "Path contains $Path"
                
                $Files = Get-ChildItem -Path $Path -Exclude *tests.ps1, *profile.ps1 |
                         Select-Object -ExpandProperty FullName
                
                if (-not ($Files)) {
                    Write-Warning -Message 'No valid files found.'
                    Return
                }

                $AST = foreach ($File in $Files) {
                    [System.Management.Automation.Language.Parser]::ParseFile($File, [ref]$null, [ref]$null)
                }

                break
            }
            'Code' {
                Write-Verbose -Message 'Code Parameter Set Selected'

                $AST = foreach ($c in $Code) {
                    [System.Management.Automation.Language.Parser]::ParseInput($c, [ref]$null, [ref]$null)
                }

                break
            }
            'ScriptBlock' {
                Write-Verbose -Message 'ScriptBlock Parameter Set Selected'
                
                $AST = $ScriptBlock.Ast
                
                break
            }
            default {
                Write-Warning -Message 'An unexpected error has occurred'
            }
        }

        if ($PsBoundParameters.AstType) {
            Write-Verbose -Message 'AstType Parameter Entered'
            
            $AST = $AST.FindAll({$args[0].GetType().Name -like "$($ASTType)Ast"}, $true)
        }

        Write-Output $AST
    }

}</pre>
It retrieves the AST from one or more PS1 and/or PSM1 files, script blocks, or random arbitrary code.
<pre class="lang:ps decode:true">Get-MrAst -Path .\Hyper-V\MrHyperV\public\</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2019/05/module-depends1a.jpg"><img class="alignnone size-full wp-image-17847" src="https://mikefrobbins.com/wp-content/uploads/2019/05/module-depends1a.jpg" alt="" width="859" height="167" /></a>

It can also be used to only retrieve specific AST types such as the definition of one or more functions.
<pre class="lang:ps decode:true">Get-MrAst -Path .\Hyper-V\MrHyperV\public\ -AstType FunctionDefinition</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2019/05/module-depends2a.jpg"><img class="alignnone size-full wp-image-17848" src="https://mikefrobbins.com/wp-content/uploads/2019/05/module-depends2a.jpg" alt="" width="859" height="205" /></a>

<em>Get-MrPrivateCommand</em> is another function in the module which is used to show private functions in a module.
<pre class="lang:ps decode:true ">#Requires -Version 3.0
function Get-MrPrivateCommand {

&lt;#
.SYNOPSIS
    Returns a list of private (unexported) commands from the specified module or snap-in.
 
.DESCRIPTION
    Get-MrPrivateFunction is an advanced function that returns a list of private commands
    that are not exported from the specified module or snap-in.
 
.PARAMETER Module
    Specify the name of a module. Enter the name of a module or snap-in, or a snap-in or module
    object. This parameter takes string values, but the value of this parameter can also be a
    PSModuleInfo or PSSnapinInfo object, such as the objects that the Get-Module, Get-PSSnapin,
    and Import-PSSession cmdlets return.
 
.EXAMPLE
     Get-MrPrivateCommand -Module Pester
 
.NOTES
    Author:  Mike F Robbins
    Website: http://mikefrobbins.com
    Twitter: @mikefrobbins
#&gt;

    [CmdletBinding()]
    param (
        [Parameter(Mandatory)]
        [Alias('PSSnapin')]
        [string]$Module
    )

    if (-not((Get-Module -Name $Module -OutVariable ModuleInfo))){
        try {
            $ModuleInfo = Import-Module -Name $Module -Force -PassThru -ErrorAction Stop
        }
        catch {
            Write-Warning -Message "$_.Exception.Message"
            Break
        }
    }

    $Global:ModuleName = $Module

    $All = $ModuleInfo.Invoke({Get-Command -Module $ModuleName -All})
    
    $Exported = (Get-Module -Name $Module -All).ExportedCommands |
                Select-Object -ExpandProperty Values

    if ($All -and $Exported) {
        Compare-Object -ReferenceObject $All -DifferenceObject $Exported |
        Select-Object -ExpandProperty InputObject |
        Add-Member -MemberType NoteProperty -Name Visibility -Value Private -Force -PassThru
    }    
}</pre>
Notice the private function named <em>Get-MrFunctionRequirment.</em>
<pre class="lang:ps decode:true">Get-MrPrivateCommand -Module MrModuleBuildTools</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2019/05/module-depends4a.jpg"><img class="alignnone size-full wp-image-17849" src="https://mikefrobbins.com/wp-content/uploads/2019/05/module-depends4a.jpg" alt="" width="859" height="189" /></a>

<em>Get-MrFunctionRequirment</em> is a helper function that's sandwiched between <em>Get-MrAST</em> and <em>Get-MrRequiredModule.</em>
<pre class="lang:ps decode:true " title="Get-MrFunctionRequirement">function Get-MrFunctionRequirement {
    [CmdletBinding(DefaultParameterSetName='File')]
    param(
        [Parameter(ValueFromPipeline,
                   ValueFromPipelineByPropertyName,
                   ValueFromRemainingArguments,
                   ParameterSetName = 'File',
                   Position = 0)]
        [ValidateNotNullOrEmpty()]
        [Alias('FilePath')]
        [string[]]$Path = ('.\*.ps1', '.\*.psm1'),

        [Parameter(ValueFromPipelineByPropertyName,
                   ValueFromRemainingArguments,
                   ParameterSetName = 'Code',
                   Position = 0)]
        [ValidateNotNull()]
        [Alias('ScriptBlock')]
        [string[]]$Code
    )

    PROCESS {
        if ($PSBoundParameters.Path) {
            Write-Verbose 'Path'
            $Results = Get-MrAST -Path $Path
        }
        elseif ($PSBoundParameters.Code) {
            Write-Verbose 'Code'
            $Results = Get-MrAST -Code $Code
        }
        else {
            Write-Verbose -Message 'Valid input not received.'
        }
        
        $Results | Select-Object -ExpandProperty ScriptRequirements | Sort-Object -Property * -Unique
    }

}</pre>
I'll use a little known trick in this example to run the <em>Get-MrFunctionRequirment</em> private function from outside of the module by invoking it within module scope. This is the same trick I use to determine the private functions in a module by comparing the exported commands to the ones that exist in module scope.
<pre class="lang:ps decode:true">(Get-Module -Name MrModuleBuildTools).Invoke({Get-MrFunctionRequirement -Path .\Hyper-V\MrHyperV\public\})</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2019/05/module-depends5a.jpg"><img class="alignnone size-full wp-image-17850" src="https://mikefrobbins.com/wp-content/uploads/2019/05/module-depends5a.jpg" alt="" width="859" height="455" /></a>

The <em>Get-MrRequiredModule</em> function is used to determine the required modules for one or more files or blocks of code.
<pre class="lang:ps decode:true ">#Requires -Version 3.0
function Get-MrRequiredModule {

&lt;#
.SYNOPSIS
    Gets a list of the required modules.
 
.DESCRIPTION
    Get-MrRequiredModule is an advanced function that returns a list of the required module dependencies from one or more
    PS1 and/or PSM1 files.
 
 .PARAMETER Path
    Specifies a path to one or more locations. Wildcards are permitted. The default location is the current directory.

.PARAMETER Code
    The code to get the required modules for. If Get-Content is being used to obtain the code, use its -Raw parameter
    otherwise the formating of the code will be lost.

.PARAMETER Detailed
    Return a detailed list of all of the modules including built-in modules that are required. This option does not
    reply on a Requires statement.
 
.EXAMPLE
     Get-MrRequiredModule -Path 'C:\Scripts'

.EXAMPLE
     Get-MrRequiredModule -Code 'function Get-PowerShellProcess {Get-Process -Name PowerShell}' -Detailed
 
.NOTES
    Author:  Mike F Robbins
    Website: http://mikefrobbins.com
    Twitter: @mikefrobbins
#&gt;    

    [CmdletBinding(DefaultParameterSetName='File')]
    param(
        [Parameter(ValueFromPipeline,
                   ValueFromPipelineByPropertyName,
                   ValueFromRemainingArguments,
                   ParameterSetName = 'File',
                   Position = 0)]
        [ValidateNotNullOrEmpty()]
        [Alias('FilePath')]
        [string[]]$Path = ('.\*.ps1', '.\*.psm1'),

        [Parameter(ValueFromPipelineByPropertyName,
                   ValueFromRemainingArguments,
                   ParameterSetName = 'Code',
                   Position = 0)]
        [ValidateNotNull()]
        [Alias('ScriptBlock')]
        [string[]]$Code,

        [switch]$Detailed
    )
    
    PROCESS{
        if (-not($PSBoundParameters.Detailed)) {
            (Get-MrFunctionRequirement -Path $Path |
             Select-Object -ExpandProperty RequiredModules -Unique).Name
        }
        else {
            $PSBoundParameters.Remove('Detailed') | Out-Null
            $AllAST = Get-MrAst @PSBoundParameters
            
            foreach ($AST in $AllAST){
                $FunctionDefinition = $AST.FindAll({$args[0].GetType().Name -like 'FunctionDefinitionAst'}, $true)
                $Commands = $AST.FindAll({$args[0].GetType().Name -like 'CommandAst'}, $true) | ForEach-Object {$_.CommandElements[0].Value} | Select-Object -Unique
                
                foreach ($Command in $Commands){
                    [pscustomobject]@{
                        Function = $FunctionDefinition.Name
                        Dependency = $Command
                        Module = (Get-Command -Name $Command -ErrorAction SilentlyContinue).Source
                    }
                }               
               
            }

        }
    }
}</pre>
When the <em>Get-MrRequiredModule</em> is used without its <em>Detailed</em> parameter, it simply returns what's listed in the <em>Requires</em> statement of the individual PS1 and/or PSM1 files.
<pre class="lang:ps decode:true">Get-MrRequiredModule -Path .\Hyper-V\MrHyperV\public\</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2019/05/module-depends6a.jpg"><img class="alignnone size-full wp-image-17851" src="https://mikefrobbins.com/wp-content/uploads/2019/05/module-depends6a.jpg" alt="" width="859" height="89" /></a>

When its <em>Detailed</em> parameter is specified, it retrieves a list of all the commands used in the files and then returns the name of the function along with the module that the command is part of including built-in modules. This does require the module to exist on your system, but it's great for figuring out what to add to the <em>Requires</em> statement. Of course, there's no reason to add built-in modules to the requires statement.
<pre class="lang:ps decode:true ">Get-MrRequiredModule -Path .\Hyper-V\MrHyperV\public\ -Detailed</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2019/05/module-depends7a.jpg"><img class="alignnone size-full wp-image-17852" src="https://mikefrobbins.com/wp-content/uploads/2019/05/module-depends7a.jpg" alt="" width="859" height="523" /></a>

As you can see, the previous example also shows how easy it would be to retrieve a list of all the commands used in a function or script with the AST for auditing purposes. Want to know if someone wrote a function that uses <em>Write-Host?</em> That would be no problem whatsoever for an entire module or hundreds of functions or scripts.

The MrModuleBuildTools PowerShell module along with all code found in this blog article can be found in <a href="https://github.com/mikefrobbins/ModuleBuildTools" target="_blank" rel="noopener noreferrer">my ModuleBuildTools repository on GitHub</a>.

Thoughts, questions, comments? Please post them as a comment to this blog article.

µ