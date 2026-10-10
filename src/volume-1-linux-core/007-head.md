---
id: 007
name: head
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

# 007 · head

## IDENTITY

| Field | Value |
|---|---|
| ID | 007 |
| Command | `head` |
| Mode / Variant | standalone |
| Family | GNU Coreutils |
| Origin | Unix V7 (1979), GNU (1987) |
| Version Verified | coreutils 9.4 |
| Platforms | Linux, macOS, BSD |
| Distros Tested | Ubuntu 22.04.4, Rocky Linux 9.3, Arch 2024.01 |
| Class | READ |
| Authorization | Open |
| Resource Cost | Low — stops after N records |

## FUNCTION

**Purpose.** Print the first N lines or bytes of a file or stream.

**Formal definition.** `head` reads from the start of the input, prints N records, then exits. Unlike `tail`, it never seeks and can stop early — valuable for very large files or unbounded streams.

**When to use.**

- Preview a file's structure.
- Sample a log's format before parsing.
- Truncate infinite streams.

**When NOT to use.**

- Last lines. Use `tail`.
- Random sample. Use `shuf` or `awk 'NR%N==0'`.

**Preferred alternatives.**

| Situation | Better tool |
|---|---|
| Random sample | `shuf -n N` |
| Structural preview | `head` (this tool) |
| Bounded read | `dd bs=1M count=1` |

## SYNTAX

**Canonical form.**

```text
head [OPTION]... [FILE]...
```

**Curated options.**

| Option | Meaning |
|---|---|
| `-n N` | First N lines |
| `-n -N` | All but last N lines |
| `-c N` | First N bytes |
| `-q` | No headers |
| `-v` | Always show headers |

**Dangerous options.** None. `head` is read-only and exits early.

**Deprecated / changed.**

| Old | Current | Note |
|---|---|---|
| `-N` numeric shortcut | `-n N` | Deprecated in POSIX; always use `-n` |

## APPLICATION

**Minimal example.**

```bash
head /etc/passwd
```

**Realistic example — sample first CSV line + 5 data rows.**

```bash
head -n 6 users.csv
```

**Expected output.**

```text
root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
sys:x:3:3:sys:/dev:/usr/sbin/nologin
sync:x:4:65534:sync:/bin:/bin/sync
games:x:5:60:games:/usr/games:/usr/sbin/nologin
```

**Interpretation.**

- `-n -N` inverts selection — rarely used but useful to drop trailing summary rows.
- `head -c N` counts bytes, not characters; multi-byte UTF-8 will truncate mid-character.
- On empty files, `head` exits 0 with no output.

**Common misreadings.**

| Misreading | Reality |
|---|---|
| `head -c` is characters | It is bytes |
| `head` on empty file errors | It exits 0 with no output |
| `head` reads the whole file | It stops after N records and closes the FD |

## OPERATIONAL

**Privilege required.** None.

**Failure modes.**

| Failure | Cause | Fix |
|---|---|---|
| `head: error reading 'dir': Is a directory` | Target is a directory | Use `ls` instead |
| Garbled output on UTF-8 | `-c` cut mid-character | Use `-n` for line-based, or `iconv` |

**Blast radius.** None.

**Recovery path.** Not applicable.

**Performance impact.** O(N). Fast. On unbounded streams (e.g., `yes`), `head` terminates the pipe after N.

## SECURITY

**MITRE ATT&CK.**

| Technique | ID | Relevance |
|---|---|---|
| File and Directory Discovery | T1083 | Adjacent |
| Data from Local System | T1005 | Reading files for staging |

**Attacker abuse.** Attackers sample files with `head` to avoid loading large logs, or to read the first lines of `/etc/shadow`, `/etc/passwd`, SSH keys, and cloud credentials.

**Detection signals.**

| Source | Signal |
|---|---|
| auditd `openat` | Rarely alerted alone; correlate with path |
| auditd execve | `head` on sensitive files by unexpected user |

**Sigma rule reference.**

```yaml
title: Sensitive File Read via head
id: 6f7a8b9c-0d1e-2f3a-4b5c-6d7e8f9a0b16
status: experimental
logsource:
  product: linux
  category: process_creation
detection:
  binary:
    Image|endswith: '/head'
  targets:
    CommandLine|contains:
      - '/etc/shadow'
      - '/etc/passwd'
      - '/root/'
      - '.ssh/'
  condition: binary and targets
level: low
```

**auditd / event ID.**

| Platform | Event |
|---|---|
| Linux | `type=OPENAT` + `type=EXECVE` with args |
| macOS | Unified log via `endpointsecurity` |

## PROVENANCE

| Field | Value |
|---|---|
| Verification Date | 2026-01-15 |
| Verified By | Ali Hazim (@SRB_X) |
| Man Page Ref | `man 1 head` |
| Version Notes | GNU 9.x; POSIX.1-2017 compliant |
| Reviewer | (pending) |
| Reviewer Signature | (pending) |

## CROSS-REFERENCE

Related entries: 006 `tail`, 005 `less`, 103 `od`

Related scenarios: SC-02 "Log review discipline"

## EDUCATIONAL

**Learning objective.** Use `head -c` correctly on binary and UTF-8 data, and explain why `head` terminates early on unbounded streams.

**Prerequisites.** Understanding of byte vs. character, UTF-8 encoding basics.

**Guided exercise (10 min).**

Run `head -c 10` on a UTF-8 file containing Arabic text. Explain the truncated output. Then run `head -n 1` and compare.

**Challenge (30 min).**

Run `yes "hello" | head -n 5`. Explain why the pipeline terminates cleanly. Then explain what happens with `yes | head -c 100` — which program exits first, and why.

**Quiz.**

1. What does `head -n -5` do?
2. Why can `head -c N` corrupt UTF-8?
3. When does `head` fail on directory input?
4. Why does `yes | head` terminate while `yes | cat` does not?

**Real-world story.**

An analyst used `head -c 100` to sample a UTF-8 log; the byte count cut a multi-byte character. Downstream `grep` misreported a filename. Lesson: bytes are not characters.
