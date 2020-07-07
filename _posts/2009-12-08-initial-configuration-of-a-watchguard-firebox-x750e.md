---
ID: 385
post_title: >
  Initial Configuration of a WatchGuard
  Firebox X750e
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2009/12/08/initial-configuration-of-a-watchguard-firebox-x750e/
published: true
post_date: 2009-12-08 19:10:11
---
The Instructions listed below are designed to assist you in performing a basic configuration on a new (out of the box) WatchGuard Firebox X750e:

Record the serial number of the Firebox: _____________________________

Log into <a href="http://www.watchguard.com/activate" target="_blank">http://www.watchguard.com/activate</a> and register the firewall.

Save the appliance feature key that is provided during the registration process to a text file. It should look similar to this:

Serial Number: XXXXXXXXXX
License ID: XXXXXXXXXX
Name: XXXXXXXXXX
Model: XXXXXXXXXX
Version: XXXXXXXXXX
Feature: XXXXXXXXXX
Feature: XXXXXXXXXX
Feature: XXXXXXXXXX
Feature: XXXXXXXXXX
Feature: XXXXXXXXXX
Feature: XXXXXXXXXX
Feature: XXXXXXXXXX
Feature: XXXXXXXXXX
Expiration: XXXXXXXXXX
Signature: XXXXXXXXXXXXXXXXXX

Download the latest WatchGuard System Manager and Fireware appliance software for the product family you’re configuring from: <a href="https://www.watchguard.com/archive/softwarecenter.asp" target="_blank">https://www.watchguard.com/archive/softwarecenter.asp</a>
I would choose an established version. I chose 10.2.11 even though 11.1 was available. Install the WatchGuard System Manager and Fireware Appliance software on the pc that will be used to configure the FireBox. Accept all of the defaults during these installations.

Determine the following information (from the Firebox's point of view) before continuing:
Default Gateway:    ___.___.___.___
External Interface:  ___.___.___.___/___
Trusted Interface:   ___.___.___.___/___

The "Default Gateway" is usually the LAN side of your router that connects you to the Internet.
The "External Interface" is a public ip-address in the range that your ISP provided to you along with the subnet mask in CIDR notation.
The "Trusted Interface" is a private ip address such as 10.0.0.254 along with the subnet mask in CIDR notation. This will be the default gateway for pc's on your LAN.

Use a crossover cable to connect Ethernet Port 1 of the Firebox to the Ethernet port of the pc that is being used to configure the firewall. The Ethernet port of the pc must be set to use DHCP.

Hold down the up arrow on the firebox and power it on. Continue holding the up arrow until the display reads “Invoking Recovery”. The display will read “Safe Mode” once the boot process is complete.

Browse to http://10.0.1.1:8080 on the pc that is connected to the firewall to begin the Quick Setup Wizard.

Continue to the “Install software on your Firebox” screen. Browse to the “C:Program FilesCommon FilesWatchGuardresourcesFireware10.2” folder and select the .wgf file. Continue with the wizard.

The Quick Setup Wizard will update the software on your firebox at this point. Be patient.

Once the software update process is complete, you will be prompted to enter the following information:
Firebox Name:       ____________
Firebox Location:   ____________
Contact Person:    ____________

Configure the External interface of your Firebox by selecting “Static IP Addressing” and entering the following information:
IP Address:           ___.___.___.___/___
Default Gateway: ___.___.___.___

Configure the Internal interface of your firebox by entering the following information:
Trusted Interface: ___.___.___.___/___
Optional Interface: This box should be empty (blank).

Configure the DNS information by checking the checkmark to manually enter the information and entering the following information:
Domain Name: This box should be empty (blank).
DNS Servers:   ___.___.___.___
#2                   ___.___.___.___
The DNS Servers should be your ISP’s DNS servers (not your internal DNS servers).

Activate the software for your firebox by copying and pasting the feature key that was previously saved when the firebox was activated.

Enter complex passwords of at least 14 characters when prompted for the status and configuration passphrases. Save the passphrases using a secure method such as with <a href="http://keepass.info/" target="_blank">KeePass Password Safe</a>.

Save the configuration to the firebox when prompted. You now have a basic configuration on your firebox that will now allow it to be managed with WatchGuard System Manager.

This basic configuration provides the following:
All TCP, DNS, and ICMP traffic is allowed from the trusted or optional interfaces to the external interface.
All traffic from external interfaces to the trusted or optional interface is blocked.
All traffic between the trusted and optional interface is blocked.
You can manage the firebox from the trusted (or optional interface if you configured it).

µ