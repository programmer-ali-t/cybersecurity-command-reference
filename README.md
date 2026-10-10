# The Cybersecurity Command Reference

**Authorized Operator Edition — Volume I: Linux Core Operations**

[![Version](https://img.shields.io/badge/version-1.0.0-blue.svg)](CHANGELOG.md)
[![License](https://img.shields.io/badge/license-CC%20BY--NC--SA%204.0-lightgrey.svg)](LICENSE)
[![DOI](https://img.shields.io/badge/DOI-pending-orange.svg)](#citation)
[![ORCID](https://img.shields.io/badge/ORCID-0009--0005--1429--9415-green.svg)](https://orcid.org/0009-0005-1429-9415)

Author: **Ali Hazim** (Independent Researcher, Iraq)
Document ID: **CCSR-V1-2026-001**
Status: **v1.0.0 — Self-Verified** (see [VERIFICATION.md](VERIFICATION.md))

---

## What this is

A methodology-verified reference of Linux commands used by
security operators. Every entry documents:

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
- Not authorization to run any command. See
  [Authorized Use](src/frontmatter/02-authorized-use.md).

## Verification model

This reference follows the protocol defined in
[VERIFICATION.md](VERIFICATION.md):

1. **Static verification** — against `man` pages and upstream docs
2. **Dynamic verification** — executed on three distributions, dated
3. **Adversarial verification** — attacker/defender signals reproduced
4. **Peer review** — invited; pending for v1.0.0
5. **Regression** — re-verified quarterly

Every entry carries a `version-verified` field naming the tool
version and test date. No entry is published as "verified" unless
all five stages have completed.

## Repository structure
