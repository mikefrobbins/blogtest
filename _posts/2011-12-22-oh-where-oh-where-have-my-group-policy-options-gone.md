---
ID: 3172
post_title: >
  Oh Where, Oh Where Have My Group Policy
  Options Gone?
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2011/12/22/oh-where-oh-where-have-my-group-policy-options-gone/
published: true
post_date: 2011-12-22 07:30:44
---
You are unable to find specific <a href="http://en.wikipedia.org/wiki/Group_Policy" target="_blank">GPO</a> options such as “Compatibility View” settings for Internet Explorer. One of the first things to look at is: Where are the policy definitions being retrieved from? The default for an Active Directory environment is from the local machine as shown in the image below:

<a href="http://mikefrobbins.com/wp-content/uploads/2011/12/missing-gpo-settings1.png"><img class="alignnone size-full wp-image-3173" title="missing-gpo-settings1" src="http://mikefrobbins.com/wp-content/uploads/2011/12/missing-gpo-settings1.png" alt="" width="537" height="570" /></a>

If you’re editing the GPO on a domain controller and have multiple domain controllers that are running different operating system versions, the available options will vary from machine to machine. Setting a GPO option on a machine with newer ADMX files:

<a href="http://mikefrobbins.com/wp-content/uploads/2011/12/missing-gpo-settings61.png"><img class="alignnone size-full wp-image-3211" title="missing-gpo-settings61" src="http://mikefrobbins.com/wp-content/uploads/2011/12/missing-gpo-settings61.png" alt="" width="640" height="217" /></a>

And then viewing the report for the same setting on a machine with older ADMX files that are unaware of that particular option will result in it showing as "Extra Registry Settings":

<a href="http://mikefrobbins.com/wp-content/uploads/2011/12/missing-gpo-settings51.png"><img class="alignnone size-full wp-image-3210" title="missing-gpo-settings51" src="http://mikefrobbins.com/wp-content/uploads/2011/12/missing-gpo-settings51.png" alt="" width="640" height="253" /></a>

To have the same options available from any machine you’re editing the GPO from, you’ll need to create a central store for the group policy administrative templates. To create a central store, copy the "C:\Windows\PolicyDefinitions" folder from one of the domain controllers (preferably the one with the newest operating system version on it out of all of your domain controllers) to "\domain.name\sysvol\domain.name\Policies":

<a href="http://mikefrobbins.com/wp-content/uploads/2011/12/missing-gpo-settings2.png"><img class="alignnone size-full wp-image-3174" title="missing-gpo-settings2" src="http://mikefrobbins.com/wp-content/uploads/2011/12/missing-gpo-settings2.png" alt="" width="640" height="170" /></a>

The problem this creates is these policy definitions are not updated automatically as they would be if the local machine ones were being used. You can see the ADMX file for Internet Explorer (inetres.admx) is much newer than the other files in the local machine folder:

<a href="http://mikefrobbins.com/wp-content/uploads/2011/12/missing-gpo-settings21.png"><img class="alignnone size-full wp-image-3181" title="missing-gpo-settings21" src="http://mikefrobbins.com/wp-content/uploads/2011/12/missing-gpo-settings21.png" alt="" width="640" height="193" /></a>

This is because it was updated automatically when Internet Explorer 9 was installed.

If you’re missing settings, compare the ADMX files in the central store to the local machine ones in "C:\Windows\PolicyDefinitions":

<a href="http://mikefrobbins.com/wp-content/uploads/2011/12/missing-gpo-settings3.png"><img class="alignnone size-full wp-image-3175" title="missing-gpo-settings3" src="http://mikefrobbins.com/wp-content/uploads/2011/12/missing-gpo-settings3.png" alt="" width="640" height="126" /></a>

You can also download updated ADMX files from Microsoft for newer products such as IE9 that you may not already have an updated ADMX file for. The IE9 ones are part of the <a href="http://www.microsoft.com/download/en/details.aspx?id=23643" target="_blank">Internet Explorer Administration Kit</a> (IEAK). Place them in the central store to make IE9 specific options available when modifying the GPO’s.

Once you have a central store, the GPO will retrieve the policy definitions from it:

<a href="http://mikefrobbins.com/wp-content/uploads/2011/12/missing-gpo-settings4.png"><img class="alignnone size-full wp-image-3176" title="missing-gpo-settings4" src="http://mikefrobbins.com/wp-content/uploads/2011/12/missing-gpo-settings4.png" alt="" width="513" height="539" /></a>

You’ll notice that I no longer have a “Compatibility View” folder under "Internet Explorer" in the image above even though this is on the same domain controller as before. That’s because the ADMX files for IE (inetres.admx and .adml) in the Central Store are older and don’t have those particular settings.

There's a good article on MSDN: "<a href="http://msdn.microsoft.com/en-us/library/bb530196.aspx" target="_blank">Managing Group Policy ADMX Files Step-by-Step Guide</a>" and another good article from Microsoft Support: "<a href="http://support.microsoft.com/kb/929841" target="_blank">How to create a Central Store for Group Policy Administrative Templates in Windows Vista</a>" on this topic.

µ