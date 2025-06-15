# Subdomain Discovery and DNS Reconnaissance Wiki

## Table of Contents

1. [Introduction](#introduction)
2. [Core Concepts](#core-concepts)
3. [Command Reference](#command-reference)
4. [Tools and Techniques](#tools-and-techniques)
5. [MassDNS Usage](#massdns-usage)
6. [Certificate Transparency Logs](#certificate-transparency-logs)
7. [Additional Subdomain Enumeration Tools](#additional-subdomain-enumeration-tools)
8. [OSINT for Subdomain Discovery](#osint-for-subdomain-discovery)
9. [Wordlist Resources](#wordlist-resources)
10. [Advanced Techniques](#advanced-techniques)
11. [Automating the Workflow](#automating-the-workflow)
12. [Best Practices](#best-practices)
13. [References](#references)
14. [Downloadable Scripts](#downloadable-scripts)

## Introduction

Subdomain discovery is a critical component of reconnaissance in security assessments and bug bounty hunting. Finding subdomains can reveal additional attack surface and potentially vulnerable endpoints that aren't immediately visible. This wiki provides comprehensive guidance on tools, techniques, and strategies for effective subdomain enumeration.

## Core Concepts

### What are Subdomains?

Subdomains are parts of a larger domain that can point to different servers or services. For example, `blog.example.com` and `mail.example.com` are subdomains of `example.com`.

### Why Enumerate Subdomains?

- Discover additional attack surface
- Find forgotten or legacy systems
- Identify testing environments
- Locate potentially vulnerable applications
- Map organizational infrastructure

### Types of Subdomain Discovery

1. **Passive Techniques**: Obtaining information without direct interaction with the target
   - Certificate transparency logs
   - Public datasets
   - Search engines
   - DNS datasets

2. **Active Techniques**: Direct interaction with the target or related systems
   - DNS brute-forcing
   - Zone transfers
   - DNS resolution
   - Virtual host discovery

## Command Reference

### Basic DNS Commands

```bash
# Check DNS records (A record)
dig example.com

# Check NS records
dig NS example.com

# Check MX records
dig MX example.com

# Check all DNS records
dig ANY example.com

# Use a specific DNS server
dig @8.8.8.8 example.com

# Attempt zone transfer
dig AXFR @ns1.example.com example.com
```

### Working with Subdomain Lists

```bash
# Sort a list of subdomains
sort -u subdomains.txt -o sorted_subdomains.txt

# Combine multiple subdomain lists
cat list1.txt list2.txt list3.txt | sort -u > combined.txt

# Count unique subdomains
wc -l subdomains.txt

# Filter subdomains by domain
grep "\.example\.com$" all_domains.txt > example_subdomains.txt
```

### Resolving and Validating Subdomains

```bash
# Basic DNS resolution with dig
cat subdomains.txt | while read sub; do dig +short $sub; done

# Check if subdomains resolve (host command)
cat subdomains.txt | while read sub; do host $sub | grep -v "not found"; done

# Multi-threaded resolution with dnsx
cat subdomains.txt | dnsx -silent -a -resp

# Filter only valid subdomains
cat subdomains.txt | dnsx -silent -a -o resolved.txt
```

### HTTP Service Discovery

```bash
# Check HTTP and HTTPS services
cat subdomains.txt | httpx -silent

# Get status code, title, content length
cat subdomains.txt | httpx -status-code -title -content-length -silent

# Filter for specific status codes
cat subdomains.txt | httpx -silent -mc 200,301,302 -o live_servers.txt

# Check specific ports
cat subdomains.txt | httpx -ports 80,443,8080,8443 -silent
```

## Tools and Techniques

### Essential Tools for Subdomain Discovery

1. **MassDNS**: Fast DNS resolver for bulk subdomain resolution
2. **Amass**: In-depth subdomain enumeration tool
3. **Subfinder**: Fast passive subdomain discovery tool
4. **Sublist3r**: OSINT subdomain discovery tool
5. **Findomain**: Cross-platform subdomain enumerator
6. **Assetfinder**: Find domains and subdomains related to a target
7. **Altdns**: Generate and permute potential subdomains
8. **Nuclei**: Template-based vulnerability scanner with subdomain capability
9. **DNSx**: DNS toolkit for running various DNS queries

### Installation Commands

```bash
# MassDNS
git clone https://github.com/blechschmidt/massdns.git
cd massdns
make

# Subfinder
GO111MODULE=on go install -v github.com/projectdiscovery/subfinder/v2/cmd/subfinder@latest

# Amass
GO111MODULE=on go install -v github.com/owasp-amass/amass/v3/...@master

# Findomain
curl -LO https://github.com/findomain/findomain/releases/latest/download/findomain-linux
chmod +x findomain-linux
sudo mv findomain-linux /usr/local/bin/findomain

# Assetfinder
go install github.com/tomnomnom/assetfinder@latest

# Altdns
pip install py-altdns

# DNSx
GO111MODULE=on go install -v github.com/projectdiscovery/dnsx/cmd/dnsx@latest

# HTTPx
GO111MODULE=on go install -v github.com/projectdiscovery/httpx/cmd/httpx@latest
```

## MassDNS Usage

MassDNS is a high-performance DNS stub resolver that can help with subdomain discovery through brute force and resolution.

### Basic Usage Commands

```bash
# Get reliable resolvers
curl -s https://raw.githubusercontent.com/janmasarik/resolvers/master/resolvers.txt > resolvers.txt

# Basic A record query
./bin/massdns -r resolvers.txt -t A -o S domains.txt

# Save results to file
./bin/massdns -r resolvers.txt -t A -o S -w results.txt domains.txt

# Query with specific record type
./bin/massdns -r resolvers.txt -t AAAA -o S domains.txt

# Use different output format (JSON)
./bin/massdns -r resolvers.txt -t A -o J domains.txt

# Use different output format (Simple)
./bin/massdns -r resolvers.txt -t A -o S domains.txt
```

### Generating Target Domain List

```bash
# Generate domains for brute force from wordlist
while read word; do
    echo "${word}.example.com"
done < wordlist.txt > domains_to_check.txt

# Alternative one-liner
sed 's/$/\.example\.com/' wordlist.txt > domains_to_check.txt
```

### Filtering MassDNS Results

```bash
# Extract valid domains (excluding NXDOMAIN)
cat massdns_output.txt | grep -v "NXDOMAIN" | awk '{print $1}' | sed 's/\.$//' > valid_domains.txt

# Extract only IPs
cat massdns_output.txt | grep -v "NXDOMAIN" | awk '{print $5}' > resolved_ips.txt

# Extract domain to IP mapping
cat massdns_output.txt | grep -v "NXDOMAIN" | awk '{print $1 " " $5}' | sed 's/\.$//' > domain_ip_mapping.txt
```

## Certificate Transparency Logs

Certificate Transparency (CT) logs are publicly available records of SSL/TLS certificates. They're an excellent source for passive subdomain discovery.

### CT Log Sources

1. **crt.sh**: A certificate search tool that queries CT logs
2. **Censys**: Search engine for internet-connected devices
3. **Certificate Search**: Google's CT log search

### crt.sh Command Line Usage

```bash
# Basic crt.sh query (view in browser)
# Visit: https://crt.sh/?q=%25.example.com

# Command line query using curl and jq
curl -s "https://crt.sh/?q=%25.example.com&output=json" | jq -r '.[].name_value' | sort -u

# Save results to a file
curl -s "https://crt.sh/?q=%25.example.com&output=json" | jq -r '.[].name_value' | sort -u > crtsh_results.txt

# Clean up results (remove wildcards, etc.)
cat crtsh_results.txt | sed 's/\*\.//g' | sort -u > clean_subdomains.txt
```

### Certspotter Command Line Usage

```bash
# Basic query with curl
curl -s "https://api.certspotter.com/v1/issuances?domain=example.com&include_subdomains=true&expand=dns_names" | jq -r '.[].dns_names[]' | sort -u

# With free API key for increased rate limits
curl -s "https://api.certspotter.com/v1/issuances?domain=example.com&include_subdomains=true&expand=dns_names" -H "Authorization: Bearer $API_KEY" | jq -r '.[].dns_names[]' | sort -u
```

## Additional Subdomain Enumeration Tools

### Amass Commands

```bash
# Basic passive enumeration
amass enum -passive -d example.com

# Save results to a file
amass enum -passive -d example.com -o amass_results.txt

# Use all sources (requires API keys in config)
amass enum -d example.com -o amass_results.txt

# Active enumeration with brute forcing
amass enum -brute -d example.com -w /path/to/wordlist.txt -o amass_results.txt

# More aggressive scan
amass enum -active -brute -d example.com -min-for-recursive 3 -o amass_results.txt
```

### Subfinder Commands

```bash
# Basic usage
subfinder -d example.com

# Silent mode with output to file
subfinder -d example.com -silent -o subfinder_results.txt

# Use all sources
subfinder -d example.com -all 

# Multiple domains
subfinder -d example.com,another.com -o results.txt

# Using specific resolvers
subfinder -d example.com -r 1.1.1.1,8.8.8.8 -o results.txt
```

### DNSx Commands

```bash
# Basic usage (resolve domains)
cat subdomains.txt | dnsx

# Show only resolved domains
cat subdomains.txt | dnsx -silent -a -resp

# Custom resolvers
cat subdomains.txt | dnsx -silent -r resolvers.txt

# Filter by CNAME records (potential subdomain takeover)
cat subdomains.txt | dnsx -silent -cname -resp

# Output JSON format
cat subdomains.txt | dnsx -silent -json -o dnsx_results.json
```

### HTTPx Commands

```bash
# Basic usage
cat subdomains.txt | httpx

# Silent mode with status codes
cat subdomains.txt | httpx -silent -status-code

# Get titles, status codes, and content length
cat subdomains.txt | httpx -title -status-code -content-length

# Check multiple ports
cat subdomains.txt | httpx -ports 80,443,8080,8443

# Filter by status codes
cat subdomains.txt | httpx -mc 200,301,302

# Screenshot websites (requires Chrome/Chromium)
cat subdomains.txt | httpx -screenshot
```

## OSINT for Subdomain Discovery

### Search Engine Techniques

Use search engines with specific operators to find subdomains:

- Google: `site:*.example.com -www`
- DuckDuckGo: `site:example.com -site:www.example.com`
- Bing: `domain:example.com -site:www.example.com`

### theHarvester Commands

```bash
# Install
git clone https://github.com/laramies/theHarvester.git
cd theHarvester
pip3 install -r requirements.txt

# Basic usage
python3 theHarvester.py -d example.com -b google,bing

# Use all data sources
python3 theHarvester.py -d example.com -b all

# Save results to HTML and XML
python3 theHarvester.py -d example.com -b all -f harvester_output
```

### OWASP Maryam Commands

```bash
# Install
git clone https://github.com/saeeddhqan/Maryam.git
cd Maryam
pip3 install -r requirements

# Basic usage
python3 maryam.py -e search --engine google -q "site:*.example.com"

# Use multiple search engines
python3 maryam.py -e search --engine=bing,google,yahoo -q "site:*.example.com"

# DNS search
python3 maryam.py -e dns_search -d example.com
```

### GitHub Reconnaissance Commands

```bash
# Install GitHub Search tool
pip install github-search

# Search for domain in repositories
github-search "example.com"

# Search for domain in specific file types
github-search "example.com filename:conf"
github-search "example.com filename:config"
github-search "example.com filename:.env"
```

## Wordlist Resources

### Common Wordlist Collections

1. **SecLists**: https://github.com/danielmiessler/SecLists
   - `/Discovery/DNS/` directory contains multiple wordlists

2. **Jhaddix All.txt**: https://gist.github.com/jhaddix/86a06c5dc309d08580a018c66354a056
   - Comprehensive subdomain wordlist

3. **Assetnote**: https://wordlists.assetnote.io/
   - Data-driven subdomain wordlists

### Downloading and Managing Wordlists

```bash
# Clone SecLists repository
git clone https://github.com/danielmiessler/SecLists.git

# Download specific wordlist
curl -o subdomains.txt https://raw.githubusercontent.com/danielmiessler/SecLists/master/Discovery/DNS/subdomains-top1million-110000.txt

# Download Jhaddix's all.txt wordlist
curl -o all.txt https://gist.githubusercontent.com/jhaddix/86a06c5dc309d08580a018c66354a056/raw/96f4e51d96b2203f19f6381c8c545b278eaa0837/all.txt

# Combine wordlists
cat wordlist1.txt wordlist2.txt | sort -u > combined_wordlist.txt

# Extract subdomains from a specific domain to create custom wordlist
cat subdomains.txt | grep -o '^[^.]*' | sort -u > prefixes.txt
```

### Wordlist Manipulation Commands

```bash
# Remove duplicates
sort -u wordlist.txt -o clean_wordlist.txt

# Extract common words for prefix list
cat existing_subdomains.txt | awk -F'.' '{print $1}' | sort -u > prefixes.txt

# Extract common words for suffix list
cat existing_subdomains.txt | awk -F'.' '{i=NF-2; print $i}' | sort -u > suffixes.txt

# Filter by length (only words longer than 3 characters)
cat wordlist.txt | awk 'length($0) > 3' > filtered_wordlist.txt

# Limit wordlist size (take top 10000 words)
head -n 10000 large_wordlist.txt > top_10000.txt
```

## Advanced Techniques

### DNS Zone Transfers

```bash
# Basic zone transfer attempt
dig @ns1.example.com example.com AXFR

# Alternative using host command
host -t axfr example.com ns1.example.com

# Check all nameservers
for ns in $(dig +short NS example.com); do
    echo "Trying $ns..."
    dig @$ns example.com AXFR
done
```

### Subdomain Takeover Testing Commands

```bash
# Install SubOver
go install github.com/Ice3man543/SubOver@latest

# Basic usage
SubOver -l subdomains.txt -v

# Using nuclei
nuclei -l subdomains.txt -t nuclei-templates/vulnerabilities/subdomain-takeover/

# Check CNAME records for potential takeovers
cat subdomains.txt | dnsx -silent -cname -resp | grep -v "example.com" > potential_takeovers.txt
```

### AltDNS Permutation Commands

```bash
# Install
pip install py-altdns

# Generate permutations
altdns -i subdomains.txt -o permutation_output.txt -w words.txt

# Generate and resolve in one step
altdns -i subdomains.txt -o resolved_output.txt -r -w words.txt

# Save to two separate files
altdns -i subdomains.txt -o permutation_output.txt -r -w words.txt -s resolved_output.txt
```

### Virtual Host Discovery Commands

```bash
# Using VHostScan
git clone https://github.com/codingo/VHostScan.git
cd VHostScan
pip install -r requirements.txt

# Basic usage
python3 VHostScan.py -t example.com -w wordlist.txt

# Using SSL
python3 VHostScan.py -t example.com -w wordlist.txt --ssl

# Custom User-Agent
python3 VHostScan.py -t example.com -w wordlist.txt --user-agent "Mozilla/5.0"
```

## Automating the Workflow

### Chaining Commands with Pipes

```bash
# Full pipeline example
subfinder -d example.com -silent | dnsx -silent -a | httpx -silent -title -status-code

# Discovery, resolution, and service identification
amass enum -passive -d example.com | tee subdomains.txt | dnsx -silent -a | tee resolved.txt | httpx -silent -status-code -title | tee http_services.txt

# Combine multiple tools
subfinder -d example.com -silent > subs.txt
amass enum -passive -d example.com >> subs.txt
cat subs.txt | sort -u | dnsx -silent -a -resp > resolved.txt
```

### Using 'tee' for Output Capture During Processing

```bash
# Save output at each step
subfinder -d example.com -silent | tee subfinder_results.txt | dnsx -silent -a | tee resolved.txt | httpx -silent -status-code | tee http_results.txt

# Append to existing files
amass enum -passive -d example.com | tee -a all_subdomains.txt
```

### Using 'xargs' for Parallel Processing

```bash
# Run dig in parallel for faster resolution
cat subdomains.txt | xargs -P 50 -I{} dig +short {} | grep -v "^$" > ips.txt

# Run httpx on multiple domains in parallel
cat domains.txt | xargs -P 10 -I{} httpx -silent -status-code -title -url {} > http_results.txt
```

## Best Practices

### Optimizing Subdomain Discovery

1. **Use multiple techniques**: Combine passive and active methods
2. **Validate findings**: Verify subdomain existence through resolution
3. **Focus on precision**: Use reliable DNS resolvers
4. **Be respectful**: Avoid excessive requests to target servers
5. **Document your process**: Keep track of your findings and methodologies
6. **Regular updates**: Update your tools and wordlists

### Subdomain Discovery Checklist

- [ ] Passive reconnaissance completed (CT logs, search engines)
- [ ] Active brute force with quality wordlists
- [ ] DNS resolution to validate findings
- [ ] HTTP/HTTPS services identified
- [ ] Virtual host discovery completed
- [ ] Results compiled and deduplicated
- [ ] Findings categorized by importance

## References

1. OWASP Amass: https://github.com/owasp-amass/amass
2. ProjectDiscovery Tools: https://github.com/projectdiscovery
3. MassDNS: https://github.com/blechschmidt/massdns
4. SecLists: https://github.com/danielmiessler/SecLists
5. Certificate Transparency: https://certificate.transparency.dev/
6. DNS Dumpster: https://dnsdumpster.com/
7. Shodan: https://www.shodan.io/
8. Censys: https://censys.io/
9. crt.sh: https://crt.sh/
10. SecurityTrails: https://securitytrails.com/

## Downloadable Scripts

The following scripts are available for download to supplement the commands in this wiki:

1. **subdomain_discovery.sh** - Main script for running comprehensive subdomain discovery
2. **cert_transparency.py** - Python script for querying certificate transparency logs
3. **module_runner.py** - Helper script for running Python modules
4. **custom_wordlist_generator.py** - Script for generating permutation-based wordlists
5. **recon_automation.sh** - Complete workflow automation for subdomain reconnaissance

Download these scripts from: [https://git.bigacloud.com/tbn/professionnal-showcase/-/tree/main/projects/DueDil_Redteam](https://git.bigacloud.com/tbn/professionnal-showcase/-/tree/main/projects/DueDil_Redteam)

### Using the Scripts

```bash
# Run the main subdomain discovery script
./subdomain_discovery.sh example.com

# Query certificate transparency logs
python3 cert_transparency.py example.com

# Generate custom wordlist
python3 custom_wordlist_generator.py example dev_prefixes.txt dev_suffixes.txt custom_wordlist.txt
```

> Note: These scripts are provided as-is and should be reviewed before execution in your environment. Always ensure you have proper authorization before performing reconnaissance activities.
