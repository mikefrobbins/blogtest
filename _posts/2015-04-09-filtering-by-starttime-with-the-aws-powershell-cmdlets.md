---
ID: 11441
post_title: >
  Filtering by StartTime with the AWS
  PowerShell Cmdlets
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2015/04/09/filtering-by-starttime-with-the-aws-powershell-cmdlets/
published: true
post_date: 2015-04-09 07:30:18
---
I was recently trying to figure out how to return an AWS Storage Gateway snapshot by providing a value for the StartTime property and it wasn't easy to say the least so I thought I would share my experience to save others the headache of figuring it out.

Most of the tutorials you'll find online show filtering something similar to this:
<pre class="lang:ps decode:true ">$filter1 = New-Object Amazon.EC2.Model.Filter
$filter1.Name = 'volume-id'
$filter1.Value.Add('myvolumeid')

$filter2 = New-Object Amazon.EC2.Model.Filter
$filter2.Name = 'status'
$filter2.Value.Add('completed')

Get-EC2Snapshot -Filter $filter1, $filter2 |
Select-Object -First 1</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/03/aws-snapshots1a.jpg"><img class="alignnone size-full wp-image-11442" src="http://mikefrobbins.com/wp-content/uploads/2015/03/aws-snapshots1a.jpg" alt="aws-snapshots1a" width="877" height="381" /></a>

You could also use multiple hash tables as shown in the following example:
<pre class="lang:ps decode:true">Get-EC2Snapshot -Filter @(
    @{
        name='volume-id'
        value='myvolumeid'
    }
    @{
        name='status'
        value='completed' 
    }
) |
Select-Object -First 1</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/03/aws-snapshots2a.jpg"><img class="alignnone size-full wp-image-11443" src="http://mikefrobbins.com/wp-content/uploads/2015/03/aws-snapshots2a.jpg" alt="aws-snapshots2a" width="877" height="393" /></a>

Viewing the help for the Filter parameter shows what the valid names are so that part was easy enough to figure out:
<pre class="lang:ps decode:true ">help Get-EC2Snapshot -Parameter filter</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/03/aws-snapshots3a.jpg"><img class="alignnone size-full wp-image-11444" src="http://mikefrobbins.com/wp-content/uploads/2015/03/aws-snapshots3a.jpg" alt="aws-snapshots3a" width="877" height="409" /></a>

You would think that I could simply copy and paste the date that was returned from one of the first two examples and it would return that one snapshot. If it were only that easy:
<pre class="lang:ps decode:true">Get-EC2Snapshot -Filter @(
    @{
        name='volume-id'
        value='myvolumeid'
    }
    @{
        name='status'
        value='completed' 
    }
    @{
        name='start-time'
        value='3/10/2015 1:02:20 AM' 
    }
)</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/03/aws-snapshots4b.jpg"><img class="alignnone size-full wp-image-11460" src="http://mikefrobbins.com/wp-content/uploads/2015/03/aws-snapshots4b.jpg" alt="aws-snapshots4b" width="877" height="226" /></a>

As it turns out, the value for StartTime has to be in the format shown in this example:
<pre class="lang:ps decode:true">(Get-Date -Date '3/10/2015 1:02:20 AM').ToUniversalTime().ToString('yyyy-MM-ddThh:mm:ss.000Z')</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/03/aws-snapshots5a.jpg"><img class="alignnone size-full wp-image-11446" src="http://mikefrobbins.com/wp-content/uploads/2015/03/aws-snapshots5a.jpg" alt="aws-snapshots5a" width="877" height="69" /></a>

Using that format for the StartTime value returned the one snapshot without issue:
<pre class="lang:ps decode:true ">Get-EC2Snapshot -Filter @(
    @{
        name='volume-id'
        value='myvolumeid'
    }
    @{
        name='status'
        value='completed' 
    }
    @{
        name='start-time'
        value='2015-03-10T06:02:20.000Z' 
    }
)</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/03/aws-snapshots6b.jpg"><img class="alignnone size-full wp-image-11459" src="http://mikefrobbins.com/wp-content/uploads/2015/03/aws-snapshots6b.jpg" alt="aws-snapshots6b" width="877" height="431" /></a>

That was easy enough to figure out, right? I'll let you decide the answer to that for yourself.

µ