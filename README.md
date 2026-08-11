<h1 align="center">InCyter</h1>

<p align="center">
  <strong>A cyber threat investigation platform for Windows that replaces a dozen browser tabs, and keeps the analysis on your machine.</strong>
</p>

<p align="center">
  <img alt="version" src="https://img.shields.io/badge/version-0.0.5-1fa8a0">
  <img alt="platform" src="https://img.shields.io/badge/Windows-10%20%7C%2011-0078d4">
  <img alt="dependencies" src="https://img.shields.io/badge/dependencies-none-brightgreen">
  <img alt="telemetry" src="https://img.shields.io/badge/telemetry-none-555">
</p>

---

## What it is

Investigating a single alert usually means a VirusTotal tab, a WHOIS lookup, a
DMARC checker, a CVE database, a hash calculator and a base64 decoder, all open
at once, with results copied between them by hand.

InCyter puts those in one window. Paste an indicator into the search bar and it
works out whether it is an IP, a domain, a URL, a hash or a CVE, then takes you
to the right scanner with the value already filled in.

The part that matters most is where the work happens. **File analysis is static
and local.** A suspicious document is inspected on your machine: its macros are
extracted and explained, its PDF structure is read without rendering it, its
archive is listed without opening it. Nothing is uploaded unless you deliberately
use a service that requires it, and samples are never executed.

On top of the raw findings sits **InCyter Intelligence**, a rule-based engine
that runs offline and turns what was found into a plain-English verdict. It is
deterministic, not a language model guessing. When it tells you an SPF record
contains thirteen duplicate IP ranges, it computed that, and it can name every
one of them.

There is also **InSentri**, an optional AI assistant that runs on your own
hardware and can read whatever analysis page you have open. It is off by default
and downloads nothing until you turn it on.

---

## Download

Grab the latest **`InCyterInstaller.exe`** from the
[Releases page](https://github.com/FadyAzzi/InCyter/releases) or
[Direct Link](https://github.com/FadyAzzi/InCyter/releases/latest/download/InCyterInstaller.exe).

Run it and follow the wizard.

**Requirements:** Windows 10 or 11, 64-bit. Nothing else.

Nothing else to install. The installer carries everything InCyter needs.
Features that reach out to a threat-intelligence service need an internet
connection and your own API key; everything static works without either.

---

## Features

### Quick Scan

Paste anything into the bar at the top and press Enter. InCyter detects the type
and routes it: IPv4 and IPv6 addresses, domains, URLs, MD5 / SHA-1 / SHA-256 /
SHA-512 hashes, and CVE identifiers.

### Home

- A scrolling ribbon of the **CISA Known Exploited Vulnerabilities** catalog, the
  authoritative list of flaws being exploited right now. Every entry is clickable
  and lands in the CVE lookup.
- A **threat feed** aggregated from CISA, the Australian Cyber Security Centre,
  BleepingComputer, The Hacker News and Cyber Security News, sorted newest first
  across all sources.
- Toggle individual sources on and off; your choice is remembered. Stories you
  have already opened are dimmed so unread items stand out.

### IOC scanners

- **File & Hash** - drop a file or paste a hash, checked against VirusTotal and
  AlienVault OTX
- **URL & Domain** - reputation, redirect-chain tracing, WHOIS, DNS and a trust
  score
- **IP & ASN** - reputation, geolocation, open ports and abuse history
- **CVE & Software** - NVD lookup by CVE ID or by keyword, plus the live KEV
  catalog

### Malware analysis

Static, local, and nothing is ever executed.

**PE / MSI Analyser**
Imports, sections, entropy and packing indicators, plus a signing-certificate
deep dive that walks the full chain from leaf to root. It warns only when
something is genuinely wrong: a self-signed certificate, an untrusted chain, or
one on the known-abused list, which is seeded with the NVIDIA code-signing
certificates leaked in the 2022 LAPSUS$ breach. Thumbprints are shown and
copyable as hunting IOCs, and no network calls are made.

**ZIP Analyser**
Every entry with type, size, compression ratio and risk flags. Detects zip bombs,
path traversal, double extensions, encrypted entries, nested archives, polyglot
files and file-count bombs. Pick a file inside the archive and InCyter extracts it
and opens it in the right analyser automatically.

**Office & PDF Analyser**
Extracts VBA macros in full and explains in plain English what they do, flagging
auto-execution triggers and dangerous calls. For PDFs it reads the structure
without rendering: embedded JavaScript, auto-actions, external URIs and embedded
files.

**Extension Analyser**
Downloads and inspects browser extensions, with no external service involved.

**Encrypted File Analyser**
Identifies the encryption format and pulls everything readable out of the
unencrypted header without needing the key: algorithm, key-derivation parameters,
strength assessment, embedded file listing and entropy. Covers PGP/GPG, Age,
ECMA-376 Office, Microsoft Purview, PDF, ZIP, 7-Zip, RAR, BitLocker, S/MIME,
OpenSSL, AESCrypt, SSH and PEM private keys, PKCS#12 stores, Windows DPAPI blobs,
self-extracting archives, and unknown high-entropy files. Scans a whole folder at
once.

**YARA Scanner** and **Fuzzy Hash**
Whole-file scanning against YARA rules, and fuzzy hashing to find files that are
similar rather than identical.

**Metadata** and **Strings**
EXIF including GPS coordinates, document authorship and revision history, and
extracted strings.

**Script & LNK Analyser**
Reverses known obfuscation and matches against a curated list of malicious
building blocks. Fully offline, never executes anything, and when no rule matches
you still get the de-obfuscated command and the extracted IOCs.

### Email analyser

Headers and routing, phishing and BEC indicators, extracted IOCs, and attachment
analysis, split across Technical Info, Threat Analysis, IOCs and Attachments.

### Domain tools

**WHOIS**, **SSL Certificate** and **Passive DNS**, plus a full email-security
analyser:

**DMARC**
A **Spoofability Score** out of 100 with the specific reasons behind it and
remediation steps, an inbox simulation, and the full policy broken down tag by
tag.

The **SPF record** is expanded through every `include:` into a browsable tree or a
flat IP list, with the owning organisation resolved for each range. A
**Duplicate & Overlap Check** then finds every CIDR authorised more than once and
every range already covered by a larger one, which matters because SPF has a hard
ten DNS-lookup limit that redundancy burns through.

**DKIM Key Health** assesses the published keys rather than merely counting them:
key length, revoked selectors, keys that do not permit SHA-256, and `t=y` testing
mode, which tells receiving servers to ignore DKIM failures and so leaves a domain
looking configured while enforcing nothing. The advice adapts to your situation.
It changes with your DMARC policy, because breaking DKIM under `p=reject` bounces
mail while under `p=none` it only shows up in reports, and it tells you when a key
is delegated by CNAME and therefore not yours to change, rather than sending you
into a DNS zone you cannot fix.

### Toolkit

- **Bulk Scan** - run a list of indicators through the scanners in one pass and
  export the results
- **IOC Extract & Defang** - pull indicators out of text, files, PCAPs, or images
  and PDFs via OCR, and defang or refang them
- **MITRE ATT&CK** browser
- **Adversary Intel**
- **Encoder / Decoder** and **JWT decoder**
- **Sigma rule converter**
- **Regex tester**

---

## InSentri, the local AI assistant

Optional, off by default, and completely dormant until you enable it. Nothing is
downloaded until you ask for it.

- **Runs on your own hardware.** No cloud account, no subscription, and your
  chats are not sent anywhere.
- **Reads the page you are on.** Ask "what is the verdict on this file?" or
  "which IPs are duplicated?" and it answers about the analysis in front of you.
- **Exact data stays exact.** Hash, IOC and CVE lists are taken straight from the
  analysis rather than retyped by the model, so the answer matches the page.
- **Bring your own Ollama.** If you already run Ollama, InSentri can use any model
  you have pulled into it, which is usually far stronger than the bundled ones.
  InCyter never installs or starts Ollama, and the built-in model keeps working
  without it.
- **Chat with documents.** PDF, Word, Excel, PowerPoint and text, including files
  too large to read in one pass.
- **Web search is opt-in and visible.** The panel always shows whether it is on,
  because looking up a sample's indicators can reveal that you are investigating
  them.
- **History is encrypted** and can be searched, renamed, pinned, exported to PDF
  or Markdown, or cleared.
- **GPU acceleration is optional** and installed on demand.

---

## InCyter Intelligence

A badge you will see next to verdicts and summaries. It marks output from
InCyter's own rule-based engine, which runs locally and offline. It is not a
language model and it does not guess: it is the layer that turns raw findings
into a verdict you can act on, and it can always show its working.

---

## Threat intelligence services

InCyter works without any of these; they extend what it can check. Keys are
yours, entered in **Settings > API Keys**, tested with one click, and encrypted
so only your Windows account on this machine can read them. A key that has not
been rotated in 90 days is flagged.

| Service | Used for |
| --- | --- |
| VirusTotal | The most impactful. File & Hash, IP, Domain, URL and Bulk Scan |
| AlienVault OTX | Threat intelligence and attribution |
| AbuseIPDB | IP abuse confidence scoring |
| Shodan | Internet-wide port and service data |
| urlscan.io | URL reputation and screenshots |
| ANY.RUN | Interactive sandbox |
| Hybrid Analysis | Automated malware sandbox |
| GreyNoise | Internet background noise and scanner reputation |

---

## Privacy

InCyter analyses files on your machine and does not upload them. It collects no
telemetry.

Network requests happen only when you use a feature that needs one: a
threat-intelligence lookup with your own key, the news feed on the home page, the
optional update check, or an InSentri model download after you enable it.

Settings, API keys, investigation history and logs are stored encrypted, tied to
your Windows account on this machine, so copying them elsewhere reveals nothing.
Configuration can be exported as a portable, passphrase-protected backup.
Everything is removed when you uninstall.

Analysed samples are never executed, and temporary copies are cleaned up
afterwards.

---

## Author

**Fady Azzi**

Copyright © 2026 Fady Azzi. All rights reserved.

---

## NOTE

Some anti-virus products or EDRs might block the download of the exe file, as it
is not yet signed with a code-signing certificate. You can download the zip file
as a workaround.

Please download the latest exe or zip version from the "Releases" section on the
right, or use the direct links below.

Direct link to the exe is:
https://github.com/FadyAzzi/InCyter/releases/latest/download/InCyterInstaller.exe

Direct link to the zip is:
https://github.com/FadyAzzi/InCyter/releases/latest/download/InCyterInstaller.zip
