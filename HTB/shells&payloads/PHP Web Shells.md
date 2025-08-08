---
Title: HTB\shells&payloads\PHP Web Shells
tags:
  - os/unknown- cybersec
source: HTB\shells&payloads\PHP Web Shells
---

# HTB\shells&payloads\PHP Web Shells

> Źródło: `HTB\shells&payloads\PHP Web Shells`

Hands-on With a PHP-Based Web Shell.
Since PHP processes code & commands on the server-side, we can use pre-written payloads to gain a shell through the browser or initiate a reverse shell session with our attack box.
In this case, we will take advantage of the vulnerability in rConfig 3.9.6 to manually upload a PHP web shell and interact with the underlying Linux host.
In addition to all the functionality mentioned earlier, rConfig allows admins to add network devices and categorize them by vendor.
Go ahead and log in to rConfig with the default credentials (admin:admin), then navigate to Devices > Vendors and click Add Vendor.

We will be using WhiteWinterWolf's PHP Web Shell (https://github.com/WhiteWinterWolf/wwwolf-php-webshell).
We can download this or copy and paste the source code into a .php file.
Keep in mind that the file type is significant, as we will soon witness.
Our goal is to upload the PHP web shell via the Vendor Logo browse button.
Attempting to do this initially will fail since rConfig is checking for the file type.
It will only allow uploading image file types (.png,.jpg,.gif, etc.). However, we can bypass this utilizing Burp Suite.

As mentioned in an earlier section, you will notice that some payloads have comments from the author that explain usage, provide kudos and links to personal blogs.
This can give us away, so it's not always best to leave the comments in place.
We will change Content-type from application/x-php to image/gif.
This will essentially "trick" the server and allow us to upload the .php file, bypassing the file type restriction.
Once we do this, we can select Forward twice, and the file will be submitted. We can turn the Burp interceptor off now and go back to the browser to see the results.

We can now attempt to use our web shell. Using the browser, navigate to this directory on the rConfig server:

/images/vendor/connect.php

Considerations when dealing with web shells

When utilizing web shells, consider the below potential issues that may arise during your penetration testing process:

1.Web applications sometimes automatically delete files after a pre-defined period
2.Limited interactivity with the operating system in terms of navigating the file system, downloading and uploading files,
chaining commands together may not work (ex. whoami && hostname), slowing progress,
especially when performing enumeration -Potential instability through a non-interactive web shell
3.Greater chance of leaving behind proof that we were successful in our attack

Depending on the engagement type (i.e., a black box evasive assessment), we may need to attempt to go undetected and cover our tracks.
We are often helping our clients test their capabilities to detect a live threat, so we should emulate as much as possible the methods a malicious attacker may attempt,
including attempting to operate stealthily. This will help our client and save us in the long run from having files discovered after an engagement period is over.
In most cases, when attempting to gain a shell session with a target, it would be wise to establish a reverse shell and then delete the executed payload.
Also, we must document every method we attempt, what worked & what did not work, and even the names of the payloads & files we tried to use.
We could include a sha1sum or MD5 hash of the file name, upload locations in our reports as proof, and provide attribution.

**Powiązane:** [[Graph-Home]] [[OS-Unknown]]
