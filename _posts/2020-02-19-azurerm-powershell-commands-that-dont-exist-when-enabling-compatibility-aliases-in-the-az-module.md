---
ID: 18323
post_title: 'AzureRM PowerShell Commands that Don&#8217;t Exist when Enabling Compatibility Aliases in the Az Module'
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2020/02/19/azurerm-powershell-commands-that-dont-exist-when-enabling-compatibility-aliases-in-the-az-module/
published: true
post_date: 2020-02-19 07:30:43
---
As I mentioned in <a href="https://mikefrobbins.com/2020/02/10/how-to-install-the-azure-az-powershell-module/" target="_blank" rel="noopener noreferrer">a previous blog article</a>, the AzureRM PowerShell module is only supported until December of 2020. It has been replaced by the Az PowerShell module which was introduced in December of 2018.

On Twitter, I recently asked if anyone was still using the AzureRM module and what was keeping them from transitioning to the Az module. One of the responses I received was due to the amount of work and time invested in scripts based on the AzureRM module.

<a href="https://twitter.com/mikefrobbins/status/1222970327216672769" target="_blank" rel="noopener noreferrer"><img class="alignnone size-full wp-image-18325" src="https://mikefrobbins.com/wp-content/uploads/2020/02/azurermalias1a.jpg" alt="" width="600" height="617" /></a>

The Az PowerShell module includes compatibility aliases for scripts written using the AzureRM PowerShell module. The compatibility aliases are enabled using the <em>Enable-AzureRmAlias</em> command. I decided to determine how compatible these so-called compatibility aliases are, at least from the standpoint of possibly missing commands altogether.

Since you can't have both modules loaded at the same time in the same session, I decided to load the AzureRM module in Windows PowerShell 5.1 and the Az module in PowerShell 7.

First, I exported a list of all the commands in the AzureRM module to a CLI XML file.
<pre class="lang:ps decode:true">Import-Module -Name AzureRM
Get-Command -Module AzureRM.* |
Export-Clixml -Path C:\tmp\azurerm.xml
</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2020/02/azurermalias2a.jpg"><img class="alignnone size-full wp-image-18326" src="https://mikefrobbins.com/wp-content/uploads/2020/02/azurermalias2a.jpg" alt="" width="859" height="89" /></a>

After importing the Az PowerShell module into PowerShell 7, I stored the current aliases from the session in a variable and then ran the <em>Enable-AzureRmAlias</em> command.
<pre class="lang:ps decode:true ">Import-Module -Name Az
$PreAlias = Get-Alias
Enable-AzureRmAlias</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2020/02/azurermalias3a.jpg"><img class="alignnone size-full wp-image-18327" src="https://mikefrobbins.com/wp-content/uploads/2020/02/azurermalias3a.jpg" alt="" width="859" height="89" /></a>

Next, I compared the aliases before and after running the <em>Enable-AzureRmAlias</em> command and stored the results in another CLI XML file.
<pre class="lang:ps decode:true">Compare-Object -ReferenceObject $PreAlias -DifferenceObject (Get-Alias) -Property Name |
Export-Clixml -Path C:\tmp\az.xml</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2020/02/azurermalias4a.jpg"><img class="alignnone size-full wp-image-18328" src="https://mikefrobbins.com/wp-content/uploads/2020/02/azurermalias4a.jpg" alt="" width="859" height="75" /></a>

I compared the commands from the AzureRM module to the compatibility aliases added from the Az module using the previously exported XML files. 159 commands exist in the AzureRM module that don't exist as aliases when enabling AzureRM compatibility aliases in the Az module.
<pre class="lang:ps decode:true ">Compare-Object -ReferenceObject (Import-Clixml -Path C:\tmp\azurerm.xml) -DifferenceObject (Import-Clixml -Path C:\tmp\az.xml) -Property Name |
Where-Object SideIndicator -eq '&lt;='</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2020/02/azurermalias5a.jpg"><img class="alignnone size-full wp-image-18329" src="https://mikefrobbins.com/wp-content/uploads/2020/02/azurermalias5a.jpg" alt="" width="859" height="90" /></a>

I ran the same command again except this time exporting the results to another CLI XML file.
<pre class="lang:ps decode:true">Compare-Object -ReferenceObject (Import-Clixml -Path C:\tmp\azurerm.xml) -DifferenceObject (Import-Clixml -Path C:\tmp\az.xml) -Property Name |
Where-Object SideIndicator -eq '&lt;=' |
Export-Clixml -Path C:\tmp\azure-diff.xml</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2020/02/azurermalias6a.jpg"><img class="alignnone size-full wp-image-18330" src="https://mikefrobbins.com/wp-content/uploads/2020/02/azurermalias6a.jpg" alt="" width="859" height="77" /></a>

I switched back to Windows PowerShell since that's where the AzureRM module is imported to determine what types of commands are missing. Based on the results, if you're not currently following PowerShell best practices and using aliases from the AzureRM module in your PowerShell code, you're out of luck with the compatibility aliases.
<pre class="lang:ps decode:true">Import-Clixml -Path C:\tmp\azure-diff.xml |
Select-Object -Property Name |
Get-Command</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2020/02/azurermalias7b.jpg"><img class="alignnone size-full wp-image-18349" src="https://mikefrobbins.com/wp-content/uploads/2020/02/azurermalias7b.jpg" alt="" width="859" height="494" /></a>

The majority of the missing commands are aliases from the AzureRM module, but there are also a few cmdlets missing.
<pre class="lang:ps decode:true">Import-Clixml -Path C:\tmp\azure-diff.xml |
Get-Command |
Group-Object -Property CommandType -NoElement</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2020/02/azurermalias8b.jpg"><img class="alignnone size-full wp-image-18351" src="https://mikefrobbins.com/wp-content/uploads/2020/02/azurermalias8b.jpg" alt="" width="859" height="188" /></a>

While your existing PowerShell code probably doesn't use these commands, it's something to be aware of.
<pre class="lang:ps decode:true">Import-Clixml -Path C:\tmp\azure-diff.xml |
Get-Command |
Where-Object CommandType -eq Cmdlet |
Sort-Object -Property Source, Name</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2020/02/azurermalias9b.jpg"><img class="alignnone size-full wp-image-18350" src="https://mikefrobbins.com/wp-content/uploads/2020/02/azurermalias9b.jpg" alt="" width="859" height="357" /></a>

If you are using any of the AzureRM aliases in your PowerShell code, one alternative is to use <a href="https://github.com/PowerShell/PSScriptAnalyzer" target="_blank" rel="noopener noreferrer">PSScriptAnalyzer</a> to automatically update the aliases to the actual cmdlet names. Since the AzureRM compatibility aliases are aliased to the new cmdlets in the Az module, PSScriptAnalyzer could also be used to automatically update your existing code to the new cmdlet names in the Az module.

µ