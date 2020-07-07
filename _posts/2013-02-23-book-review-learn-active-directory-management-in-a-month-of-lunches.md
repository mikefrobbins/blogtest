---
ID: 6792
post_title: 'Book Review: Learn Active Directory Management in a Month of Lunches'
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2013/02/23/book-review-learn-active-directory-management-in-a-month-of-lunches/
published: true
post_date: 2013-02-23 10:30:30
---
<span style="line-height:1.5;"><a href="http://manning.com/siddaway3/"><img class="alignleft size-thumbnail wp-image-6793" alt="siddaway3_cover150" src="http://mikefrobbins.com/wp-content/uploads/2013/02/siddaway3_cover150.jpg?w=119" width="119" height="150" /></a>The <a href="http://manning.com/siddaway3/" target="_blank">Learn Active Directory Management in a Month of Lunches</a> book by PowerShell MVP <a href="http://richardspowershellblog.wordpress.com/" target="_blank">Richard Siddaway</a> is now available on the <a href="http://manning.com/" target="_blank">Manning.com</a> website via their Early Access Program (MEAP). As Richard says in Chapter 1: This book is "A straight forward guide to administering Active Directory delivered in lunch sized pieces". It focuses on what you need to know to do your job as an Active Directory administrator in the real world.</span>

When I first heard of this book, I was excited because I thought it would be a pure PowerShell book since it's written by a PowerShell MVP who has written other books that are dedicated to PowerShell. I was also looking for a book on Active Directory that was updated for PowerShell version 3. What I discovered is this book isn't a pure PowerShell book, but a book that covers the best of both worlds, the GUI (Graphical User Interface) and PowerShell.

I hadn't previously used the Active Directory Administrative Center (ADAC) graphical user interface tool before starting to read this book. What I learned is that when using the ADAC GUI in Windows Server 2012, it shows the PowerShell command that will accomplish the same task. This feature alone is a learning mechanism in itself for anyone who is still using the GUI, but wants to learn PowerShell.

<span style="line-height:1.5;">Thanks to this book, now I also know that what an ex-coworker of mine use to call "the OU's without the picture of a ham sandwich in them" are containers and not organizational units. An OU is shown with a red box around it and a container is shown with a blue box around it in the image below:</span>

<img class="alignnone size-full wp-image-2454" alt="denali-service-accts4" src="http://mikefrobbins.com/wp-content/uploads/2011/06/denali-service-accts4.png" width="300" height="406" />

There are some other great details about these containers covered in this book.

My favorite part of the first seven chapters in this book is Chapter 6, Section 3 "Managing the secure channel".  I've personally experienced this issue where the secure channel between a computer and domain controller in Active Directory becomes corrupt. Now I know that I can use PowerShell to test and repair the problem if necessary rather than having to resort to removing the computer from the domain and re-adding it which can be a time consuming process due to the restarts and the other problems that removing it and re-adding it back to the domain can create.

This is a great book for any Active Directory administrator regardless of what level you're at in your career. Part one of this book which is what is currently available focuses on managing Active Directory Users and Computers to include Group Management, Organizational Units, and it touches on Group Policy. You're taught how to accomplish each task that is covered using both graphical interface tools: Active Directory Users and Computer (ADUC) and Active Directory Administrative Center (ADAC). You're also taught how to accomplish each of these tasks using PowerShell. I would have no issue turning this book over to my junior level administrators who often visit me when permissions need to be changed or added in Active Directory since this book covers concepts such as using <a href="http://en.wikipedia.org/wiki/AGDLP" target="_blank">AGDLP</a>. The book doesn't use that acronym, but it covers the concept well.

Interested in purchasing this book? Through February 26th, receive 50% off "<a href="http://manning.com/siddaway3/" target="_blank">Learn Active Directory Management in a Month of Lunches</a>" with promo code: <strong>learnadmau</strong>

µ