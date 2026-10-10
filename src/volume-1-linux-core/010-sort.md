---
id: 010
name: sort
mode: standalone
family: GNU Coreutils
origin: Unix V7 (1979), GNU (1987)
version-verified: coreutils 9.4
platforms: Linux, macOS, BSD
distros-tested: Ubuntu 22.04.4 LTS, Rocky Linux 9.3, Arch Linux 2024.01.01
class: READ
authorization-tier: open
resource-cost: medium to high
---

# 010 · sort

## IDENTITY

| Field | Value |
|---|---|
| ID | 010 |
| Command | `sort` |
| Mode / Variant | standalone with external merge |
| Family | GNU Coreutils |
| Origin | Unix V7 (1979), GNU (1987) |
| Version Verified | coreutils 9.4 |
| Platforms | Linux, macOS, BSD |
| Distros Tested | Ubuntu 22.04.4, Rocky Linux 9.3, Arch 2024.01 |
| Class | READ |
| Authorization | Open |
| Resource Cost | Medium to high (external merge for large files) |

## FUNCTION

**Purpose.** Sort lines of text by field or full line, with numeric, human-readable, month, and version semantics.

**Formal definition.** `sort` reads all input, splits into fields by `-t`, applies comparison based on `-n`, `-h`, `-M`, `-V`, or lexicographic, and writes sorted output. For inputs exceeding memory, it uses external merge sort with temporary files in `$TMPDIR`. By default, comparison uses the locale's collation order unless `LC_ALL=C` is set.

**When to use.**

- Deterministic output ordering.
- Prepare for `uniq` or `join`.
- Frequency ranking (with `uniq -c`).
- Timeline construction from timestamped logs.

**When NOT to use.**

- Randomization. Use `shuf`.
- Structured CSV with quoted commas. Use `csvsort`.
- Already-sorted data. If you only need to remove duplicates, verify sort first.

**Preferred alternatives.**

| Situation | Better tool |
|---|---|
| Randomize | `shuf` |
| Numeric heavy | `sort -n` (this tool) |
| Structured CSV | `csvsort`, `mlr sort` |
| Multi-key with complex rules | `sort -k1,1 -k2,2n` (this tool) |

## SYNTAX

**Canonical form.**

```text
sort [OPTION]... [FILE]...
```

**Curated options.**

| Option | Meaning |
|---|---|
| `-n` | Numeric sort |
| `-h` | Human-readable (K, M, G, T suffixes) |
| `-M` | Month name sort (JAN, FEB, ...) |
| `-V` | Version sort (1.2 < 1.10) |
| `-g` | General numeric (floats, scientific notation) |
| `-r` | Reverse |
| `-u` | Unique — removes duplicate lines after sorting |
| `-k N,M` | Sort key: fields N through M |
| `-t X` | Field delimiter X |
| `-z` | Null-terminated records (pairs with `find -print0`) |
| `-S SIZE` | Buffer size for in-memory sort (e.g., `-S 2G`) |
| `-T DIR` | Temp directory for external merge |
| `-o FILE` | Output to FILE (safe in-place on GNU) |
| `--stable` | Preserve original order for equal keys |
| `--parallel=N` | Use N threads (GNU 8.6+) |

**Dangerous options.**

| Option | Risk |
|---|---|
| `sort -o file file` | Safe in GNU; POSIX says undefined — other implementations may truncate |
| Default `sort` on multi-GB | Uses `$TMPDIR`; can fill root filesystem |
| `-r` on numeric with mixed signs | Behavior depends on comparison mode; test before trusting |

**Deprecated / changed.**

| Old | Current | Note |
|---|---|---|
| `+POS` / `-POS` (obsolete key syntax) | `-k N,M` | Removed in POSIX.1-2008 |
| `sort -m` (merge mode only) | `sort -m` still valid | Merge presorted files without reloading |

## APPLICATION

**Minimal example.**

```bash
sort usernames.txt
```

**Realistic example — top source IPs in an access log.**

```bash
awk '{print $1}' access.log | sort | uniq -c | sort -rn | head
```

**Expected output.**

```text
  4123 192.0.2.10
   512 198.51.100.42
    38 203.0.113.7
     7 192.0.2.55
```

**Interpretation.**

- Default sort is byte order under `LC_ALL=C`; locale order otherwise. Use `LC_ALL=C` in scripts for determinism.
- `-k2,2n` sorts numerically on field 2 only; ties preserve original order with `--stable`.
- `uniq -c` requires sorted input; that is why `sort` comes first.
- `-h` handles suffixes (1K < 1M < 1G); plain `-n` does not.

**Common misreadings.**

| Misreading | Reality |
|---|---|
| `sort` is stable by default | GNU is stable only with `--stable`; default may reorder ties |
| Locale-independent | Default collation follows locale |
| `-n` handles decimals | It handles integers; use `-g` for floats |
| `sort` uses minimal memory | It uses memory + disk for large inputs; `-S` and `-T` control this |
| `sort -u` matches `uniq` | `sort -u` global dedupe; `uniq` only adjacent |

## OPERATIONAL

**Privilege required.** None.

**Failure modes.**

| Failure | Cause | Fix |
|---|---|---|
| `sort: cannot create temporary file` | `$TMPDIR` full or unwritable | `-T /var/tmp` with sufficient space |
| Non-deterministic output across systems | Locale collation differs | `LC_ALL=C` |
| Slow on 10 GB | External merge passes | Increase `-S`, ensure fast `-T` disk |
| `sort -o file file` truncates on BSD | POSIX undefined | Use temp file + `mv`, or GNU `sort` |

**Blast radius.** `-o FILE` overwrites `FILE` — safe in GNU. Other implementations may truncate before reading.

**Recovery path.** Always back up before `-o`. Prefer redirection or temp + `mv`.

**Performance impact.** Memory-bound for small files. Disk-bound for large ones. `-S 2G` reduces merge passes. `--parallel=N` scales CPU-bound sorts.

## SECURITY

**MITRE ATT&CK.**

| Technique | ID | Relevance |
|---|---|---|
| File and Directory Discovery | T1083 | Adjacent |
| Data from Local System | T1005 | Ranking of candidate data |

**Attacker abuse.** Frequency analysis of logs; ranking of candidate credentials, tokens, or hostnames. Adversaries use `sort | uniq -c | sort -rn` to prioritize targets from scraped data.

**Detection signals.**

| Source | Signal |
|---|---|
| auditd | Large `sort` with `-T` outside expected paths |
| Process tree | `sort | uniq -c | sort -rn` pipeline in unprivileged shell |
| File activity | Large writes to `$TMPDIR` from a single PID |

**Sigma rule reference.**

```yaml
title: Frequency Analysis Pipeline (sort | uniq -c)
id: 9c0d1e2f-3a4b-5c6d-7e8f-9a0b1c2d3e19
status: experimental
logsource:
  product: linux
  category: process_creation
detection:
  sort_binary:
    Image|endswith: '/sort'
  uniq_parent:
    ParentImage|endswith: '/uniq'
  condition: sort_binary or uniq_parent
level: low
falsepositives:
  - Data analytics pipelines
  - Log summarization scripts
```

**auditd / event ID.**

| Platform | Event |
|---|---|
| Linux | `type=SYSCALL` on `openat` for `$TMPDIR` files |
| Linux | `type=EXECVE` chain: `awk`, `sort`, `uniq`, `sort`, `head` |

## PROVENANCE

| Field | Value |
|---|---|
| Verification Date | 2026-01-15 |
| Verified By | Ali Hazim (@SRB_X) |
| Man Page Ref | `man 1 sort` |
| Version Notes | coreutils 9.x adds `--parallel`, refined `-h` |
| Reviewer | (pending) |
| Reviewer Signature | (pending) |

## CROSS-REFERENCE

Related entries: 011 `uniq`, 008 `wc`, 115 `awk`

Related scenarios: SC-02 "Log review discipline"

## EDUCATIONAL

**Learning objective.** Control sort stability, locale, key ranges, and external-merge behavior on large inputs.

**Prerequisites.** Understanding of collation, locale settings, memory vs. disk limits.

**Guided exercise (20 min).**

Create a file with duplicate first fields and varying second fields. Sort by field 2 numerically, ties broken by field 1 lexicographically using `-k2,2n -k1,1`. Explain why `--stable` matters.

**Challenge (45 min).**

Generate a 1 GB file of random integers, one per line. Sort with default settings and time it. Then sort with `-S 1G -T /fast-disk`. Compare wall time. Explain the difference.

**Quiz.**

1. What does `-k2,2n` select?
2. Why is `LC_ALL=C` recommended in scripts?
3. When does `sort` spill to disk?
4. What is the difference between `sort -u` and `uniq`?

**Real-world story.**

A forensic timeline was built with `sort -k1` without specifying `LC_ALL`. Two machines produced different orderings due to locale. The timeline diverged by 4 minutes at scale. Always pin locale.
