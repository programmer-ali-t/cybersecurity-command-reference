here# Errata

All verified errors are recorded here. Entries are never silently corrected.

---

## v1.0.0 — 2026-10-10

### Current Errata

*(none yet — this section is populated as errors are identified during review or use)*

To report an error, open an issue in this repository or contact [@SRB_X](https://t.me/SRB_X) on Telegram. All corrections are published here first, then applied in the next minor release.

---

## Inherited Corrections from Predecessor

### Cybersecurity Command Encyclopedia v1.0.0 → CCSR v1.0.0

The following errors were identified in the predecessor document and are corrected in this reference. All corrections are verifiable by consulting the `man` page or upstream documentation for the affected tool.

| # | v1.0 error | v1.0 correct | Type |
|---|-----------|--------------|------|
| 014 | `printf '\$s\n' \*log \| xargs` | `find -print0 \| xargs -0` | Syntax |
| 019 | `printf 'host=ls\n' 'S (hostname)'` | `printf 'host=%s\n' "$(hostname)"` | Syntax |
| 036 | `suoders` | `sudoers` | Typo |
| 037 | `ps aux --sort=- -cpu head` | `ps aux --sort=-%cpu \| head` | Syntax |
| 043 | `Iscpu` | `lscpu` | Typo |
| 044 | `Isblk` | `lsblk` | Typo |
| 051 | `Isof` | `lsof` | Typo |
| 065 | `resolvelct` | `resolvectl` | Typo |
| 088 | Duplicate table entry | Merged | Structure |
| 113 | Unnumbered entry | Numbered | Structure |
| 119 | `areport` | `aureport` | Typo |
| 127 | `date -u +8Y- 1m- 8dT8H:8M:8SZ` | `date -u +%Y-%m-%dT%H:%M:%SZ` | Syntax |
| 133 | `ictat` | `icat` | Typo |
| 134 | `1stat` | `istat` | Typo |
| 135 | `1mactime` | `mactime` | Typo |
| 136 | `1photorec` | `photorec` | Typo |
| 137 | `1yara` | `yara` | Typo |
| 138 | `1clamscan` | `clamscan` | Typo |
| 151 | `systemctl is- active ash` | `systemctl is-active ssh` | Typo×2 |
| 157 | `systcl` | `sysctl` | Typo |
| 173 | `Get-ComputertInfo` | `Get-ComputerInfo` | Typo |
| 173 | `OaVersion` | `OsVersion` | Typo |
| 181 | `Get-McpComputerStatus` | `Get-MpComputerStatus` | Typo |
| 182 | `Get-McpThreatDetection` | `Get-MpThreatDetection` | Typo |
| 196 | `jq - e '.status == "ok"` | `jq -e '.status == "ok"'` | Syntax |
| 199 | `env sort` | `env \| sort` | Syntax |
| 200 | `crontab-1` | `crontab -l` | Typo |

**Summary:** 27 corrections — 20 typographical, 7 syntactic, 2 structural.

Each corrected entry in v1.0.0 references this table in its provenance block where applicable.

---

**Channel:** [t.me/KaliLinux_Arabic](https://t.me/KaliLinux_Arabic) · **Contact:** [@SRB_X](https://t.me/SRB_X)
