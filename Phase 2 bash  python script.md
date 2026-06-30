

```
#!/bin/bash

# Recon Phase 2 - Full Automation Script
# Usage: ./recon_phase2.sh <domain> <output_dir>

DOMAIN=$1
OUTDIR=${2:-recon_$DOMAIN}
mkdir -p "$OUTDIR"

echo "[+] Starting Recon Phase 2 for $DOMAIN"
echo "[+] Output directory: $OUTDIR"

# 2.1 Subdomain Enumeration
echo "[*] 2.1 Subdomain Enumeration"
subfinder -d "$DOMAIN" -all -silent > "$OUTDIR/subdomains_passive.txt"
assetfinder --subs-only "$DOMAIN" >> "$OUTDIR/subdomains_passive.txt"
cat "$OUTDIR/subdomains_passive.txt" | sort -u > "$OUTDIR/subdomains.txt"

# Active Enumeration
amass enum -passive -d "$DOMAIN" -o "$OUTDIR/subdomains_amass.txt"
cat "$OUTDIR/subdomains_amass.txt" "$OUTDIR/subdomains.txt" | sort -u > "$OUTDIR/all_subdomains.txt"

# 2.2 Probing — Finding Live Hosts
echo "[*] 2.2 Probing Live Hosts"
cat "$OUTDIR/all_subdomains.txt" | httpx -silent -status-code -title -tech-detect -o "$OUTDIR/live_hosts.txt"

# 2.3 Technology Fingerprinting
echo "[*] 2.3 Technology Fingerprinting"
cat "$OUTDIR/live_hosts.txt" | httpx -silent -tech-detect -o "$OUTDIR/tech_fingerprint.txt"

# 2.4 Content Discovery
echo "[*] 2.4 Content Discovery"
# Directory Bruteforce
ffuf -u "https://FUZZ.$DOMAIN" -w /usr/share/wordlists/dirsearch.txt -o "$OUTDIR/dir_bruteforce.json" -of json
# Web Crawling
katana -list "$OUTDIR/live_hosts.txt" -jc -kf all -o "$OUTDIR/crawled_urls.txt"
# Parameter Discovery
arjun -i "$OUTDIR/live_hosts.txt" -oJ "$OUTDIR/parameters.json"
# Historical URLs
waybackurls "$DOMAIN" > "$OUTDIR/historical_urls.txt"

# 2.5 JavaScript Analysis
echo "[*] 2.5 JavaScript Analysis"
katana -list "$OUTDIR/live_hosts.txt" -jc -o "$OUTDIR/js_files.txt"
# Endpoint Extraction & Secrets
cat "$OUTDIR/js_files.txt" | grep -E '\.js$' | httpx -silent | nuclei -t nuclei-templates/http/exposures/ -o "$OUTDIR/js_secrets.txt"
# Link Discovery
cat "$OUTDIR/js_files.txt" | getJS | tee "$OUTDIR/js_links.txt"

# 2.6 Port Scanning
echo "[*] 2.6 Port Scanning"
naabu -list "$OUTDIR/all_subdomains.txt" -top-ports 1000 -o "$OUTDIR/ports.txt"

# 2.7 Certificate Transparency Logs
echo "[*] 2.7 Certificate Transparency Logs"
crtsh "$DOMAIN" > "$OUTDIR/ct_logs.txt"

# 2.8 Dorking
echo "[*] 2.8 Dorking"
# Google Dorking (manual example saved)
echo "site:$DOMAIN filetype:pdf" > "$OUTDIR/google_dorks.txt"
# GitHub Dorking
github-search "$DOMAIN" > "$OUTDIR/github_dorks.txt"
# Shodan Dorking (manual)
echo "hostname:$DOMAIN" > "$OUTDIR/shodan_dorks.txt"

# 2.9 Origin IP Discovery & WAF Bypass
echo "[*] 2.9 Origin IP Discovery & WAF Bypass"
cat "$OUTDIR/all_subdomains.txt" | dnsx -a -resp -o "$OUTDIR/origin_ips.txt"
# WAF Bypass example (manual)
echo "Use cloudflared or similar for WAF bypass" > "$OUTDIR/waf_bypass.txt"

# 2.10 Parameter Filtering for Mass Hunting
echo "[*] 2.10 Parameter Filtering"
cat "$OUTDIR/live_hosts.txt" | gf xss > "$OUTDIR/gf_xss.txt"
cat "$OUTDIR/live_hosts.txt" | gf sqli > "$OUTDIR/gf_sqli.txt"
cat "$OUTDIR/live_hosts.txt" | gf lfi > "$OUTDIR/gf_lfi.txt"
cat "$OUTDIR/live_hosts.txt" | gf redirect > "$OUTDIR/gf_redirect.txt"

# 2.11 Email & DNS Recon
echo "[*] 2.11 Email & DNS Recon"
theHarvester -d "$DOMAIN" -b all -f "$OUTDIR/emails.txt"
dnsrecon -d "$DOMAIN" -t std,axfr -o "$OUTDIR/dnsrecon.txt"

echo "[+] Recon Phase 2 completed. Results saved in $OUTDIR"

```


```
#!/usr/bin/env python3
"""
Recon Phase 2 - Full Automation Script (Python)
Usage: python3 recon_phase2.py <domain> [output_dir]
"""

import subprocess
import sys
import os

def run_cmd(cmd, output_file=None):
    try:
        result = subprocess.run(cmd, shell=True, capture_output=True, text=True)
        if output_file:
            with open(output_file, "w") as f:
                f.write(result.stdout)
        return result.stdout.strip()
    except Exception as e:
        print(f"[!] Error running: {cmd}")
        print(e)
        return ""

def main():
    if len(sys.argv) < 2:
        print("Usage: python3 recon_phase2.py <domain> [output_dir]")
        sys.exit(1)

    domain = sys.argv[1]
    outdir = sys.argv[2] if len(sys.argv) > 2 else f"recon_{domain}"
    os.makedirs(outdir, exist_ok=True)

    print(f"[+] Starting Recon Phase 2 for {domain}")
    print(f"[+] Output directory: {outdir}")

    # 2.1 Subdomain Enumeration
    print("[*] 2.1 Subdomain Enumeration")
    run_cmd(f"subfinder -d {domain} -all -silent", f"{outdir}/subdomains_passive.txt")
    run_cmd(f"assetfinder --subs-only {domain} >> {outdir}/subdomains_passive.txt")
    run_cmd(f"sort -u {outdir}/subdomains_passive.txt -o {outdir}/subdomains.txt")

    run_cmd(f"amass enum -passive -d {domain} -o {outdir}/subdomains_amass.txt")
    run_cmd(f"cat {outdir}/subdomains_amass.txt {outdir}/subdomains.txt | sort -u > {outdir}/all_subdomains.txt")

    # 2.2 Probing — Finding Live Hosts
    print("[*] 2.2 Probing Live Hosts")
    run_cmd(f"cat {outdir}/all_subdomains.txt | httpx -silent -status-code -title -tech-detect -o {outdir}/live_hosts.txt")

    # 2.3 Technology Fingerprinting
    print("[*] 2.3 Technology Fingerprinting")
    run_cmd(f"cat {outdir}/live_hosts.txt | httpx -silent -tech-detect -o {outdir}/tech_fingerprint.txt")

    # 2.4 Content Discovery
    print("[*] 2.4 Content Discovery")
    run_cmd(f"ffuf -u 'https://FUZZ.{domain}' -w /usr/share/wordlists/dirsearch.txt -o {outdir}/dir_bruteforce.json -of json")
    run_cmd(f"katana -list {outdir}/live_hosts.txt -jc -kf all -o {outdir}/crawled_urls.txt")
    run_cmd(f"arjun -u {outdir}/live_hosts.txt -oJ {outdir}/parameters.json")
    run_cmd(f"waybackurls {domain} > {outdir}/historical_urls.txt")

    # 2.5 JavaScript Analysis
    print("[*] 2.5 JavaScript Analysis")
    run_cmd(f"katana -list {outdir}/live_hosts.txt -jc -o {outdir}/js_files.txt")
    run_cmd(f"cat {outdir}/js_files.txt | grep -E '\\.js$' | httpx -silent | nuclei -t nuclei-templates/http/exposures/ -o {outdir}/js_secrets.txt")
    run_cmd(f"cat {outdir}/js_files.txt | getJS | tee {outdir}/js_links.txt")

    # 2.6 Port Scanning
    print("[*] 2.6 Port Scanning")
    run_cmd(f"naabu -list {outdir}/all_subdomains.txt -top-ports 1000 -o {outdir}/ports.txt")

    # 2.7 Certificate Transparency Logs
    print("[*] 2.7 Certificate Transparency Logs")
    run_cmd(f"crtsh {domain} > {outdir}/ct_logs.txt")

    # 2.8 Dorking
    print("[*] 2.8 Dorking")
    with open(f"{outdir}/google_dorks.txt", "w") as f:
        f.write(f"site:{domain} filetype:pdf\n")
    run_cmd(f"github-search {domain} > {outdir}/github_dorks.txt")
    with open(f"{outdir}/shodan_dorks.txt", "w") as f:
        f.write(f"hostname:{domain}\n")

    # 2.9 Origin IP Discovery & WAF Bypass
    print("[*] 2.9 Origin IP Discovery & WAF Bypass")
    run_cmd(f"cat {outdir}/all_subdomains.txt | dnsx -a -resp -o {outdir}/origin_ips.txt")
    with open(f"{outdir}/waf_bypass.txt", "w") as f:
        f.write("Use cloudflared or similar for WAF bypass\n")

    # 2.10 Parameter Filtering for Mass Hunting
    print("[*] 2.10 Parameter Filtering")
    run_cmd(f"cat {outdir}/live_hosts.txt | gf xss > {outdir}/gf_xss.txt")
    run_cmd(f"cat {outdir}/live_hosts.txt | gf sqli > {outdir}/gf_sqli.txt")
    run_cmd(f"cat {outdir}/live_hosts.txt | gf lfi > {outdir}/gf_lfi.txt")
    run_cmd(f"cat {outdir}/live_hosts.txt | gf redirect > {outdir}/gf_redirect.txt")

    # 2.11 Email & DNS Recon
    print("[*] 2.11 Email & DNS Recon")
    run_cmd(f"theHarvester -d {domain} -b all -f {outdir}/emails.txt")
    run_cmd(f"dnsrecon -d {domain} -t std,axfr -o {outdir}/dnsrecon.txt")

    print(f"[+] Recon Phase 2 completed. Results saved in {outdir}")

if __name__ == "__main__":
    main()

```
