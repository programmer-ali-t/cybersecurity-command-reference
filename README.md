# The Cybersecurity Command Reference

**Authorized Operator Edition — Volume I: Linux Core Operations**

[![Version](https://img.shields.io/badge/version-1.0.0-blue.svg)](CHANGELOG.md)
[![DOI](https://img.shields.io/badge/DOI-10.5281%2Fzenodo.23279844-blue.svg)](https://doi.org/10.5281/zenodo.23279844)
[![License](https://img.shields.io/badge/license-CC%20BY--NC--SA%204.0-lightgrey.svg)](LICENSE)
[![ORCID](https://img.shields.io/badge/ORCID-0009--0005--1429--9415-green.svg)](https://orcid.org/0009-0005-1429-9415)
[![Channel](https://img.shields.io/badge/Telegram-KaliLinux__Arabic-blue.svg)](https://t.me/KaliLinux_Arabic)

📡 **Publishing Channel:** [t.me/KaliLinux_Arabic](https://t.me/KaliLinux_Arabic)

**Author:** Ali Hazim · **Document ID:** CCSR-V1-2026-001
**Status:** v1.0.0 — Self-Verified · **DOI:** [10.5281/zenodo.23279844](https://doi.org/10.5281/zenodo.23279844)

---

## What this is

A methodology-verified reference of Linux commands used by security operators. Every entry documents:

- **Function** — what the command does, and its formal definition
- **Syntax** — canonical form and curated options
- **Application** — minimal and realistic examples with expected output
- **Operational** — privilege, failure modes, blast radius, recovery path
- **Security** — MITRE ATT&CK mapping, attacker abuse, detection signals, Sigma rules
- **Provenance** — verified versions, dates, reviewers
- **Cross-reference** — related entries and scenarios
- **Educational** — learning objectives, guided exercises, quiz *(learning edition only)*

## What this is NOT

- Not a cheatsheet. Entries are 3–4 pages each.
- Not a claim of external peer review. Reviews are invited.
- Not a substitute for `man` pages or vendor documentation.
- Not authorization to run any command. See [Authorized Use](src/frontmatter/02-authorized-use.md).

## Verification model

This reference follows the protocol defined in [VERIFICATION.md](VERIFICATION.md):

1. **Static verification** — against `man` pages and upstream docs
2. **Dynamic verification** — executed on three distributions, dated
3. **Adversarial verification** — attacker/defender signals reproduced
4. **Peer review** — invited; pending for v1.0.0
5. **Regression** — re-verified quarterly

Every entry carries a `version-verified` field naming the tool version and test date. No entry is published as "verified" unless all five stages have completed.

## Repository structure

```
.
├── LICENSE
├── CITATION.cff
├── README.md
├── CHANGELOG.md
├── ERRATA.md
├── VERIFICATION.md
├── GOVERNANCE.md
├── CONTRIBUTING.md
├── templates/
│   └── entry-template.md
├── src/
│   ├── frontmatter/
│   ├── volume-1-linux-core/
│   │   ├── 001-pwd.md
│   │   ├── 002-ls.md
│   │   ├── 003-stat.md
│   │   ├── 004-file.md
│   │   ├── 005-less.md
│   │   ├── 006-tail.md
│   │   ├── 007-head.md
│   │   ├── 008-wc.md
│   │   ├── 009-cut.md
│   │   └── 010-sort.md
│   ├── scenarios/
│   └── detection/
│       ├── sigma/
│       ├── auditd/
│       └── windows-events/
├── reviews/
├── tools/
└── build/
```

## Current contents

| Volume | Scope | Entries | Status |
|--------|-------|---------|--------|
| I | Linux Core Operations | 10 of 30 | In progress |
| II | Network & Web Diagnostics | 0 of 25 | Planned |
| III | Windows / PowerShell SecOps | 0 of 20 | Planned |
| IV | Forensics & Incident Response | 0 of 25 | Planned |

## Roadmap

| Version | Target | Contents |
|---------|--------|----------|
| **v1.0.0** | 2026-10-10 | 10 entries (this release) |
| v1.1.0 | ~30 days | 20 entries |
| v1.2.0 | ~60 days | 30 entries |
| v2.0.0 | ~6 months | Volume I complete + Volume II started |

## Versioning

Semantic versioning (`MAJOR.MINOR.PATCH`). See [CHANGELOG.md](CHANGELOG.md).

## Errata

All verified errors are recorded in [ERRATA.md](ERRATA.md). Corrections are never silent. Entries inherited from the predecessor *Cybersecurity Command Encyclopedia* v1.0 are cross-referenced in `ERRATA.md` with their corrections.

## Contributing

Corrections, additions, and reviews are welcome. Reviewers who sign a Review Record appear in `reviews/`. See [CONTRIBUTING.md](CONTRIBUTING.md) for the workflow.

## Citation

```bibtex
@book{hazim2026ccsr,
  author    = {Hazim, Ali},
  title     = {The Cybersecurity Command Reference ---
               Authorized Operator Edition: Volume I ---
               Linux Core Operations},
  year      = {2026},
  version   = {1.0.0},
  publisher = {Zenodo},
  doi       = {10.5281/zenodo.23279844},
  orcid     = {0009-0005-1429-9415},
  url       = {https://doi.org/10.5281/zenodo.23279844},
  note      = {Document ID: CCSR-V1-2026-001}
}
```

APA:

> Hazim, A. (2026). *The Cybersecurity Command Reference — Authorized Operator Edition: Volume I — Linux Core Operations* (Version 1.0.0) [Book]. Zenodo. https://doi.org/10.5281/zenodo.23279844

See [CITATION.cff](CITATION.cff) for machine-readable metadata.

## Author

**Ali Hazim** — Independent Researcher, Iraq

| Platform | Link |
|---|---|
| ORCID | [0009-0005-1429-9415](https://orcid.org/0009-0005-1429-9415) |
| Personal Telegram | [@SRB_X](https://t.me/SRB_X) |
| **Publishing Channel** | **[t.me/KaliLinux_Arabic](https://t.me/KaliLinux_Arabic)** |
| GitHub | [programmer-ali-t](https://github.com/programmer-ali-t) |
| DOI | [10.5281/zenodo.23279844](https://doi.org/10.5281/zenodo.23279844) |

## Publishing Channel

All releases, updates, translations, and errata notices for this reference are announced on the author's Telegram channel:

### 📡 [t.me/KaliLinux_Arabic](https://t.me/KaliLinux_Arabic)

The channel covers:

- New versions of this reference
- Corrections and errata notices
- Related educational material in Arabic
- Verification methodology discussions

Subscribe to receive update notifications.

## License

This work is licensed under the [Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License](LICENSE).

You may share and adapt for non-commercial purposes with attribution and under the same license.

---

**Document ID:** CCSR-V1-2026-001
**DOI:** [10.5281/zenodo.23279844](https://doi.org/10.5281/zenodo.23279844)
**Contact:** [@SRB_X](https://t.me/SRB_X) · **Channel:** [t.me/KaliLinux_Arabic](https://t.me/KaliLinux_Arabic)
