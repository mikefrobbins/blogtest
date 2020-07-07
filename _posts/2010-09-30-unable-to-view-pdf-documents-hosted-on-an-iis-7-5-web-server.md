---
ID: 1308
post_title: >
  Unable to view PDF documents hosted on
  an IIS 7.5 Web Server
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2010/09/30/unable-to-view-pdf-documents-hosted-on-an-iis-7-5-web-server/
published: true
post_date: 2010-09-30 21:00:37
---
If you’ve recently migrated a web server that houses downloadable PDF documents to IIS 7.5, your users may be encountering issues viewing some of the PDF documents in a web browser. This is due to a change in the way IIS 7.5 handles the downloading of PDF documents. It doesn't affect all versions of Adobe Reader and it doesn't affect all PDF documents so it is very difficult to troubleshoot. Updating Adobe Reader to the latest version on the client end will normally resolve the problem. There's a <a href="http://support.microsoft.com/kb/979543/en-us" target="_blank">hotfix</a> available upon request from Microsoft that resolves this problem on the server side since if your IIS 7.5 server is publicly accessible, updating Adobe Reader to the latest version on the client end may not be a feasible solution.

µ