---
ID: 3579
post_title: 'Using PowerShell to Ace the Scripting &#038; Regular Expression Interview Question'
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2012/03/22/using-powershell-to-ace-the-scripting-regular-expression-interview-question/
published: true
post_date: 2012-03-22 07:30:17
---
Last week I saw a tweet that referenced a couple of interesting articles on interview questions for programmers. I'm no programmer, but one of them (Steve Yegge's <a href="https://sites.google.com/site/steveyegge2/five-essential-phone-screen-questions" target="_blank">The Five Essential Phone-Screen Questions</a>) had an interesting question about identifying which of 50,000 web pages had phone numbers in them. You have to identify all of the web pages with phone numbers hard coded in them. All of the web pages are HTML files in a directory tree under a folder named website. The phone numbers are in the format of ###-###-#### or (###) ###-####. You have two days to deliver a list of the files with phone numbers in them and their paths. I'm paraphrasing the requirements.

I couple of days after I read this article, I started reading another book on PowerShell (via <a href="http://www.safaribooksonline.com/" target="_blank">Safari Books Online</a>) named "Windows PowerShell in Action, Second Edition" by Bruce Payette. The first page in Chapter One had an example of piping Dir to Select-String to do a search for the word "error" in the log files from the Windows directory. This gave me an idea about how to solve the phone number problem. Why not use PowerShell? Displaying help on <em>Select-String</em> confirmed that it is able to use regular expressions.
<pre class="lang:ps decode:true">Get-ChildItem -Path /website -Filter *.html -Recurse |
Select-String -Pattern "^(?:\(\d{3}\)\ ?|(?:\d{3}\-))\d{3}\-\d{4}$" |
Format-Table Path, Linenumber –AutoSize</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2012/03/ps-search4phone1.png"><img class="alignnone size-full wp-image-3581" title="ps-search4phone1" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/03/ps-search4phone1.png" width="366" height="140" /></a>

I also recently learned that if a line ends with a pipe symbol or comma then you don't need to use the backtick (grave accent) character for line continuation. That tip came from "Learn Windows PowerShell in a Month of Lunches" by Don Jones which is also available on Safari Books Online.

µ