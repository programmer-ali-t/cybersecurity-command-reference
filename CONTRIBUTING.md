# Contributing

Thank you for your interest in improving The Cybersecurity Command Reference. This document explains how to report errors, propose additions, and become a reviewer.

---

## Ways to Contribute

### 1. Report an Error

If you find a technical error, typo, or outdated behavior:

1. Open an issue in this repository with the label `errata`.
2. Include:
   - Entry ID (e.g., `003`)
   - The incorrect text
   - The correct text
   - Your verification method (man page, tested output, upstream docs)
   - Your environment (distro, kernel, tool version)
3. Every confirmed error is recorded in [ERRATA.md](ERRATA.md).

**Errors are never corrected silently.** Transparency is part of the reference's value.

### 2. Propose a New Entry

Entries must follow the schema in [templates/entry-template.md](templates/entry-template.md). A valid submission includes:

- All 40 fields completed
- At least one minimal and one realistic example
- Tested output (not invented)
- At least one detection signal
- A Sigma rule where applicable

Submit via pull request. PRs enter verification automatically.

### 3. Become a Reviewer

Reviewers validate entries under [VERIFICATION.md](VERIFICATION.md), Stage 4.

**Eligibility** — you must hold at least one:

- OSCP, OSEP, OSWE
- GCIH, GCIA, GCFA, GPEN, GWAPT
- CISSP, CISM
- CREST CRT, CCT
- A published CVE in the last 5 years
- Maintainership of a security tool with >500 stars
- A peer-reviewed publication in security

- **Process:**

1. Contact [@SRB_X](https://t.me/SRB_X) on Telegram.
2. Provide your credential and a verifiable reference.
3. You receive a batch of entries assigned to your domain.
4. Re-run Stage 2 on your own environment.
5. Complete a Review Record.
6. Your name is added to `reviews/` and to the entry's provenance block.

### 4. Translate

Translations are welcome under CC BY-NC-SA 4.0.

Requirements:

- Preserve the original document ID.
- Preserve author attribution.
- Preserve the erratum table.
- State the translation version and date.
- Publish under the same license.

Open an issue titled `Translation: <language>` before starting.

---

## Style Guide

### Writing

- Use plain English.
- Prefer "you" over "the operator."
- One idea per sentence.
- Document limitations, not just capabilities.

### Code

- Use fenced code blocks.
- Specify the language (`bash`, `yaml`, `text`).
- Use `$` for unprivileged prompt, `#` for privileged.
- Never use real credentials, tokens, or personal data.
- Use RFC 5737 documentation IPs (`192.0.2.0/24`, etc.).
- Use `example.com`, `example.invalid` for domains.

### Attribution

Every claim derived from another source must cite that source. Common sources:

- `man` pages
- Upstream project documentation
- MITRE ATT&CK
- NIST publications
- Peer-reviewed papers

- ---

## Code of Conduct

- Be precise, not personal.
- Disagree with the content, not the contributor.
- Report security issues privately to [@SRB_X](https://t.me/SRB_X).
- Public disclosure follows responsible disclosure norms.

---

## Versioning

Contributions are merged into the next MINOR or PATCH release depending on scope:

- New entry → MINOR
- Correction → PATCH
- Structural change → MAJOR

See [GOVERNANCE.md](GOVERNANCE.md) for full decision rules.

---

## Contact

- **Personal:** [@SRB_X](https://t.me/SRB_X)
- **Channel:** [t.me/KaliLinux_Arabic](https://t.me/KaliLinux_Arabic)
- **Issues:** [github.com/programmer-ali-t/cybersecurity-command-reference/issues](https://github.com/programmer-ali-t/cybersecurity-command-reference/issues)

---

*This project follows the governance described in [GOVERNANCE.md](GOVERNANCE.md).*
