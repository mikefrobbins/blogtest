---
ID: 8425
post_title: 'Windows 8.1 RSAT PowerShell Cmdlets Get-ADUser &#038; Get-ADComputer : One or more Properties are Invalid'
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2013/11/07/windows-8-1-rsat-powershell-cmdlets-get-aduser-get-adcomputer-one-or-more-properties-are-invalid/
published: true
post_date: 2013-11-07 07:30:11
---
I saw a tweet yesterday from <a href="http://twitter.com/gpduck" target="_blank">Chris Duck</a> about a PowerShell version 4.0 bug:

<a href="http://twitter.com/gpduck" target="_blank"><img class="alignnone size-full wp-image-8426" src="http://mikefrobbins.com/wp-content/uploads/2013/11/aduser-bug1a.png" alt="aduser-bug1a" width="485" height="64" /></a>

Here's a <a href="https://connect.microsoft.com/PowerShell/feedback/details/806452/windows-8-1-powershell-4-0-get-adcomputer-properties-bug" target="_blank">link</a> to the Connect Bug on this particular issue.

The issue occurs when you try to use the <em>Get-ADUser</em> or <em>Get-ADCompute</em>r cmdlets along with specifying the <em>Properties</em> parameter with the asterisk "*" wildcard character to select all of the properties.

No issue when the client is running Windows 8.1 with the <a href="http://www.microsoft.com/en-us/download/details.aspx?id=39296" target="_blank">RSAT tools</a> installed and the Active Directory domain controllers are running Windows Server 2012 R2:
<pre class="lang:ps decode:true">Get-ADUser jasonh -Properties *</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2013/11/aduser-bug1b.png"><img class="alignnone size-full wp-image-8427" src="http://mikefrobbins.com/wp-content/uploads/2013/11/aduser-bug1b.png" alt="aduser-bug1b" width="877" height="211" /></a>

The Active Directory domain in this environment is running schema version 69:
<pre class="lang:ps decode:true">Get-ADObject -Identity "cn=Schema,cn=Configuration,dc=mikefrobbins,dc=com" -Properties objectVersion | Format-Table objectVersion -AutoSize</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2013/11/aduser-bug1h.png"><img class="alignnone size-full wp-image-8441" src="http://mikefrobbins.com/wp-content/uploads/2013/11/aduser-bug1h.png" alt="aduser-bug1h" width="877" height="145" /></a>

I can confirm that the issue occurs when the client is running Windows 8.1 with the RSAT tools installed and the domain controllers are running Windows Server 2008 R2:
<pre class="lang:ps decode:true">Get-ADUser jdoe -Properties *</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2013/11/aduser-bug1c1.png"><img class="alignnone size-full wp-image-8454" src="http://mikefrobbins.com/wp-content/uploads/2013/11/aduser-bug1c1.png" alt="aduser-bug1c1" width="877" height="167" /></a>

<em><span style="color: #ff0000;">Get-ADUser : One or more properties are invalid.</span></em>
<em> <span style="color: #ff0000;">Parameter name: msDS-AssignedAuthNPolicy</span></em>
<em> <span style="color: #ff0000;">At line:1 char:1</span></em>
<em> <span style="color: #ff0000;">+ Get-ADUser jdoe -Properties *</span></em>
<em> <span style="color: #ff0000;">+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~</span></em>
<em> <span style="color: #ff0000;"> + CategoryInfo : InvalidArgument: (jdoe:ADUser) [Get-ADUser], ArgumentException</span></em>
<em> <span style="color: #ff0000;"> + FullyQualifiedErrorId : ActiveDirectoryCmdlet:System.ArgumentException,Microsoft.ActiveDirectory.Management.Commands.GetADUser</span></em>

A similar error is return when attempting to select all properties with the <em>Get-ADComputer</em> cmdlet:

<em><span style="color: #ff0000;">Get-ADComputer : One or more properties are invalid.</span></em>
<em> <span style="color: #ff0000;">Parameter name: msDS-AssignedAuthNPolicy</span></em>
<em> <span style="color: #ff0000;">At line:1 char:1</span></em>
<em> <span style="color: #ff0000;">+ Get-ADComputer pc01 -Properties *</span></em>
<em> <span style="color: #ff0000;">+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~</span></em>
<em> <span style="color: #ff0000;"> + CategoryInfo : InvalidArgument: (pc01:ADComputer) [Get-ADComputer], ArgumentException</span></em>
<em> <span style="color: #ff0000;"> + FullyQualifiedErrorId : ActiveDirectoryCmdlet:System.ArgumentException,Microsoft.ActiveDirectory.Management.Commands.GetADComputer</span></em>

The Active Directory domain in this environment which I'm encountering these problems with is running schema version 47:
<pre class="lang:ps decode:true">Get-ADObject -Identity "cn=Schema,cn=Configuration,dc=mikefrobbins,dc=local" -Properties objectVersion | Format-Table objectVersion -AutoSize</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2013/11/aduser-bug1i.png"><img class="alignnone size-full wp-image-8442" src="http://mikefrobbins.com/wp-content/uploads/2013/11/aduser-bug1i.png" alt="aduser-bug1i" width="988" height="138" /></a>

Let's use PowerShell to troubleshoot this problem. I'll first grab a list of all of the property names from the environment where the command completes successfully:
<pre class="lang:ps decode:true">Get-ADUser jasonh -Properties * | Select-Object -ExpandProperty PropertyNames | clip.exe</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2013/11/aduser-bug1d.png"><img class="alignnone size-full wp-image-8429" src="http://mikefrobbins.com/wp-content/uploads/2013/11/aduser-bug1d.png" alt="aduser-bug1d" width="877" height="61" /></a>

I'll now massage the data a little, then iterate through the collection of property names and return a list of the ones where the command fails in the environment with the problem:

<strong>Note:</strong> I have the <a href="http://pscx.codeplex.com/" target="_blank">PowerShell Community Extensions</a> module installed on the machine I'm demonstrating the following example on:
<pre class="lang:ps decode:true">$properties = (Get-Clipboard) -split "`r`n"

foreach ($property in $properties) {
    try {
        Get-ADUser jdoe -Properties $property -ErrorAction Stop | Out-Null
    }
    catch {
        Write-Warning "Error Accessing Property: $property"
    }
}</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2013/11/aduser-bug1e.png"><img class="alignnone size-full wp-image-8430" src="http://mikefrobbins.com/wp-content/uploads/2013/11/aduser-bug1e.png" alt="aduser-bug1e" width="540" height="211" /></a>

You could also use the .NET Framework to access the clipboard if you're unable to load third party modules due to policies and/or restrictions that are outside of your control:
<pre class="lang:ps decode:true">Add-Type -Assembly PresentationCore
$properties = ([Windows.Clipboard]::GetText()) -split "`r`n"

foreach ($property in $properties) {
    try {
        Get-ADUser jdoe -Properties $property -ErrorAction Stop | Out-Null
    }
    catch {
        Write-Warning "Error Accessing Property: $property"
    }
}</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2013/11/aduser-bug1f.png"><img class="alignnone size-full wp-image-8436" src="http://mikefrobbins.com/wp-content/uploads/2013/11/aduser-bug1f.png" alt="aduser-bug1f" width="544" height="224" /></a>

As you can see in the results from the previous example, the command errors out on the <em>AuthenticationPolicy</em> and <em>AuthenticationPolicySilo</em> properties. The last element in the array is empty which is why there's an extra warning at the end with no property name. The <em>Get-ADComputer</em> cmdlet has issues with the same two properties, but also has an issue with the <em>msDS-GenerationId</em> property.

You might be wondering why I'm using the clipboard in the first place? One of these environments is running on a private network so there's no network access to it.

Now I'll jump over to the environment that's running Windows Server 2008 R2 on its domain controllers and get a list of the property names:
<pre class="lang:ps decode:true">Get-ADUser jdoe -Properties * | Select-Object -ExpandProperty PropertyNames</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2013/11/aduser-bug1g.png"><img class="alignnone size-full wp-image-8439" src="http://mikefrobbins.com/wp-content/uploads/2013/11/aduser-bug1g.png" alt="aduser-bug1g" width="988" height="127" /></a>

Notice that the two property names that are causing the errors don't exist so it appears that there are some new properties for an Active Directory user in a domain running 2012 R2 that the Windows 8.1 RSAT tools are looking for that don't exist on older domain controllers. In my opinion, there would need to be a check to see if your running version X or higher then select these properties, otherwise select the legacy list of properties.

In the meantime, you can use implicit remoting to work around this issue. He's a blog article "<a href="http://mikefrobbins.com/2012/10/30/use-powershell-to-create-active-directory-user-accounts-from-data-contained-in-the-adventure-works-2012-database/" target="_blank">Use PowerShell to Create Active Directory User Accounts from Data Contained in the Adventure Works 2012 Database</a>" where I used implicit remoting if you're interesting in exploring that option.

µ