---
ID: 17143
post_title: >
  PowerShell Script Module Design
  Philosophy
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2018/09/21/powershell-script-module-design-philosophy/
published: true
post_date: 2018-09-21 07:30:26
---
Years ago, when I first learned how to create PowerShell script modules, I built them with all the functions in one huge monolithic PSM1 file. I like the monolithic script module design from a performance and security standpoint along with the ease of signing fewer files if you’re taking advantage of code signing to digitally sign your scripts and modules (there are fewer files to sign). What I don’t like is that collaborating with others using one huge file is a merge conflict waiting for a place to happen and if someone only wants one of my functions instead of the entire module, they’re out of luck unless they want to copy and paste it.

Several years ago, I started building my script modules using what I call a non-monolithic script module design where each function is in a separate PS1 file that is dot-sourced from the PSM1 file. This design also has pros and cons. I prefer this design for development and collaboration, but it’s slow to load and has potential security issues. If a malicious script gets dropped into your module’s folder, then it’s dot-sourced along with all of your functions.

Why not use the best of both both worlds? The new way I’m designing my PowerShell script modules does just that, it encompasses the best of both. When developing a PowerShell script module, I'll use the non-monolithic design, placing functions in separate PS1 files that are dot-sourced from the PSM1 script module file for better collaboration and fewer merge conflicts. The development versions will exist on <a href="https://github.com/mikefrobbins" target="_blank" rel="noopener">GitHub</a>. When it’s time to release a production version of the module, I'll use the monolithic design by packaging the functions up and shipping them in one huge monolithic PSM1 file. The production versions will reside in the <a href="https://www.powershellgallery.com/" target="_blank" rel="noopener">PowerShell Gallery</a>. The process of transitioning the modules from one design to the other (Development to Production) needs to be automated otherwise it probably won’t happen, or at best, it will be haphazard and error prone.

I needed to get the content of the functions in the PS1 files and add them all to one single file. Sounds simple or so I thought. Why not just use the <em>Get-Content</em> cmdlet? One of the problems is that I use a <em>Requires</em> statement at the top of my PS1 files that specifies the required PowerShell version and any module dependencies. That information needs to be stripped out. Well, not only does it need to be stripped out, but I need to add that information to the module manifest. You might be wondering, why not just do that to start with for the development version of the module? It goes back to someone grabbing a copy of a single function. When functions are in separate PS1 files, these dependencies need to reside as close as possible to the code so there’s less chance of them becoming separated or being neglected as far as updating them when changes are made to the code. Basically, it’s the same reason that you’ll pry comment based help from my cold dead hands. The farther the documentation lives from the content, the less likely it will be updated and the more chance it will become separated from the code itself.

The Abstract Syntax Tree (AST) is the solution to retrieving the information that I need from the individual functions and that's what I'll be focusing on in my next couple of blog articles.

As always, feel free to post your opinions or suggestions as a comment to this blog article. You wouldn't believe how much I've learned over the years from others sharing their point of view.

µ