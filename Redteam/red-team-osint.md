# OSINT Tools Installation and Usage Guide

This guide provides detailed instructions for installing, configuring, and using five essential OSINT (Open Source Intelligence) tools for cybersecurity researchers, digital investigators, and security professionals.

## Table of Contents

- [OSINT Framework](#osint-framework)
- [Maltego](#maltego)
- [Shodan](#shodan)
- [TheHarvester](#theharvester)
- [Mitaka Browser Extension](#mitaka-browser-extension)
- [OSINT Investigation Best Practices](#osint-investigation-best-practices)

## OSINT Framework

### Overview
OSINT Framework is a web-based collection of OSINT resources, organized by category. It provides links to hundreds of tools for various investigation needs.

### Installation
OSINT Framework doesn't require installation as it's a web-based resource.

### Configuration & Usage
1. Visit [https://osintframework.com/](https://osintframework.com/)
2. Navigate the interactive mind map by clicking on categories and subcategories
3. Each node in the framework represents either a subcategory or a direct link to a tool
4. Click on tool links (indicated by a globe icon) to access the respective resource
5. Use the search function at the top to find specific tools or categories

### Tips
- Bookmark the OSINT Framework for quick access during investigations
- Explore all categories to understand the breadth of available resources
- The framework is regularly updated, so check back for new tools
- Consider contributing to the framework via its GitHub repository if you discover useful tools not listed

## Maltego

### Overview
Maltego is a data mining and visualization tool that maps relationships between information entities such as people, companies, domains, and more.

### Installation
1. Visit [https://www.maltego.com/downloads/](https://www.maltego.com/downloads/)
2. Create a Maltego account if you don't have one
3. Download the appropriate version for your operating system:
   - Windows (64-bit)
   - macOS
   - Linux
4. Run the installer and follow the prompts
5. Launch Maltego after installation

### Configuration
1. Upon first launch, you'll be guided through the setup wizard
2. Log in with your Maltego account credentials
3. Select your edition (Community, Classic, XL, etc.)
4. Configure the transforms (data sources) you want to use:
   - Community transforms (free)
   - Transform Hub options (some require payment)
   - Custom transforms (if available)
5. Configure API keys for transforms that require them under "Manage" → "Manage Transforms"

### Usage
1. Create a new graph through File → New
2. Start by adding an entity from the palette on the left (e.g., Person, Domain, IP Address)
3. Right-click on an entity and select "Run Transform" to discover related information
4. Common transforms include:
   - For domains: DNS information, WHOIS data, linked IP addresses
   - For people: Social media profiles, email addresses, company associations
5. As transforms run, new entities will appear, creating a visualization of connections
6. Organize your graph using the layout tools in the top toolbar
7. Save your investigation as a Maltego graph file (.mtgx)

### Advanced Features
1. Use the "Machine" feature to run pre-configured sequences of transforms
2. Create custom entity types for specialized investigations
3. Export results in various formats (CSV, JSON, etc.)
4. Use bookmarks to organize complex investigations
5. Create custom palettes for frequently used entities

### Tips
- Start with the free Community transforms before investing in commercial options
- Take advantage of the "Detail View" to see all properties of selected entities
- Use the filtering options to manage large graphs
- Group related entities to keep your visualizations clean

## Shodan

### Overview
Shodan is a search engine for Internet-connected devices, from servers and routers to IoT devices and industrial control systems.

### Installation Options

#### Web Interface
1. Visit [https://www.shodan.io/](https://www.shodan.io/)
2. Create an account (free or paid)
3. No installation required

#### Command Line Interface
1. Ensure Python and pip are installed on your system
2. Install the Shodan CLI:
   ```bash
   pip install shodan
   ```
3. Initialize with your API key:
   ```bash
   shodan init YOUR_API_KEY
   ```

#### Browser Extension
1. Visit your browser's extension store:
   - [Chrome Extension](https://chrome.google.com/webstore/detail/shodan/jjalcfnidlmpjhdfepjhjbhnhkbgleap)
   - [Firefox Add-on](https://addons.mozilla.org/en-US/firefox/addon/shodan-addon/)
2. Add the extension to your browser
3. Configure with your Shodan API key in the extension settings

### Configuration
1. Obtain your API key from your Shodan account dashboard
2. For CLI: Configure using `shodan init YOUR_API_KEY`
3. For browser extension: Enter your API key in the extension settings
4. For API usage: Store your API key securely for use in scripts or applications

### Usage

#### Web Interface
1. Basic search: Enter keywords or filters in the search bar
   - Examples: `apache`, `webcam`, `country:US port:22`
2. Filter results using the left sidebar
3. Click on a result to view detailed information about the device
4. Use "Explore" to browse popular searches and tags
5. Check "Maps" to visualize search results geographically

#### Command Line Interface
```bash
# Basic search
shodan search apache

# Get information about a specific IP
shodan host 8.8.8.8

# Download search results
shodan download results apache

# Parse downloaded results
shodan parse results.json.gz

# Get your account information
shodan info
```

#### Advanced Search Queries
- Network: `net:192.168.0.0/16`
- Organization: `org:"Microsoft"`
- Country: `country:DE`
- Operating System: `os:"Windows Server 2012"`
- Port: `port:3389`
- Product: `product:nginx`
- Vulnerability: `vuln:ms17-010`

### Tips
- Use specific filters to narrow down results and avoid overwhelming data
- Create a paid account for more comprehensive access and higher query limits
- Save important searches for later reference
- Combine multiple filters for precise targeting
- Use the "Alert" feature to monitor for new devices matching your criteria

## TheHarvester

### Overview
TheHarvester is a tool designed to gather emails, subdomains, hosts, employee names, open ports, and banners from different public sources.

### Installation

#### On Kali Linux
TheHarvester comes pre-installed on Kali Linux. To update:
```bash
sudo apt update
sudo apt install theharvester
```

#### Manual Installation
```bash
# Clone the repository
git clone https://github.com/laramies/theHarvester.git

# Navigate to the directory
cd theHarvester

# Install requirements
pip3 install -r requirements.txt
```

### Configuration
1. Create an API keys file:
   ```bash
   cp api-keys.yaml.sample api-keys.yaml
   ```

2. Edit the file to add your API keys:
   ```bash
   nano api-keys.yaml
   ```

3. Add keys for services you use (optional but recommended):
   - Shodan
   - Hunter.io
   - BinaryEdge
   - Censys
   - SecurityTrails
   - Others as available

### Usage

#### Basic Syntax
```bash
theHarvester -d [domain] -b [sources] -l [limit]
```

#### Common Parameters
- `-d`: Target domain to investigate
- `-b`: Data sources to use (comma-separated)
- `-l`: Limit the number of results
- `-f`: Output file name to save results
- `-S`: Start result number (for pagination)
- `-p`: Use proxy server
- `-n`: Perform DNS reverse lookup

#### Examples
```bash
# Basic search against a domain using Google and LinkedIn
theHarvester -d company.com -b google,linkedin

# Search using all available sources and save results
theHarvester -d company.com -b all -f output_file

# Search with limited results
theHarvester -d company.com -b bing -l 200

# DNS brute force to find subdomains
theHarvester -d company.com -b dnsdumpster,virustotal -c
```

#### Available Sources
- search engines: google, googleCSE, bing, bingapi, yandex
- social media: linkedin, twitter, instagram
- specialized: shodan, censys, virustotal, dnsdumpster
- Use `-b all` to use all available sources

### Output Options
1. Console output (default)
2. HTML report (`-f output_file -o output.html`)
3. XML report (`-f output_file -o output.xml`)
4. JSON format (`-f output_file -o output.json`)

### Tips
- Combine data from multiple sources for the most comprehensive results
- Use targeted searches before trying "all" sources to avoid rate limiting
- Rotate IP addresses or use proxies for large-scale reconnaissance
- Verify findings with other tools to reduce false positives
- Be mindful of API rate limits with services like Shodan or Censys

## Mitaka Browser Extension

### Overview
Mitaka is a browser extension that provides quick lookups for various indicators of compromise (IoCs) across multiple search engines and OSINT services.

### Installation

#### Chrome/Chromium-based Browsers
1. Visit the [Chrome Web Store](https://chrome.google.com/webstore/detail/mitaka/bfjbejmeoibbdpfdbmbacmefcbannnbg)
2. Click "Add to Chrome"
3. Confirm the installation when prompted

#### Firefox
1. Visit the [Firefox Add-ons page](https://addons.mozilla.org/en-US/firefox/addon/mitaka/)
2. Click "Add to Firefox"
3. Confirm the installation when prompted

### Configuration
1. Click on the Mitaka icon in your browser toolbar
2. Select "Options" or "Preferences" (varies by browser)
3. Configure which search engines to use for each indicator type:
   - IP address
   - Domain
   - URL
   - Hash (MD5, SHA1, SHA256)
   - Email
   - Cryptocurrency address
4. Set default search engines for each indicator type
5. Enable or disable specific search engines based on your needs

### Usage
1. Select text on any webpage containing an indicator (IP, domain, hash, etc.)
2. Right-click and navigate to the Mitaka submenu
3. Choose a search engine to query the selected indicator
   - Example: Select an IP address → Right-click → Mitaka → IP → VirusTotal
4. Alternatively, click the Mitaka icon in your browser toolbar
5. Enter an indicator in the search field
6. Select the appropriate search engine from the dropdown

### Supported Indicator Types
- IP addresses
- Domains
- URLs
- Email addresses
- Cryptocurrency addresses (Bitcoin, Ethereum, etc.)
- File hashes (MD5, SHA1, SHA256)
- ASN
- CVE IDs

### Supported Search Engines/Services
- VirusTotal
- AlienVault OTX
- Censys
- Shodan
- DomainTools
- SecurityTrails
- RiskIQ
- IBM X-Force Exchange
- And many more

### Tips
- Learn the keyboard shortcuts for faster lookups
- Use "Search on all" sparingly as it opens multiple tabs
- Customize the right-click menu to include only services you frequently use
- Pin the extension for quick access during investigations
- Use the "Options" page to organize services in order of preference

## OSINT Investigation Best Practices

### Planning and Preparation
1. Define clear objectives for your investigation
2. Create a structured methodology before starting
3. Prepare a secure environment:
   - Use a dedicated browser profile or VM
   - Consider using a VPN or proxy service
   - Keep tools updated to the latest versions

### Data Collection and Management
1. Document all findings methodically
2. Maintain a clear chain of evidence
3. Record search parameters used for reproducibility
4. Save raw data before processing or analysis
5. Use multiple tools to verify information
6. Create a consistent file naming convention

### Operational Security
1. Be aware that some OSINT activities may be traceable
2. Use anonymization techniques when appropriate
3. Understand legal boundaries of information gathering
4. Avoid actions that could be interpreted as hacking or intrusion
5. Consider the ethical implications of your investigation

### Tool Integration and Workflow
1. Develop a systematic approach to move between tools
2. Export data in compatible formats when possible
3. Use findings from one tool to inform searches in others
4. Consider automation for repetitive tasks
5. Create templates for common investigation types

### Analysis and Reporting
1. Distinguish between facts, assumptions, and inferences
2. Identify information gaps and limitations
3. Create visual representations of complex data relationships
4. Structure reports based on audience needs:
   - Executive summaries for management
   - Technical details for security teams
   - Legal-focused reporting for compliance

### Continuous Improvement
1. Review and update your toolkit regularly
2. Stay informed about new OSINT techniques and sources
3. Participate in OSINT communities to share knowledge
4. Practice with capture-the-flag (CTF) exercises
5. Document lessons learned after each investigation

---

## Resources and Further Learning

### Communities and Forums
- [OSINT Framework](https://osintframework.com/)
- [IntelTechniques Forum](https://inteltechniques.com/forum/)
- [r/OSINT on Reddit](https://www.reddit.com/r/OSINT/)

### Training Resources
- [SANS FOR578: Cyber Threat Intelligence](https://www.sans.org/cyber-security-courses/cyber-threat-intelligence/)
- [Open Source Intelligence Techniques](https://inteltechniques.com/book1.html) by Michael Bazzell
- [OSINT Handbook](https://i-intelligence.eu/uploads/public-documents/OSINT_Handbook_2020.pdf)

### Additional Tools to Explore
- [Recon-ng](https://github.com/lanmaster53/recon-ng) - Web Reconnaissance framework
- [SpiderFoot](https://www.spiderfoot.net/) - Automated OSINT platform
- [Amass](https://github.com/OWASP/Amass) - In-depth DNS enumeration
- [FOCA](https://github.com/ElevenPaths/FOCA) - Metadata analysis tool
- [Metagoofil](https://github.com/laramies/metagoofil) - Document metadata extraction

---

**Disclaimer**: Always use these tools ethically and legally. Different jurisdictions have different laws regarding information gathering, privacy, and computer access. Ensure your OSINT activities comply with applicable laws and regulations.
