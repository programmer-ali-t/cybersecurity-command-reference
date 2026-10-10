---
id: 003
name: stat
mode: standalone
family: GNU Coreutils
origin: Unix V7 stat(2), GNU stat(1) since 1992
version-verified: coreutils 9.4
platforms: Linux, macOS, BSD
distros-tested: Ubuntu 22.04.4 LTS, Rocky Linux 9.3, Arch Linux 2024.01.01
class: READ
authorization-tier: open
resource-cost: low
---

# 003 · stat

## IDENTITY

| Field | Value |
|---|---|
| ID | 003 |
| Command | `stat` |
| Mode / Variant | file or filesystem |
| Family | GNU Coreutils |
| Origin | Unix V7 `stat(2)`, GNU `stat(1)` since 1992 |
| Version Verified | coreutils 9.4 |
| Platforms | Linux, macOS, BSD |
| Distros Tested | Ubuntu 22.04.4, Rocky Linux 9.3, Arch 2024.01 |
| Class | READ |
| Authorization | Open |
| Resource Cost | Low — one `statx` per file |

## FUNCTION

**Purpose.** Display complete file metadata: size, blocks, inode, hard links, permissions, ownership, and all three timestamps (atime, mtime, ctime).

**Formal definition.** `stat` calls `statx(2)` (modern) or `stat(2)` and prints structured output. Unlike `ls -l`, it shows nanosecond timestamps, inode numbers, device IDs, and filesystem block counts. It also reports filesystem-level metadata with `-f`.

**When to use.**

- Forensic timeline — extract mtime/atime/ctime.
- Verify inode for hard-link identification.
- Confirm filesystem type and block size.
- Script filesystem checks with `-c` format strings.

**When NOT to use.**

- Listing directory contents. Use `ls`.
- Bulk metadata across millions of files. Use `find -printf`.

**Preferred alternatives.**

| Situation | Better tool |
|---|---|
| Bulk metadata | `find . -printf '%p %s %T@\n'` |
| Filesystem-level info | `stat -f`, `tune2fs -l` |
| JSON output | `stat --printf`, `jq` |

## SYNTAX

**Canonical form.**

```text
stat [OPTION]... FILE...
stat -f FILE
```

**Curated options.**

| Option | Meaning |
|---|---|
| `-c FORMAT` | Custom output; `%n` name, `%s` size, `%i` inode, `%Y` mtime epoch, `%y` mtime human |
| `-f` | Filesystem info instead of file |
| `-L` | Follow symlinks |
| `-t` | Terse output for scripts |
| `--printf` | Like `-c` but interprets backslash escapes |

**Dangerous options.** None. `stat` is read-only.

**Deprecated / changed.**

| Old | Current | Note |
|---|---|---|
| Birth time `%w` | May show `-` if FS does not store it | ext4 with `statx` supports it |

## APPLICATION

**Minimal example.**

```bash
stat /etc/hosts
```

**Realistic example — epoch mtime for timeline.**

```bash
stat -c '%n|%s|%i|%Y|%Z' /var/log/auth.log
```

**Expected output.**

```text
  File: /etc/hosts
  Size: 221          Blocks: 8          IO Block: 4096   regular file
Device: fd00h/64768d Inode: 131074      Links: 1
Access: (0644/-rw-r--r--)  Uid: (    0/    root)   Gid: (    0/    root)
Access: 2026-01-15 08:00:00.123456789 +0000
Modify: 2025-12-01 12:00:00.000000000 +0000
Change: 2025-12-01 12:00:00.000000000 +0000
 Birth: 2025-12-01 11:59:59.000000000 +0000
```

**Interpretation.**

- `Access` (atime) — last read; often disabled or relatime-merged.
- `Modify` (mtime) — last content change; what most people mean by "file time".
- `Change` (ctime) — last inode metadata change (permissions, ownership, link count). Not "creation".
- `Birth` (btime) — creation time; not present on all filesystems.
- `Links: 1` — link count; greater than 1 indicates hard links.

**Common misreadings.**

| Misreading | Reality |
|---|---|
| ctime = creation time | ctime = inode change time; btime is creation |
| atime is reliable | Many systems mount `noatime` or `relatime` |
| Timestamps prove activity | Timestamps can be altered with `touch` and filesystem tools |
| Nanosecond precision = nanosecond accuracy | Filesystem clock granularity varies; ext4 = 1 ns theoretically, ~1 ms in practice |

## OPERATIONAL

**Privilege required.** None. But `stat` on a directory requires `x` traversal on every path component.

**Failure modes.**

| Failure | Cause | Fix |
|---|---|---|
| Birth time `-` | Filesystem does not store btime (older ext3, some NFS) | Expected |
| `stat: cannot statx` on NFS | Server-side `statx` not supported | Falls back automatically on most clients |
| Permissions denied on parent | Missing `x` on a directory | `sudo` or fix ACL |

**Blast radius.** None.

**Recovery path.** Not applicable.

**Performance impact.** One `statx` syscall per file. On local SSD ~5 microseconds. On NFS ~5 ms. Bulk `stat` over NFS is expensive.

## SECURITY

**MITRE ATT&CK.**

| Technique | ID | Relevance |
|---|---|---|
| File and Directory Discovery | T1083 | Direct |
| Timestomp | T1070.006 | Detection by comparing mtime, ctime, btime inconsistency |

**Attacker abuse.** Attackers use `stat` to confirm timestomping worked or to find recently modified files (malware droppers, log cleanup). Defenders use `stat` to prove timestomping: a file whose `Modify` is old but whose `Birth` is recent is suspicious.

**Detection signals.**

| Source | Signal |
|---|---|
| auditd execve | `/usr/bin/stat` invoked by web server user |
| auditd stat audit | `-a always,exit -F arch=b64 -S statx -k file_meta` |
| fsnotify / inotify | Metadata change events |

**Sigma rule reference.**

```yaml
title: Timestomp Detection via Timestamp Inconsistency
id: 5d3c8f1a-2b4e-4a7c-9e1f-8a2b3c4d5e12
status: experimental
logsource:
  product: linux
  service: auditd
detection:
  birth_after_modify:
    type: 'STATX'
  condition: birth_after_modify
level: low
```

**auditd / event ID.**

| Platform | Event |
|---|---|
| Linux (auditd) | `type=SYSCALL` on `statx`, `newfstatat`, `stat` |
| SELinux | `stat` requires `getattr` permission |

## PROVENANCE

| Field | Value |
|---|---|
| Verification Date | 2026-01-15 |
| Verified By | Ali Hazim (@SRB_X) |
| Man Page Ref | `man 1 stat`, `man 2 statx` |
| Version Notes | GNU 9.x supports `%w` birth time where available |
| Reviewer | (pending) |
| Reviewer Signature | (pending) |

## CROSS-REFERENCE

Related entries: 002 `ls`, 024 `last`, 135 `mactime`

Related scenarios: SC-06 "Timeline reconstruction"

## EDUCATIONAL

**Learning objective.** Distinguish the four file timestamps and explain why ctime is not creation time.

**Prerequisites.** Basic filesystem concepts, understanding of inodes.

**Guided exercise (20 min).**

Create a file. Run `stat` on it. Then `chmod 600`. Run `stat` again. Which timestamp changed? Why?

**Challenge (45 min).**

Using `touch -d`, set mtime to 2020. Explain why ctime cannot be forced below current time without filesystem manipulation.

**Quiz.**

1. Which timestamp changes when permissions change?
2. What is btime and when is it unavailable?
3. Why can two files with the same mtime have different ctimes?
4. How does timestomping reveal itself?

**Real-world story.**

In the 2021 SolarWinds investigation, responders used `stat` to identify the SUNBURST DLL because its btime was anomalous relative to surrounding build artifacts. Timestamp forensics is not dead.
