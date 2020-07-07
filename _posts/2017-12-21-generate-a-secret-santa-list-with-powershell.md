---
ID: 15982
post_title: >
  Generate a Secret Santa List with
  PowerShell
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2017/12/21/generate-a-secret-santa-list-with-powershell/
published: true
post_date: 2017-12-21 07:30:44
---
It's supposed to be the most wonderful time of the year and while you might buy multiple Christmas gifts for everyone in your immediate family, often times buying for everyone in your extended family or for all of your co-workers is cost prohibitive.

I originally started out with a simple idea to create a PowerShell script to take a list of names and generate a second random list of names based off of the first one while making sure the corresponding name on the second list doesn't match the first one. Sounds simple enough, right?

Everything seemed well and fine until I figured out there was a problem with my logic because if the last entry in both lists are the same, there's no way to prevent a duplicate other than not performing a match at all which means someone would be left out.
<pre class="lang:ps decode:true">$Users = Get-ADUser -Filter * -SearchBase 'OU=AdventureWorks Users,OU=Users,OU=Test,DC=mikefrobbins,DC=com' |
Select-Object -First 10 -ExpandProperty Name

$Match = $Users

foreach($User in $Users) {
    
    $Result = $Match.Where({$_ -ne $User}) | Get-Random

    [pscustomobject]@{
        Name = $User
        Match = $Result
    }
    
    $Match = $Match.Where({$_ -ne $Result})

}</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2017/12/secret-santa1a.jpg"><img class="alignnone size-full wp-image-15983" src="http://mikefrobbins.com/wp-content/uploads/2017/12/secret-santa1a.jpg" alt="" width="861" height="428" /></a>

It took running the code a number of times for the problem to occur. As you can see in the previous example, Alan Brewer doesn't have a match because the only one left in the second list was himself.

I decided to take a different approach while at the same time checking to see if the last person in the two lists matched and just regenerate the second list if they did.
<pre class="lang:ps decode:true">$Gifters = Get-ADUser -Filter * -SearchBase 'OU=AdventureWorks Users,OU=Users,OU=Test,DC=mikefrobbins,DC=com' |
Select-Object -First 10 -ExpandProperty Name

do {
    $Giftees = $Gifters | Sort-Object {Get-Random}
}
while ($Gifters[$Gifters.Length -1] -eq $Giftees[$Giftees.Length -1] )

for ($i = 0; $i -lt ($Gifters.Length); $i += 1) {
    [pscustomobject]@{
        Gifter = $Gifters[$i]
        Giftee = $Giftees[$i]
    }
}</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2017/12/secret-santa2a.jpg"><img class="alignnone size-full wp-image-15984" src="http://mikefrobbins.com/wp-content/uploads/2017/12/secret-santa2a.jpg" alt="" width="859" height="392" /></a>

The problem with my second approach is that it didn't prevent a person from being matched to themselves in the middle of the list.

Clearly this was going to be a little more difficult than I initially thought. If I'm going to put more effort into this, I'll just create a function for it.
<pre class="lang:ps decode:true " title="Get-MrSecretSantaList">#Requires -Version 3.0
function Get-MrSecretSantaList {
&lt;#
.SYNOPSIS
    Generates a unique list of gift givers and gift receivers based on a single list of names.
.DESCRIPTION
    Get-MrSecretSantaList is an advanced function that generates a unique list of gift givers
    and gift receivers based on a single list of names.
.PARAMETER Name
    The name of the person. A minimum of 2 names must be provided.
.EXAMPLE
    Get-MrSecretSantaList -Name 'Mike Robbins', 'Joe Doe'
 .EXAMPLE
    Get-MrSecretSantaList -Name (Get-ADUser -Filter "Enabled -eq '$true'" | Select-Object -ExpandProperty Name)
.INPUTS
    None
.OUTPUTS
    PSCustomObject
.NOTES
    Author:  Mike F Robbins
    Website: http://mikefrobbins.com
    Twitter: @mikefrobbins
#&gt;
    [CmdletBinding()]
    param (
        [Parameter(Mandatory)]
        [ValidateCount(2,32768)]
        [string[]]$Name
    )
    if ($Name.Length % 2 -ne 0) {
        Throw 'An even number of Names must be specified in order for matching to occur.'
    }
    do {
        $Giftee = $Name | Sort-Object {Get-Random}
    }
    while (
        $(for ($i = 0; $i -lt ($Name.Length); $i += 1) {
            if ($Name[$i] -eq $Giftee[$i]) {
                Write-Verbose -Message "A duplicate has occured in loop $i Name: $($Name[$i]) cannot match Giftee: $($Giftee[$i])"
                $true
                break
            }
        })
    )
    for ($i = 0; $i -lt ($Name.Length); $i += 1) {
        [pscustomobject]@{
            Gifter = $Name[$i]
            Giftee = $Giftee[$i]
        }
    }
}</pre>
I attempted to use <em>ValidateScript</em> to make sure there were an even number of items provided, but it appears that you're unable to retrieve the count or length property in the param block.

I nested a for loop inside the do/while loop to check each entry and if it matches itself, the break statement causes it to immediately exit the loop, regenerate the second list, and start over.

The function generates two lists with the same names that are matched randomly while making sure no one is matched to themselves.
<pre class="lang:ps decode:true ">$Gifters = Get-ADUser -Filter * -SearchBase 'OU=AdventureWorks Users,OU=Users,OU=Test,DC=mikefrobbins,DC=com' |
Select-Object -First 10 -ExpandProperty Name

Get-MrSecretSantaList -Name $Gifters</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2017/12/secret-santa3a.jpg"><img class="alignnone size-full wp-image-15985" src="http://mikefrobbins.com/wp-content/uploads/2017/12/secret-santa3a.jpg" alt="" width="859" height="273" /></a>

If less than two names are provided or if an odd number of names is provided, an error is generated.
<pre class="lang:ps decode:true">Get-MrSecretSantaList -Name 'Mike Robbins', 'John Doe', 'Jane Doe'</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2017/12/secret-santa4a.jpg"><img class="alignnone size-full wp-image-15986" src="http://mikefrobbins.com/wp-content/uploads/2017/12/secret-santa4a.jpg" alt="" width="859" height="142" /></a>

While duplicates don't seem to occur that often, they do occur. The verbose parameter can be used to see the duplicates.
<pre class="lang:ps decode:true ">$null = Get-MrSecretSantaList -Name (1..32768) -Verbose</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2017/12/secret-santa5a.jpg"><img class="alignnone size-full wp-image-15987" src="http://mikefrobbins.com/wp-content/uploads/2017/12/secret-santa5a.jpg" alt="" width="859" height="81" /></a>

You could query Active Directory similarly to what I've done in the examples shown in this blog article and add their email address. Then the <em>Send-MailMessage</em> cmdlet could be used to automatically email each of the users with the name of the person they're buying a gift for.

The other possibility that I thought about is simply generating a random offset less than the number of names in the list and offsetting the matching list by that number, but it wouldn't purely random like the examples shown in this blog article. I'd love to hear your thoughts and know if there's a simpler way to accomplish this task?

µ