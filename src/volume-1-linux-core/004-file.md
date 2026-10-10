---
id: 004
name: file
mode: standalone
family: file project (Christos Zoulas)
origin: Unix V7 (1973), current file project (1986)
version-verified: file 5.45
platforms: Linux, macOS, BSD
distros-tested: Ubuntu 22.04.4 LTS, Rocky Linux 9.3, Arch Linux 2024.01.01
class: READ
authorization-tier: open
resource-cost: low to medium
---

# 004 · file

## IDENTITY

| Field | Value |
|---|---|
| ID | 004 |
| Command | `file` |
| Mode / Variant | standalone |
| Family | file project (Christos Zoulas) |
| Origin | Unix V7, 1973; current `file` project, 1986 |
| Version Verified | file 5.45 |
| Platforms | Linux, macOS, BSD |
| Distros Tested | Ubuntu 22.04.4, Rocky Linux 9.3, Arch 2024.01 |
| Class | READ |
| Authorization | Open |
| Resource Cost | Low to medium (large binaries) |

## FUNCTION

**Purpose.** Identify file type by inspecting content signatures ("magic") rather than extension.

**Formal definition.** `file` reads the first bytes of a file (and, for some types, further offsets) and matches them against a compiled magic database at `/usr/share/misc/magic.mgc` (or `/usr/share/file/magic.mgc`). It also applies structural heuristics for text files (encoding, line endings) and can detect embedded structures in containers (ELF, PE, PDF, ZIP).

**When to use.**

- Triage unknown files during incident response.
- Verify a file's true type before opening or executing.
- Confirm MIME type for upload validation in scripts.

**When NOT to use.**

- As a malware verdict. `file` reports type, not intent.
- On attacker-controlled filenames. Trust content, not name.

**Preferred alternatives.**

| Situation | Better tool |
|---|---|
| MIME type for automation | `file --mime-type -b` |
| Deep binary analysis | `binwalk`, `readelf`, `pefile` |
| Malware classification | `yara`, `clamscan`, sandbox |

## SYNTAX

**Canonical form.**

```text
file [OPTION]... FILE...
```

**Curated options.**

| Option | Meaning |
|---|---|
| `-b` | Brief — no filename prefix |
| `-i` | MIME type output |
| `--mime-type` | MIME type only |
| `--mime-encoding` | Encoding only |
| `-L` | Follow symlinks |
| `-h` | Do not follow symlinks |
| `-k` | Keep going after first match |
| `-z` | Inspect compressed files |
| `-e` | Exclude a test from magic list |

**Dangerous options.** None at the file level. Note that `-L` on a symlink to a FIFO could block; use `-h` on untrusted input.

**Deprecated / changed.**

| Old | Current | Note |
|---|---|---|
| `file -i` output as `text/plain; charset=...` | Stable | Use `--mime-type -b` for clean parse |

## APPLICATION

**Minimal example.**

```bash
file sample.bin
```

**Realistic example — triage a directory by type.**

```bash
file -b * | sort | uniq -c | sort -rn
```

**Expected output.**

```text
sample.bin: ELF 64-bit LSB pie executable, x86-64, version 1
(SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2,
BuildID[sha1]=..., stripped
```

**Interpretation.**

- `ELF 64-bit` — binary format.
- `LSB` — little-endian.
- `pie executable` — position-independent, modern hardening.
- `dynamically linked` — depends on shared libraries.
- `stripped` — symbol table removed; harder to debug.

**Common misreadings.**

| Misreading | Reality |
|---|---|
| `file` says "data" = not executable | Could be packed or encrypted |
| `file` on a `.jpg` returning JPEG = safe | Content can be polyglot (JPEG + PHP) |
| Mismatch of extension and content = malicious | Often just an operational mistake |
| `file` reads the whole file | It reads header bytes and consults magic; not full-content |

## OPERATIONAL

**Privilege required.** None.

**Failure modes.**

| Failure | Cause | Fix |
|---|---|---|
| `file: command not found` | Minimal container | Install `file` package |
| `cannot open ... (No such file or directory)` | Path wrong | `realpath` |
| Returns `data` for a known file | Magic DB stale | `file --version`, update magic |

**Blast radius.** None.

**Recovery path.** Not applicable.

**Performance impact.** Fast for small files. Slower for large archives where `file` seeks through headers. `file` on 10 GB disk image ~ 100 ms; on 1M small files ~ 20 s.

## SECURITY

**MITRE ATT&CK.**

| Technique | ID | Relevance |
|---|---|---|
| File and Directory Discovery | T1083 | Adjacent |
| Masquerading: Match Legitimate Name or Location | T1036.005 | Extension/content mismatch is a hallmark |

**Attacker abuse.** Attackers rename binaries to disguise them (`.jpg` containing ELF). `file` is a defender's first check. Also used by attackers to identify target binaries before exploitation.

**Detection signals.**

| Source | Signal |
|---|---|
| auditd execve | `/usr/bin/file` invoked by non-admin user in `/tmp` |
| Filesystem watchers | `file` on files in `/dev/shm` |

**Sigma rule reference.**

```yaml
title: File Type Identification on Suspicious Path
id: 9a4b2c7d-1e5f-4b8a-9c2d-6e7f8a9b0c13
status: experimental
logsource:
  product: linux
  category: process_creation
detection:
  binary:
    Image|endswith: '/file'
  path:
    CommandLine|contains:
      - '/tmp/'
      - '/dev/shm/'
      - '/var/tmp/'
  condition: binary and path
level: low
```

**auditd / event ID.**

| Platform | Event |
|---|---|
| Linux | `execve /usr/bin/file` |
| Windows | No native equivalent; PowerShell `Get-Content -Encoding Byte` used |

## PROVENANCE

| Field | Value |
|---|---|
| Verification Date | 2026-01-15 |
| Verified By | Ali Hazim (@SRB_X) |
| Man Page Ref | `man 1 file`, `man 5 magic` |
| Version Notes | 5.45 adds LLVM bitcode, improves PE detection |
| Reviewer | (pending) |
| Reviewer Signature | (pending) |

## CROSS-REFERENCE

Related entries: 129 `binwalk`, 139 `readelf`, 137 `yara`

Related scenarios: SC-03 "Malware triage on Linux host"

## EDUCATIONAL

**Learning objective.** Use `file` to detect content/extension mismatches and explain why it is not a malware verdict.

**Prerequisites.** Understanding of magic numbers, basic file formats (ELF, PE, JPEG).

**Guided exercise (20 min).**

Copy `/bin/ls` to `/tmp/image.jpg`. Run `file`. Then run `file --mime-type -b`. Explain the difference between what `file` says and what a web server would do with it.

**Challenge (45 min).**

Create a polyglot file (GIF + PHP or ZIP + HTML). Show that `file` reports the leading type. Discuss the security implication for upload handlers.

**Quiz.**

1. What database does `file` consult?
2. Why might `file` return `data` for a valid file?
3. What is a polyglot file?
4. Why is `file` not a substitute for AV?

**Real-world story.**

In a 2020 investigation, a web shell was identified because the uploaded "image" was actually a PHP script that passed `file` as `PHP script, ASCII text`. The upload handler had validated only the extension. Lesson: validate content, not names.
