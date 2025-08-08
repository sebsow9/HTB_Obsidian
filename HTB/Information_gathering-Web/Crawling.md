---
Title: HTB\Information_gathering-Web\Crawling
tags:
  - os/unknown- cybersec
source: HTB\Information_gathering-Web\Crawling
---

# HTB\Information_gathering-Web\Crawling

> Źródło: `HTB\Information_gathering-Web\Crawling`

How crawlers work:

The basic operation of a web crawler is straightforward yet powerful. It starts with a seed URL, which is the initial web page to crawl.
The crawler fetches this page, parses its content, and extracts all its links. It then adds these links to a queue and crawls them, repeating the process iteratively.
Depending on its scope and configuration, the crawler can explore an entire website or even a vast portion of the web.

Extracting valuable information:

Links (Internal and External): These are the fundamental building blocks of the web, connecting pages within a website (internal links) and to other websites (external links). Crawlers meticulously collect these links, allowing you to map out a website's structure, discover hidden pages, and identify relationships with external resources.
Comments: Comments sections on blogs, forums, or other interactive pages can be a goldmine of information. Users often inadvertently reveal sensitive details, internal processes, or hints of vulnerabilities in their comments.
Metadata: Metadata refers to data about data. In the context of web pages, it includes information like page titles, descriptions, keywords, author names, and dates. This metadata can provide valuable context about a page's content, purpose, and relevance to your reconnaissance goals.
Sensitive Files: Web crawlers can be configured to actively search for sensitive files that might be inadvertently exposed on a website. This includes backup files (e.g., .bak, .old), configuration files (e.g., web.config, settings.php), log files (e.g., error_log, access_log), and other files containing passwords, API keys, or other confidential information. Carefully examining the extracted files, especially backup and configuration files, can reveal a trove of sensitive information, such as database credentials, encryption keys, or even source code snippets.

robots.txt
its a simple text file that informs bot web crawlers, which sites they can and cannot visit.

some directives in robots.txt structure:
Disallow -	Specifies paths or patterns that the bot should not crawl.	Disallow: /admin/ (disallow access to the admin directory)
Allow- 	Explicitly permits the bot to crawl specific paths or patterns, even if they fall under a broader Disallow rule.	Allow: /public/ (allow access to the public directory)
Crawl-delay -	Sets a delay (in seconds) between successive requests from the bot to avoid overloading the server.	Crawl-delay: 10 (10-second delay between requests)
Sitemap -	Provides the URL to an XML sitemap for more efficient crawling.	Sitemap: https://www.example.com/sitemap.xml

robots.txt in web recon

- Uncovering Hidden Directories: Disallowed paths in robots.txt often point to directories or files the website owner intentionally wants to keep out of reach from search engine crawlers. These hidden areas might house sensitive information, backup files, administrative panels, or other resources that could interest an attacker.
- Mapping Website Structure: By analyzing the allowed and disallowed paths, security professionals can create a rudimentary map of the website's structure. This can reveal sections that are not linked from the main navigation, potentially leading to undiscovered pages or functionalities.
- Detecting Crawler Traps: Some websites intentionally include "honeypot" directories in robots.txt to lure malicious bots. Identifying such traps can provide insights into the target's security awareness and defensive measures.

Well knows Urls
The .well-known standard, defined in RFC 8615, serves as a standardized directory within a website's root domain.
This designated location, typically accessible via the /.well-known/ path on a web server, centralizes a website's critical metadata, including configuration files and information related to its services, protocols, and security mechanisms.

Notable exampels of well known URLS
security.txt -	Contains contact information for security researchers to report vulnerabilities.	- Permanent -	RFC 9116
/.well-known/change-password -	Provides a standard URL for directing users to a password change page. -	Provisional -	https://w3c.github.io/webappsec-change-password-url/#the-change-password-well-known-uri
openid-configuration -	Defines configuration details for OpenID Connect, an identity layer on top of the OAuth 2.0 protocol. -	Permanent -	http://openid.net/specs/openid-connect-discovery-1_0.html
assetlinks.json -	Used for verifying ownership of digital assets (e.g., apps) associated with a domain. -	Permanent -	https://github.com/google/digitalassetlinks/blob/master/well-known/specification.md
mta-sts.txt -	Specifies the policy for SMTP MTA Strict Transport Security (MTA-STS) to enhance email security. - Permanent -	RFC 8461

web recon and .well-known
The openid-configuration URI is part of the OpenID Connect Discovery protocol, an identity layer built on top of the OAuth 2.0 protocol.
When a client application wants to use OpenID Connect for authentication, it can retrieve the OpenID Connect Provider's configuration by accessing the https://example.com/.well-known/openid-configuration endpoint.
This endpoint returns a JSON document containing metadata about the provider's endpoints, supported authentication methods, token issuance, and more.

The information obtained from the openid-configuration endpoint provides multiple exploration opportunities:

Endpoint Discovery:
Authorization Endpoint: Identifying the URL for user authorization requests.
Token Endpoint: Finding the URL where tokens are issued.
Userinfo Endpoint: Locating the endpoint that provides user information.
JWKS URI: The jwks_uri reveals the JSON Web Key Set (JWKS), detailing the cryptographic keys used by the server.
Supported Scopes and Response Types: Understanding which scopes and response types are supported helps in mapping out the functionality and limitations of the OpenID Connect implementation.
Algorithm Details: Information about supported signing algorithms can be crucial for understanding the security measures in place.

Popular web crawlers:
1. Burp Suite Spider: Burp Suite, a widely used web application testing platform, includes a powerful active crawler called Spider. Spider excels at mapping out web applications, identifying hidden content, and uncovering potential vulnerabilities.
2. OWASP ZAP (Zed Attack Proxy): ZAP is a free, open-source web application security scanner. It can be used in automated and manual modes and includes a spider component to crawl web applications and identify potential vulnerabilities.
3. Scrapy (Python Framework): Scrapy is a versatile and scalable Python framework for building custom web crawlers. It provides rich features for extracting structured data from websites, handling complex crawling scenarios, and automating data processing. Its flexibility makes it ideal for tailored reconnaissance tasks.
4. Apache Nutch (Scalable Crawler): Nutch is a highly extensible and scalable open-source web crawler written in Java. It's designed to handle massive crawls across the entire web or focus on specific domains. While it requires more technical expertise to set up and configure, its power and flexibility make it a valuable asset for large-scale reconnaissance projects.

Scrapy
installation:
```bash
pip3 install scrapy
```

ReconSpider
installation:
```bash
wget -O ReconSpider.zip https://academy.hackthebox.com/storage/modules/144/ReconSpider.v1.2.zip
```

unzip ReconSpider.zip
running:
```bash
python3 ReconSpider.py http://inlanefreight.com
```

Keys from extracted json after web crawling with reconspider
emails -	Lists email addresses found on the domain.
links -	Lists URLs of links found within the domain.
external_files -	Lists URLs of external files such as PDFs.
js_files -	Lists URLs of JavaScript files used by the website.
form_fields -	Lists form fields found on the domain (empty in this example).
images -	Lists URLs of images found on the domain.
videos -	Lists URLs of videos found on the domain (empty in this example).
audio -	Lists URLs of audio files found on the domain (empty in this example).
comments -	Lists HTML comments found in the source code.

**Powiązane:** [[Graph-Home]] [[OS-Unknown]]
