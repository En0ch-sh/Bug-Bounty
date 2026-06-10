

```
#!/usr/bin/env python3
"""
Phase 3: Vulnerability Hunting - Full Automation Script (Python)
Usage: python3 phase3_vuln_hunting.py <domain> [output_dir]
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
        print("Usage: python3 phase3_vuln_hunting.py <domain> [output_dir]")
        sys.exit(1)

    domain = sys.argv[1]
    outdir = sys.argv[2] if len(sys.argv) > 2 else f"vuln_{domain}"
    os.makedirs(outdir, exist_ok=True)

    print(f"[+] Starting Phase 3 Vulnerability Hunting for {domain}")
    print(f"[+] Output directory: {outdir}")

    # Load live hosts from Phase 2
    live_file = f"recon_{domain}/live_hosts.txt"
    if not os.path.exists(live_file):
        print("[!] live_hosts.txt not found. Run Phase 2 first.")
        sys.exit(1)

    # 3.1 SQL Injection
    print("[*] 3.1 SQL Injection")
    run_cmd(f"cat {live_file} | nuclei -t nuclei-templates/vulnerabilities/sqli/ -o {outdir}/sqli.txt")

    # 3.2 Cross-Site Scripting (XSS)
    print("[*] 3.2 Cross-Site Scripting (XSS)")
    run_cmd(f"cat {live_file} | nuclei -t nuclei-templates/vulnerabilities/xss/ -o {outdir}/xss.txt")

    # 3.3 Server-Side Request Forgery (SSRF)
    print("[*] 3.3 SSRF")
    run_cmd(f"cat {live_file} | nuclei -t nuclei-templates/vulnerabilities/ssrf/ -o {outdir}/ssrf.txt")

    # 3.4 Local File Inclusion (LFI)
    print("[*] 3.4 LFI")
    run_cmd(f"cat {live_file} | nuclei -t nuclei-templates/vulnerabilities/lfi/ -o {outdir}/lfi.txt")

    # 3.5 Remote Code Execution (RCE)
    print("[*] 3.5 RCE")
    run_cmd(f"cat {live_file} | nuclei -t nuclei-templates/vulnerabilities/rce/ -o {outdir}/rce.txt")

    # 3.6 Open Redirect
    print("[*] 3.6 Open Redirect")
    run_cmd(f"cat {live_file} | nuclei -t nuclei-templates/vulnerabilities/open-redirect/ -o {outdir}/open_redirect.txt")

    # 3.7 IDOR
    print("[*] 3.7 IDOR")
    run_cmd(f"cat {live_file} | nuclei -t nuclei-templates/vulnerabilities/idor/ -o {outdir}/idor.txt")

    # 3.8 File Upload Vulnerabilities
    print("[*] 3.8 File Upload")
    run_cmd(f"cat {live_file} | nuclei -t nuclei-templates/vulnerabilities/file-upload/ -o {outdir}/file_upload.txt")

    # 3.9 Subdomain Takeover
    print("[*] 3.9 Subdomain Takeover")
    run_cmd(f"subzy -targets {live_file} -o {outdir}/subdomain_takeover.txt")

    # 3.10 CRLF / Header Injection
    print("[*] 3.10 CRLF Injection")
    run_cmd(f"cat {live_file} | nuclei -t nuclei-templates/vulnerabilities/crlf-injection/ -o {outdir}/crlf.txt")

    # 3.11 XXE
    print("[*] 3.11 XXE")
    run_cmd(f"cat {live_file} | nuclei -t nuclei-templates/vulnerabilities/xxe/ -o {outdir}/xxe.txt")

    # 3.12 SSTI
    print("[*] 3.12 SSTI")
    run_cmd(f"cat {live_file} | nuclei -t nuclei-templates/vulnerabilities/ssti/ -o {outdir}/ssti.txt")

    # 3.13 CSRF
    print("[*] 3.13 CSRF")
    run_cmd(f"cat {live_file} | nuclei -t nuclei-templates/vulnerabilities/csrf/ -o {outdir}/csrf.txt")

    # 3.14 Race Conditions
    print("[*] 3.14 Race Conditions")
    run_cmd(f"cat {live_file} | nuclei -t nuclei-templates/vulnerabilities/race-condition/ -o {outdir}/race_condition.txt")

    # 3.15 JWT Attacks
    print("[*] 3.15 JWT Attacks")
    run_cmd(f"cat {live_file} | nuclei -t nuclei-templates/vulnerabilities/jwt/ -o {outdir}/jwt.txt")

    # 3.16 NoSQL Injection
    print("[*] 3.16 NoSQL Injection")
    run_cmd(f"cat {live_file} | nuclei -t nuclei-templates/vulnerabilities/nosql-injection/ -o {outdir}/nosql.txt")

    # 3.17 GraphQL Attacks
    print("[*] 3.17 GraphQL")
    run_cmd(f"cat {live_file} | nuclei -t nuclei-templates/vulnerabilities/graphql/ -o {outdir}/graphql.txt")

    # 3.18 Business Logic Flaws
    print("[*] 3.18 Business Logic Flaws")
    run_cmd(f"cat {live_file} | nuclei -t nuclei-templates/vulnerabilities/business-logic/ -o {outdir}/business_logic.txt")

    # 3.19 HTTP Request Smuggling
    print("[*] 3.19 HTTP Request Smuggling")
    run_cmd(f"cat {live_file} | nuclei -t nuclei-templates/vulnerabilities/http-request-smuggling/ -o {outdir}/http_smuggling.txt")

    # 3.20 Authentication Attacks
    print("[*] 3.20 Authentication Attacks")
    run_cmd(f"cat {live_file} | nuclei -t nuclei-templates/vulnerabilities/authentication/ -o {outdir}/authentication.txt")

    # 3.21 Deserialization Attacks
    print("[*] 3.21 Deserialization")
    run_cmd(f"cat {live_file} | nuclei -t nuclei-templates/vulnerabilities/deserialization/ -o {outdir}/deserialization.txt")

    # 3.22 Prototype Pollution
    print("[*] 3.22 Prototype Pollution")
    run_cmd(f"cat {live_file} | nuclei -t nuclei-templates/vulnerabilities/prototype-pollution/ -o {outdir}/prototype_pollution.txt")

    # 3.23 Cache Poisoning
    print("[*] 3.23 Cache Poisoning")
    run_cmd(f"cat {live_file} | nuclei -t nuclei-templates/vulnerabilities/cache-poisoning/ -o {outdir}/cache_poisoning.txt")

    # 3.24 Supply Chain / Dependency Confusion
    print("[*] 3.24 Supply Chain")
    run_cmd(f"cat {live_file} | nuclei -t nuclei-templates/vulnerabilities/supply-chain/ -o {outdir}/supply_chain.txt")

    # 3.25 Payment Logic Flaws
    print("[*] 3.25 Payment Logic")
    run_cmd(f"cat {live_file} | nuclei -t nuclei-templates/vulnerabilities/payment-logic/ -o {outdir}/payment_logic.txt")

    # 3.26 Second-Order Vulnerabilities
    print("[*] 3.26 Second-Order")
    run_cmd(f"cat {live_file} | nuclei -t nuclei-templates/vulnerabilities/second-order/ -o {outdir}/second_order.txt")

    # 3.27 HTTP/2 & WebSocket Attacks
    print("[*] 3.27 HTTP/2 & WebSocket")
    run_cmd(f"cat {live_file} | nuclei -t nuclei-templates/vulnerabilities/http2-websocket/ -o {outdir}/http2_websocket.txt")

    # 3.28 CORS Misconfiguration
    print("[*] 3.28 CORS")
    run_cmd(f"cat {live_file} | nuclei -t nuclei-templates/vulnerabilities/cors/ -o {outdir}/cors.txt")

    # 3.29 Captcha Bypass
    print("[*] 3.29 Captcha Bypass")
    run_cmd(f"cat {live_file} | nuclei -t nuclei-templates/vulnerabilities/captcha/ -o {outdir}/captcha.txt")

    # 3.30 Default Credentials
    print("[*] 3.30 Default Credentials")
    run_cmd(f"cat {live_file} | nuclei -t nuclei-templates/vulnerabilities/default-credentials/ -o {outdir}/default_credentials.txt")

    # 3.31 LDAP Injection
    print("[*] 3.31 LDAP Injection")
    run_cmd(f"cat {live_file} | nuclei -t nuclei-templates/vulnerabilities/ldap-injection/ -o {outdir}/ldap.txt")

    # 3.32 XPath Injection
    print("[*] 3.32 XPath Injection")
    run_cmd(f"cat {live_file} | nuclei -t nuclei-templates/vulnerabilities/xpath-injection/ -o {outdir}/xpath.txt")

    # 3.33 Email Security Misconfig
    print("[*] 3.33 Email Security Misconfig")
    run_cmd(f"cat {live_file} | nuclei -t nuclei-templates/vulnerabilities/email-misconfig/ -o {outdir}/email_misconfig.txt")

    # 3.34 DNS Attacks
    print("[*] 3.34 DNS Attacks")
    run_cmd(f"cat {live_file} | nuclei -t nuclei-templates/vulnerabilities/dns-attacks/ -o {outdir}/dns_attacks.txt")

    # 3.35 Headless Browser Testing
    print("[*] 3.35 Headless Browser Testing")
    run_cmd(f"cat {live_file} | nuclei -t nuclei-templates/vulnerabilities/headless-browser/ -o {outdir}/headless_browser.txt")

    # 3.36 Electron App Security
    print("[*] 3.36 Electron App Security")
    run_cmd(f"cat {live_file} | nuclei -t nuclei-templates/vulnerabilities/electron-security/ -o {outdir}/electron_security.txt")

    # 3.37 WordPress / CMS Plugin Vulnerabilities
    print("[*] 3.37 WordPress / CMS")
    run_cmd(f"cat {live_file} | nuclei -t nuclei-templates/vulnerabilities/wordpress/ -o {outdir}/wordpress.txt")

    print(f"[+] Phase 3 Vulnerability Hunting completed. Results saved in {outdir}")

if __name__ == "__main__":
    main()

```


```
#!/bin/bash

# Phase 3: Vulnerability Hunting - Full Automation Script (Bash)
# Usage: ./phase3_vuln_hunting.sh <domain> [output_dir]

DOMAIN=$1
OUTDIR=${2:-vuln_$DOMAIN}
LIVE_FILE="recon_$DOMAIN/live_hosts.txt"

if [ ! -f "$LIVE_FILE" ]; then
    echo "[!] live_hosts.txt not found from Phase 2. Exiting."
    exit 1
fi

mkdir -p "$OUTDIR"
echo "[+] Starting Phase 3 Vulnerability Hunting for $DOMAIN"
echo "[+] Output directory: $OUTDIR"

# 3.1 SQL Injection
echo "[*] 3.1 SQL Injection"
cat "$LIVE_FILE" | nuclei -t nuclei-templates/vulnerabilities/sqli/ -o "$OUTDIR/sqli.txt"

# 3.2 Cross-Site Scripting (XSS)
echo "[*] 3.2 Cross-Site Scripting (XSS)"
cat "$LIVE_FILE" | nuclei -t nuclei-templates/vulnerabilities/xss/ -o "$OUTDIR/xss.txt"

# 3.3 Server-Side Request Forgery (SSRF)
echo "[*] 3.3 SSRF"
cat "$LIVE_FILE" | nuclei -t nuclei-templates/vulnerabilities/ssrf/ -o "$OUTDIR/ssrf.txt"

# 3.4 Local File Inclusion (LFI)
echo "[*] 3.4 LFI"
cat "$LIVE_FILE" | nuclei -t nuclei-templates/vulnerabilities/lfi/ -o "$OUTDIR/lfi.txt"

# 3.5 Remote Code Execution (RCE)
echo "[*] 3.5 RCE"
cat "$LIVE_FILE" | nuclei -t nuclei-templates/vulnerabilities/rce/ -o "$OUTDIR/rce.txt"

# 3.6 Open Redirect
echo "[*] 3.6 Open Redirect"
cat "$LIVE_FILE" | nuclei -t nuclei-templates/vulnerabilities/open-redirect/ -o "$OUTDIR/open_redirect.txt"

# 3.7 IDOR
echo "[*] 3.7 IDOR"
cat "$LIVE_FILE" | nuclei -t nuclei-templates/vulnerabilities/idor/ -o "$OUTDIR/idor.txt"

# 3.8 File Upload Vulnerabilities
echo "[*] 3.8 File Upload"
cat "$LIVE_FILE" | nuclei -t nuclei-templates/vulnerabilities/file-upload/ -o "$OUTDIR/file_upload.txt"

# 3.9 Subdomain Takeover
echo "[*] 3.9 Subdomain Takeover"
subzy -targets "$LIVE_FILE" -o "$OUTDIR/subdomain_takeover.txt"

# 3.10 CRLF / Header Injection
echo "[*] 3.10 CRLF Injection"
cat "$LIVE_FILE" | nuclei -t nuclei-templates/vulnerabilities/crlf-injection/ -o "$OUTDIR/crlf.txt"

# 3.11 XXE
echo "[*] 3.11 XXE"
cat "$LIVE_FILE" | nuclei -t nuclei-templates/vulnerabilities/xxe/ -o "$OUTDIR/xxe.txt"

# 3.12 SSTI
echo "[*] 3.12 SSTI"
cat "$LIVE_FILE" | nuclei -t nuclei-templates/vulnerabilities/ssti/ -o "$OUTDIR/ssti.txt"

# 3.13 CSRF
echo "[*] 3.13 CSRF"
cat "$LIVE_FILE" | nuclei -t nuclei-templates/vulnerabilities/csrf/ -o "$OUTDIR/csrf.txt"

# 3.14 Race Conditions
echo "[*] 3.14 Race Conditions"
cat "$LIVE_FILE" | nuclei -t nuclei-templates/vulnerabilities/race-condition/ -o "$OUTDIR/race_condition.txt"

# 3.15 JWT Attacks
echo "[*] 3.15 JWT Attacks")
cat "$LIVE_FILE" | nuclei -t nuclei-templates/vulnerabilities/jwt/ -o "$OUTDIR/jwt.txt"

# 3.16 NoSQL Injection
echo "[*] 3.16 NoSQL Injection"
cat "$LIVE_FILE" | nuclei -t nuclei-templates/vulnerabilities/nosql-injection/ -o "$OUTDIR/nosql.txt"

# 3.17 GraphQL Attacks
echo "[*] 3.17 GraphQL")
cat "$LIVE_FILE" | nuclei -t nuclei-templates/vulnerabilities/graphql/ -o "$OUTDIR/graphql.txt"

# 3.18 Business Logic Flaws
echo "[*] 3.18 Business Logic Flaws")
cat "$LIVE_FILE" | nuclei -t nuclei-templates/vulnerabilities/business-logic/ -o "$OUTDIR/business_logic.txt"

# 3.19 HTTP Request Smuggling
echo "[*] 3.19 HTTP Request Smuggling")
cat "$LIVE_FILE" | nuclei -t nuclei-templates/vulnerabilities/http-request-smuggling/ -o "$OUTDIR/http_smuggling.txt"

# 3.20 Authentication Attacks
echo "[*] 3.20 Authentication Attacks")
cat "$LIVE_FILE" | nuclei -t nuclei-templates/vulnerabilities/authentication/ -o "$OUTDIR/authentication.txt"

# 3.21 Deserialization Attacks
echo "[*] 3.21 Deserialization")
cat "$LIVE_FILE" | nuclei -t nuclei-templates/vulnerabilities/deserialization/ -o "$OUTDIR/deserialization.txt"

# 3.22 Prototype Pollution
echo "[*] 3.22 Prototype Pollution")
cat "$LIVE_FILE" | nuclei -t nuclei-templates/vulnerabilities/prototype-pollution/ -o "$OUTDIR/prototype_pollution.txt"

# 3.23 Cache Poisoning & Web Cache Deception
echo "[*] 3.23 Cache Poisoning")
cat "$LIVE_FILE" | nuclei -t nuclei-templates/vulnerabilities/cache-poisoning/ -o "$OUTDIR/cache_poisoning.txt"

# 3.24 Supply Chain / Dependency Confusion
echo "[*] 3.24 Supply Chain")
cat "$LIVE_FILE" | nuclei -t nuclei-templates/vulnerabilities/supply-chain/ -o "$OUTDIR/supply_chain.txt"

# 3.25 Payment Logic Flaws
echo "[*] 3.25 Payment Logic")
cat "$LIVE_FILE" | nuclei -t nuclei-templates/vulnerabilities/payment-logic/ -o "$OUTDIR/payment_logic.txt"

# 3.26 Second-Order Vulnerabilities
echo "[*] 3.26 Second-Order")
cat "$LIVE_FILE" | nuclei -t nuclei-templates/vulnerabilities/second-order/ -o "$OUTDIR/second_order.txt"

# 3.27 HTTP/2 & WebSocket Attacks
echo "[*] 3.27 HTTP/2 & WebSocket")
cat "$LIVE_FILE" | nuclei -t nuclei-templates/vulnerabilities/http2-websocket/ -o "$OUTDIR/http2_websocket.txt"

# 3.28 CORS Misconfiguration
echo "[*] 3.28 CORS")
cat "$LIVE_FILE" | nuclei -t nuclei-templates/vulnerabilities/cors/ -o "$OUTDIR/cors.txt"

# 3.29 Captcha Bypass
echo "[*] 3.29 Captcha Bypass")
cat "$LIVE_FILE" | nuclei -t nuclei-templates/vulnerabilities/captcha/ -o "$OUTDIR/captcha.txt"

# 3.30 Default Credentials
echo "[*] 3.30 Default Credentials")
cat "$LIVE_FILE" | nuclei -t nuclei-templates/vulnerabilities/default-credentials/ -o "$OUTDIR/default_credentials.txt"

# 3.31 LDAP Injection
echo "[*] 3.31 LDAP Injection")
cat "$LIVE_FILE" | nuclei -t nuclei-templates/vulnerabilities/ldap-injection/ -o "$OUTDIR/ldap.txt"

# 3.32 XPath Injection
echo "[*] 3.32 XPath Injection")
cat "$LIVE_FILE" | nuclei -t nuclei-templates/vulnerabilities/xpath-injection/ -o "$OUTDIR/xpath.txt"

# 3.33 Email Security Misconfig (SPF/DKIM/DMARC)
echo "[*] 3.33 Email Security Misconfig")
cat "$LIVE_FILE" | nuclei -t nuclei-templates/vulnerabilities/email-misconfig/ -o "$OUTDIR/email_misconfig.txt"

# 3.34 DNS Attacks
echo "[*] 3.34 DNS Attacks")
cat "$LIVE_FILE" | nuclei -t nuclei-templates/vulnerabilities/dns-attacks/ -o "$OUTDIR/dns_attacks.txt"

# 3.35 Headless Browser Testing (Puppeteer, Playwright)
echo "[*] 3.35 Headless Browser Testing")
cat "$LIVE_FILE" | nuclei -t nuclei-templates/vulnerabilities/headless-browser/ -o "$OUTDIR/headless_browser.txt"

# 3.36 Electron App Security
echo "[*] 3.36 Electron App Security")
cat "$LIVE_FILE" | nuclei -t nuclei-templates/vulnerabilities/electron-security/ -o "$OUTDIR/electron_security.txt"

# 3.37 WordPress / CMS Plugin Vulnerabilities
echo "[*] 3.37 WordPress / CMS")
cat "$LIVE_FILE" | nuclei -t nuclei-templates/vulnerabilities/wordpress/ -o "$OUTDIR/wordpress.txt"

echo "[+] Phase 3 Vulnerability Hunting completed. Results saved in $OUTDIR"

```