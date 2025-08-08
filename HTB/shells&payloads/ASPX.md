---
Title: HTB\shells&payloads\ASPX
tags:
  - os/windows- cybersec
source: HTB\shells&payloads\ASPX
---

# HTB\shells&payloads\ASPX

> Źródło: `HTB\shells&payloads\ASPX`

Active Server Page Extended (ASPX) is a file type/extension written for Microsoft's ASP.NET Framework.
On a web server running the ASP.NET framework, web form pages can be generated for users to input data. On the server side, the information will be converted into HTML.
We can take advantage of this by using an ASPX-based web shell to control the underlying Windows operating system. Let's witness this first-hand by utilizing the Antak Webshell.

Antak Webshell
Antak is a web shell built in ASP.Net included within the Nishang project.
Nishang is an Offensive PowerShell toolset that can provide options for any portion of your pentest.
Since we are focused on web applications for the moment, let's keep our eyes on Antak.
Antak utilizes PowerShell to interact with the host, making it great for acquiring a web shell on a Windows server.
The UI is even themed like PowerShell. It's time to dive in and experiment with Antak.

Working with Antak
The Antak files can be found in the /usr/share/nishang/Antak-WebShell directory.
Ovas420@htb[/htb]$ ls /usr/share/nishang/Antak-WebShell

antak.aspx  Readme.md

Antak web shell functions like a Powershell Console.
However, it will execute each command as a new process.
It can also execute scripts in memory and encode commands you send. As a web shell, Antak is a pretty powerful tool.

Move copy for modification
Ovas420@htb[/htb]$ cp /usr/share/nishang/Antak-WebShell/antak.aspx /home/administrator/Upload.aspx
Make sure you set credentials for access to the web shell. Modify line 14, adding a user (green arrow) and password (orange arrow).
This comes into play when you browse to your web shell, much like Laudanum.
This can help make your operations more secure by ensuring random people can't just stumble into using the shell.
It can be prudent to remove the ASCII art and comments from the file. These items in a payload are often signatured on and can alert the defenders/AV to what you are doing.

Modify the shell for use
For the sake of demonstrating the tool, we are uploading it to the same status portal we used for Laudanum.
That host was a Windows host, so our shell should work just fine with PowerShell.
Upload the file and then navigate to the page for use. It will give you a user and password prompt.
Remember, with this web application, the files are stored in the \\files\ directory.
When you navigate to the upload.aspx file, you should see a prompt as we have below.

**Powiązane:** [[Graph-Home]] [[OS-Windows]]
