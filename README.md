# Penetration Test Report: itskillscentre.com.ng

**Date:** September 16, 2025
**Author:** [Your Name/GitHub Handle]
**Type:** External Black-Box Assessment & OSINT
**Tags:** `#PenetrationTesting` `#Cloudflare` `#OSINT` `#Reconnaissance` `#WebSecurity`

## Executive Summary

A comprehensive external assessment was conducted against the target `itskillscentre.com.ng`. The primary objectives were to discover the origin server IP address hidden behind Cloudflare and perform an initial application vulnerability assessment.

The origin IP **(`192.185.93.75`)** was successfully identified through advanced DNS reconnaissance. The application demonstrates strong security hygiene, utilizing a modern Laravel framework, updated libraries, and robust default configurations that thwarted common automated attacks. No critical vulnerabilities were identified in the publicly accessible attack surface.

This phase of testing is complete. Further assessment is required to evaluate authenticated functionalities and the origin server directly.

---

## Table of Contents
1.  [Objectives](#objectives)
2.  [Methodology & Findings](#methodology--findings)
3.  [Conclusion](#conclusion)
4.  [Recommendations & Next Steps](#recommendations--next-steps)
5.  [Tools & Commands Used](#tools--commands-used)

## Objectives

-   [x] **Bypass Cloudflare:** Identify the origin IP address of the web server.
-   [x] **Reconnaissance:** Enumerate subdomains and analyze associated technologies.
-   [x] **Initial Assessment:** Test for low-hanging vulnerabilities and misconfigurations on the public-facing application.
-   [ ] **Authenticated Testing:** Assess user and administrative functionalities. *(Blocked Pending Credentials)*
-   [ ] **Origin Testing:** Conduct a full assessment of the origin server. *(Blocked Pending Authorization)*

## Methodology & Findings

### 1. Origin IP Discovery

The target's DNS records provided the critical lead. The SPF record contained a third-party service inclusion.

```bash
# Querying the SPF record
dig TXT itskillscentre.com.ng
# "v=spf1 a mx include:websitewelcome.com ~all"

# Investigating the included domain
dig A websitewelcome.com
# websitewelcome.com.	14400	IN	A	192.185.93.75

# Verifying the reverse lookup
dig -x 192.185.93.75
# 75.93.185.192.in-addr.arpa. 14400 IN	PTR	websitewelcome.com.
```

**Verification:** The origin server was confirmed by manipulating HTTP Host headers, which resulted in a `302 Redirect` instead of a generic hosting page, proving it recognized the domain.

```bash
# Confirmation via Host Header
curl -I -H "Host: itskillscentre.com.ng" http://192.185.93.75
# HTTP/1.1 302 Found
# Location: /404.html
```

**Finding:** The origin server is **192.185.93.75** (websitewelcome.com).

### 2. Subdomain Enumeration

Manual enumeration of common subdomains was performed. All resolvable subdomains were proxied through Cloudflare.

| Subdomain | Status | IP Address | Note |
| :--- | :--- | :--- | :--- |
| dev | NXDOMAIN | - | - |
| staging | NXDOMAIN | - | - |
| **mail** | **LIVE** | 104.18.19.166 | Cloudflare |
| **cpanel** | **LIVE** | 104.18.18.166 | Cloudflare |
| ftp | NXDOMAIN | - | - |

### 3. Application Vulnerability Assessment

-   **WAF Bypass:** Attempts (path obfuscation, verb tampering, header spoofing) were unsuccessful. Cloudflare's rules are effective.
-   **Login Page:** Located at `/user-login`. Default credential attacks are mitigated by CSRF tokens (`419 Page Expired` error).
-   **Technology Stack:**
    -   **Framework:** Laravel (Identified via CSRF tokens and error messages)
    -   **Frontend:** jQuery 3.5.1, Bootstrap 5.0.2 (No known critical CVEs)
-   **Sensitive Files:** Checks for `.env`, `robots.txt`, `backup.zip`, `.git/`, and common admin paths (`/wp-admin/`, `/phpmyadmin`) returned `404` or `302` responses.
-   **OSINT:** No exposed credentials, API keys, or sensitive data found in public repositories or breach databases.

## Conclusion

The target application is well-hardened against external, unauthenticated attacks. The successful identification of the origin IP was achieved through meticulous OSINT and DNS analysis, not through a misconfiguration. The application's use of modern frameworks, security-focused defaults, and a properly configured WAF presents a robust attack surface.

**The assessment is currently paused.** The highest-risk areas—aut# itskillscentre-security-assessment
A comprehensive penetration test report and reconnaissance findings for itskillscentre.com.ng
henticated functionality and the origin server itself—remain untested due to the target's secure configuration and the scope of this initial phase.

## Recommendations & Next Steps

1.  **Client Authorization:** Obtain explicit permission to test the origin server (`192.185.93.75`) directly.
2.  **Provision of Credentials:** Acquire at least two sets of test credentials (user and admin) to assess authenticated workflows, access controls, and business logic flaws.
3.  **Comprehensive Testing:** With the above provided, proceed with:
    -   Manual authenticated penetration testing.
    -   Directory and file bruteforcing against the origin.
    -   Port scanning of the origin server (subject to authorization limits).

## Tools & Commands Used

-   **DNS Recon:** `dig`
-   **HTTP Analysis:** `curl`
-   **Certificate Lookup:** `crt.sh`
-   **OSINT:** Public GitHub search, HaveIBeenPwned, Wayback Machine

---
*This report documents a legal and authorized security assessment for educational and defensive purposes.*
