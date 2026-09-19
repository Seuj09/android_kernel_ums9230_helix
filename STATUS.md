# Helix — android_kernel_ums9230_helix

**Current status: skeleton only — OEM import done; ports not started.**

Goal: clean Helix rebase base for ums9230 on realme Android U OEM 5.4.254.  
**JUST BOOT** — first prove `boot_completed=1` on **A13 GSI** (gsi ≤ 13) with Image-only flash.

## OEM source (verified)

| Field | Value |
|-------|--------|
| Repo | https://github.com/realme-kernel-opensource/realme_C51_C53_Note50_C60_C51_N53-AndroidU-kernel-source |
| Default branch | `master` |
| Tip SHA (`git rev-parse`) | `dc9bfd6f17e9555972307fcc605f6bd3db006a6a` |
| Commit date | 2024-07-08 17:37:39 +0800 |
| Subject | Upload realme_N53 AndroidU kernel source |
| Kernel base | **5.4.254** Android U (realme C51 / C53 / Note50 / C60 / C51 / N53) |

Verified locally after shallow clone: `git rev-parse HEAD` of the import commit == OEM tip above.

## Import approach

- **Shallow clone** of OEM `master` (`--depth 1`) then push that tip as Helix `master`.
- **OEM tip commit SHA preserved** as the parent of the STATUS commit (`dc9bfd6…`).
- History beyond the tip was **intentionally flattened** (disk-tight workspace at import; full OEM history not fetched).
- Remote `oem` retained pointing at the upstream OEM URL for reference.

## Old reference repo (untouched)

- **Seuj09/android_kernel_ums9230** — do **not** delete or force-push.
- Helix ports **from** that tree later if needed; this repo stays a clean OEM rebase base.

## First Image target (A13 GSI)

**First Image = OEM C53 Android U import + minimal ums9230 board/defconfig only.**

| Step | Action |
|------|--------|
| 1 | Minimal defconfig / DTS / `LOCALVERSION=-Helix` for ums9230_1h10 / Jeus so CI builds Image |
| 2 | **CI Image green** |
| 3 | **Image-only flash** |
| 4 | Prove **`boot_completed=1` on A13 GSI** (gsi ≤ 13) |

**Do NOT port for first prove:** UFFD, BPF completeness, ReSukiSU, `cgroup_no_v1`, AK3 cgroup zip, or any other Helix extras.

A16 / A17 GSI work is **later**, after A13 boot proof.

## Deferred (after A13 boot_completed=1)

- UFFD P0 only if a later GSI target requires it
- BPF backports vs eun (never stub `net_namespace`)
- ReSukiSU
- cgroup: stock-like empty/minimal CMDLINE first; `cgroup_no_v1` + boot-nested AK3 only as A17 GSI follow-up

## Constraints

- Stock A13 cmdline baseline had **no** `cgroup_disable` / `cgroup_no_v1` — keep stock-like empty/minimal CMDLINE first.
- No kitchen-sink quiet/mute/nowatchdog on day one.
- **CI Image must be green before any flash ask.**
- Old repo `Seuj09/android_kernel_ums9230` stays untouched.

## Checklist

- [x] Public repo created: `Seuj09/android_kernel_ums9230_helix`
- [x] OEM tree imported (shallow tip = verified SHA `dc9bfd6…`)
- [x] `STATUS.md` added (JUST BOOT / A13 GSI first)
- [ ] Minimal ums9230 board/defconfig/LOCALVERSION for Image build
- [ ] CI Image green
- [ ] Image-only flash → `boot_completed=1` on A13 GSI
- [ ] A16/A17 + feature ports only after A13 proof
