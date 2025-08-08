---
Title: HTB\Information_gathering-Web\Fingerprinting
tags:
  - os/linux
  - os/network- cybersec
source: HTB\Information_gathering-Web\Fingerprinting
---

# HTB\Information_gathering-Web\Fingerprinting

> Źródło: `HTB\Information_gathering-Web\Fingerprinting`

Fingerprinting servers as a cornerstone of web reconnaissance for several reasons:
1. Targeted Attacks: By knowing the specific technologies in use, attackers can focus their efforts on exploits and vulnerabilities that are known to affect those systems. This significantly increases the chances of a successful compromise.
2. Identifying Misconfigurations: Fingerprinting can expose misconfigured or outdated software, default settings, or other weaknesses that might not be apparent through other reconnaissance methods.
3. Prioritising Targets: When faced with multiple potential targets, fingerprinting helps prioritise efforts by identifying systems more likely to be vulnerable or hold valuable information.
4. Building a Comprehensive Profile: Combining fingerprint data with other reconnaissance findings creates a holistic view of the target's infrastructure, aiding in understanding its overall security posture and potential attack vectors.

Techniques
- Banner Grabbing: Banner grabbing involves analysing the banners presented by web servers and other services. These banners often reveal the server software, version numbers, and other details.
- Analysing HTTP Headers: HTTP headers transmitted with every web page request and response contain a wealth of information. The Server header typically discloses the web server software, while the X-Powered-By header might reveal additional technologies like scripting languages or frameworks.
- Probing for Specific Responses: Sending specially crafted requests to the target can elicit unique responses that reveal specific technologies or versions. For example, certain error messages or behaviours are characteristic of particular web servers or software components.
- Analysing Page Content: A web page's content, including its structure, scripts, and other elements, can often provide clues about the underlying technologies. There may be a copyright header that indicates specific software being used

Some tools/technologies:

Wappalyzer -	Browser extension and online service for website technology profiling. -	Identifies a wide range of web technologies, including CMSs, frameworks, analytics tools, and more.
BuiltWith -	Web technology profiler that provides detailed reports on a website's technology stack. -	Offers both free and paid plans with varying levels of detail.
WhatWeb -	Command-line tool for website fingerprinting. -	Uses a vast database of signatures to identify various web technologies.
Nmap -	Versatile network scanner that can be used for various reconnaissance tasks, including service and OS fingerprinting.	- Can be used with scripts (NSE) to perform more specialised fingerprinting.
Netcraft -	Offers a range of web security services, including website fingerprinting and security reporting.	- Provides detailed reports on a website's technology, hosting provider, and security posture.
wafw00f -	Command-line tool specifically designed for identifying Web Application Firewalls (WAFs).	- Helps determine if a WAF is present and, if so, its type and configuration.

Wafw00f - web application firewalls are security solutions designed to protect web applications from various attacks. Before proceeding with further fingerpinting,
it's crucial to determine if a website employs a WAF, as it could interfere with our probes or potentially block our requests.

To detect the presence of WAF, we'll use the wafw00f tool. To install wafw00f, use pip3:
```bash
pip3 install git+https://github.com/EnableSecurity/wafw00f
```

usage:
wafw00f <domain> (eg. inlanefreight.com)

Nikto
its a a web server scanner. In addition to its primary function as a vulnerability assessment tool, Nikto's fingerprinting capabilities provide insights into a website's technology stack.

installation:
```bash
sudo apt update && sudo apt install -y perl
```
git clone https://github.com/sullo/nikto
cd nikto/program
```bash
chmod +x ./nikto.pl
```

usage(only fingerprinting):
nikto -h inlanefreight.com -Tuning b
The -h flag specifies the target host. The -Tuning b flag tells Nikto to only run the Software Identification modules.

**Powiązane:** [[Graph-Home]] [[OS-Linux]] [[OS-Network]]
