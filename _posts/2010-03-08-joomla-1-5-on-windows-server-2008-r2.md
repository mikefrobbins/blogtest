---
ID: 614
post_title: Joomla 1.5 on Windows Server 2008 R2
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2010/03/08/joomla-1-5-on-windows-server-2008-r2/
published: true
post_date: 2010-03-08 12:17:52
---
<a href="http://mikefrobbins.com/wp-content/uploads/2010/03/joomla_vert_logo_90x70.jpg"><img class="alignleft size-full wp-image-635" title="joomla_vert_logo_90x70" alt="" src="http://mikefrobbins.com/wp-content/uploads/2010/03/joomla_vert_logo_90x70.jpg" width="90" height="70" /></a>I will be diving into the dark side with this blog since I prefer working with Microsoft based technolgies such as SharePoint and ASP.NET. Each time I work with PHP and MySQL, it seems like it takes a little Voodoo to make them work. This blog is the bare minimum configuration to get Joomla working on a Windows 2008 R2 Server with Apache, PHP, and a remote MySQL server.

First, download the latest version of <a href="http://httpd.apache.org/download.cgi" target="_blank">Apache HTTP Server</a> win32 binary without crypto MSI installer, <a href="http://windows.php.net/download/" target="_blank">PHP</a> VC6 thread safe zip file, <a href="http://www.mysql.com/downloads/mysql/" target="_blank">MySQL</a> Community Server essential 64 bit MSI installer, and <a href="http://www.joomla.org/download.html" target="_blank">Joomla</a>. Currently, Apache does not have an official 64 bit version, but the 32 bit version works fine on 64 bit operating systems. For PHP, make sure you download a VC6 version since the VC9 versions are only for use with IIS.  If for some reason you are going to move an existing Joomla 1.0 installation to a new server without upgrading it to Joomla 1.5, be sure to use PHP 5.2.x since PHP 5.3.x is not supported with Joomla 1.0. The content on your pages will not display correctly if you use Joomla 1.0 and PHP 5.3.x. For this demonstration, I have built two Windows 2008 R2 servers, one will host the frontend or Apache, PHP, and Joomla, and the other will host the backend or MySQL.

On the frontend server install Apache, do a custom install and take all of the defaults except change the drive letter to the data drive. Opening up the operating system drive or partition to the Internet via a web server is bad mojo. Add the httpd.exe program to the allowed programs firewall exception list. At this point you should be able to access the web server and receive a test page that says “It Works!”

Next, unzip the PHP zip file into a subfolder of the Apache folder and name the folder php. Unzip the Joomla zip file into a subfolder of the Apache\htdocs folder and name the folder joomla. Make a copy of the Apache\conf\httpd.conf file and open the original file in your favorite text editor. Add the following in the section with all of the other “LoadModules”:
<pre class="lang:php decode:true">LoadModule php5_module php/php5apache2_2.dll</pre>
Add the following towards the bottom of the file:
<pre class="lang:php decode:true">AddType application/x-httpd-php .php
PHPIniDir "D:/Program Files (x86)/Apache Software Foundation/Apache2.2/php/"</pre>
This section already exists; you just need to add the additional default documents:
<pre class="lang:php decode:true">&lt;IfModule dir_module&gt;
DirectoryIndex index.html index.php main.php index.html.var index.cgi
&lt;/IfModule&gt;</pre>
Change both of these lines to the new document root location:
<pre class="lang:php decode:true">&lt;Directory "D:/Program Files (x86)/Apache Software Foundation/Apache2.2/htdocs/joomla"&gt;
DocumentRoot "D:/Program Files (x86)/Apache Software Foundation/Apache2.2/htdocs/joomla"</pre>
Make a copy of the Apachephpphp.ini-production and name it php.ini. Open the php.ini in your favorite text editor. Uncomment the following three lines and set the path for the extension directory:
<pre class="lang:php decode:true">extension=php_mbstring.dll
extension=php_mysql.dll
extension_dir = "D:/Program Files (x86)/Apache Software Foundation/Apache2.2/php/ext"</pre>
Restart the Apache service on the frontend server.

Install MySQL on the database server. Choose the custom install option and change the destination drive to the data drive. Select configure MySQL now and take the standard configuration option. Select the “Install as Windows Service” and “Include Bin Directory in Windows PATH” options. Set a root password.

Open the MySQL Command Line Client. Create a database and user for Joomla. Assign the user permissions to the database.
<pre class="lang:mysql decode:true">CREATE DATABASE joomladb;
CREATE USER 'joomladbuser'@'%' IDENTIFIED BY 'joomladbpassword';
GRANT ALL PRIVILEGES ON `joomladb` . * TO 'joomladbuser'@'%';</pre>
<span style="font-family: Georgia, 'Times New Roman', 'Bitstream Charter', Times, serif; line-height: 19px; white-space: normal; font-size: 13px;"><a href="http://mikefrobbins.com/wp-content/uploads/2010/03/joomladb-create1.jpg"><img class="alignnone size-full wp-image-663" title="joomladb-create" alt="" src="http://mikefrobbins.com/wp-content/uploads/2010/03/joomladb-create1.jpg" width="600" height="199" /></a></span>

Add the mysqld.exe program to the allowed programs firewall exception list on the backend server and restart the MySQL service.

Open a web browser and direct it to the DNS name of the website. The Joomla install should start. On the pre-installation check, if all of the options are not in green, you did something wrong.
<a href="http://mikefrobbins.com/wp-content/uploads/2010/03/joomla-preinstall_check.jpg"><img class="alignnone size-full wp-image-615" title="joomla-preinstall_check" alt="" src="http://mikefrobbins.com/wp-content/uploads/2010/03/joomla-preinstall_check.jpg" width="600" height="345" /></a>

Enter the information you used when creating the database and user on this page:
<a href="http://mikefrobbins.com/wp-content/uploads/2010/03/joomla-dbsettings.jpg"><img class="alignnone size-full wp-image-666" title="joomla-dbsettings" alt="" src="http://mikefrobbins.com/wp-content/uploads/2010/03/joomla-dbsettings.jpg" width="600" height="359" /></a>

Enter the appropriate information on this page. If you want to install the sample data, you actually need to click the button, just having the radio button selected and clicking next will not install the sample data:
<a href="http://mikefrobbins.com/wp-content/uploads/2010/03/joomla-sampledata.jpg"><img class="alignnone size-full wp-image-668" title="joomla-sampledata" alt="" src="http://mikefrobbins.com/wp-content/uploads/2010/03/joomla-sampledata.jpg" width="600" height="385" /></a>

Once the installation is finished, you will need to remove the installation folder that is located in the Apache\htdocs\joomla folder before you will be able to access the website.

You now have a fully working version of Joomla installed:
<a href="http://mikefrobbins.com/wp-content/uploads/2010/03/joomla-mainpage.jpg"><img class="alignnone size-full wp-image-616" title="joomla-mainpage" alt="" src="http://mikefrobbins.com/wp-content/uploads/2010/03/joomla-mainpage.jpg" width="600" height="358" /></a>

Troubleshooting PHP. Create a phptest.php file in the Apachehtdocsjoomla directory that contains the following:
<pre class="lang:php decode:true">&lt;?php
phpinfo();
?&gt;</pre>
Navigate to the DNS name of the website /phptest.php. You should receive a page that shows all of the PHP settings:
<a href="http://mikefrobbins.com/wp-content/uploads/2010/03/php-info.jpg"><img class="alignnone size-full wp-image-617" title="php-info" alt="" src="http://mikefrobbins.com/wp-content/uploads/2010/03/php-info.jpg" width="600" height="314" /></a>

Troubleshooting MySQL Connectivity. Create a mysqltext.php in the Apachehtdocsjoomla directory that contains the following:
<pre class="lang:php decode:true">&lt;?php
mysql_connect("mysqlservername", "mysqlusername", "mysqluserpassword") or die(mysql_error());
echo "Connected to MySQL&lt;br /&gt;";
?&gt;</pre>
Navigate to the DNS name of the website /mysqltest.php. You should receive a page that shows:
<a href="http://mikefrobbins.com/wp-content/uploads/2010/03/connected-to-mysql.jpg"><img class="alignnone size-full wp-image-618" title="connected-to-mysql" alt="" src="http://mikefrobbins.com/wp-content/uploads/2010/03/connected-to-mysql.jpg" width="151" height="38" /></a>

If you receive nothing at all or an HTTP 500 Internal Server Error, edit the php.ini file and set the following. Restart the Apache service on the frontend server for the changes to take effect.
<pre class="lang:php decode:true">display_errors = On</pre>
This will allow you to see the actual error message which may be something similar to this:

<a href="http://mikefrobbins.com/wp-content/uploads/2010/03/mysql-fatal_error.jpg"><img class="alignnone size-full wp-image-619" title="mysql-fatal_error" alt="" src="http://mikefrobbins.com/wp-content/uploads/2010/03/mysql-fatal_error.jpg" width="600" height="28" /></a>

This is most commonly caused by not uncommenting the two modules in the php.ini file listed above or not setting the extension_dir parameter correctly in the php.ini.

Be sure to turn the display_errors option to off and delete both the php and mysql test files before placing the server into production.

µ