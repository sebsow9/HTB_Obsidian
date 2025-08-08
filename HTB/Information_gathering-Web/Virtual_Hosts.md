---
Title: HTB\Information_gathering-Web\Virtual_Hosts
tags:
  - os/linux
  - os/network- cybersec
source: HTB\Information_gathering-Web\Virtual_Hosts
---

# HTB\Information_gathering-Web\Virtual_Hosts

> Źródło: `HTB\Information_gathering-Web\Virtual_Hosts`

Some steps of loading a browser from a virtual host:
1. Browser Requests a Website: When you enter a domain name (e.g., www.inlanefreight.com) into your browser, it initiates an HTTP request to the web server associated with that domain's IP address.
2. Host Header Reveals the Domain: The browser includes the domain name in the request's Host header, which acts as a label to inform the web server which website is being requested.
3. Web Server Determines the Virtual Host: The web server receives the request, examines the Host header, and consults its virtual host configuration to find a matching entry for the requested domain name.
4. Serving the Right Content: Upon identifying the correct virtual host configuration, the web server retrieves the corresponding files and resources associated with that website from its document root and sends them back to the browser as the HTTP response.
In essence, the Host header functions as a switch, enabling the web server to dynamically determine which website to serve based on the domain name requested by the browser.

Types of virtual hosting:
1. Name-Based Virtual Hosting: This method relies solely on the HTTP Host header to distinguish between websites. It is the most common and flexible method, as it doesn't require multiple IP addresses. It’s cost-effective, easy to set up, and supports most modern web servers. However, it requires the web server to support name-based virtual hosting and can have limitations with certain protocols like SSL/TLS.
2. IP-Based Virtual Hosting: This type of hosting assigns a unique IP address to each website hosted on the server. The server determines which website to serve based on the IP address to which the request was sent. It doesn't rely on the Host header, can be used with any protocol, and offers better isolation between websites. Still, it requires multiple IP addresses, which can be expensive and less scalable.
3. Port-Based Virtual Hosting: Different websites are associated with different ports on the same IP address. For example, one website might be accessible on port 80, while another is on port 8080. Port-based virtual hosting can be used when IP addresses are limited, but it’s not as common or user-friendly as name-based virtual hosting and might require users to specify the port number in the URL.

Virtual host discovery tools:

gobuster -	A multi-purpose tool often used for directory/file brute-forcing, but also effective for virtual host discovery. -	Fast, supports multiple HTTP methods, can use custom wordlists. (link: https://github.com/OJ/gobuster)
Feroxbuster - Similar to Gobuster, but with a Rust-based implementation, known for its speed and flexibility. -	Supports recursion, wildcard discovery, and various filters. (link: https://github.com/epi052/feroxbuster)
ffuf -	Another fast web fuzzer that can be used for virtual host discovery by fuzzing the Host header. -	Customizable wordlist input and filtering options. (link: https://github.com/ffuf/ffuf)

gobuster v host search usage:
gobuster vhost -u http://<target_IP_address> -w <wordlist_file> --append-domain
The -u flag specifies the target URL (replace <target_IP_address> with the actual IP).
The -w flag specifies the wordlist file (replace <wordlist_file> with the path to your wordlist).
The --append-domain flag appends the base domain to each word in the wordlist.

Consider using the -t flag to increase the number of threads for faster scanning.
The -k flag can ignore SSL/TLS certificate errors.
You can use the -o flag to save the output to a file for later analysis.

Virtual host discovery can generate significant traffic and might be detected by intrusion detection systems (IDS) or web application firewalls (WAF). Exercise caution and obtain proper authorization before scanning any targets.

solving the task:
used commands:
added a host in /etc/hosts
94.237.49.188 inlanefreight.htb
than performed a vhost lookup
gobuster vhost -u http://inlanefreight.htb:55658 -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-110000.txt --append-domain

**Powiązane:** [[Graph-Home]] [[OS-Linux]] [[OS-Network]]
