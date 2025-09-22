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

---

# 1) **CMSeeK / CMSeek** — general CMS detection + exploitation suite

What it is: multi-CMS detection & exploitation (WordPress, Joomla, Drupal and many others). Very similar in scope to CMSmap and available in Kali repos.
When to use: you want an all-in-one CMS reconnaissance tool that can do both detection and some exploitation/enumeration.
Install / run (Kali / git):

```bash
# from Kali package (if available)
sudo apt update && sudo apt install cmseek

# or from GitHub
git clone https://github.com/Tuhinshubhra/CMSeeK.git
cd CMSeeK
python3 cmseek.py -u https://example.com
```

Example run: `python3 cmseek.py -u https://example.com --deep` (performs deeper plugin/theme enumeration). ([GitHub][1])

---

# 2) **WPScan / Vane** — WordPress-focused (best for WP)

What it is: WPScan is the canonical, actively maintained WordPress vulnerability scanner; **Vane** is a fork/alternative focusing on WP as well.
When to use: target is WordPress — use these instead of generic CMS scanners for better plugin/theme vuln detection and a maintained vulnerability DB.
Install / run (WPScan):

```bash
# Kali / apt
sudo apt install wpscan

# quick scan (requires API token for some features)
wpscan --url https://example.com --enumerate vp,vt,u
```

Strength: huge WP vulnerability database and active updates. ([WPScan][2])

---

# 3) **Droopescan** — Drupal & SilverStripe (plugin-based)

What it is: plugin-based CMS scanner focusing on Drupal, SilverStripe and a few others; extensible via plugins.
When to use: target looks like Drupal/SilverStripe — Droopescan gives better coverage than generic scanners for those platforms.
Install / run:

```bash
git clone https://github.com/droopescan/droopescan.git
cd droopescan
python3 setup.py install
droopescan scan drupal https://example.com
```

Docs / usage examples are on the project page. ([GitHub][3])

---

# 4) **JoomScan (OWASP)** — Joomla-specific scanner

What it is: OWASP project to detect Joomla vulnerabilities and components.
When to use: target is Joomla — more accurate Joomla plugin/component checks than a generic tool.
Install / run:

```bash
# Kali package or from GitHub
sudo apt install joomscan
joomscan -u https://example.com
```

Lightweight and made specifically for Joomla sites. ([GitHub][4])

---

# 5) **CMSeek (Kali packaged CMSeeK) — packaged & scriptable**

Note: CMSeek/CMSeeK often appears in Kali and Docker registries; convenient if you want a packaged experience or Docker image for automated pipelines. Use the Kali package or the official Docker image for CI. ([Kali Linux][5])

---

# 6) **Th3inspector / other reconnaissance suites** — broader recon (not just CMS)

What it is: multi-purpose recon suites that include CMS detection modules along with subdomain, fingerprinting, and asset discovery. Good when you want CMS detection integrated with wider recon. Use when you want to chain discovery → fingerprinting → vuln checks. ([Linux Security Expert][6])

---

# 7) **Fallback tools: WhatWeb, Wappalyzer, Nmap + NSE**

Why: these aren’t CMS exploit scanners but are excellent for accurate tech identification (WhatWeb / Wappalyzer) and low-level fingerprinting (Nmap http-\* scripts). Use them first to confirm the CMS before running CMS-specific tools. Example:

```bash
whatweb example.com
nmap -p80,443 --script=http-headers,http-enum example.com
```

Combine these with CMS scanners for best results. ([GeeksforGeeks][7])

---

## Quick picking guide

* If **WordPress** → **WPScan / Vane** (best accuracy + DB). ([WPScan][2])
* If **Joomla** → **JoomScan**. ([GitHub][4])
* If **Drupal/SilverStripe** → **Droopescan**. ([GitHub][3])
* If you want a **CMSmap-like all-in-one** → **CMSeeK / CMSeek** (or Th3inspector for broader recon). ([GitHub][1])

---

## Example short workflow (replace tools depending on CMS)

```bash
# 1) tech fingerprint
whatweb https://example.com

# 2) quick WAF check / header info
nmap -p80,443 --script=http-headers example.com

# 3) run CMS-specific scan (example WordPress)
wpscan --url https://example.com --enumerate vp,vt

# 4) if general CMS detection needed
python3 CMSeeK/cmseek.py -u https://example.com
```

---

## Final notes / safety

* Always have explicit authorization before scanning.
* Some tools (WPScan) require API keys for large-scale vulnerability DB lookups; check docs. ([WPScan][2])

## Short pros & cons summary

* **WhatWeb** — fast, scriptable, broad detection. Good first pass. ([GitHub][1])
* **Wappalyzer** — browser-like detection, great for JS/CSS libs. ([GitHub][2])
* **BuiltWith** — best for bulk/historical/paid insights. ([BuiltWith][4])
* **Nmap (HTTP NSE)** — essential when you want network + HTTP context and custom NSE scripts. ([Nmap][5])
* **WAFW00F** — focused WAF detection; useful pre-scan step. ([GeeksforGeeks][7])
* **httprint / CMS tools** — specialized fingerprinting (server vs CMS). ([net-square.com][8])

---
