Ninja Hacker Academy – Enterprise Breach Simulation
Overview
This repository describes a realistic red-team style lab simulating a mid-size company operating two Active Directory forests:

academy.ninja.lan – internal training and lab forest for students and junior staff.

ninja.hack – corporate forest for executives, finance, and core IT operations.

You start with a Kali attack box on the internal network, no domain credentials, and network access into the Academy segment. Your long-term objective is to obtain Domain Admin in both forests by exploiting exposed services, weak configurations, and trust relationships.​

Lab Topology
Network
Address space: 10.2.10.0/23

Core Hosts
10.2.10.21 – win11-staff.academy.ninja.lan

Windows 11 workstation used by an Academy staff member.

Likely has cached credentials, mapped shares, and access to internal applications.​

10.2.10.30 – dc01.ninja.hack

Domain Controller for ninja.hack (corporate forest root).

Hosts AD DS, Kerberos, LDAP, Global Catalog, WinRM, RDP.​

10.2.10.31 – dc02.academy.ninja.lan

Domain Controller for academy.ninja.lan (training forest root).

Supports students and lab users via AD.​

10.2.10.32 – web.academy.ninja.lan

IIS web server hosting the Ninja Hacker Academy portal.

Primary external/trainee-facing entry point into the environment.​

10.2.10.33 – sql.academy.ninja.lan

Microsoft SQL Server 2019 instance.

Stores training data, application state, and reporting databases.​

10.2.10.34 – share.academy.ninja.lan

File / application server.

Holds course material, staff documents, and internal tools.​

All systems are fully patched with Microsoft Defender enabled and reasonable baseline security settings, making the environment resemble a moderately hardened enterprise rather than a deliberately broken CTF.​

Attacker Starting Position
You have:

A Kali VM reachable inside 10.2.10.0/23.

No valid Active Directory credentials.

No prior knowledge beyond the fact that this is a training and corporate environment.

Initial tasks:

Identify all Windows assets and key services in the subnet.

Map those assets to domains, FQDNs, and business roles.

Configure local DNS and Kerberos to operate across both forests.​

High-Level Objectives
Reconnaissance

Enumerate hosts and open ports across 10.2.10.0/23.

Identify domain controllers, web servers, SQL servers, file servers, and workstations.

Build a network map that connects technical assets to business functions.​

Domain & Role Mapping

Determine which hosts belong to academy.ninja.lan vs ninja.hack.

Identify trust relationships and potential cross-forest access paths.

Classify each host as infrastructure, application, data, or workstation.​

Name Resolution & Kerberos Configuration

Configure /etc/hosts on Kali with the lab FQDNs and IPs.

Set up dnsmasq as a local DNS forwarder.

Configure /etc/krb5.conf to talk to both dc02.academy.ninja.lan and dc01.ninja.hack as KDCs.​

Entry-Point Analysis

Identify the primary entry point (the Academy web portal) and secondary targets (e.g., the Windows 11 workstation) that are realistic for credential theft and lateral movement.​

Privilege Escalation & Lateral Movement (future extensions)

Exploit weaknesses in the Academy web application.

Abuse SQL Server misconfigurations to gain OS-level footholds.

Move laterally into domain controllers and across forests to escalate to Domain Admin.

Suggested Exercise Flow
The scenario is broken into levels that can be implemented in a cyber range platform or followed manually.

1. Executive Briefing
Read the scenario description.

Understand the dual-forest design and business roles of each domain.

Identify the main categories of flags (data exposure, credential compromise, full domain takeover).

2. Network Recon – Hosts and Services
Run a full TCP scan on 10.2.10.0/23 using a two-stage Nmap approach (port sweep → service/version scan).

Identify at least six Windows assets, including the Windows 11 workstation and all servers.​

Example flag:
NHA{RECON_6_WINDOWS_ASSETS_10_2_10}

3. Domain and Role Mapping
Use Kerberos/LDAP ports and banners to identify domain controllers for academy.ninja.lan and ninja.hack.

Associate each IP with its FQDN and function (web, SQL, file, workstation).​

Example flag:
NHA{NINJA_HACK_AND_ACADEMY_MAPPED}

4. Configure FQDN and Kerberos
Add /etc/hosts entries for all six Windows hosts.

Enable dnsmasq and verify name resolution with host.

Configure Kerberos realms for both forests and validate basic ticket operations.​

Example flag:
NHA{FQDN_DNSMASQ_KERBEROS_READY}

5. Entry Points and High-Value Targets
Analyze which hosts accept untrusted input (e.g., HTTP on web.academy.ninja.lan).

Identify the Windows 11 workstation as a high-value secondary target (user activity, cached creds, mapped shares).​

Example flag:
NHA{PRIMARY_ENTRY_WEB_SECONDARY_WIN11}
