# Helix — android_kernel_ums9230_helix

**Current status: CI Image GREEN — AOSP Clang 12 + Jeus helix_defconfig (LTO_NONE).**

Goal: clean Helix rebase base for ums9230 on realme Android U OEM 5.4.254.  
**JUST BOOT** — first prove `boot_completed=1` on **A13 GSI** (gsi ≤ 13) with Image-only flash.

## Green tip / CI

| Field | Value |
|-------|--------|
| Tip SHA | `7e923f8b5f2766e80bc5b89409e7dd315bf9a047` |
| CI run | https://github.com/Seuj09/android_kernel_ums9230_helix/actions/runs/35447926963 |
| Artifact | `kernel-Image-7e923f8b5f2766e80bc5b89409e7dd315bf9a047` (Image.xz) |
| Artifact zip digest (GHA) | `sha256:894f5be8ee221ab63f4710f4ad78e65fe062357fc7447786a5843641c1ad2871` |
| Image.xz SHA256 | `b88dffaa38b28466cfd1bf810d575d439c4068e6e0fc55259146d9eee7d2e2c1` |
| Image (decompressed) SHA256 | `dcd7cbb98b2c1101c29aee9c72a06817756df57b764db24ac01e9054daa4bf14` |
| Image size | 27908608 bytes |

## OEM source (verified)

| Field | Value |
|-------|--------|
| Repo | https://github.com/realme-kernel-opensource/realme_C51_C53_Note50_C60_C51_N53-AndroidU-kernel-source |
| Tip SHA | `dc9bfd6f17e9555972307fcc605f6bd3db006a6a` |
| Kernel base | **5.4.254** Android U |

## First Image contents

| Item | Choice | Notes |
|------|--------|--------|
| Defconfig | **Jeus `helix_defconfig`** (from 5.4.210 device `.config`) + `olddefconfig` | Not old-tree `unisoc_defconfig`; not stock-only `sprd_qogirl6` for this prove |
| LOCALVERSION | Jeus empty / AUTO | Branding optional; not required for first prove |
| CMDLINE | **empty** `CONFIG_CMDLINE=""` | Stock-like — no quiet/mute/nowatchdog, no cgroup_disable/no_v1 |
| LTO | **`CONFIG_LTO_NONE`** | Jeus had full `LTO_CLANG`; full LTO of `vmlinux.o` killed the GHA runner — disabled for first Image |
| DTS / dtbo | OEM stock | Image-only artifact; keep OEM dtb/dtbo on device |
| Ports from old tree | **None** for first Image | UFFD / BPF / ReSukiSU / cgroup_no_v1 deferred |
| Compile fix | `omnivision_tcm_i2c.c` init `retval=-EIO` | Clang 12 `-Werror,-Wsometimes-uninitialized` |

## CI (Build Kernel)

- Toolchain: **AOSP Clang 12** (`clang-r416183b`) + GNU `aarch64-linux-gnu-` binutils
- **No Proton** (do not reintroduce)
- Build: `helix_defconfig` → `olddefconfig` → `Image`

## Flash / prove (Jeus)

```bash
# Download artifact Image.xz, decompress:
xz -dk Image.xz
# Pack Image-only into existing boot.img (keep OEM dtb/dtbo), flash boot
# Prove on A13 GSI:
adb wait-for-device
adb shell getprop sys.boot_completed
# expect: 1
```

**Flash = Image-only.** No AK3 cgroup zip for first prove.

## Deferred (after A13 boot_completed=1)

- Re-enable ThinLTO / LTO_CLANG if desired
- UFFD, BPF completeness, ReSukiSU, cgroup_no_v1, AK3 cgroup zip
- A16/A17 GSI work

## Old reference repo (untouched)

- **Seuj09/android_kernel_ums9230** — do not delete or force-push

## Checklist

- [x] Public repo + OEM import
- [x] Jeus helix_defconfig + AOSP Clang 12 CI
- [x] CI Image green
- [ ] Image-only flash → `boot_completed=1` on A13 GSI
- [ ] Feature ports only after A13 proof
