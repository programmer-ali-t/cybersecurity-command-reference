---
id: 006
name: tail
mode: standalone
family: GNU Coreutils
origin: Unix V7 (1979), GNU (1987)
version-verified: coreutils 9.4
platforms: Linux, macOS, BSD
distros-tested: Ubuntu 22.04.4 LTS, Rocky Linux 9.3, Arch Linux 2024.01.01
class: READ
authorization-tier: open
resource-cost: low
---

# 006 · tail

## IDENTITY

| Field | Value |
|---|---|
| ID | 006 |
| Command | `tail` |
| Mode / Variant | standalone with follow modes |
| Family | GNU Coreutils |
| Origin | Unix V7 (1979), GNU (1987) |
| Version Verified | coreutils 9.4 |
| Platforms | Linux, macOS, BSD |
| Distros Tested | Ubuntu 22.04.4, Rocky Linux 9.3, Arch 2024.01 |
| Class | READ |
| Authorization | Open |
| Resource Cost | Low — seek to end, read backward |

## FUNCTION

**Purpose.** Print the last N lines of a file, or follow a file for appended data in real time.

**Formal definition.** `tail` seeks to the end of the file and reads backward to the Nth newline. With `-f` (follow), it enters a loop of `read()` calls after the file's current size, printing bytes as they are appended. `-F` extends `-f` with automatic reopen on rotation — critical for production log monitoring.

**When to use.**

- Read recent log entries.
- Follow live logs (`tail -F`).
- Extract last N records from a large file.

**When NOT to use.**

- Searching for specific content. Use `grep`.
- Files that may be binary. Use `xxd` or `od`.

**Preferred alternatives.**

| Situation | Better tool |
|---|---|
| Follow with rotation tolerance | `tail -F` (this tool) |
| Structured log browsing | `lnav` |
| Timeline filters | `journalctl --since` |

## SYNTAX

**Canonical form.**

```text
tail [OPTION]... [FILE]...
```

**Curated options.**

| Option | Meaning |
|---|---|
| `-n N` | Last N lines (default 10) |
| `-n +N` | From line N to end |
| `-c N` | Last N bytes |
| `-f` | Follow appended data |
| `-F` | Follow + reopen on rotation |
| `--pid=PID` | Stop when PID exits |
| `-q` | No headers for multiple files |
| `-v` | Always show headers |

**Dangerous options.**

| Option | Risk |
|---|---|
| `tail -f` on a FIFO | Blocks forever |
| `tail -f` on `/var/log/*` in a shell | Opens hundreds of FDs |
| `tail -n 1000000` on prod log | Massive output |

**Deprecated / changed.**

| Old | Current | Note |
|---|---|---|
| `tail -f` on rotating file | `tail -F` | `-F` handles inode change |

## APPLICATION

**Minimal example.**

```bash
tail -n 20 /var/log/syslog
```

**Realistic example — follow with rotation tolerance.**

```bash
tail -F -n 0 /var/log/nginx/access.log
```

**Expected output.**

```text
192.0.2.10 - - [15/Jan/2026:08:14:22 +0000] "GET / HTTP/1.1" 200 612
192.0.2.10 - - [15/Jan/2026:08:14:23 +0000] "GET /favicon.ico HTTP/1.1" 404 153
```

**Interpretation.**

- `-n 0` starts fresh; no historical lines.
- `-F` keeps the FD alive across logrotate; `-f` would hold the old inode and see nothing after rotation.
- On SIGINT, `tail` exits cleanly.

**Common misreadings.**

| Misreading | Reality |
|---|---|
| `-f` follows rotation | Only `-F` follows rotation |
| `tail -n +N` counts from start | `-n +N` starts at line N |
| Output ordering is guaranteed | When tailing multiple files, interleaving depends on write timing |

## OPERATIONAL

**Privilege required.** None for readable files. `/var/log/auth.log` requires `sudo` or `adm` group membership.

**Failure modes.**

| Failure | Cause | Fix |
|---|---|---|
| `tail: file truncated` | Log rotation | Use `-F` |
| `-f` shows nothing after rotation | Old inode held | Switch to `-F` |
| `--pid` does not exit | PID not own child | Expected; check PID |

**Blast radius.** None.

**Recovery path.** `Ctrl-C`.

**Performance impact.** Seek + read. Negligible CPU. Follow mode holds one FD.

## SECURITY

**MITRE ATT&CK.**

| Technique | ID | Relevance |
|---|---|---|
| File and Directory Discovery | T1083 | Indirect |
| Data from Local System | T1005 | Reading sensitive logs |

**Attacker abuse.** Attackers `tail` auth logs to observe whether their login attempts were detected. Defenders use `tail -F` for live monitoring.

**Detection signals.**

| Source | Signal |
|---|---|
| auditd `openat` | `openat("/var/log/auth.log")` by non-root |

**Sigma rule reference.**

```yaml
title: Suspicious Auth Log Read by Non-Root
id: 3c4d5e6f-7a8b-9c0d-1e2f-3a4b5c6d7e15
status: experimental
logsource:
  product: linux
  service: auditd
detection:
  open_auth:
    type: 'OPENAT'
    name: '/var/log/auth.log'
  condition: open_auth
level: low
```

**auditd / event ID.**

| Platform | Event |
|---|---|
| Linux | `type=OPENAT` with key `auth_log` |

## PROVENANCE

| Field | Value |
|---|---|
| Verification Date | 2026-01-15 |
| Verified By | Ali Hazim (@SRB_X) |
| Man Page Ref | `man 1 tail` |
| Version Notes | GNU 9.x stable; POSIX behavior unchanged |
| Reviewer | (pending) |
| Reviewer Signature | (pending) |

## CROSS-REFERENCE

Related entries: 005 `less`, 007 `head`, 050 `journalctl`

Related scenarios: SC-02 "Log review discipline"

## EDUCATIONAL

**Learning objective.** Distinguish `-f` from `-F` and explain inode-based rotation behavior.

**Prerequisites.** Understanding of inodes, log rotation, and file descriptors.

**Guided exercise (20 min).**

`tail -f` a log file. Rotate it (with `logrotate -f`). Observe stall. Switch to `-F`. Observe continuation.

**Challenge (45 min).**

Write a script that follows a log and exits after 30 seconds of silence. Use `--pid` and `timeout`.

**Quiz.**

1. Why does `-f` stall after rotation?
2. What does `-n +N` do?
3. When is `-F` mandatory in production?
4. Why might `tail -f` on a FIFO block forever?

**Real-world story.**

A SOC missed a live intrusion because their monitoring used `-f` instead of `-F`. Logrotate moved the file; the tail held the dead inode. Three hours of attack traffic went unobserved.
