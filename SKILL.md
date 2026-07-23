---
name: anyvm
version: "1.3.0"
description: "Run, manage, debug, and test/build/run code inside BSD, Illumos, Linux, Android, GNU Hurd, and Plan 9 VMs with anyvm + QEMU. Covers FreeBSD, GhostBSD, MidnightBSD, OpenBSD, NetBSD, DragonFlyBSD, Solaris, OmniOS, OpenIndiana, Tribblix, Haiku, Ubuntu, BlissOS, GNU Hurd, and Plan 9 (9front) across x86_64, aarch64, riscv64, sparc64, powerpc64, and s390x. Use when the user is writing code that must compile, run, or be tested on one of these operating systems or CPU architectures, needs to reproduce a platform-specific bug, checks cross-platform or cross-architecture portability, or wants to start, SSH into, port-forward, or share folders with such a VM."
argument-hint: 'anyvm freebsd, anyvm openbsd debug networking, anyvm start ubuntu vm'
allowed-tools: Bash, Read, Write, Edit, WebSearch, WebFetch
homepage: https://github.com/anyvm-org/anyvm
repository: https://github.com/anyvm-org/anyvm
author: neilpang
license: MIT
user-invocable: true
metadata:
  tags:
    - vm
    - qemu
    - freebsd
    - ghostbsd
    - midnightbsd
    - openbsd
    - netbsd
    - dragonflybsd
    - solaris
    - omnios
    - openindiana
    - tribblix
    - haiku
    - ubuntu
    - blissos
    - android
    - hurd
    - plan9
    - bsd
    - illumos
    - linux
    - virtualization
---

# anyvm Skill

You are an expert at running and debugging BSD, Illumos, Linux, Android (BlissOS), GNU Hurd, and Plan 9 virtual machines using **anyvm** — a single-file Python tool that bootstraps guest VMs with QEMU on Linux, macOS, and Windows. It downloads pre-built cloud images, sets up firmware, configures access (SSH, or telnet+9P for Plan 9), and boots the guest with sane defaults.

## When to Activate

Activate this skill when the user wants to:
- Start, stop, or manage a BSD/Illumos/Linux/Haiku/Android/Hurd/Plan 9 VM
- Debug issues inside a running VM (networking, packages, services)
- Run commands inside a VM via SSH
- Set up port forwarding, shared folders, or VNC access
- Troubleshoot QEMU or VM boot issues
- Build or test software across many OS / CPU-architecture combinations

## Supported Guest Operating Systems

Architecture columns: x86_64 / aarch64 / riscv64 / powerpc64 / sparc64 / s390x.

| Guest OS | x86_64 | aarch64 | riscv64 | ppc64 | sparc64 | s390x |
|---|---|---|---|---|---|---|
| FreeBSD (12.4–15.0, desktop: -xfce/-gnome/-kde6) | Yes | Yes | Yes | Yes | — | — |
| OpenBSD (7.3–7.9, desktop: -xfce/-gnome/-kde6/-mate/-lxqt/-lumina/-enlightenment) | Yes | Yes | Yes | — | Yes | — |
| NetBSD | Yes | Yes | Yes | — | Yes | — |
| DragonFlyBSD | Yes | — | — | — | — | — |
| MidnightBSD | Yes | — | — | — | — | — |
| GhostBSD (FreeBSD-based desktop: MATE default, -xfce, -gershwin) | Yes | — | — | — | — | — |
| Solaris | Yes | — | — | — | — | — |
| OmniOS | Yes | — | — | — | — | — |
| OpenIndiana | Yes | — | — | — | — | — |
| Tribblix | Yes | — | — | — | — | — |
| Haiku | Yes | — | — | — | — | — |
| Ubuntu (e.g. 24.04) | Yes | Yes | Yes | Yes | — | Yes |
| BlissOS (Android-x86; 14/15/16 = Android 11/12L/13) | Yes | — | — | — | — | — |
| GNU Hurd (Debian; also 32-bit i386 via `--arch i386`) | Yes | — | — | — | — | — |
| Plan 9 (9front; telnet console + 9P sync, not SSH) | Yes | — | — | — | — | — |

> The `--os` value is one of: `freebsd`, `ghostbsd`, `midnightbsd`, `openbsd`, `netbsd`, `dragonflybsd`, `solaris`, `omnios`, `openindiana`, `tribblix`, `haiku`, `ubuntu`, `blissos`, `hurd`, `plan9`.

### Special guests (read before using)

- **Plan 9 (9front)** has **no SSH**. anyvm talks to it over a telnet console and syncs `-v` folders over **9P** automatically. Starting it drops you into an interactive telnet session (press `Ctrl-]` to detach and leave the VM running). The SSH aliases, `ssh <port>` reconnect, `-- cmd` (goes to ssh), and `--sync rsync/scp/sshfs/nfs` guidance below do **not** apply to `plan9`.
- **GNU Hurd (Debian)** runs x86_64 or 32-bit `--arch i386`. Folder sync works with `rsync`, `scp`, or `nfs`, but **not `sshfs`** (Hurd has no FUSE).
- **BlissOS (Android)** supports `scp` sync only; anyvm defaults its `--sync` to `scp`. You get root SSH plus the Android home screen on the VNC Web UI.

## Host Support

Hardware acceleration (KVM on Linux, HVF on macOS, WHPX/Hyper-V on Windows) is applied automatically when available (WHPX is auto-enabled on Windows when present); everything else runs under TCG software emulation (slow).

| Host | x86_64 | aarch64 | riscv64 | s390x | ppc64 | sparc64 |
|---|---|---|---|---|---|---|
| Linux x86_64 | Yes | Yes | Yes | Yes | Yes | Yes |
| Linux aarch64 (arm64) | — | Yes | — | — | — | — |
| Linux s390x (IBM Z) | — | — | — | Yes (KVM) | — | — |
| macOS Apple Silicon | Yes (TCG) | Yes (HVF) | — | — | — | — |
| Windows x86_64 native | Yes | — | — | — | — | — |
| Windows x86_64 WSL | Yes | Yes | Yes | Yes | Yes | Yes |

## Installation

> **CRITICAL: anyvm is a single Python file. Do NOT use `brew install`, `pip install`, `pipx`, or any package manager. Just `curl` the file.**

```bash
# Check if anyvm.py exists locally
ls anyvm.py 2>/dev/null || which anyvm 2>/dev/null

# If not found, download the single file — this is the ONLY installation method
curl -fsSL https://raw.githubusercontent.com/anyvm-org/anyvm/v0.5.2/anyvm.py -o anyvm.py
chmod +x anyvm.py
```

Only QEMU and standard system tools are required as dependencies (no pip packages).

### Install QEMU dependencies

**Linux (Debian/Ubuntu):**
```bash
sudo apt-get --no-install-recommends -y install \
  zstd ovmf xz-utils qemu-utils ca-certificates \
  qemu-system-x86 qemu-system-arm qemu-efi-aarch64 \
  qemu-efi-riscv64 qemu-system-riscv64 qemu-system-misc u-boot-qemu \
  qemu-system-ppc qemu-system-s390x qemu-system-sparc \
  openssh-client
```
(Drop the non-x86 packages if you only need x86_64 guests.)

**macOS:**
```bash
brew install qemu
```

**Windows:**
```bash
# MSYS2
pacman.exe -S --noconfirm mingw-w64-ucrt-x86_64-qemu
# or Chocolatey
choco install qemu
```

**Docker (zero local install):**
```bash
docker run --rm -it ghcr.io/anyvm-org/anyvm:latest --os freebsd
```

## Core Commands

### Start a VM

```bash
# Basic: start a FreeBSD VM (auto-downloads image, auto-configures SSH)
python3 anyvm.py --os freebsd

# Specify version and architecture
python3 anyvm.py --os freebsd --release 14.3 --arch aarch64
python3 anyvm.py --os freebsd --release 14.3 --arch riscv64

# OpenBSD (incl. sparc64)
python3 anyvm.py --os openbsd --release 7.5 --arch aarch64
python3 anyvm.py --os openbsd --arch sparc64

# Ubuntu across architectures (powerpc64 / s390x as well)
python3 anyvm.py --os ubuntu
python3 anyvm.py --os ubuntu --release 24.04
python3 anyvm.py --os ubuntu --arch s390x
python3 anyvm.py --os ubuntu --arch powerpc64

# Illumos family
python3 anyvm.py --os solaris
python3 anyvm.py --os omnios
python3 anyvm.py --os openindiana
python3 anyvm.py --os tribblix

# GhostBSD (FreeBSD-based desktop)
python3 anyvm.py --os ghostbsd

# BlissOS (Android-x86): root ssh + the Android desktop on the VNC console
python3 anyvm.py --os blissos                 # latest (16, Android 13)
python3 anyvm.py --os blissos --release 15    # Android 12L
python3 anyvm.py --os blissos --release 14    # Android 11

# GNU Hurd (Debian; 64-bit or 32-bit i386)
python3 anyvm.py --os hurd
python3 anyvm.py --os hurd --arch i386

# Plan 9 (9front) — telnet console + 9P folder sync, NOT ssh (Ctrl-] to detach)
python3 anyvm.py --os plan9

# Custom resources
python3 anyvm.py --os freebsd --mem 4096 --cpu 4

# Pin a CPU model (e.g. for emulated aarch64)
python3 anyvm.py --os openbsd --arch aarch64 --cpu-type cortex-a72
```

If `--release` is omitted, anyvm auto-selects an available release for that OS.
`--arch` accepts: `x86_64` (default = host), `aarch64`, `riscv64`, `sparc64`, `powerpc64`, `s390x` (plus `i386` for GNU Hurd). Only the combinations marked Yes in the table above are built.

Resource defaults: `--mem` is 4096 MB when the host has more than 4 GB RAM (else 2048); `--cpu` is the host core count capped at 8 with hardware acceleration, or 2 under TCG. Pass explicit values to override.

### Desktop releases (graphical, view via VNC Web UI)

```bash
# FreeBSD desktops
python3 anyvm.py --os freebsd --release 15.0-xfce
python3 anyvm.py --os freebsd --release 15.0-gnome
python3 anyvm.py --os freebsd --release 15.0-kde6

# OpenBSD desktops
python3 anyvm.py --os openbsd --release 7.9-xfce
python3 anyvm.py --os openbsd --release 7.9-gnome
python3 anyvm.py --os openbsd --release 7.9-kde6
python3 anyvm.py --os openbsd --release 7.9-mate
python3 anyvm.py --os openbsd --release 7.9-lxqt
python3 anyvm.py --os openbsd --release 7.9-lumina
python3 anyvm.py --os openbsd --release 7.9-enlightenment

# GhostBSD desktops (MATE is the default)
python3 anyvm.py --os ghostbsd
python3 anyvm.py --os ghostbsd --release 26.1-xfce
python3 anyvm.py --os ghostbsd --release 26.1-gershwin

# BlissOS exposes the Android home screen itself over the VNC Web UI
python3 anyvm.py --os blissos
```

### Run commands inside VM

**Start a VM and run an initial command:**
```bash
# anyvm starts the VM, runs the command, but the VM keeps running in the background
python3 anyvm.py --os freebsd -- uname -a

# Or start without an initial command
python3 anyvm.py --os freebsd
```

**After the VM is running, always use SSH directly for all subsequent commands.**
anyvm writes an SSH config alias keyed by the SSH port (and by `--ssh-name` if set), so you can use any of these:
```bash
# By the port-number alias anyvm prints (e.g. it forwarded host port 2222)
ssh 2222 uname -a

# Or explicitly with the port
ssh -p 2222 -o StrictHostKeyChecking=no -o UserKnownHostsFile=/dev/null root@localhost uname -a
ssh -p 2222 -o StrictHostKeyChecking=no -o UserKnownHostsFile=/dev/null root@localhost "pkg install -y cmake && cmake --version"
```

> **Important:** The VM does NOT shut down after the initial command completes — it keeps running in the background. anyvm prints exactly how to reconnect (`ssh <name>`, `ssh <port>`, `ssh <ssh-name>`). Once a VM is started, always use `ssh` directly for any further commands. Do NOT call `python3 anyvm.py --os ... -- command` again — that would try to start a new VM instance. Use `--ssh-name <name>` to get a memorable alias.
>
> **Plan 9 exception:** `plan9` has no SSH. anyvm drops you into an interactive telnet console; press `Ctrl-]` to detach and leave the VM running. Reconnect with `telnet` on the port anyvm printed rather than `ssh`.

### Networking

```bash
# Custom SSH port (default: auto-detected free port, NOT a fixed number)
python3 anyvm.py --os freebsd --ssh-port 2222

# Memorable SSH alias (lets you `ssh myvm` afterward)
python3 anyvm.py --os freebsd --ssh-name myvm

# Additional port forwards (host:guest)
python3 anyvm.py --os freebsd -p 8080:80 -p 3306:3306

# Explicit protocol
python3 anyvm.py --os freebsd -p tcp:8443:443
python3 anyvm.py --os freebsd -p udp:5353:53

# Pick a network card model
python3 anyvm.py --os freebsd --nc e1000

# Public access (bind mapped ports to 0.0.0.0)
python3 anyvm.py --os freebsd --public -p 8080:80
# Narrower variants:
python3 anyvm.py --os freebsd --public-ssh    # expose only SSH on 0.0.0.0
python3 anyvm.py --os freebsd --public-vnc    # expose only the VNC Web UI on 0.0.0.0

# Enable IPv6 (disabled by default in QEMU slirp)
python3 anyvm.py --os freebsd --enable-ipv6
```

### Shared Folders

```bash
# Share host directory into VM (repeatable)
python3 anyvm.py --os freebsd -v /home/user/project:/root/project

# Choose sync method
python3 anyvm.py --os freebsd -v /src:/root/src --sync sshfs
```

Sync modes (`--sync`): `rsync` (default), `sshfs`, `nfs`, `sys-nfs`, `scp`, `no`/`off` (disable).
- `nfs` runs anyvm's **bundled user-space NFS server** (anyvm-org/nfsd, v3/v4 + portmapper) on the host — no kernel nfsd, no root, and it works on Linux, macOS, and Windows hosts (`mynfs` is an accepted alias).
- On **Linux**, the v3-only guests (`openbsd`, `netbsd`, `dragonflybsd`) need portmapper port 111, which the system `rpcbind` usually owns — use `--sync sys-nfs` for them there. `sys-nfs` uses the host kernel NFS server (needs root/sudo; not available on macOS/Windows).
- `sshfs` needs FUSE in the guest and is **not supported on Windows hosts**; `rsync` needs `rsync.exe` on Windows.
- Guest-specific: **Plan 9** ignores `--sync` and shares `-v` folders over 9P automatically; **GNU Hurd** supports rsync/scp/nfs but not sshfs; **BlissOS** supports scp only (and defaults to it).

### Display and Console

```bash
# VNC Web UI is on by default (http://localhost:6080, auto-increments if busy)
python3 anyvm.py --os freebsd --release 15.0-kde6

# Custom resolution (default: 1280x800)
python3 anyvm.py --os freebsd --release 15.0-kde6 --res 1920x1080

# VNC with password (login username can be anything; password must match)
python3 anyvm.py --os freebsd --release 15.0-kde6 --vnc-password mypass

# Remote VNC tunnel (auto / cf / lhr / pinggy / serveo)
python3 anyvm.py --os freebsd --release 15.0-kde6 --remote-vnc cf

# Pick a VGA device (default virtio; std for NetBSD/Haiku; cirrus for OpenBSD amd64 desktops)
python3 anyvm.py --os netbsd --vga std

# Serial console on a host TCP port (auto from 7000 if no port given)
python3 anyvm.py --os freebsd --serial 7000

# QEMU monitor via telnet on localhost
python3 anyvm.py --os freebsd --mon 4444

# Disable VNC entirely
python3 anyvm.py --os freebsd --vnc off
```

### Run mode

```bash
# Foreground / console mode
python3 anyvm.py --os freebsd --console

# Background / detach (do not auto-enter an interactive SSH session)
python3 anyvm.py --os freebsd --detach
```

### Snapshot and local images

```bash
# Ephemeral VM — changes are NOT saved to disk
python3 anyvm.py --os freebsd --snapshot -- "pkg install -y nginx && nginx -v"

# Use a local qcow2 (skip downloading)
python3 anyvm.py --os freebsd --qcow2 ./output/freebsd/freebsd-14.3.qcow2

# Pin a specific builder version (selects matching cloud images)
python3 anyvm.py --os netbsd --builder 2.1.4
```

### Acceleration control

```bash
# Force pure software emulation (no KVM/HVF/WHPX) — slow but generic
python3 anyvm.py --os tribblix --tcg

# Windows: force WHPX (normally auto-enabled when available; use --tcg to opt out)
python3 anyvm.py --os freebsd --whpx

# Expose host PMU (perf/pmcstat/VTune); off by default to avoid #GP crashes
python3 anyvm.py --os ubuntu --enable-pmu -- perf stat ls
```

## Debugging Guide

### VM won't start
1. Check QEMU is installed for the target arch: `qemu-system-x86_64 --version` (or `-aarch64`, `-riscv64`, `-s390x`, `-ppc64`, `-sparc64`)
2. Check KVM is available: `ls /dev/kvm` (Linux)
3. Run with `--debug` for verbose output: `python3 anyvm.py --os freebsd --debug`
4. Check disk space — images are ~1-3GB each
5. If it boots but times out, raise the boot timeout: `--boot-timeout-sec 1800`
   (default is 600s; emulated/non-native arches and heavy guests like Solaris or DragonFlyBSD under TCG often need more)
6. If acceleration is flaky, force software emulation with `--tcg`

### SSH connection fails
1. Wait for the VM to fully boot (may take 30-60s, much longer under TCG)
2. Use the SSH port/alias shown in anyvm's output (it is auto-assigned, not a fixed 2222)
3. Try manually: `ssh -p <port> -o StrictHostKeyChecking=no -o UserKnownHostsFile=/dev/null root@localhost`
4. Inspect boot via serial console: `python3 anyvm.py --os freebsd --serial 7000` then `nc localhost 7000`
5. `plan9` has no SSH — connect via telnet on the port anyvm printed instead

### Networking issues inside VM
1. SSH into the VM and check: `ifconfig` or `ip addr`
2. Test DNS: `ping -c1 google.com`
3. QEMU user-mode networking uses 10.0.2.0/24 — the gateway is 10.0.2.2
4. For IPv6 issues, ensure `--enable-ipv6` is set (off by default)
5. Try a different NIC model if the guest lacks a driver: `--nc e1000`

### Shared folder not syncing
1. Check sync mode compatibility with the guest OS (see the Shared Folders table above)
2. rsync is the most reliable default across BSD/Illumos/Linux
3. For `nfs`, remember the Linux port-111 caveat for v3-only guests — use `--sync sys-nfs` there
4. sshfs requires FUSE in the guest (no Hurd, no Windows host); try `--sync scp` as a fallback, or `--sync no` to disable

### VNC Web UI not loading
1. Check if port 6080 is already in use: `ss -tlnp | grep 6080`
2. anyvm auto-increments the web port if busy — check the terminal output (6081, 6082, ...)
3. Try a different VNC display: `--vnc 1` (port 5901)
4. For remote/headless hosts, use a tunnel: `--remote-vnc cf` (or `lhr`/`pinggy`/`serveo`)

### Performance issues
1. Increase memory: `--mem 4096` or `--mem 8192`
2. Increase CPUs: `--cpu 8` (default caps at 8 with acceleration, 2 under TCG)
3. Ensure KVM/HVF/WHPX acceleration is active (check `--debug` output for "accel" lines)
4. Use `--disktype virtio` (default; `ide` for DragonFlyBSD)
5. Non-native arches (e.g. aarch64/riscv64/s390x/ppc64/sparc64 on an x86 host) run under TCG and are inherently slow

## Key File Locations

- **Images**: `./output/{os}/{os}-{version}.qcow2` (override the base dir with `--data-dir`)
- **UEFI vars**: per-VM writable VARS file copied next to the image
- **SSH keys**: downloaded alongside images (`.key` files)
- **Custom data dir**: `--data-dir /path/to/storage` (default `./output`)
- **Cache dir**: `--cache-dir /path/to/cache` (avoids re-download/re-extract; pairs with `--snapshot`)

## Important Notes

- anyvm downloads pre-built images automatically on first run — internet access is required
- Default SSH credentials use key-based auth (keys are bundled with images)
- The default SSH host port is an auto-detected free port, not a fixed 2222 — read anyvm's output
- The VNC Web UI is a built-in feature — no extra software needed
- Use `--snapshot` for disposable testing — it won't modify the base image
- On macOS Apple Silicon, x86_64 VMs run via QEMU TCG (slower, no KVM) while aarch64 VMs use HVF
- On nested AMD KVM hosts (e.g. KVM inside WSL2 / Hyper-V), anyvm automatically drops AVX512 from `-cpu host` (nested AMD-V corrupts AVX512 state and makes modern guests like Ubuntu 26.04+ randomly segfault); override with `--cpu-type` if needed
- All guest images are built by CI in companion repos (e.g., anyvm-org/freebsd-builder) and published as GitHub Releases
- Builder/release fetching honors `GITHUB_TOKEN` / `GH_TOKEN` if set (avoids API rate limits)
