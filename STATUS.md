# Helix — android_kernel_ums9230_helix

**Current status: AOSP Clang 12 + Jeus helix_defconfig; LTO_NONE for CI (full LTO killed GHA runner).**

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

## First Image contents (this tip)

| Item | Choice | Notes |
|------|--------|--------|
| Defconfig | **OEM `sprd_qogirl6_defconfig`** | ums9230 = qogirl6 family; not old-tree `unisoc_defconfig` |
| LOCALVERSION | `-Helix` | Trivial branding only |
| CMDLINE | **OEM empty** `CONFIG_CMDLINE=""` | Stock-like — no quiet/mute/nowatchdog, no cgroup_disable/no_v1 |
| DTS / dtbo | **OEM stock** (`ums9230-1h10-overlay` already in tree) | No port; Image-only artifact does not pack dtb |
| Ports from old `android_kernel_ums9230` | **None** for first Image | Kitchen-sink unisoc_defconfig / UFFD / BPF / ReSukiSU / cgroup_no_v1 deferred |

## CI (Build Kernel)
- Toolchain: **AOSP Clang 12** (`clang-r416183b`) + `CROSS_COMPILE=aarch64-linux-gnu-`
- **No Proton**
- Defconfig: `helix_defconfig` then `olddefconfig`, then `Image`


## Flash / prove (Jeus — after CI Image green)

```bash
# Flash Image only (replace path with downloaded artifact, decompress xz first)
# Example with magiskboot / anyboot packing into existing boot.img — keep OEM dtb/dtbo.
# Prove on A13 GSI:
adb wait-for-device
adb shell getprop sys.boot_completed
# expect: 1
```

**Flash = Image-only.** Do not require AK3 cgroup zip for first prove.

## Old reference repo (untouched)

- **Seuj09/android_kernel_ums9230** — do **not** delete or force-push.
- Helix ports **from** that tree later if needed; this repo stays a clean OEM rebase base.

## Deferred (after A13 boot_completed=1)

- UFFD P0 only if a later GSI target requires it
- BPF backports vs eun (never stub `net_namespace`)
- ReSukiSU
- cgroup: stock-like empty/minimal CMDLINE first; `cgroup_no_v1` + boot-nested AK3 only as A17 GSI follow-up
- Kitchen-sink cmdline from old `unisoc_defconfig`

## Constraints

- Stock A13 cmdline baseline had **no** `cgroup_disable` / `cgroup_no_v1` — keep stock-like empty/minimal CMDLINE first.
- No kitchen-sink quiet/mute/nowatchdog on day one.
- **CI Image must be green before any flash ask.**
- Old repo `Seuj09/android_kernel_ums9230` stays untouched.

## Checklist

- [x] Public repo created: `Seuj09/android_kernel_ums9230_helix`
- [x] OEM tree imported (shallow tip = verified SHA `dc9bfd6…`)
- [x] `STATUS.md` added (JUST BOOT / A13 GSI first)
- [x] Minimal ums9230 board/defconfig/LOCALVERSION for Image build (OEM qogirl6 + `-Helix`)
- [ ] CI Image green
- [ ] Image-only flash → `boot_completed=1` on A13 GSI
- [ ] A16/A17 + feature ports only after A13 proof

## Defconfig (first A13 Image)
- Source: Jeus device `.config` (Linux/arm64 **5.4.210** auto-generated, 6464 lines)
- In-tree: `arch/arm64/configs/helix_defconfig`
- After `helix_defconfig`, CI runs `olddefconfig` so symbols settle on this **5.4.254** OEM tree
- `CONFIG_CMDLINE=""` (stock-like); `CONFIG_LOCALVERSION=""` (as Jeus provided — no `-Helix` unless asked)
- Already Clang 12.0.5 / LTO_CLANG / CFI; `CONFIG_ARCH_SPRD=y`, `USERFAULTFD=y`, `TRAN_HIBER_SUPPORT=y`
- Replaces earlier `sprd_qogirl6_defconfig` CI target for first prove

