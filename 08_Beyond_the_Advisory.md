> **TLP:WHITE | For Public Distribution**

---

# Threat Intelligence Brief

## Beyond the Advisory: Jinan USR IOT Technology's Systemic Exposure Across Critical Infrastructure

*A Pattern of Insecure Design, Vendor Non-Cooperation, and Compound Chinese Hardware Risk in Global Telecom Networks*

| Date | Author | Confidence |
|------|--------|------------|
| May 29, 2026 | Daniel Struble \| Struble Threat Intelligence \| [github.com/dnlstruble-png/Bearings-to-Bits](https://github.com/dnlstruble-png/Bearings-to-Bits) | Moderate-High |

---

## 1. Executive Summary

A May 2026 CISA advisory disclosing hardcoded credentials in the Jinan USR IOT Technology (PUSR) USR-W610 serial converter served as the entry point for a broader investigation into this Chinese manufacturer's global deployment footprint. No internet-exposed W610 devices were found, but what emerged from the broader search is a threat picture significantly more concerning than the original advisory suggested.

Open-source research using Shodan identified 1,570 internet-facing devices matching the USR product line fingerprint across 38 countries. The majority of indexed devices appear on networks registered to telecom providers and related infrastructure organizations. Multiple product lines carry critical-severity vulnerabilities with hardcoded root credentials dating to 2022. The manufacturer has refused to coordinate with CISA. No patches were identified for the vulnerabilities described in this brief at the time of publication. On at least one US carrier network device, a co-located Hikvision surveillance interface was discovered. Hikvision is a Chinese state-linked manufacturer independently banned from US federal networks. The vulnerabilities in these devices appear less like oversight and more like persistent design decisions. They function as features, not bugs.

---

## 2. Background

### 2.1 Initiating Advisory

On May 28, 2026, CISA published advisory ICSA-26-148-02 disclosing CVE-2026-7786, a critical vulnerability in the Jinan USR IOT Technology USR-W610 RS232/485 to Wi-Fi/Ethernet Converter. The advisory assigned a CVSS score of 9.8 and described plaintext administrative credentials embedded directly in the device firmware. Notably, Jinan USR IOT Technology did not respond to CISA's coordination attempts prior to publication.

An attempt to locate internet-exposed USR-W610 devices via Shodan returned no results, suggesting the W610 is deployed primarily in internal network segments rather than directly internet-facing, consistent with its function as a facility-level serial bridge. However, expanding the search to the broader Jinan USR product line revealed a significantly more concerning picture. The W610 advisory served as the entry point. What follows is what the broader investigation uncovered.

### 2.2 Manufacturer Profile

Jinan USR IOT Technology Limited, marketed as PUSR, is a Chinese manufacturer of industrial IoT connectivity hardware headquartered in Jinan, Shandong, China. Their product line spans serial-to-ethernet converters, cellular gateways, and industrial routers. WHOIS records indicate the company's website is registered through Alibaba Cloud Computing, a common hosting choice among Chinese businesses. No publicly accessible security disclosure program or vulnerability reporting mechanism was identified on the manufacturer's website. The manufacturer's knowledge base is login-gated and presented exclusively in Mandarin Chinese, creating a practical barrier to security coordination for non-Chinese-speaking operators and researchers.

The company has demonstrated a documented pattern of non-engagement with security researchers and Western cybersecurity authorities spanning multiple years and multiple CVEs. In April 2022, the researcher who discovered CVE-2022-29730 contacted PUSR and received no response within five days, resulting in immediate public disclosure. For CVE-2024-42682, Tanto Security first contacted PUSR in July 2024. PUSR responded once, acknowledged the undocumented uid=0 account existed, described it as being for development purposes, stated they do not provide the password to customers, and asked that the findings not be published. PUSR then ceased all communication. Follow-up messages sent in August 2024, March 2025, May 2025, and June 2025 received no response. The research was published in April 2026 in accordance with Tanto Security's responsible disclosure policy. For CVE-2026-7786, PUSR again declined to coordinate with CISA prior to public disclosure.

---

## 3. Vulnerability Summary

The initiating CVE affects the USR-W610. Expanding the investigation to the broader USR product line revealed two additional critical CVEs affecting the USR-G806 series, the devices that dominate the internet-facing exposure profile:

| CVE ID | CVSS | Description | Device |
|--------|------|-------------|--------|
| CVE-2026-7786 | 9.8 Critical | Hardcoded admin credentials embedded in firmware image | USR-W610 |
| CVE-2022-29730 | 9.8 Critical | Hidden hardcoded root credentials (usr/www.usr.cn) in Linux firmware | USR-G806 series |
| CVE-2024-42682 | Not assigned | Undocumented uid=0 account enabling remote SSH root access; password recoverable via firmware reverse engineering | USR-G806AU |

CVE-2026-7786 is included for context as the initiating advisory. The G806 series CVEs represent the primary findings of this investigation, as these are the devices with confirmed internet-facing exposure at scale. Whether the W610 shares the same firmware credential architecture as the G806 series is unconfirmed and should not be assumed without independent verification.

For CVE-2022-29730, the hardcoded root credential is publicly documented: username usr, password www.usr.cn. This grants full root shell access and cannot be changed through normal user configuration. Public exploit code has been available since April 2022.

For CVE-2024-42682, Tanto Security recovered the usr account password through reverse engineering of the usr_root binary, which stores the password in an encoded form. PUSR acknowledged the account in correspondence with Tanto Security, describing it as existing for development purposes. The password has not been publicly disclosed by Tanto Security, but they confirmed it allows remote SSH access as uid=0. PUSR did not dispute the findings.

---

## 4. Findings

### 4.1 Global Exposure: USR-G806 Series

On May 29th, 2026, Shodan facet analysis using the query `http.title:"USR" port:80` returned 1,570 internet-facing devices across 38 countries. Australia accounts for 953 devices, representing approximately 61% of the total indexed footprint. China itself accounts for only 20 devices, a relatively small number compared to Western deployments and an anomaly worth noting given the device's country of manufacture. The complete geographic distribution is presented below.

| Country | Devices (`http.title:"USR" port:80`) |
|---------|--------------------------------------|
| Australia | 953 |
| Italy | 147 |
| Hong Kong | 102 |
| Nicaragua | 70 |
| New Zealand | 61 |
| United States | 37 |
| Spain | 34 |
| China | 20 |
| Israel | 15 |
| Norway | 14 |
| Thailand | 11 |
| Qatar | 10 |
| South Africa | 10 |
| Argentina | 8 |
| Brazil | 8 |
| Malaysia | 8 |
| Poland | 8 |
| Hungary | 7 |
| Japan | 6 |
| North Macedonia | 6 |
| Taiwan | 6 |
| Singapore | 4 |
| Turkey | 4 |
| Canada | 2 |
| Czech Republic | 2 |
| Germany | 2 |
| France | 2 |
| United Kingdom | 2 |
| Moldova | 2 |
| Bulgaria | 1 |
| Estonia | 1 |
| Finland | 1 |
| India | 1 |
| Kuwait | 1 |
| Lithuania | 1 |
| Mexico | 1 |
| Slovenia | 1 |
| Tanzania | 1 |

The United States accounts for 37 devices across 17 cities including Philadelphia, Chicago, Ashburn, Salt Lake City, Simi Valley, and others. Cursory review of individual results indicated presence on networks registered to major carriers and infrastructure providers. A systematic review of all 37 US results was not conducted as part of this research. The question of whether the US deployment pattern represents a purchasing anomaly or deliberate placement remains open. Organizational attribution for the US carrier deployments described in Sections 4.2 and 4.3 was confirmed via ARIN WHOIS registration data. Specific organizational names have been withheld from public disclosure.

### 4.2 Major US Carrier Network: Chicago Metropolitan Region

> **KEY FINDING: Chicago Carrier Device Profile**
>
> Five USR-G806 devices were identified on a major US carrier's infrastructure in the Chicago metropolitan area, all presenting an identical service profile:
>
> - **Port 23 (TCP):** BusyBox telnetd, cleartext authentication, default port, banner: `USR-G806 login:`
> - **Port 80 (TCP):** Web management interface, subject to CVE-2022-29730 hardcoded credentials
> - **Port 2222 (TCP):** Dropbear SSH on non-standard port, obscured from automated scanning
>
> The combination of cleartext telnet on the default port with publicly documented root credentials means these devices present an open root shell to any actor who connects. The Shodan crawl timestamp on at least one Chicago device was May 29, 2026, confirming active status.

The service profile presents a deliberate contradiction: SSH is placed on a non-standard port, a practice typically associated with security awareness, while cleartext telnet is left wide open on its default port. This asymmetry suggests either profound configuration inconsistency or an intentional design that preserves access while creating an appearance of security consideration.

### 4.3 US Carrier Network: Philadelphia Region

A second cluster of USR-G806s devices was identified on legacy carrier infrastructure in the Philadelphia region. The USR-G806s variant adds a serial port to the standard G806 form factor. Notable differences in the Philadelphia configuration include:

- BusyBox telnetd placed on non-standard port 2233 rather than the default port 23
- Additional web services on ports 8586, 8587, and 8588
- Ports 8586 and 8587 serve identical content (confirmed via matching ETag values), suggesting redundant service configuration
- Port 8588 serves distinct content with more modern security headers, suggesting a separate application stack

The Philadelphia and Chicago clusters share a nearly identical service profile despite being geographically separated. This consistency is analytically notable given that both deployments fall under the same corporate umbrella through a series of acquisitions. The similar configurations across inherited legacy infrastructure suggest these devices may represent a systemic deployment pattern rather than isolated incidents.

### 4.4 Compound Finding: Hikvision on Legacy US Carrier Infrastructure

> **KEY FINDING: Hikvision Interface on Legacy US Carrier Philadelphia Device**
>
> The external interface on port 8586 of a USR-G806s device on legacy US carrier infrastructure in the Philadelphia region resolves to a Hikvision login page.
>
> Hikvision (Hangzhou Hikvision Digital Technology Co., Ltd.) is the world's largest manufacturer of video surveillance equipment and is partly owned by the Chinese state. Hikvision hardware has been:
>
> - Banned from US federal government procurement under the 2018 National Defense Authorization Act (NDAA)
> - Designated a national security threat by the FCC
> - Associated with documented backdoors and covert data collection capabilities
>
> The presence of a Hikvision interface accessible through a Jinan USR cellular gateway on legacy US carrier infrastructure represents a compound risk: two Chinese manufacturers, both with national security designations, operating in combination on inherited US telecommunications infrastructure. No patches were identified for the relevant vulnerabilities in either device class at the time of publication. Neither vendor has demonstrated engagement with Western security researchers or authorities.

---

## 5. Analysis

### 5.1 Vendor Pattern: Negligence or Intent?

The evidence across CVE-2026-7786, CVE-2022-29730, and CVE-2024-42682 reflects a consistent pattern rather than isolated security failures. Hardcoded credentials that cannot be changed by end users, across multiple product lines, over a span of years, with public exploit code available since 2022 and no vendor response. This is not the profile of a company unaware of its security posture. It is the profile of a company that has chosen it.

CISA's standard remediation playbook assumes a vendor operating in good faith. When the vendor is unresponsive and the credential exposure may be intentional, the playbook fails completely. CISA's published recommendation that operators contact the vendor to keep devices updated is functionally useless advice in this context. No patches were identified for any of the vulnerabilities described in this brief at the time of publication. The architectural nature of the hardcoded credential vulnerabilities, specifically that they are embedded in firmware and not configurable by end users, suggests remediation through traditional patching may not be feasible. Organizations should not assume patches will be forthcoming and should treat affected devices as persistently compromised pending replacement.

The vulnerabilities in these devices seem to function as features rather than bugs. The distinction matters because it changes the threat model entirely: this is not a remediation problem waiting for a patch cycle. It is a persistent access problem requiring hardware replacement.

### 5.2 Passive Collection at Scale

The primary threat posed by internet-facing USR devices with hardcoded credentials is passive collection. A cellular gateway with root-level access sits at the intersection of network traffic flows. An actor with those credentials, which are publicly documented and unchangeable, can observe authentication exchanges, configuration data, and depending on deployment context, subscriber traffic.

The geographic concentration in Australia and Italy warrants specific attention. Australia is a Five Eyes partner with significant intelligence value. Italy is a NATO member with known Chinese intelligence targeting history. The concentration of 938 devices on a single Australian national carrier is not consistent with ordinary commercial deployment patterns.

Among the US results reviewed, devices appeared on networks registered to major carriers and infrastructure providers in multiple cities. A single device placed deliberately inside critical infrastructure is harder to detect, harder to attribute, and potentially higher value than a large volume deployment. Whether the US distribution reflects deliberate placement or ordinary procurement decisions could not be determined from the data reviewed.

### 5.3 The Compound Risk of Co-Located Chinese Hardware

The Hikvision finding on legacy US carrier infrastructure in the Philadelphia region elevates the threat picture from a single-vendor vulnerability concern to a compound infrastructure risk. Both Jinan USR IOT Technology and Hikvision share common characteristics: Chinese manufacture, partial or full state connection, documented security vulnerabilities, refusal to engage with Western security authorities, and no patches identified for known critical flaws at time of publication.

Their co-location on the same network device, one providing cellular backhaul and one providing surveillance capability, is not necessarily coordinated. But it represents the practical outcome of procurement decisions that have introduced multiple layers of opaque Chinese hardware into sensitive US telecommunications infrastructure.

---

## 6. Recommendations

### For Network Operators and Security Teams

- Audit your environment immediately for any Jinan USR IOT Technology devices across all product lines, not just the USR-W610 named in CVE-2026-7786
- Treat any existing USR-G806 series deployment as potentially compromised; the hardcoded credentials are public and the devices cannot be patched
- Network segmentation is the minimum mitigation: USR devices must not be internet-facing under any circumstances
- Any USR device with an open telnet service on port 23 should be considered an active threat and isolated immediately pending replacement
- Audit co-located hardware on any network segment hosting USR devices; the Hikvision finding suggests these deployments may not be isolated
- Replace affected devices with hardware from vendors that maintain active security disclosure programs and respond to researcher coordination

### For Detection and SOC Teams

- Flag any internal devices presenting Dropbear SSH banners on port 2222 combined with BusyBox telnetd on any port
- Monitor for authentication attempts using the credential pair usr / www.usr.cn against any network device
- Any Hikvision interface accessible through a cellular gateway should be treated as a priority investigation item

---

## 7. References

1. Zeroscience Advisory ZSL-2022-5705, CVE-2022-29730 Primary Disclosure: https://zeroscience.mk/en/vulnerabilities/ZSL-2022-5705.php
2. CISA Advisory ICSA-26-148-02: https://www.cisa.gov/news-events/ics-advisories/icsa-26-148-02
3. NVD, CVE-2022-29730: https://nvd.nist.gov/vuln/detail/CVE-2022-29730
4. Tanto Security, Route to Root in a 4G Industrial Router: https://tantosec.com/blog/2026/04/route-to-root-in-4g-industrial-router/
5. Shodan Facet Analysis, Queries: `http.title:"USR" port:80` | `"Jinan USR"`
6. MIT Technology Review, Hikvision and Chinese Surveillance Technology: https://www.technologyreview.com/2022/06/22/1054586/hikvision-worlds-biggest-surveillance-company/

---

## Disclaimer

This brief represents independent open-source research using publicly available tools and data. No systems were accessed, tested, or exploited in the course of this research. All Shodan data reflects publicly indexed information. This brief is intended for defensive security purposes and general security community awareness. The author has no affiliation with any vendor or government agency mentioned herein.

---

> **TLP:WHITE | For Public Distribution**
