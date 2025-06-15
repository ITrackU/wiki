# WPScan Documentation

## Overview

WPScan is a black box WordPress security scanner that can be used to scan remote WordPress installations to find security issues. This document provides instructions for installing, configuring, and running WPScan with specific scan profiles.

## Table of Contents

- [Installation](#installation)
- [Configuration](#configuration)
- [Usage](#usage)
  - [Basic Usage](#basic-usage)
  - [API Token](#api-token)
  - [Scan Profiles](#scan-profiles)
- [Execution Script](#execution-script)
- [Scan Options Explained](#scan-options-explained)
- [Output Files](#output-files)
- [Troubleshooting](#troubleshooting)
- [Additional Resources](#additional-resources)

## Installation

### Prerequisites

- Ruby installed (2.5 or higher recommended)
- RubyGems
- Git (for installation from source)
- Dependencies: libcurl4-openssl-dev, libxml2-dev, libxslt1-dev, ruby-dev, build-essential, libffi-dev

### Installation Methods

#### Using RubyGems (Recommended)

```bash
gem install wpscan
```

#### Using Docker

```bash
docker pull wpscanteam/wpscan
```

#### From Source

```bash
git clone https://github.com/wpscanteam/wpscan.git
cd wpscan
bundle install
```

### Verify Installation

```bash
wpscan --version
```

## Configuration

### API Token Registration

1. Go to [WPScan.com](https://wpscan.com) and create an account
2. Navigate to your profile to generate an API token
3. Use this token with the `--api-token` parameter for enhanced vulnerability detection

## Usage

### Basic Usage

```bash
wpscan --url https://example.com
```

### API Token

Using an API token significantly improves the quality of scan results by allowing access to the WPScan vulnerability database:

```bash
wpscan --url https://example.com --api-token YOUR_API_TOKEN
```

### Scan Profiles

WPScan supports different enumeration options:

- `vp`: Vulnerable plugins
- `vt`: Vulnerable themes
- `u`: User enumeration
- `ap`: All plugins
- `at`: All themes

## Execution Script

Below is a sample script for running multiple WPScan profiles against a target domain:

```bash
#!/bin/bash

domain="$1"
mkdir -p ./$domain
cd ./$domain

api_token="EqOaeF8IHoGTk8UyHC2nZc3apeB4qeI9MciZBTlVsU0"

# Scan for vulnerable plugins
wpscan --url https://$domain --api-token $api_token -e vp \
  --plugins-detection aggressive \
  --detection-mode aggressive \
  --interesting-findings-detection passive \
  --wp-version-detection passive \
  --main-theme-detection passive \
  --exclude-content-based 'xmlrpc.php,readme.html,wp-content/uploads,wp-cron.php' \
  -f json -o wpscan_${domain}_vp.json

# Scan for vulnerable themes
wpscan --url https://$domain --api-token $api_token -e vt \
  --plugins-detection aggressive \
  --interesting-findings-detection passive \
  --wp-version-detection passive \
  --main-theme-detection passive \
  --exclude-content-based 'xmlrpc.php,readme.html,wp-content/uploads,wp-cron.php' \
  -f json -o wpscan_${domain}_vt.json

# Scan for users
wpscan --url https://$domain --api-token $api_token -e u \
  --interesting-findings-detection passive \
  --wp-version-detection passive \
  --main-theme-detection passive \
  --exclude-content-based 'xmlrpc.php,readme.html,wp-content/uploads,wp-cron.php' \
  -f json -o wpscan_${domain}_u.json
```

**Usage:**
```bash
chmod +x scan_script.sh
./scan_script.sh example.com
```

## Scan Options Explained

| Option | Description |
|--------|-------------|
| `--url` | Target WordPress URL |
| `--api-token` | WPScan API Token for vulnerability data |
| `-e` | Enumeration option (vp, vt, u, etc.) |
| `--plugins-detection` | Detection mode for plugins (passive, aggressive, mixed) |
| `--detection-mode` | Overall detection mode |
| `--interesting-findings-detection` | Mode for detecting interesting findings |
| `--wp-version-detection` | WordPress version detection mode |
| `--main-theme-detection` | Main theme detection mode |
| `--exclude-content-based` | Exclude specific URLs from content-based checks |
| `-f` | Output format (json, cli, etc.) |
| `-o` | Output file |

### Detection Modes

- **Passive**: Fastest but potentially less accurate; relies on metadata
- **Aggressive**: Slower but more thorough; actively checks components
- **Mixed**: Balanced approach

## Output Files

The script creates three JSON output files:

1. `wpscan_domain_vp.json`: Vulnerable plugins scan results
2. `wpscan_domain_vt.json`: Vulnerable themes scan results
3. `wpscan_domain_u.json`: User enumeration scan results

## Troubleshooting

### Common Issues

1. **API Rate Limiting**
   - Free API tokens have limited daily requests
   - Error: "The request has been rate limited"
   - Solution: Wait 24 hours or upgrade to a paid plan

2. **Target Unreachable**
   - Error: "The URL supplied 'https://example.com' seems to be down"
   - Solution: Verify the URL is accessible and contains WordPress

3. **WAF/Firewall Blocking**
   - Error: "The target is responding with 403 forbidden responses"
   - Solution: Reduce scan aggressiveness or try `--random-user-agent`

### Increasing Scan Success

- Use `--disable-tls-checks` for self-signed certificates
- Add `--throttle` to slow down requests and avoid triggering security measures
- Try `--random-user-agent` to bypass simple user-agent filtering

## Additional Resources

- [Official WPScan Documentation](https://github.com/wpscanteam/wpscan/wiki)
- [WPScan API Documentation](https://wpscan.com/api)
- [WordPress Security Best Practices](https://wordpress.org/support/article/hardening-wordpress/)
