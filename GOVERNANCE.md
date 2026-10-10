# Governance

**Document ID:** CCSR-GOV-001
**Version:** 1.0.0
**Effective:** 2026-10-10

---

## 1. Roles

**Author** — Ali Hazim (@SRB_X)
Owns direction, scope, and final publishing decisions.

**Technical Lead** — appointed by Author
Owns the verification protocol and merging of entries.

**Reviewers** — appointed per domain
Validate entries under the Peer Review protocol.

**Contributors** — public
May submit corrections, new entries, or translations.

## 2. Decision Rules

| Decision | Authority |
|----------|-----------|
| Scope of a volume | Author |
| Acceptance of a new entry | Technical Lead + 1 Reviewer |
| Change to verification protocol | Author + Technical Lead |
| Change to license | Author |
| Removal of a published entry | Author, published in ERRATA |
| Translation publication | Technical Lead after spot review |

## 3. Contribution Path

1. Open an issue describing the correction or addition.
2. Fork the repository.
3. Add or edit the entry under `src/`.
4. Run `tools/lint-entries.py` locally.
5. Submit a pull request.
6. PR enters Stage 1 verification automatically.
7. On Stage 4 approval, PR is merged.

## 4. Versioning

Semantic versioning: MAJOR.MINOR.PATCH

- **MAJOR** — structural change to the reference (new volume, new entry schema).
- **MINOR** — new entries added, or substantive content updates.
- **PATCH** — corrections, typo fixes, errata.

## 5. Errata Policy

Any verified error, regardless of size, is recorded in `ERRATA.md` with:

- Entry ID
- Version affected
- Description of the error
- Correction
- Date discovered
- Discovered by

Errors are never silently corrected.

## 6. Conflict of Interest

A reviewer must not review an entry whose topic is their paid product, employer, or client, unless disclosed in writing.

## 7. Sunset

If the Author ceases maintenance, the Technical Lead may fork under the same license with a clear succession note in README. The DOI of the original remains with the original.

## 8. Community Contact

All governance notices, errata announcements, and version releases are published on the channel:

📡 [t.me/KaliLinux_Arabic](https://t.me/KaliLinux_Arabic)

Direct contact: [@SRB_X](https://t.me/SRB_X)

---

*This governance is binding for v1.x. Changes require a MAJOR version bump.*
