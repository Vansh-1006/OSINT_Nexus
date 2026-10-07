# OSINT Intelligence Framework

A practical OSINT framework for finding and connecting publicly available information around a **username, email address, or domain**.

The goal isn't to throw hundreds of random lookups at a target. Instead, the framework collects public evidence, follows useful leads, correlates related findings, and organizes everything into a structured investigation.

Built with **Python** and designed to run naturally on **Kali Linux**.

## What it can do

The framework currently supports multiple investigation areas, including:

- Username and profile discovery
- Email and public exposure checks
- GitHub, GitLab and Gitea intelligence
- Domain and RDAP information
- DNS intelligence
- Certificate Transparency discovery
- Public avatar information
- Public document discovery and extraction
- Historical public pages through Internet Archive
- Search-query generation
- Bounded investigation pivots
- Evidence-based correlation
- Confidence scoring
- SQLite investigation storage
- JSON, CSV, Markdown and HTML reporting

Providers are isolated from the core engine, so adding another source doesn't require rewriting the whole project.

## Why I built it

Most OSINT tools are either very limited username checkers or collections of unrelated scripts.

I wanted something more structured:

```text
Target
  ↓
Normalization
  ↓
Initial Discovery
  ↓
Entity Extraction
  ↓
Pivot Generation
  ↓
Secondary Discovery
  ↓
Correlation
  ↓
Confidence Scoring
  ↓
Investigation Report
```

A discovery can become a lead for the next stage of the investigation, but expansion is bounded to avoid endless recursion and unnecessary requests.

## Quick Start

The project is designed to be simple to run on Kali Linux.

Extract the ZIP into your chosen directory, for example:

```bash
~/Downloads/OSINT_Project/
```

Then:

```bash
cd ~/Downloads/OSINT_Project
./install.sh
```

After the first setup:

```bash
./osint-tool username123
```

or:

```bash
./osint-tool person@example.com
```

You can also run:

```bash
./osint-tool
```

and enter the target interactively.

No manual virtual-environment activation is required.

## Common Commands

Basic investigation:

```bash
./osint-tool username123
```

Deep investigation:

```bash
./osint-tool username123 --deep
```

HTML report:

```bash
./osint-tool username123 --format html
```

JSON report:

```bash
./osint-tool username123 --format json
```

Check available providers:

```bash
./osint-tool --list-providers
```

Check provider health:

```bash
./osint-tool --provider-status
```

View previous investigations:

```bash
./osint-tool --history
```

Check the installed version:

```bash
./osint-tool --version
```

Generated reports are stored in:

```text
reports/
```

Investigation data is stored locally in:

```text
data/osint.db
```

## Provider Architecture

The framework uses a provider-based architecture:

```text
                    ┌─────────────────────┐
                    │   Investigation     │
                    │       Engine        │
                    └──────────┬──────────┘
                               │
          ┌────────────────────┼────────────────────┐
          │                    │                    │
      Discovery             Domains             Developers
          │                    │                    │
   ┌──────┼──────┐        ┌────┼────┐        ┌─────┼─────┐
   │      │      │        │    │    │        │     │     │
 Search Profiles Email   RDAP DNS  CT      GitHub GitLab Gitea
```

Every provider returns normalized findings to the investigation engine.

This makes providers independently configurable and allows failures to be isolated. If one source is unavailable, the rest of the investigation can continue.

## Evidence & Confidence

One of the most important design goals is avoiding false positives.

A matching username doesn't automatically mean two accounts belong to the same person.

The framework distinguishes between:

```text
Observed Fact
Inference
Correlation
Hypothesis
Unknown
```

Findings can receive confidence levels such as:

```text
CONFIRMED PUBLIC ASSOCIATION
HIGH CONFIDENCE
MEDIUM CONFIDENCE
LOW CONFIDENCE
UNVERIFIED
```

Each finding keeps useful evidence such as:

- Source
- URL
- Timestamp
- Evidence
- Category
- Confidence
- Related entities

This makes the output much easier to investigate instead of simply producing a huge list of URLs.

## Configuration

Provider settings are kept outside the core engine:

```text
config/
├── providers.yaml
└── profiles.yaml
```

Providers can be enabled or disabled without changing application code.

Some services may require their own API credentials.

For example:

```bash
export GITHUB_TOKEN="your_token"
export HIBP_API_KEY="your_api_key"
```

Credentials should never be committed to Git or included in reports.

## Project Structure

```text
OSINT_Project/
├── install.sh
├── osint-tool
├── pyproject.toml
├── README.md
├── .env.example
│
├── config/
│   ├── providers.yaml
│   └── profiles.yaml
│
├── data/
│   └── osint.db
│
├── reports/
│
├── docs/
│   └── ARCHITECTURE.md
│
├── osint_tool/
│   ├── engine.py
│   ├── correlation.py
│   ├── pivot.py
│   ├── models.py
│   ├── normalize.py
│   ├── http.py
│   ├── cache.py
│   ├── db.py
│   │
│   ├── providers/
│   │   ├── github.py
│   │   ├── gitlab.py
│   │   ├── gitea.py
│   │   ├── hibp.py
│   │   ├── domain.py
│   │   ├── dns.py
│   │   ├── rdap.py
│   │   ├── ct.py
│   │   ├── documents.py
│   │   ├── archive.py
│   │   ├── avatar.py
│   │   └── ...
│   │
│   └── reporting/
│
├── tests/
└── .venv/
```

## Responsible Use

This project is intended for:

- OSINT research
- Cybersecurity research
- Defensive investigations
- Threat intelligence
- Incident response
- Authorized reconnaissance
- Digital-footprint analysis

It is **not** designed to break into accounts or obtain non-public information.

The framework does not attempt to:

- Bypass authentication
- Access private accounts
- Defeat CAPTCHAs
- Circumvent access controls
- Steal credentials
- Retrieve passwords, cookies or tokens
- Exploit vulnerabilities
- Evade provider security mechanisms

When a provider blocks access, requires authentication, or isn't legitimately available, the framework records the limitation and moves on.

## Important Accuracy Note

OSINT is inherently noisy.

Different people can share the same:

- Username
- Name
- Email alias
- Avatar
- Domain name
- Organization

Because of that, the framework treats correlations as evidence rather than automatically declaring identity.

A good OSINT result should answer:

> **What was actually observed, where was it observed, and how strong is the connection?**

rather than simply:

> **I found the same username, so it must be the same person.**

## Testing

The project includes automated tests for core functionality, normalization, providers, pivot handling, reporting, HTTP behavior, and the investigation engine.

Run:

```bash
pytest -q
```

## Roadmap

The architecture is designed to grow through independent providers and investigation modules.

Future expansion can include additional public APIs, search providers, document sources, historical sources, image analysis, graph visualization, and more advanced correlation techniques without replacing the core engine.
