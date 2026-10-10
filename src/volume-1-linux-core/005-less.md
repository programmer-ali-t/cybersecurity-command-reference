---
id: 005
name: less
mode: standalone, interactive pager
family: GNU less (originally Mark Nudelman, 1984)
origin: Mark Nudelman, 1984; GNU project maintains since 2000s
version-verified: less 590
platforms: Linux, macOS, BSD
distros-tested: Ubuntu 22.04.4 LTS, Rocky Linux 9.3, Arch Linux 2024.01.01
class: READ
authorization-tier: open
resource-cost: low
---

# 005 · less

## IDENTITY

| Field | Value |
|---|---|
| ID | 005 |
| Command | `less` |
| Mode / Variant | interactive pager |
| Family | GNU less (originally Mark Nudelman, 1984) |
| Origin | Mark Nudelman, 1984 |
| Version Verified | less 590 |
| Platforms | Linux, macOS, BSD |
| Distros Tested | Ubuntu 22.04.4, Rocky Linux 9.3, Arch 2024.01 |
| Class | READ |
| Authorization | Open |
| Resource Cost | Low — lazy load, single file descriptor |

## FUNCTION

**Purpose.** Page through large text files without loading the entire content into a terminal buffer; supports search, navigation, and command-line filters.

**Formal definition.** `less` opens the file, reads lazily via `mmap` or `read`, and renders only the visible viewport. It supports regular expression search (forward and back), line marking, shell escape, and piping through external commands without leaving the pager.

**When to use.**

- Review log files without `cat` flooding the terminal.
- Follow a growing log (`less +F`).
- Search within a large file interactively.

**When NOT to use.**

- In scripts. `less` is interactive; use `grep`, `head`, `sed`.
- On files that must remain unchanged while read. `less` normally does not write, but a misissued `!` command can.

**Preferred alternatives.**

| Situation | Better tool |
|---|---|
| Non-interactive view | `head`, `tail`, `sed` |
| Structured log browsing | `lnav`, `glogg` |
| JSON logs | `jq` + `less`, or `fx` |

## SYNTAX

**Canonical form.**

```text
less [OPTION]... [FILE]...
command | less
```

**Curated options.**

| Option | Meaning |
|---|---|
| `+F` | Follow mode (like `tail -f`) |
| `+G` | Start at end of file |
| `-S` | Chop long lines (no wrap) |
| `-N` | Show line numbers |
| `-R` | Pass raw ANSI colors through |
| `-X` | Do not clear screen on exit |
| `+PATTERN` | Start with first match |
| `-p PATTERN` | Alias for `+/PATTERN` |

**Interactive keys.**

| Key | Action |
|---|---|
| `/pattern` | Search forward |
| `?pattern` | Search backward |
| `n` / `N` | Next / previous match |
| `g` / `G` | Top / bottom |
| `q` | Quit |
| `!command` | Shell escape |
| `&pattern` | Show only matching lines |

**Dangerous options / behaviors.**

| Behavior | Risk |
|---|---|
| `!rm` inside `less` | Executes shell; can destroy files if mistyped |
| `v` (edit) opens `$EDITOR` on the file | Editor may overwrite |
| `less` on a FIFO | Blocks until writer closes |

**Deprecated / changed.** None notable.

## APPLICATION

**Minimal example.**

```bash
less /var/log/syslog
```

**Realistic example — open at first ERROR, no wrap, line numbers.**

```bash
less -SN +/ERROR /var/log/syslog
```

**Expected output (interactive).**

```text
/var/log/syslog
Jan 15 08:00:01 host sshd[1234]: Accepted publickey for analyst
Jan 15 08:00:02 host systemd[1]: Started Session 42 ...
Jan 15 08:14:22 host kernel: [12345.678] ERROR: ...
:
```

**Interpretation.**

- The bottom line shows `:` when idle.
- `(END)` appears at end of file.
- `Waiting for data...` appears in follow mode.
- Search wraps at EOF unless `--no-wrap` is set.

**Common misreadings.**

| Misreading | Reality |
|---|---|
| `less` loads entire file into memory | It reads lazily; safe on multi-GB files |
| `less -X` is safe with `tmux` | Can leave screen artifacts |
| `/pattern` is regex by default | Basic regex; use `-R` for escapes, `\+` for extended |

## OPERATIONAL

**Privilege required.** None for reading a file you can read.

**Failure modes.**

| Failure | Cause | Fix |
|---|---|---|
| Terminal garbled after exit | `-X` misused with TUI | `reset` |
| `Binary file ... may be displayed` | Binary content | Use `-R` for safe render, or `cat -v` |
| Follow mode exits on file rotation | `tail -F` handles rotation; `less +F` does not | Use `tail -F` for rotate-tolerant follow |

**Blast radius.** Minimal. `!` shell escape can execute commands as the invoking user.

**Recovery path.** `q` to exit; `Ctrl-C` to cancel pending commands.

**Performance impact.** Negligible for text up to several GB. Regex search on multi-GB files can take seconds.

## SECURITY

**MITRE ATT&CK.**

| Technique | ID | Relevance |
|---|---|---|
| File and Directory Discovery | T1083 | Indirect |
| Data from Local System | T1005 | Reading files for staging |

**Attacker abuse.** `less` is a favorite "look innocent" command. Attackers also use `!` shell escape in `less` to launch commands without registering a new process at the shell level (still visible in auditd's `execve` of the child, but hidden from naive shell history analysis).

**Detection signals.**

| Source | Signal |
|---|---|
| auditd execve | `/usr/bin/less` reading `/etc/shadow` or key files |
| Process tree | `less` as parent of `bash -c ...` from `!` escape |
| Shell history | `less` followed by no subsequent commands is not suspicious; `less` followed by process spawn is |

**Sigma rule reference.**

```yaml
title: Shell Escape from less — Suspicious Child Process
id: 1b2c3d4e-5f6a-7b8c-9d0e-1f2a3b4c5d14
status: experimental
logsource:
  product: linux
  category: process_creation
detection:
  parent_less:
    ParentImage|endswith: '/less'
  child_shell:
    Image|endswith:
      - '/bash'
      - '/sh'
      - '/zsh'
  condition: parent_less and child_shell
level: medium
falsepositives:
  - Legitimate use of `!` to run a search helper
```

**auditd / event ID.**

| Platform | Event |
|---|---|
| Linux | `execve` of `/usr/bin/less`, plus child `execve` |
| macOS | Unified log via `endpointsecurity` |

## PROVENANCE

| Field | Value |
|---|---|
| Verification Date | 2026-01-15 |
| Verified By | Ali Hazim (@SRB_X) |
| Man Page Ref | `man 1 less` |
| Version Notes | less 590 adds `--header` interaction improvements |
| Reviewer | (pending) |
| Reviewer Signature | (pending) |

## CROSS-REFERENCE

Related entries: 006 `tail` (rotation), 012 `grep`, 116 `sed`

Related scenarios: SC-02 "Log review discipline"

## EDUCATIONAL

**Learning objective.** Use `less` efficiently on multi-GB logs and explain the risk of the `!` shell escape.

**Prerequisites.** Basic terminal navigation, understanding of pipes and file descriptors.

**Guided exercise (20 min).**

Open `/var/log/syslog`. Practice: `/pattern`, `n`, `N`, `g`, `G`, `-N`, `-S`.

**Challenge (45 min).**

Use `less` with `+F` on a growing log while generating events from another terminal. Compare with `tail -F`. Explain why `tail -F` is preferred in production.

**Quiz.**

1. What is the difference between `less +F` and `tail -F`?
2. What does `!` do inside `less`, and why is it a security concern?
3. How does `less` handle a 10 GB file?
4. What does `(END)` at the bottom mean?

**Real-world story.**

A SOC analyst reviewing auth logs used `!grep` inside `less`. The command started with a `!rm` that they had not intended. The escape executed a destructive command. Recovery took three hours. Lesson: `less` is a terminal escape hatch, and escape hatches need discipline.
