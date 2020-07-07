---
ID: 4817
post_title: >
  Collections Are More Transparent In
  PowerShell v3
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2012/07/19/collections-are-more-transparent-in-powershell-v3/
published: true
post_date: 2012-07-19 07:30:09
---
In PowerShell version 2, it took me a while to figure out why using the dotted notation method of selecting a property worked at times and not at others.

In the following example $v2 is a variable so I'm able to select the CPU property by using $v2.cpu or accomplish the same thing by piping $v2 to Select-Object with the -ExpandProperty parameter and specifying the CPU property:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/07/col-v3-1.jpg"><img class="alignnone size-full wp-image-4818" title="col-v3-1" src="http://mikefrobbins.com/wp-content/uploads/2012/07/col-v3-1.jpg" alt="" width="591" height="155" /></a>

In the scenario shown in the following image, why didn't $v2.cpu return anything? Because it's a collection. In this example, $v2 contains a collection of items (objects) which is why $v2.cpu doesn't return anything in PowerShell version 2. Piping $v2 to Select-Object as in the previous example returns all of the items in the collection for the CPU property:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/07/col-v3-2.jpg"><img class="alignnone size-full wp-image-4820" title="col-v3-2" src="http://mikefrobbins.com/wp-content/uploads/2012/07/col-v3-2.jpg" alt="" width="505" height="227" /></a>

Collections are zero based and each individual item in it can be accessed by specifying the position of the item you want to retrieve. The number of items that a collection contains can be determined by retrieving the length property:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/07/col-v3-3.jpg"><img class="alignnone size-full wp-image-4822" title="col-v3-3" src="http://mikefrobbins.com/wp-content/uploads/2012/07/col-v3-3.jpg" alt="" width="240" height="145" /></a>

Piping the collection to Select-Object or ForEach-Object are ways to retrieve all of the items in the collection:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/07/col-v3-4.jpg"><img class="alignnone size-full wp-image-4823" title="col-v3-4" src="http://mikefrobbins.com/wp-content/uploads/2012/07/col-v3-4.jpg" alt="" width="338" height="142" /></a>

In PowerShell version 3, using the dotted notation method of accessing a property that's part of a collection will return all of the items in the collection unlike in PowerShell version 2. An example is shown in the following image where $v3.processname returns all of the ProcessName items from the $v3 collection without having to pipe it to Select-Object or ForEach-Object as would have been necessary in PowerShell version 2:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/07/col-v3-5.jpg"><img class="alignnone size-full wp-image-4824" title="col-v3-5" src="http://mikefrobbins.com/wp-content/uploads/2012/07/col-v3-5.jpg" alt="" width="507" height="238" /></a>

Now for one last screenshot to make sure that what's happening is clear. The same commands are run in PowerShell version 2 and 3 and they produce different results:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/07/col-v3-6.jpg"><img class="alignnone size-full wp-image-4968" title="col-v3-6" src="http://mikefrobbins.com/wp-content/uploads/2012/07/col-v3-6.jpg" alt="" width="558" height="382" /></a>

As you can see, when working with a collection in PowerShell version 3, it's more transparent than version 2 because you don't even need to know you're working with a collection to return items using the dotted notation method of selecting a property.

µ