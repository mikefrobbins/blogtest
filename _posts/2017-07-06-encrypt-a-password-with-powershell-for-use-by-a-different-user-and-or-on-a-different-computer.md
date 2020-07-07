---
ID: 15376
post_title: >
  Encrypt a Password with PowerShell for
  use by a Different User and/or on a
  Different Computer
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2017/07/06/encrypt-a-password-with-powershell-for-use-by-a-different-user-and-or-on-a-different-computer/
published: true
post_date: 2017-07-06 07:30:33
---
Storing a password in an encrypted file for use by the same user on the same computer using PowerShell is fairly easy, but storing a password in an encrypted file for use on another computer or by another user is a bit more challenging. It requires the use of a key file and of course if someone else can read the key file, then they also can decrypt the password.

In my scenario, I work in a VDI (Virtual Desktop Infrastructure) environment that doesn't use persistent desktops and I need to have a certificate available to decrypt emails from certain individuals. The certificate is password protected and does not exist in the certificate store each time I logon.

I'll start out by defining a few things such as the files and my super secure password that will be used throughout this blog article:
<pre class="lang:ps decode:true">$EncryptionKeyFile = 'C:\tmp\registry-backup070517.reg'
$PasswordFile = 'C:\tmp\license-key.txt'
$Password = 'MyP@ssW0rd'</pre>
Don't make things too easy for someone trying to find your key file. Notice that the file for mine uses a naming convention that appears to be a registry key backup and the encrypted password has a file name that appears to be a license key. Also consider setting NTFS permissions on these files so only the necessary users and/or computers have access to them.

Create a 256 bit AES key that's an array of thirty-two 8 bit unsigned integers. This will be used as the encryption key:
<pre class="lang:ps decode:true">Get-Random -Count 32 -InputObject (0..255) |
Out-File -FilePath $EncryptionKeyFile</pre>
Encrypt the password with the previously generated key and store it in a file:
<pre class="lang:ps decode:true ">ConvertTo-SecureString -String $Password -AsPlainText -Force |
ConvertFrom-SecureString -Key (Get-Content -Path $EncryptionKeyFile) |
Out-File -FilePath $PasswordFile</pre>
Now for the command I want to run when I login to my VDI computer that imports the certificate:
<pre class="lang:ps decode:true">Import-PfxCertificate -FilePath C:\tmp\BrowserCertificate.p12 -Password (Get-Content -Path C:\tmp\pass.txt |
ConvertTo-SecureString -Key (Get-Content -Path C:\tmp\registry-backup070517.key)) -CertStoreLocation Cert:\CurrentUser\My -Exportable</pre>
The problem is that as soon as someone looks at the file I plan to save that command in, they will know exactly how to decrypt the password. I'll obfuscate the command to at least make it a little more difficult for them.
<pre class="lang:ps decode:true">[Convert]::ToBase64String([System.Text.Encoding]::Unicode.GetBytes('Import-PfxCertificate -FilePath C:\tmp\BrowserCertificate.p12 -Password (Get-Content -Path C:\tmp\pass.txt | ConvertTo-SecureString -Key (Get-Content -Path C:\tmp\registry-backup070517.key)) -CertStoreLocation Cert:\CurrentUser\My -Exportable'))</pre>
The results will definitely keep what I'm doing more secure than plain text.
<pre class="lang:ps decode:true">SQBtAHAAbwByAHQALQBQAGYAeABDAGUAcgB0AGkAZgBpAGMAYQB0AGUAIAAtAEYAaQBsAGUAUABhAHQAaAAgAEMAOgBcAHQAbQBwAFwAQgByAG8AdwBzAGUAcgBDAGUAcgB0AGkAZ
gBpAGMAYQB0AGUALgBwADEAMgAgAC0AUABhAHMAcwB3AG8AcgBkACAAKABHAGUAdAAtAEMAbwBuAHQAZQBuAHQAIAAtAFAAYQB0AGgAIABDADoAXAB0AG0AcABcAHAAYQBzAHMALg
B0AHgAdAAgAHwAIABDAG8AbgB2AGUAcgB0AFQAbwAtAFMAZQBjAHUAcgBlAFMAdAByAGkAbgBnACAALQBLAGUAeQAgACgARwBlAHQALQBDAG8AbgB0AGUAbgB0ACAALQBQAGEAdAB
oACAAQwA6AFwAdABtAHAAXAByAGUAZwBpAHMAdAByAHkALQBiAGEAYwBrAHUAcAAwADcAMAA1ADEANwAuAGsAZQB5ACkAKQAgAC0AQwBlAHIAdABTAHQAbwByAGUATABvAGMAYQB0
AGkAbwBuACAAQwBlAHIAdAA6AFwAQwB1AHIAcgBlAG4AdABVAHMAZQByAFwATQB5ACAALQBFAHgAcABvAHIAdABhAGIAbABlAA==</pre>
Now I just need to add the following command to a bat or cmd file that's in the startup for my user on my VDI computer and I'll be all set:
<pre class="lang:ps decode:true ">powershell.exe -NoProfile -NonInteractive -WindowStyle Hidden -ExecutionPolicy Bypass -EncodedCommand SQBtAHAAbwByAHQALQBQAGYAeABDAGUAcgB0AGkAZgBpAGMAYQB0AGUAIAAtAEYAaQBsAGUAUABhAHQAaAAgAEMAOgBcAHQAbQBwAFwAQgByAG8AdwBzAGUAcgBDAGUAcgB0AGkAZ
gBpAGMAYQB0AGUALgBwADEAMgAgAC0AUABhAHMAcwB3AG8AcgBkACAAKABHAGUAdAAtAEMAbwBuAHQAZQBuAHQAIAAtAFAAYQB0AGgAIABDADoAXAB0AG0AcABcAHAAYQBzAHMALg
B0AHgAdAAgAHwAIABDAG8AbgB2AGUAcgB0AFQAbwAtAFMAZQBjAHUAcgBlAFMAdAByAGkAbgBnACAALQBLAGUAeQAgACgARwBlAHQALQBDAG8AbgB0AGUAbgB0ACAALQBQAGEAdAB
oACAAQwA6AFwAdABtAHAAXAByAGUAZwBpAHMAdAByAHkALQBiAGEAYwBrAHUAcAAwADcAMAA1ADEANwAuAGsAZQB5ACkAKQAgAC0AQwBlAHIAdABTAHQAbwByAGUATABvAGMAYQB0
AGkAbwBuACAAQwBlAHIAdAA6AFwAQwB1AHIAcgBlAG4AdABVAHMAZQByAFwATQB5ACAALQBFAHgAcABvAHIAdABhAGIAbABlAA==</pre>
If you know of a better way to shared a saved password within PowerShell between different users and/or computers, please share via a comment to this blog article.

I recently wrote a blog article on "<a href="http://mikefrobbins.com/2017/06/15/simple-obfuscation-with-powershell-using-base64-encoding/" target="_blank" rel="noopener">Simple Obfuscation with PowerShell using Base64 Encoding</a>" that you might also find interesting.

µ