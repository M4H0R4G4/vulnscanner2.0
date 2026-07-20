# 🔍 VulnScanner

> **Vulnerability scanner** that identifies open ports, service versions, CPEs, NSE script output, and known CVEs — with a built-in visual web interface and professional HTML/PDF reports.

![Python](https://img.shields.io/badge/Python-3.10+-blue?style=flat-square&logo=python)
![Nmap](https://img.shields.io/badge/Nmap-required-00a6d6?style=flat-square)
![License](https://img.shields.io/badge/License-MIT-green?style=flat-square)
![NVD](https://img.shields.io/badge/Data-NVD%20API-orange?style=flat-square)

---

## ✨ Features

- **Port scanning** via Nmap with service/version detection (`-sV`)
- **Built-in visual interface** running locally in the browser
- **Nmap scan profiles**: `default`, `detailed`, and `vuln`
- **NSE script output** for richer service and vulnerability details
- **Host details** including state, hostname, MAC/vendor, OS guesses, and CPEs
- **CVE lookup** via the [NVD API 2.0](https://nvd.nist.gov/developers/vulnerabilities)
- **Parallel CVE queries** using `ThreadPoolExecutor`
- **HTML report** — self-contained, dark-themed, with severity details
- **PDF report** — generated with ReportLab
- **Risk scoring** — automatic `CRITICAL` / `HIGH` / `MEDIUM` / `LOW` classification
- **CLI interface** with authorization confirmation prompt
- **Python package support** with `pyproject.toml`, `setup.py`, and `vulnscan` console command

---

## 🖥️ Web Interface

VulnScanner includes a local web UI for running scans without leaving the browser.

![VulnScanner web interface](assets/web-ui.png)

The interface lets you configure:

- target host, IP, or URL
- port range
- timeout
- scan profile
- extra Nmap arguments
- optional NVD API key
- CVE lookup toggle

---

## 🚀 Quick Start

### Requirements

- Python 3.10+
- [Nmap](https://nmap.org/download.html) installed on your system

Install Nmap on Debian/Ubuntu:

```bash
sudo apt update
sudo apt install nmap
```

Install Nmap on macOS:

```bash
brew install nmap
```

Install Nmap on Windows with Winget:

```powershell
winget install Insecure.Nmap
```

Or download the Windows installer from:

https://nmap.org/download.html

After installing, open a new terminal and confirm:

```bash
nmap --version
```

> On Windows, if Nmap is installed but not available in `PATH`, VulnScanner also checks the default installation paths automatically:
>
> `C:\Program Files\Nmap\nmap.exe`
>
> `C:\Program Files (x86)\Nmap\nmap.exe`

### Install

```bash
git clone https://github.com/M4H0R4G4/vulnscanner.git
cd vulnscanner
python -m pip install -r requirements.txt
```

Optional virtual environment:

```bash
python -m venv .venv
```

Activate on Windows PowerShell:

```powershell
.\.venv\Scripts\Activate.ps1
```

Activate on Linux/macOS:

```bash
source .venv/bin/activate
```

Optional editable install:

```bash
python -m pip install -e .
```

---

## 🌐 Run the Web UI

Start the local visual interface:

```bash
python main.py --web
```

Open in your browser:

```text
http://127.0.0.1:8080
```

If port `8080` is already in use:

```bash
python main.py --web --port 8084
```

Then open:

```text
http://127.0.0.1:8084
```

---

## ⚡ Run from CLI

```bash
# Basic scan (ports 1-1024)
python main.py scanme.nmap.org

# Custom port range
python main.py 192.168.1.1 --ports 1-65535

# Scan specific ports
python main.py scanme.nmap.org --ports 22,80,443

# Detailed scan with default NSE scripts and OS detection
python main.py scanme.nmap.org --profile detailed

# Vulnerability-focused scan with NSE vuln scripts
python main.py scanme.nmap.org --profile vuln

# Add extra Nmap arguments
python main.py scanme.nmap.org --nmap-args "--top-ports 100"

# PDF only, no CVE lookup (faster)
python main.py 10.0.0.5 --format pdf --no-cve

# HTML report with NVD API key (higher rate limits)
python main.py target.com --api-key YOUR_KEY --format html

# If installed with pip install -e .
vulnscan scanme.nmap.org --no-cve
```

---

## 📋 CLI Options

| Option | Short | Default | Description |
|--------|-------|---------|-------------|
| `target` | — | required | Target IP address, hostname, or URL |
| `--ports` | `-p` | `1-1024` | Port range to scan |
| `--format` | `-f` | `both` | `html`, `pdf`, or `both` |
| `--output` | `-o` | auto | Custom output file path |
| `--no-cve` | — | false | Skip NVD CVE lookup |
| `--api-key` | `-k` | env | NVD API key |
| `--timeout` | `-t` | `60` | Nmap scan timeout in seconds |
| `--profile` | — | `default` | Scan profile: `default`, `detailed`, or `vuln` |
| `--nmap-args` | — | empty | Extra Nmap arguments appended to the selected profile |
| `--web` | — | false | Start the built-in local web interface |
| `--host` | — | `127.0.0.1` | Web interface bind host |
| `--port` | — | `8080` | Web interface port |
| `--verbose` | `-v` | false | Enable verbose logging |

**Tip:** Set `NVD_API_KEY` as an environment variable to avoid passing it every time:

```bash
export NVD_API_KEY="your-key-here"
```

On Windows PowerShell:

```powershell
$env:NVD_API_KEY="your-key-here"
```

Get a free key at:

https://nvd.nist.gov/developers/request-an-api-key

---

## 🧭 Scan Profiles

| Profile | Nmap arguments | Best for |
|---------|----------------|----------|
| `default` | `-sV --version-intensity 5 -T4` | Fast service/version detection |
| `detailed` | `-sV -sC -O --version-all -T4` | Richer enumeration, default NSE scripts, OS detection |
| `vuln` | `-sV -sC --script vuln --version-all -T4` | Vulnerability-oriented NSE checks |

Some Nmap features, especially OS detection (`-O`) and certain NSE scripts, may require administrator/root privileges.

---

## 🧪 Running Tests

Install development dependencies:

```bash
python -m pip install -r requirements-dev.txt
```

Run the test suite:

```bash
python -m pytest
```

Expected result:

```text
23 passed
```

---

## 🗂️ Project Structure

```text
vulnscanner/
├── assets/
│   └── web-ui.png              # Web interface screenshot
├── main.py                     # CLI and web entrypoint
├── setup.py                    # Package installation config
├── pyproject.toml              # Modern build config
├── requirements.txt            # Runtime dependencies
├── requirements-dev.txt        # Test/development dependencies
├── README.md                   # Documentation
├── LICENSE                     # MIT license
├── .gitignore                  # Ignores cache, reports, env files
├── scanner/
│   ├── __init__.py
│   ├── core.py                 # VulnScanner engine + NVD client
│   ├── report_html.py          # HTML report generator
│   ├── report_pdf.py           # PDF report generator
│   └── webapp.py               # Built-in local web interface
├── reports/                    # Generated reports (gitignored)
└── tests/
    ├── __init__.py
    └── test_core.py            # Unit tests
```

---

## 📊 Sample Report

After scanning, reports are saved to the `reports/` folder:

```text
reports/
├── scanme_nmap_org_20260720_143201.html
└── scanme_nmap_org_20260720_143201.pdf
```

You can also choose a custom output path:

```bash
python main.py scanme.nmap.org --format html --output reports/scanme.html
```

---

## 🎯 Safe Targets for Testing

Use only legal, authorized targets:

- `scanme.nmap.org`
- OWASP Juice Shop
- OWASP WebGoat
- PortSwigger Web Security Academy
- Hack The Box labs
- TryHackMe rooms
- Your own local machines and lab environments

---

## 🛠️ Troubleshooting

### Nmap executable was not found

Install Nmap and confirm:

```bash
nmap --version
```

On Windows, restart your terminal after installing Nmap. If it is still not in `PATH`, VulnScanner will try the default installation paths automatically.

### Cannot resolve host

Check the target value. These formats are valid:

```text
example.com
https://example.com
192.168.1.1
```

### Detailed or vuln scans are slow

This is expected. The `detailed` and `vuln` profiles run more Nmap checks. For faster scans, use the `default` profile and limit the port range.

---

## ⚠️ Legal Disclaimer

**VulnScanner is for authorized security testing only.**

Only use this tool on systems you own or have explicit written permission to test. Unauthorized scanning may be illegal. The tool includes an authorization confirmation prompt before CLI scans and an authorization checkbox in the web interface.

---

## 🔧 Extending

### Add a new report format

Create `scanner/report_json.py` and implement:

```python
def generate_json(result: ScanResult, output_path: str) -> str:
    ...
```

### Use as a library

```python
from scanner.core import VulnScanner
from scanner.report_html import generate_html

scanner = VulnScanner(api_key="optional")
result = scanner.scan("192.168.1.1", ports="22,80,443")
generate_html(result, "report.html")
```

---

## 📚 References

- [NVD API Documentation](https://nvd.nist.gov/developers/vulnerabilities)
- [MITRE CVE](https://cve.mitre.org/)
- [Nmap Reference Guide](https://nmap.org/book/man.html)
- [Nmap NSE Documentation](https://nmap.org/book/nse.html)
- [python-nmap](https://xael.org/pages/python-nmap-en.html)
- [OWASP Juice Shop](https://owasp.org/www-project-juice-shop/)
- [OWASP WebGoat](https://owasp.org/www-project-webgoat/)

---

## 📄 License

MIT — see [LICENSE](LICENSE) for details.
