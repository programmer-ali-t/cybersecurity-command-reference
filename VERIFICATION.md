# Verification Protocol

**Document ID:** CCSR-VERIFY-001
**Version:** 1.0.0
**Effective:** 2026-10-10
**Status:** Binding for all entries in v1.x

---

## 1. Purpose

This document defines the mandatory verification procedure every entry in the Cybersecurity Command Reference must pass before publication. An entry that has not completed all five stages is not eligible for inclusion.

## 2. The Five Verification Stages

### Stage 1 — Static Verification

**Objective:** Confirm command behavior against authoritative documentation.

**Procedure:**
1. Read the command's `man` page on the target distribution.
2. Read `--help` output.
3. Read the upstream project documentation (GNU, Microsoft Learn, project site).
4. Note the tool version at time of reading.
5. Record discrepancies between `man`, `--help`, and upstream docs.

**Output:** A `sources` block in the entry with URLs and versions.

**Pass criteria:** Command name, options, and outputs match at least two of the three sources. Discrepancies are documented in the entry itself.

---

### Stage 2 — Dynamic Verification

**Objective:** Confirm behavior by executing the command in a controlled environment.

**Procedure:**
1. Deploy a clean VM from a versioned image.
2. Take a snapshot.
3. Execute the minimal example. Record exact output.
4. Execute the realistic example. Record exact output.
5. Repeat on at least three distributions.
6. Restore snapshot between tests.

**Required environments:**

| Tier | Distribution | Version | Purpose |
|------|-------------|---------|---------|
| A | Ubuntu LTS | current | Debian family baseline |
| B | Rocky Linux | current | RHEL family baseline |
| C | Arch Linux or Alpine | current | Independent baseline |

**Output:** A `tested` block listing distro, version, kernel, date, and operator.

**Pass criteria:** Documented output matches Stage 1 expectations on all three environments. Divergences are documented.

---

### Stage 3 — Adversarial Verification

**Objective:** Confirm the entry accurately describes attacker abuse and defender detection.

**Procedure:**
1. Execute the command from an attacker's perspective. Record what appears in target logs, IDS, EDR, firewall.
2. Execute the command from a defender's perspective. Verify that listed detection signals actually fire.
3. Test each Sigma rule against a sample log source.
4. Document false positive scenarios.

**Output:** MITRE ATT&CK mapping, detection signals, Sigma rule references, false positive notes.

**Pass criteria:** Every listed detection signal was reproduced at least once in a lab. Sigma rules parse and match under `sigma-cli`.

---

### Stage 4 — Peer Review

**Objective:** External validation by a credentialled reviewer with no stake in the entry.

**Procedure:**
1. Assign a reviewer per technical domain.
2. Reviewer must hold at least one credential from the Approved Credentials list (Section 5) or demonstrate equivalent public work.
3. Reviewer receives the entry, the verification artifacts, and a blank Review Record.
4. Reviewer re-runs Stage 2 on their own environment.
5. Reviewer completes the Review Record including signature.

**Output:** A signed Review Record in `reviews/v1.0.0/`.

**Pass criteria:** Reviewer signs "Approved" or "Approved with notes." Any "Rejected" requires re-entry to Stage 1.

---

### Stage 5 — Regression

**Objective:** Maintain accuracy across tool and OS updates.

**Procedure:**
1. On the first day of each quarter, re-run Stage 1 for every entry whose tool has released a new version.
2. Re-run Stage 2 for entries whose tool version changed materially.
3. Update `CHANGELOG.md` and `ERRATA.md` accordingly.
4. Bump the entry's `version-verified` field.

**Output:** Updated entries and a dated regression report.

**Pass criteria:** No entry may carry a `version-verified` older than 12 months.

---

## 3. Entry Lifecycle
