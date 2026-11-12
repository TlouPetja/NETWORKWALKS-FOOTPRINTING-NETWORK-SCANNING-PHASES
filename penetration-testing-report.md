# PENETRATION TESTING REPORT

## Footprinting, Reconnaissance & Network Scanning

**Assessment type:** Educational cybersecurity practical  
**Target domain:** `networkwalks.com`  
**Local network scope:** `192.168.1.0/24`  
**Platform:** Kali Linux  
**Evidence:** Screenshots captured during the practical exercise

---

## 1. Liability Disclaimer

This report was prepared for authorized educational and cybersecurity learning
purposes. The activities documented here should only be performed against
systems, applications and networks for which appropriate permission has been
obtained.

The observations in this report are based on the evidence captured during the
practical exercise. The presence of an open port, technology, HTTP header,
DNS record or WAF does not by itself prove that a vulnerability is exploitable.
Further validation would require explicit authorization and an appropriate
testing scope.

---

## 2. Introduction

The purpose of this practical was to perform initial penetration-testing
reconnaissance and network-scanning activities.

The reconnaissance phase focused on collecting publicly observable information
about `networkwalks.com`, including domain registration information, DNS
resolution, web technologies, HTTP response information, DNS records and web
application firewall detection.

A separate network-scanning exercise was performed against the local
`192.168.1.0/24` network using Zenmap/Nmap. The scan was used to identify
active hosts, exposed TCP services and the basic topology of the discovered
hosts.

The results provide an initial view of the attack surface and can be used to
identify areas that require further authorized security assessment.

---

## 3. Tools Used

| Tool | Purpose |
|---|---|
| WHOIS | Domain registration and registrar information |
| nslookup | DNS name-to-IP resolution |
| WhatWeb | Web technology and application fingerprinting |
| curl | HTTP response and security-header inspection |
| wafw00f | Web Application Firewall detection |
| DNSRecon | DNS record enumeration |
| Zenmap / Nmap | Network host and service discovery |

---

# 4. Activities Performed

## 4.1 Footprinting & Reconnaissance

### 4.1.1 WHOIS

WHOIS was used to obtain registration information associated with
`networkwalks.com`.

The captured result identifies:

- Domain: `NETWORKWALK.COM`
- Registrar: GoDaddy.com, LLC
- Registrar WHOIS server: `whois.godaddy.com`
- Name servers shown in the result:
  - `NS35.DOMAINCONTROL.COM`
  - `NS36.DOMAINCONTROL.COM`
- The output also displayed domain status information and registration dates.

### Observation

WHOIS information can assist an assessor in understanding the administrative
and infrastructure background of a domain. Public registration information
may also help correlate a domain with its registrar and DNS infrastructure.

**Security significance:** Informational / reconnaissance.

![WHOIS evidence](../screenshots/05-whois.png)

---

### 4.1.2 nslookup

The `nslookup` command was used to resolve the target domain.

The captured result returned:

```text
Server:  192.168.1.1
Address: 192.168.1.1#53

Name:    networkwalks.com
Address: 192.232.216.135
```

### Observation

The DNS query successfully resolved `networkwalks.com` to
`192.232.216.135`.

**Security significance:** Informational. DNS resolution establishes the IP
address associated with the tested hostname but does not by itself indicate a
vulnerability.

![nslookup evidence](../screenshots/03-nslookup.png)

---

### 4.1.3 WhatWeb

WhatWeb was used to identify technologies associated with the web application.

The captured output identified technologies including:

- Apache
- Bootstrap `7.1.4`
- WordPress `7.1.4`
- jQuery `3.7.1`
- WordPress Download Manager `3.3.76`
- Google Tag Manager
- HTML5
- JSON / application-related technologies
- Open Graph Protocol

### Observation

The results provide useful technology fingerprinting information. Knowing the
web server, CMS and client-side libraries can help an authorized assessor
identify which components should be reviewed for patching, configuration and
known security issues.

**Security significance:** Informational / reconnaissance.

![WhatWeb evidence](../screenshots/04-whatweb.png)

---

### 4.1.4 curl HTTP Response Inspection

`curl` was used to inspect the HTTP response from the web server.

The captured response included:

- `HTTP/2 200`
- `server: Apache`
- `content-type: text/html; charset=UTF-8`
- WordPress-related information
- A WordPress REST API link:
  `https://networkwalks.com/wp-json/`
- Several security and browser-policy related headers, including
  `referrer-policy` and `permissions-policy`.

### Observation

The server returned a successful HTTP response and exposed several response
headers and technology indicators.

The presence of `/wp-json/` indicates that the WordPress REST API endpoint is
advertised by the application. This is not, by itself, evidence of a
vulnerability; its security depends on the endpoints and information exposed
by the application.

**Security significance:** Informational; requires further authorized
validation before any vulnerability conclusion is made.

![curl evidence](../screenshots/02-curl.png)

---

### 4.1.5 WAF Detection with wafw00f

wafw00f was used to identify whether a Web Application Firewall was present.

The captured result reported:

```text
The site https://networkwalks.com is behind ModSecurity (SpiderLabs) WAF.
Number of requests: 2
```

### Observation

The reconnaissance tool identified ModSecurity/SpiderLabs as the WAF protecting
the tested site.

A WAF can provide an additional defensive layer by inspecting and filtering
malicious web traffic.

**Security significance:** Informational / defensive control identified.

![wafw00f evidence](../screenshots/01-wafw00f.png)

---

### 4.1.6 DNSRecon

DNSRecon was used to perform DNS enumeration.

The captured output shows:

- Enumeration performed for `networkwalks.com`
- DNSSEC-related query activity
- TXT records
- DMARC-related TXT information
- An SPF-style record shown in the output
- No SRV records found

### Observation

DNS records can reveal information about email security configuration and
supporting infrastructure. The DNSRecon output provides useful reconnaissance
data for an authorized assessment.

**Security significance:** Informational / reconnaissance.

![DNSRecon evidence](../screenshots/06-dnsrecon.png)

---

# 4.2 Network Scanning with Zenmap

## 4.2.1 Network Discovery

Zenmap was used to scan the local network:

```text
192.168.1.0/24
```

The captured scan identified the following hosts:

```text
192.168.1.1
192.168.1.103
192.168.1.105
192.168.1.107
```

The topology view also visually represented these hosts in relation to the
local scanning system.

### Observation

Four hosts responded during the displayed scan. This establishes the
currently observed active devices within the tested address range.

**Security significance:** Informational. Network discovery should only be
performed on networks where the tester has authorization.

![Zenmap topology](../screenshots/07-zenmap-topology.png)

---

## 4.2.2 Open TCP Ports and Services

The Zenmap output displayed the following services.

### Host: 192.168.1.1

| Port | State | Service |
|---:|---|---|
| 22/tcp | open | ssh |
| 23/tcp | open | telnet |
| 53/tcp | open | domain |
| 80/tcp | open | http |
| 443/tcp | open | https |
| 1900/tcp | open | upnp |

### Host: 192.168.1.103

| Port | State | Service |
|---:|---|---|
| 135/tcp | open | msrpc |
| 139/tcp | open | netbios-ssn |
| 445/tcp | open | microsoft-ds |
| 5357/tcp | open | wsdapi |
| 7070/tcp | open | realserver |

### Host: 192.168.1.107

| Port | State | Service |
|---:|---|---|
| 7070/tcp | open | realserver |

### Host: 192.168.1.105

The displayed scan did not report an open TCP port for this host.

### Observation

The scan demonstrates that multiple network services are reachable on the
discovered hosts. In particular, Telnet on TCP/23 and Windows-related services
such as TCP/139 and TCP/445 warrant security review in an authorized
environment.

An open service should not automatically be classified as a vulnerability.
The actual security risk depends on authentication, configuration, patch level,
network exposure and whether the service is required.

![Zenmap scan](../screenshots/08-zenmap-scan.png)

---

## 4.2.3 Network Topology

The Zenmap topology view displayed relationships between the scanning system
and the discovered devices:

- `192.168.1.1`
- `192.168.1.103`
- `192.168.1.105`
- `192.168.1.107`

The topology provides a visual representation of the devices identified during
the scan.

### Observation

Network topology information can help an authorized security assessor
understand the size and structure of a local network and determine where
additional segmentation or monitoring may be required.

![Topology evidence](../screenshots/07-zenmap-topology.png)

---

# 5. Risk Analysis / Impact

The following assessment is based on the evidence captured during this
practical. These are areas for review rather than confirmed exploitable
vulnerabilities.

| Finding / Observation | Risk | Reason |
|---|---|---|
| Telnet exposed on `192.168.1.1:23` | Medium | Telnet is an insecure remote-access protocol and should generally be replaced with a secure alternative such as SSH where possible. |
| HTTP/HTTPS services exposed on `192.168.1.1` | Low–Medium | Web services increase the reachable attack surface and should be securely configured and maintained. |
| Windows SMB-related ports `139/445` on `192.168.1.103` | Medium | SMB exposure can increase attack surface and should be restricted to trusted networks where possible. |
| WordPress technology identified | Informational | CMS identification helps defenders identify components requiring patching and configuration review. |
| `/wp-json/` advertised | Informational | The WordPress REST API should be reviewed for unnecessary information exposure and access-control weaknesses. |
| WAF detected | Positive control | ModSecurity/SpiderLabs indicates an additional web-application defensive layer. |
| Public DNS/WHOIS information | Informational | Public infrastructure information assists reconnaissance but is normal for internet-facing domains. |

---

# 6. Recommendations

## 6.1 Telnet

If Telnet is not required, disable it. Where remote administration is needed,
use SSH or another secure, authenticated management protocol.

## 6.2 Restrict SMB Exposure

Where `139/tcp` and `445/tcp` are not required outside a trusted internal
network, restrict access using host firewalls and network segmentation.

## 6.3 Maintain Web Application Components

Keep WordPress, plugins, themes, Apache and supporting libraries updated and
remove components that are no longer required.

## 6.4 Review WordPress REST API Exposure

Review the WordPress REST API and verify that it exposes only information and
functionality that is intended to be publicly accessible. Apply appropriate
authentication and authorization controls to sensitive functionality.

## 6.5 Continue WAF Monitoring

Maintain and monitor the ModSecurity/SpiderLabs WAF configuration. WAF
protection should complement secure application development and server
hardening rather than replace them.

## 6.6 Review DNS Security

Continue reviewing SPF, DKIM and DMARC configuration for the domain and ensure
email-security records reflect the organization's intended mail infrastructure.

## 6.7 Minimize Network Exposure

Services should only be enabled when operationally necessary. Unused
services and management interfaces should be disabled or restricted to
authorized management networks.

---

# 7. Conclusion

The practical successfully demonstrated the initial stages of penetration
testing through footprinting, reconnaissance and network scanning.

The web reconnaissance activities identified the target's public DNS
resolution, registration information, web technologies, HTTP response
characteristics and the presence of a ModSecurity/SpiderLabs WAF.

The Zenmap scan identified four responding hosts on the tested
`192.168.1.0/24` network and showed several accessible TCP services. The most
notable areas requiring security review include Telnet exposure on
`192.168.1.1` and SMB-related services on `192.168.1.103`.

These observations should be treated as the starting point for an authorized
security assessment. Additional validation would be required to determine
whether any identified services or configurations contain exploitable
vulnerabilities.

---

# 8. Evidence

The following evidence was collected during the practical:

1. `01-wafw00f.png` — WAF identification
2. `02-curl.png` — HTTP response/header inspection
3. `03-nslookup.png` — DNS resolution
4. `04-whatweb.png` — Web technology fingerprinting
5. `05-whois.png` — WHOIS information
6. `06-dnsrecon.png` — DNS enumeration
7. `07-zenmap-topology.png` — Network topology
8. `08-zenmap-scan.png` — Nmap/Zenmap service scan

---

## 9. Summary

This project demonstrates practical use of reconnaissance and network-scanning
tools in a controlled educational context. The evidence has been retained in
the repository so that each major conclusion can be traced back to a captured
result.
