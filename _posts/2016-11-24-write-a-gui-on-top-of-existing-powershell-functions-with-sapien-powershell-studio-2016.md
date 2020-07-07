---
ID: 14715
post_title: >
  Write a GUI on Top of Existing
  PowerShell Functions with SAPIEN
  PowerShell Studio 2016
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2016/11/24/write-a-gui-on-top-of-existing-powershell-functions-with-sapien-powershell-studio-2016/
published: true
post_date: 2016-11-24 07:30:03
---
This blog article will demonstrate how to write a GUI on top of your existing PowerShell functions using <a href="https://www.sapien.com/software/powershell_studio" target="_blank">SAPIEN PowerShell Studio 2016</a>. I've previously written a couple of functions for managing SQL Server agent jobs. These two functions, <em>Get-MrSqlAgentJobStatus</em> and <em>Start-MrSqlAgentJob</em> can be found in <a href="https://github.com/mikefrobbins/SQL" target="_blank">my SQL repository on GitHub</a>.

Launch PowerShell Studio. Select file, the arrow next to new, and new form:

<img class="alignnone size-full wp-image-14725" src="http://mikefrobbins.com/wp-content/uploads/2016/11/sqlagent-gui1a.png" alt="sqlagent-gui1a" width="859" height="531" />

For this particular GUI, I'll select the "Dialog Style Template" since I want a fixed border without any minimize or maximize buttons:

<img class="alignnone size-full wp-image-14726" src="http://mikefrobbins.com/wp-content/uploads/2016/11/sqlagent-gui2a.png" alt="sqlagent-gui2a" width="686" height="358" />

One thing to note is the Toolbox auto-hides by default. It can be found on the left side of the screen:

<img class="alignnone size-full wp-image-14727" src="http://mikefrobbins.com/wp-content/uploads/2016/11/sqlagent-gui3a.png" alt="sqlagent-gui3a" width="859" height="489" />

Add another button to the form:

<img class="alignnone size-full wp-image-14728" src="http://mikefrobbins.com/wp-content/uploads/2016/11/sqlagent-gui4a.png" alt="sqlagent-gui4a" width="859" height="485" />

Click on the button on the form itself and them modify the properties in the lower right side of PowerShell Studio:

<img class="alignnone size-full wp-image-14729" src="http://mikefrobbins.com/wp-content/uploads/2016/11/sqlagent-gui5a.png" alt="sqlagent-gui5a" width="236" height="453" />

The text of both buttons and the size of one of them has been changed:

<img class="alignnone size-full wp-image-14730" src="http://mikefrobbins.com/wp-content/uploads/2016/11/sqlagent-gui6a.png" alt="sqlagent-gui6a" width="358" height="351" />

Double-click on the exit button and add the code to close the form:
<pre class="lang:ps decode:true">$buttonExit_Click={
	$form1.Close()
}</pre>
<img class="alignnone size-full wp-image-14731" src="http://mikefrobbins.com/wp-content/uploads/2016/11/sqlagent-gui7a.png" alt="sqlagent-gui7a" width="859" height="232" />

Add the existing functions to the project:

<img class="alignnone size-full wp-image-14732" src="http://mikefrobbins.com/wp-content/uploads/2016/11/sqlagent-gui8a.png" alt="sqlagent-gui8a" width="236" height="388" />

Select the functions to add:

<img class="alignnone size-full wp-image-14733" src="http://mikefrobbins.com/wp-content/uploads/2016/11/sqlagent-gui9a.png" alt="sqlagent-gui9a" width="656" height="372" />

You can see that the functions have been added:

<img class="alignnone size-full wp-image-14762" src="http://mikefrobbins.com/wp-content/uploads/2016/11/sqlagent-gui17a.png" alt="sqlagent-gui17a" width="236" height="388" />

They can also be seen (and modified if needed) in the script pane:

<img class="alignnone size-full wp-image-14734" src="http://mikefrobbins.com/wp-content/uploads/2016/11/sqlagent-gui10a.png" alt="sqlagent-gui10a" width="859" height="245" />

Either double-click on the other button from the Designer tab or simply add the button click event handler for it and write the code to wire the exiting functions up to it:
<pre class="lang:ps decode:true ">$buttonRunSQLAgentJob_Click={
	$Params = @{
		ServerInstance = 'SQL011'
		Name = 'test'
	}
	
	$result = Start-MrSQLAgentJob @Params
	
	if ($result -eq $true) {
		Start-Sleep -Seconds 5
		while ((Get-MrSQLAgentJobStatus @Params).Outcome -eq 'Executing') {
			$buttonRunSQLAgentJob.Text = 'In Progress'
			$buttonRunSQLAgentJob.BackColor = 'Orange'
			Start-Sleep -Seconds 20
		}
		
		if ((Get-MrSQLAgentJobStatus @Params).Status -eq 'Succeeded') {
			$buttonRunSQLAgentJob.Text = 'Success'
			$buttonRunSQLAgentJob.BackColor = 'Green'
		}
		else {
			$buttonRunSQLAgentJob.Text = 'Error!'
			$buttonRunSQLAgentJob.BackColor = 'Red'
		}
		
	}
	else {
		$buttonRunSQLAgentJob.Text = 'Error!'
		$buttonRunSQLAgentJob.BackColor = 'Red'
	}
}</pre>
The button simply calls the functions as needed and performs the necessary logic based on the results.

<img class="alignnone size-full wp-image-14735" src="http://mikefrobbins.com/wp-content/uploads/2016/11/sqlagent-gui11a.png" alt="sqlagent-gui11a" width="859" height="281" />

To run this GUI as a user who has been given access to run the SQL agent jobs without actually giving those rights to their user accounts, select "Settings" underneath Package:

<img class="alignnone size-full wp-image-14737" src="http://mikefrobbins.com/wp-content/uploads/2016/11/sqlagent-gui13a.png" alt="sqlagent-gui13a" width="376" height="148" />

On the Output Settings tab, set the Run Mode to "RunAs user" and enter the user name and password information for that account. This account should be setup with the privileged of least principle just in case someone is able to reverse engineer the executable and retrieve the credentials.

<img class="alignnone size-full wp-image-14739" src="http://mikefrobbins.com/wp-content/uploads/2016/11/sqlagent-gui14b.png" alt="sqlagent-gui14b" width="786" height="510" />

Select "Build" underneath package to create an executable:

<img class="alignnone size-full wp-image-14736" src="http://mikefrobbins.com/wp-content/uploads/2016/11/sqlagent-gui12a.png" alt="sqlagent-gui12a" width="422" height="139" />

Run the executable and then click the "Run SQL Agent Job" button:

<img class="alignnone size-full wp-image-14763" src="http://mikefrobbins.com/wp-content/uploads/2016/11/sqlagent-gui18a.png" alt="sqlagent-gui18a" width="300" height="301" />

The SQL agent job is running:

<img class="alignnone size-full wp-image-14740" src="http://mikefrobbins.com/wp-content/uploads/2016/11/sqlagent-gui15a.png" alt="sqlagent-gui15a" width="300" height="301" />

It completed successfully:

<img class="alignnone size-full wp-image-14741" src="http://mikefrobbins.com/wp-content/uploads/2016/11/sqlagent-gui16a.png" alt="sqlagent-gui16a" width="300" height="301" />

The GUI shown in this blog article is a proof of concept and certainly isn't meant to be a fully functional application. It's meant to show how to write a GUI to leverage your existing PowerShell functions so you don't have to reinvent the wheel. If you were writing a GUI to accomplish this task, you would at least want to first check to see if the job is running and disable the button to run it while the job is running.

Don't own PowerShell Studio? <a href="https://www.sapien.com/software/powershell_studio" target="_blank">Download the 45 day eval</a>.

µ