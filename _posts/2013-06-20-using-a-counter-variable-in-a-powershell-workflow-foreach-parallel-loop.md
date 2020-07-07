---
ID: 7546
post_title: >
  Using a Counter Variable in a PowerShell
  Workflow Foreach -Parallel Loop
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2013/06/20/using-a-counter-variable-in-a-powershell-workflow-foreach-parallel-loop/
published: true
post_date: 2013-06-20 07:30:35
---
As I'm sure most of my blog readers are aware, I competed in the advanced track of the Scripting Games this year and ended up doing quite well. Two first place finishes and three second places finishes with the official judges and four first place crowd score finishes. I also ended up being the <a href="http://powershell.org/wp/2013/06/11/overall-winners-of-the-scripting-games/" target="_blank">overall winner of the advanced track</a>.

A few days ago someone on twitter asked me as the winner of the Scripting Games advanced track, what I would do with it? I plan to use it as a tool. I'll continue doing the same thing I've been doing of learning more and trying to spread the word about PowerShell to others as well as sharing my knowledge of what I learn. Maybe as a Scripting Games winner, a few more people will listen.

Today I'll start the first of many blogs to come in a series that shares information about what I learned during the Scripting Games. During event six, one of the goals was to name servers as SERVER1, SERVER2, etc. I started out doing something like this in a foreach loop:
<pre class="lang:ps decode:true">foreach ($_ in 1..10){
    $i++
    $a = "SERVER"
    "$a$i"
}</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2013/06/workflow-counter1.png"><img class="alignnone size-full wp-image-7547" alt="workflow-counter1" src="http://mikefrobbins.com/wp-content/uploads/2013/06/workflow-counter1.png" width="361" height="236" /></a>

Then I ended up using a workflow with foreach -parallel and that didn't work out so well:
<pre class="lang:ps decode:true">workflow test {
    foreach -parallel ($_ in 1..10){
        $i++
        $a = "SERVER"
        "$a$i"
    }
}
test</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2013/06/workflow-counter2.png"><img class="alignnone size-full wp-image-7548" alt="workflow-counter2" src="http://mikefrobbins.com/wp-content/uploads/2013/06/workflow-counter2.png" width="319" height="280" /></a>

Here's what I discovered was the solution to return the desired results in a workflow foreach -parallel loop:
<pre class="lang:ps decode:true">workflow test {
    foreach -parallel ($_ in 1..10){
        $workflow:i++
        $a = "SERVER"
        "$a$i"
    }
}
test</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2013/06/workflow-counter3.png"><img class="alignnone size-full wp-image-7549" alt="workflow-counter3" src="http://mikefrobbins.com/wp-content/uploads/2013/06/workflow-counter3.png" width="315" height="280" /></a>

I ended up not using this particular method of naming the servers, but this was something that wasn't easy to figure out and there isn't much documentation in books or on the Internet about this so I thought it was worth sharing and in six months when I need to do something like this again, I can search my blog to figure out how I did it.

<strong>Update:</strong> Be sure to see the comments on this blog article. As the tip from Jeffery Hicks in the comments states, you can't count on the results from the workflow being sequential.

µ