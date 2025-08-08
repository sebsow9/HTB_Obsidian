---
Title: HTB\notki.md
tags:
  - os/windows
  - os/linux- cybersec
source: HTB\notki.md
---

# HTB\notki.md

> Źródło: `HTB\notki.md`

## Notki z Kursu
- obtain written consent from the owner or authorized respresntative of the computer or network being tested
- conduct the testing within the scope of the consent obtained only and respect any limitations specified
- take measures to prevent causing damage to the systems or networks being tested
- Do not access, use or disclose personal data or any other information obtained during the testing without permission
- Do not intercept electronic communications without consent of one of the parties to the communication
- Do not conduct testing on systems or networks that are covered by the Health Insurannce Portability and accountability act HIPPA without proper authorization

## Pentest steps
1. Pre - Engagement is educating the client and adjusting the contract, it is usually a meeting to define things such as:
    - NDA non disclosure agreement
    - goals of the pentest
    - scope of the pentest
    - time estimation
    - Rules of Engagement

    # Further Description

    Contract - Checklist

    ☐ NDA
        Non-Disclosure Agreement (NDA) refers to a secrecy contract between the client and the contractor regarding all written or verbal information concerning an order/project. The contractor agrees to treat all confidential information brought to its attention as strictly confidential, even after the order/project is completed. Furthermore, any exceptions to confidentiality, the transferability of rights and obligations, and contractual penalties shall be stipulated in the agreement. The NDA should be signed before the kick-off meeting or at the latest during the meeting before any information is discussed in detail.
    ☐ Goals
    	Goals are milestones that must be achieved during the order/project. In this process, goal setting is started with the significant goals and continued with fine-grained and small ones.
    ☐ Scope
        The individual components to be tested are discussed and defined. These may include domains, IP ranges, individual hosts, specific accounts, security systems, etc. Our customers may expect us to find out one or the other point by ourselves. However, the legal basis for testing the individual components has the highest priority here.
    ☐ Penetration Testing Type
        When choosing the type of penetration test, we present the individual options and explain the advantages and disadvantages. Since we already know the goals and scope of our customers, we can and should also make a recommendation on what we advise and justify our recommendation accordingly. Which type is used in the end is the client's decision.
    ☐ Methodologies
        Examples: OSSTMM, OWASP, automated and manual unauthenticated analysis of the internal and external network components, vulnerability assessments of network components and web applications, vulnerability threat vectorization, verification and exploitation, and exploit development to facilitate evasion techniques.
    ☐ Penetration Testing Locations
        External: Remote (via secure VPN) and/or Internal: Internal or Remote (via secure VPN)
    ☐ Time Estimation
    	For the time estimation, we need the start and the end date for the penetration test. This gives us a precise time window to perform the test and helps us plan our procedure. It is also vital to explicitly ask how time windows the individual attacks (Exploitation / Post-Exploitation / Lateral Movement) are to be carried out. These can be carried out during or outside regular working hours. When testing outside regular working hours, the focus is more on the security solutions and systems that should withstand our attacks.
    ☐ Third Parties
        For the third parties, it must be determined via which third-party providers our customer obtains services. These can be cloud providers, ISPs, and other hosting providers. Our client must obtain written consent from these providers describing that they agree and are aware that certain parts of their service will be subject to a simulated hacking attack. It is also highly advisable to require the contractor to forward the third-party permission sent to us so that we have actual confirmation that this permission has indeed been obtained.
    ☐ Evasive Testing
    	Evasive testing is the test of evading and passing security traffic and security systems in the customer's infrastructure. We look for techniques that allow us to find out information about the internal components and attack them. It depends on whether our contractor wants us to use such techniques or not.
    ☐ Risks
        We must also inform our client about the risks involved in the tests and the possible consequences. Based on the risks and their potential severity, we can then set the limitations together and take certain precautions.
    ☐ Scope Limitations & Restrictions
    	It is also essential to determine which servers, workstations, or other network components are essential for the client's proper functioning and its customers. We will have to avoid these and must not influence them any further, as this could lead to critical technical errors that could also affect our client's customers in production.
    ☐ Information Handling
    	HIPAA, PCI, HITRUST, FISMA/NIST, etc.
    ☐ Contact Information
    	For the contact information, we need to create a list of each person's name, title, job title, e-mail address, phone number, office phone number, and an escalation priority order.
    ☐ Lines of Communication
    	It should also be documented which communication channels are used to exchange information between the customer and us. This may involve e-mail correspondence, telephone calls, or personal meetings.
    ☐ Reporting
    	Apart from the report's structure, any customer-specific requirements the report should contain are also discussed. In addition, we clarify how the reporting is to take place and whether a presentation of the results is desired.
    ☐ Payment Terms
    	Finally, prices and the terms of payment are explained.

2. Information Gathering
    describes how we obtain information about the necessary components in various ways. We search for information about the target company and the software and hardware in use to find potential security gaps that we may be able to leverage for a foothold.

3. Vulnerability Assesment.
    We amalyze the resulst of Information Gathering, looking for known vulnerabilities in the systems, applications, and various versions of each to discover possible attack vectors. Vulnerability assesment is the evaluation of potential vulnerabilities, both manually and through automated means. This is used to determine the threat level and the suscepitibility of a companys network infrastracture to cyber attacks

4. Exploitation,
    we use the results to test our attacks against the potential vectors and execute them against the target systems to gain initial access to those systems.

5. Post Exploitation
    at this stage , we already have access to the exploited machine and ensure that we still have access to it even if modifications and changes are made. We may try to escalate our priviliges, and hunt for data that the client sees as sensitive (pillaging).

6. Lateral Movement
    Describes movement within the internal network of our target company to access additional hosts at the same or a higher privilege level. It is usually an iterative process compined with post-exploitation. We may get access to a web server, we escalate the prviliges, find a password, crack it and move a database server. From here we can pillage sensitive data, or find other credentials to further access deeper into network.

7. Proof-of-Concept
    We document step-by-step the steps we took to achieve the network compromise or some level of access. Our goal is to paint a clear picture of how we were able to chain together multiple weaknesses to each our goal so they can see a clear picture of how each vulnerability fits in and help prioritize their remedation efforts. If possible we should create a python script to automate the steps to assist the client in reproducing our findings.

8. Post-Engagement
    Detailed documentation is prepared for the client to understand the severity of the vulnerabilities found. We should also clean up any traces of our actions

    # Document content
        - An attack chain (in the event of full internal compromise or external to internal access) detailing steps taken to achieve compromise
        - A strong executive summary that a non-technical audience can understand
        - Detailed findings specific to the client's environment that include a risk rating, finding impact, remediation recommendations, and high-quality external references related to the issue
        - Adequate steps to reproduce each finding so the team responsible for remediation can understand and test the issue while putting fixes in place
        - Near, medium, and long-term recommendations specific to the environment
        - Appendices which include information such as the target scope, OSINT data (if relevant to the engagement), password cracking analysis (if relevant), discovered ports/services, compromised hosts, compromised accounts, files transferred to client-owned systems, any account creation/system modifications, an Active Directory security analysis (if relevant), relevant scan data/supplementary documentation, and any other information necessary to explain a specific finding or recommendation further
## Useful links
    - https://www.stationx.net/common-ports-cheat-sheet/
    cheat sheet portow
    - Owasp top 10
    https://owasp.org/www-project-top-ten/
    - https://book.hacktricks.xyz - privilege escalation
    - https://github.com/swisskyrepo/PayloadsAllTheThings -privilege escalation
    - https://github.com/rebootuser/LinEnum - linux enumerator
    - https://github.com/rebootuser/LinEnum - windows enum
    - https://gtfobins.github.io - commands on linux to get a shell
    - https://lolbas-project.github.io/# - windows list to get a shell

## practice
- https://owasp.org/www-project-juice-shop/ owasp juice box
- https://docs.rapid7.com/metasploit/metasploitable-2-exploitability-guide/
- https://github.com/rapid7/metasploitable3
- https://github.com/digininja/DVWA
- https://portswigger.net/web-security - podatne maszyny od burp suite

## youtube channels
- https://www.youtube.com/channel/UCa6eh7gCkpPo5XXUDfygQQA
- https://www.youtube.com/channel/UCpoyhjwNIWZmsiKNKpsMAQQ
- https://www.youtube.com/channel/UCQN2DsjnYH60SFBIA6IkNwg
- https://www.youtube.com/channel/UClcE-kVhqyiHCcjYwcpfj9w

## blog
 - https://0xdf.gitlab.io/

## windows powershell/linux
- https://underthewire.tech/century
- https://overthewire.org/wargames/

## htb
- https://app.hackthebox.com/tracks/Beginner-Track - TRacks

- https://app.hackthebox.com/machines/1

- https://app.hackthebox.com/machines/51

- https://app.hackthebox.com/machines/121

- https://app.hackthebox.com/machines/108

- https://app.hackthebox.com/machines/144

- https://app.hackthebox.com/prolabs/overview/dante

**Powiązane:** [[Graph-Home]] [[OS-Windows]] [[OS-Linux]]
