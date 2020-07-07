---
ID: 17341
post_title: 'PowerShell Script Module Design: Building Tools to Automate the Process'
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2018/11/01/powershell-script-module-design-building-tools-to-automate-the-process/
published: true
post_date: 2018-11-01 07:30:39
---
As I previously mentioned a little over a month ago in my blog article "<a href="https://mikefrobbins.com/2018/09/21/powershell-script-module-design-philosophy/" target="_blank" rel="noopener">PowerShell Script Module Design Philosophy</a>", I'm transitioning my module build process to a non-monolithic design in development and a monolithic design for production to take advantage of the best of both worlds. Be sure to read the previously referenced blog article for more details on the subject.

My goal is to write a reusable tool to retrieve the necessary information from a non-monolithic script module that's necessary to create a monolithic version of the same module. Initially, I wrote some horrendous code to get the necessary information and finally decided that I needed to learn about the Abstract Syntax Tree (AST). Be sure to read the blog articles I've written about the AST over the past few weeks:
<ul>
 	<li><a href="https://mikefrobbins.com/2018/09/28/learning-about-the-powershell-abstract-syntax-tree-ast/" target="_blank" rel="noopener">Learning about the PowerShell Abstract Syntax Tree (AST)</a></li>
 	<li><a href="https://mikefrobbins.com/2018/10/24/learn-about-the-powershell-abstract-syntax-tree-ast-part-2/" target="_blank" rel="noopener">Learn about the PowerShell Abstract Syntax Tree (AST) – Part 2</a></li>
 	<li><a href="https://mikefrobbins.com/2018/10/25/learn-about-the-powershell-abstract-syntax-tree-ast-part-3/" target="_blank" rel="noopener">Learn about the PowerShell Abstract Syntax Tree (AST) – Part 3</a></li>
</ul>
While there are multiple ways of retrieving the necessary information, the easiest is to simply use the PowerShell Abstract Syntax Tree (AST) otherwise this process could be more convoluted than trying to put Humpty Dumpty back together again.

First, I’ll create a helper function to get all of the AST types. This function is based off of some of the code I wrote in <a href="https://mikefrobbins.com/2018/10/25/learn-about-the-powershell-abstract-syntax-tree-ast-part-3/" target="_blank" rel="noopener">part 3 of my AST blog article</a> series. Normally, I don’t recommend sorting your output from within a function because it slows down receiving the output, but in this case it was an easy way to make sure the returned values are unique.
<pre class="lang:ps decode:true " title="Get-MrAstType">#Requires -Version 3.0
function Get-MrAstType {
    [CmdletBinding()]
    param ()

    ([System.Management.Automation.Language.ArrayExpressionAst].Assembly.GetTypes() |
    Where-Object {$_.Name.EndsWith('Ast') -and $_.Name -ne 'Ast'}).Name -replace '(?&lt;!^)ExpressionAst$|Ast$' |
    Sort-Object -Unique

}</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2018/09/ast1a.png"><img class="alignnone size-full wp-image-17081" src="https://mikefrobbins.com/wp-content/uploads/2018/09/ast1a.png" alt="" width="859" height="560" /></a>

Although I’ve shown the results in the previous set of results, the <em>Get-MrAstType</em> function will remain private in the <a href="https://github.com/mikefrobbins/ModuleBuildTools" target="_blank" rel="noopener">MrModuleBuildTools module</a>. That function is used by my <em>Get-MrAst</em> function. While I could have added those results to a <em>ValidateSet</em>, it would have been a long list of static comma separated values. I decided to use a dynamic parameter to create a dynamic validate set based on the results of <em>Get-MrAstType</em>.

I’ll break the <em>Get-MrAst</em> function down into smaller pieces and elaborate a little about each one. The following section specifies the required version of PowerShell and the function declaration.
<pre class="lang:ps decode:true ">#Requires -Version 3.0
function Get-MrAst {</pre>
The next part is the function is its comment-based help. Although there’s a push by some to move to Microsoft Assistance Markup Language (MAML) based help, in my opinion, the documentation needs to reside as close as possible to the code otherwise there’s a greater chance it won’t be used or updated and that it could easily become separated if someone just grabs one function instead of the entire module. My recommendation is that if you need multilingual support for your help, use MAML, otherwise use comment-based help. I know, some would argue that MAML also gives you updatable help, but with the PowerShell Gallery, it’s just as easy to release a new minor version of a module to correct bugs in its help. If you’re going to use MAML, definitely check out <a href="https://github.com/PowerShell/platyPS" target="_blank" rel="noopener">PlatyPS</a>.
<pre class="lang:ps decode:true">&lt;#
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
#&gt;</pre>
CmdletBinding is used to turn the function into an advanced function. The default parameter set is defined within CmdletBinding.
<pre class="lang:ps decode:true">[CmdletBinding(DefaultParameterSetName='Path')]</pre>
The function has three parameters, each of which are in a different parameter set. One or more <em>Path(s)</em> is accepted via parameter input or via the pipeline. Pipeline input is accepted by both value (type) and property name for this parameter. If no parameters or values are specified, it defaults to all of the PS1 and PSM1 files in the current location. One thing that you may be wondering about is that all of the parameters are set to “<em>ValueFromRemainingArguments</em>“. That’s <a href="https://stackoverflow.com/questions/45021674/how-do-you-specify-both-static-and-dynamic-positional-parameters-in-powershell" target="_blank" rel="noopener">a trick I picked up from Stack Overflow</a> where static and dynamic parameters only seem to honor positional binding for parameters of the same type and this was the only way to make it bind the dynamic parameter first.
<pre class="lang:ps decode:true ">param(
        [Parameter(ValueFromPipeline,
                   ValueFromPipelineByPropertyName,
                   ValueFromRemainingArguments,
                   ParameterSetName = 'Path',
                   Position = 1)]
        [ValidateNotNull()]
        [Alias('FilePath')]
        [string[]]$Path = ('.\*.ps1', '.\*.psm1'),</pre>
The next parameter, which is in a different parameter set, is the <em>Code</em> parameter. It accepts arbitrary code as its input. I originally set the <em>Code</em> parameter to <em>ValidateNotNullOrEmpty</em> instead of <em>Mandatory</em>. When I would retrieve the code from a function with <em>Get-Content</em>, it would generate an error when set to <em>Mandatory</em>. Although changing the parameter to <em>ValidateNotNullOrEmpty</em> made it run without error, the format was off for the results of the AST. The solution (<a href="https://stackoverflow.com/questions/15041857/powershell-keep-text-formatting-when-reading-in-a-file" target="_blank" rel="noopener">from Rob Campbell on Stack Overflow</a>) was to use the <em>Raw</em> parameter when retrieving the content of a function with <em>Get-Content</em>.
<pre class="lang:ps decode:true ">        [Parameter(Mandatory,
                   ValueFromPipelineByPropertyName,
                   ValueFromRemainingArguments,
                   ParameterSetName = 'Code')]
        [string[]]$Code,</pre>
The final standard parameter is <em>ScriptBlock.</em> As you could have probably guessed, accepts one or more script blocks. It exists in a separate parameter set from the two parameters.
<pre class="lang:ps decode:true">    [Parameter(Mandatory,
               ValueFromPipelineByPropertyName,
               ValueFromRemainingArguments,
               ParameterSetName = 'ScriptBlock')]
    [scriptblock[]]$ScriptBlock
)</pre>
As previously mentioned, a dynamic parameter is used to create a dynamic validate set as this isn’t possible with the <em>ValidateSet</em> parameter validation attribute. I found two blog articles on the Hey, Scripting Scripting Guy! Blog that helped me figure out how to use parameter validation with a dynamic parameter. “<a href="https://blogs.technet.microsoft.com/pstips/2014/06/09/dynamic-validateset-in-a-dynamic-parameter/" target="_blank" rel="noopener">Dynamic ValidateSet in a Dynamic Parameter</a>” and “<a href="https://blogs.technet.microsoft.com/undocumentedfeatures/2018/07/30/creating-a-function-or-script-with-powershell-dynamic-parameters/" target="_blank" rel="noopener">Creating a function or script with PowerShell Dynamic Parameters</a>“.
<pre class="lang:ps decode:true">DynamicParam {
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
}</pre>
The <em>Begin</em> block is simply used to assign whatever value is provided for the <em>AstType</em> parameter to a variable. It’s used later to narrow down the results if that particular parameter is specified.
<pre class="lang:ps decode:true ">BEGIN {
    $AstType = $PsBoundParameters[$ParameterName]
}</pre>
Last, but not least, there’s the <em>Process</em> block. The parameter set name that’s being used is determined via a switch statement. <em>$PSCmdlet.ParameterSetName</em> is used instead of <em>$PSBoundParameters</em> because the later does not work when using pipeline input, only when parameter input is used.
<pre class="lang:ps decode:true ">PROCESS {
    switch ($PSCmdlet.ParameterSetName) {</pre>
One of two different .NET static methods is used depending on whether or not the <em>Path</em> or <em>Code</em> parameter set is being used. See <a href="https://docs.microsoft.com/en-us/dotnet/api/system.management.automation.language.parser" target="_blank" rel="noopener">the Microsoft documentation for the Parser Class</a> for more information.

If the <em>Path</em> parameter set is used, <em>Get-ChildItem</em> retrieves the <em>FullName</em> for each item (the specified files and/or paths). Then it runs those though a <em>foreach</em> loop and assigns the results from all of them to a variable. The first <em>$null</em> variable is used for the tokens and the second one is for errors. I don’t care about those so I’m sending them directly to the bit bucket by using the $null variable. This is one scenario where if you used actual variable names for those items, you’d need to initialize them first before using them otherwise an error would be generated.
<pre class="lang:ps decode:true ">'Path' {
    Write-Verbose -Message 'Path Parameter Set Selected'
    Write-Verbose "Path contains $Path"
    
    $Files = Get-ChildItem -Path $Path -Exclude *tests.ps1, *profile.ps1 |
             Select-Object -ExpandProperty FullName
    
    if (-not ($Files)) {
        Write-Warning -Message 'No validate files found.'
        Return
    }

    $AST = foreach ($File in $Files) {
        [System.Management.Automation.Language.Parser]::ParseFile($File, [ref]$null, [ref]$null)
    }

    break
}</pre>
The process is similar, but simpler if the <em>Code</em> parameter set is used.
<pre class="lang:ps decode:true ">'Code' {
    Write-Verbose -Message 'Code Parameter Set Selected'

    $AST = foreach ($c in $Code) {
        [System.Management.Automation.Language.Parser]::ParseInput($c, [ref]$null, [ref]$null)
    }

    break
}</pre>
The process is even simpler if the <em>ScriptBlock</em> parameter set is used. That’s because beginning with PowerShell version 3.0, the AST is already available from a script block via a property named “AST”.
<pre class="lang:ps decode:true ">'ScriptBlock' {
    Write-Verbose -Message 'ScriptBlock Parameter Set Selected'
    
    $AST = $ScriptBlock.Ast
    
    break
}</pre>
I also added a default value so the code always has an execution path. It also let’s me know that my code’s not working properly (if it reaches that point) which has been known to happen from time to time. And finally, the switch statement ends with the closing curly brace.
<pre class="lang:ps decode:true ">    default {
        Write-Warning -Message 'An unexpected error has occurred'
    }
}</pre>
If the <em>AstType</em> parameter is specified, then the results are narrowed down to only the type specified.
<pre class="lang:ps decode:true ">if ($PsBoundParameters.AstType) {
    Write-Verbose -Message 'AstType Parameter Entered'
    
    $AST = $AST.FindAll({$args[0].GetType().Name -like "*$ASTType*Ast"}, $true)
}</pre>
The results are outputted, the <em>Process</em> block is closed, and the function ends.
<pre class="lang:ps decode:true ">        Write-Output $AST
    }

}</pre>
The <em>Get-MrAst</em> function is shown in its entirety in the following example.
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
                    Write-Warning -Message 'No validate files found.'
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
            
            $AST = $AST.FindAll({$args[0].GetType().Name -like "*$ASTType*Ast"}, $true)
        }

        Write-Output $AST
    }

}</pre>
Now, let’s take a look at the default output from the <em>Get-MrAst</em> function.
<pre class="lang:ps decode:true ">Get-MrAst -Path $ModulePath\private\Get-MrAstType.ps1</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2018/09/ast2a.png"><img class="alignnone size-full wp-image-17082" src="https://mikefrobbins.com/wp-content/uploads/2018/09/ast2a.png" alt="" width="859" height="465" /></a>

Determine the PowerShell version and the modules (if any) specified via the requires statement in the function is simple and there’s no need to try to parse the file with a complicated regular expression.
<pre class="lang:ps decode:true ">(Get-MrAst -Path $ModulePath\private\Get-MrAstType.ps1).ScriptRequirements</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2018/09/ast3a.png"><img class="alignnone size-full wp-image-17083" src="https://mikefrobbins.com/wp-content/uploads/2018/09/ast3a.png" alt="" width="859" height="200" /></a>

Specifying the AstType makes it easy to retrieve the name of the function.
<pre class="lang:ps decode:true ">(Get-MrAst -Path $ModulePath\private\Get-MrAstType.ps1 -AstType FunctionDefinition).Name</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2018/09/ast4a.png"><img class="alignnone size-full wp-image-17084" src="https://mikefrobbins.com/wp-content/uploads/2018/09/ast4a.png" alt="" width="859" height="70" /></a>

As are the contents of the function itself.  Notice that this allows you to retrieve the function without the requires statement being included since I’ll want to strip that information out when combining the functions in a PSM1 file.
<pre class="lang:ps decode:true ">(Get-MrAst -Path $ModulePath\private\Get-MrAstType.ps1 -AstType FunctionDefinition).Extent.Text</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2018/09/ast5a.png"><img class="alignnone size-full wp-image-17085" src="https://mikefrobbins.com/wp-content/uploads/2018/09/ast5a.png" alt="" width="859" height="167" /></a>

Those should be all of the items that I’ll need to put the pieces back together.

The development version of the functions shown in this blog article can be downloaded from<a href="https://github.com/mikefrobbins/ModuleBuildTools" target="_blank" rel="noopener"> my ModuleBuildTools repository on GitHub</a>.

µ