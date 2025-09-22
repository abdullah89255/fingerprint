# fingerprint
## 1) WhatWeb — fast, pluginable tech + CMS detector

**What it does:** fingerprints web servers, frameworks, CMSs, plugins and more using signatures and plugins. Great for quick reconnaissance and scripted runs. ([GitHub][1])
**When to use:** first-pass automation for many targets; supports verbose plugins and custom fingerprints.
**Example:**

```bash
# simple
whatweb example.com

# verbose (shows plugin/output details)
whatweb -v example.com

# multiple targets from file, save output
whatweb -iL hosts.txt -oT whatweb_results.txt
```

**Strengths / notes:** extensible, fast. Use proxies or rate limits if target imposes WAF/IP limits. ([GitHub][1])

---

## 2) Wappalyzer (CLI / library / browser) — browser-style tech detection

**What it does:** detects JavaScript libraries, CMS, analytics, e-commerce stacks — available as browser extension, CLI and libraries. Good for UI-level tech fingerprinting. ([GitHub][2])
**When to use:** when you want the same technology list you’d see in-browser (useful for tracking JS libs, analytics).
**Example (CLI / Go/Python variants):**

```bash
# using a Wappalyzer CLI (different community CLIs exist)
wappalyzer-cli https://example.com --output json > wappalyzer.json

# or use a python library to scan programmatically
python -c "from wappalyzer import Wappalyzer, WebPage; ... "
```

**Strengths / notes:** browser-like detection; many fingerprints maintained by community projects. ([PyPI][3])

---

## 3) BuiltWith — commercial, high-quality aggregated tech database

**What it does:** SaaS / web UI that lists technologies across millions of sites; great for large-scale leads & historical tech lookups. ([BuiltWith][4])
**When to use:** when you need rich reports, historic tech usage, or bulk lookups (paid tiers).
**Example:** use the web UI or BuiltWith Pro APIs to export reports (login required).
**Strengths / notes:** excellent data & reporting for market research, but mainly paid.

---

## 4) Nmap (HTTP NSE scripts) — low-level banners, headers, enumerations

**What it does:** with NSE scripts like `http-headers`, `http-title`, `http-enum`, `http-methods` etc., Nmap can fingerprint servers, enumerate common paths, and collect headers. Use it when you want port-level/contextual info and to combine network/HTTP fingerprinting. ([Nmap][5])
**When to use:** when you need both network and web app info (e.g., which ports/services, server banners, allowed methods).
**Example:**

```bash
# get HTTP headers + title
nmap -p 80,443 --script=http-headers,http-title example.com

# enumerate common web paths (http-enum)
nmap -sV --script=http-enum example.com

# check allowed HTTP methods
nmap --script=http-methods example.com
```

**Strengths / notes:** powerful when you want to merge network discovery and web fingerprinting; many scripts available. Always run with appropriate privileges and caution. ([Nmap][6])

---

## 5) WAFW00F — detect Web Application Firewalls (WAFs)

**What it does:** identifies whether a site is behind a WAF and which WAF vendor is protecting it. Useful early to know whether to expect blocking/false positives. ([GeeksforGeeks][7])
**When to use:** before aggressive scanning or fuzzing — helps choose evasive settings.
**Example:**

```bash
wafw00f example.com
wafw00f -a example.com   # aggressive detection
```

**Strengths / notes:** quick and focused — not a full fingerprinting suite but very handy.

---

## 6) httprint — classic web server fingerprinting (banner + behavior)

**What it does:** fingerprint web servers based on header patterns and behavior (server-type detection even if banner is obfuscated). Good for identifying web server software/version. ([net-square.com][8])
**When to use:** when you need server/app server identification even with banner masking.
**Example:**

```bash
httprint -h example.com -s httprint_signatures.txt -o httprint_out.txt
```

---

## 7) CMS-specific scanners — WPScan, CMSmap, Droopescan, Droopescan alternatives

**What they do:** target-specific fingerprinting + vulnerability checks (WordPress, Joomla, Drupal, etc.). Use these after initial tech detection if you find the site runs a known CMS. ([Sucuri Blog][9])
**When to use:** when WhatWeb/Wappalyzer/BuiltWith indicate a CMS (WordPress, Joomla, Drupal).
**Examples:**

```bash
# WordPress
wpscan --url example.com --enumerate u,p,vt,tt

# CMSmap (Joomla/WordPress/Drupal)
cmsmap -t example.com
```

**Strengths / notes:** gives plugin/theme enumeration and known vuln checks for the CMS family.

---

## Quick, practical fingerprinting workflow (starter script)

1. Passive / browser-level checks: Wappalyzer extension or BuiltWith (quick glance).
2. Automated tech scan: WhatWeb + Wappalyzer-CLI.
3. Network + HTTP probes: `nmap -p 80,443 --script=http-headers,http-title,http-enum example.com`.
4. WAF check: `wafw00f example.com`.
5. If CMS found → follow with `wpscan` / `cmsmap` / `droopescan`.

Example combined commands:

```bash
whatweb example.com && wappalyzer-cli https://example.com --output json
nmap -p 80,443 --script=http-headers,http-title,http-enum example.com
wafw00f example.com
# if WordPress:
wpscan --url example.com --enumerate vp,vt
```

---

## Short pros & cons summary

* **WhatWeb** — fast, scriptable, broad detection. Good first pass. ([GitHub][1])
* **Wappalyzer** — browser-like detection, great for JS/CSS libs. ([GitHub][2])
* **BuiltWith** — best for bulk/historical/paid insights. ([BuiltWith][4])
* **Nmap (HTTP NSE)** — essential when you want network + HTTP context and custom NSE scripts. ([Nmap][5])
* **WAFW00F** — focused WAF detection; useful pre-scan step. ([GeeksforGeeks][7])
* **httprint / CMS tools** — specialized fingerprinting (server vs CMS). ([net-square.com][8])

---

## Legal & safety note (short)

Only fingerprint/scan systems you **own** or have **explicit authorization** to test. Unauthorized scanning can be illegal and harmful — always get permission and follow a scope.

---

If you want, I can:

* Give you a **one-file bash script** that runs the workflow above and outputs JSON/text results, or
* Produce **examples for Kali** (install + apt/Go/PIP commands) for any 3 tools you pick.

Which option do you want next?

[1]: https://github.com/urbanadventurer/WhatWeb?utm_source=chatgpt.com "urbanadventurer/WhatWeb: Next generation web scanner"
[2]: https://github.com/tomnomnom/wappalyzer?utm_source=chatgpt.com "tomnomnom/wappalyzer: Identify technology on websites."
[3]: https://pypi.org/project/wappalyzer/?utm_source=chatgpt.com "wappalyzer"
[4]: https://builtwith.com/?utm_source=chatgpt.com "BuiltWith Technology Lookup"
[5]: https://nmap.org/nsedoc/scripts/http-enum.html?utm_source=chatgpt.com "http-enum NSE script"
[6]: https://nmap.org/book/nse-usage.html?utm_source=chatgpt.com "Usage and Examples | Nmap Network Scanning"
[7]: https://www.geeksforgeeks.org/linux-unix/identification-of-web-application-firewall-using-wafw00f-in-kali-linux/?utm_source=chatgpt.com "Identification of Web Application Firewall using WAFW00F ..."
[8]: https://net-square.com/httprint.html?utm_source=chatgpt.com "httprint"
[9]: https://blog.sucuri.net/2023/12/wpscan-intro-how-to-scan-for-wordpress-vulnerabilities.html?utm_source=chatgpt.com "WPScan Intro: How to Scan for WordPress Vulnerabilities"
