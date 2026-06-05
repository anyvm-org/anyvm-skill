---
name: anyvm
version: "1.1.0"
description: "Run, manage, and debug BSD, Illumos, and Linux VMs with anyvm + QEMU. Supports FreeBSD, GhostBSD, MidnightBSD, OpenBSD, NetBSD, DragonFlyBSD, Solaris, OmniOS, OpenIndiana, Tribblix, Haiku, and Ubuntu across x86_64, aarch64, and riscv64 architectures."
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
    - bsd
    - illumos
    - linux
    - virtualization
---

# anyvm Skill

You are an expert at running and debugging BSD, Illumos, and Linux virtual machines using **anyvm** — a single-file Python tool that bootstraps guest VMs with QEMU on Linux, macOS, and Windows. It downloads pre-built cloud images, sets up firmware, configures SSH, and boots the guest with sane defaults.

## When to Activate

Activate this skill when the user wants to:
- Start, stop, or manage a BSD/Illumos/Linux/Haiku VM
- Debug issues inside a running VM (networking, packages, services)
- Run commands inside a VM via SSH
- Set up port forwarding, shared folders, or VNC access
- Troubleshoot QEMU or VM boot issues
- Build or test software in a BSD/Solaris/Linux environment

## Supported Guest Operating Systems

| Guest OS | x86_64 | aarch64 | riscv64 |
|---|---|---|---|
| FreeBSD (12.4–15.0, desktop: -xfce/-gnome/-kde6) | Yes | Yes | Yes |
| OpenBSD (7.3–7.9, desktop: -xfce/-gnome/-kde6/-mate/-lxqt/-lumina/-enlightenment) | Yes | Yes | Yes |
| NetBSD | Yes | Yes | No |
| DragonFlyBSD | Yes | No | No |
| MidnightBSD | Yes | No | No |
| GhostBSD (FreeBSD-based desktop: MATE default, -xfce, -gershwin) | Yes | No | No |
| Solaris | Yes | No | No |
| OmniOS | Yes | No | No |
| OpenIndiana | Yes | No | No |
| Tribblix | Yes | No | No |
| Haiku | Yes | No | No |
| Ubuntu (e.g. 24.04) | Yes | No | No |

> The `--os` value is one of: `freebsd`, `ghostbsd`, `midnightbsd`, `openbsd`, `netbsd`, `dragonflybsd`, `solaris`, `omnios`, `openindiana`, `tribblix`, `haiku`, `ubuntu`.

## Host Support

| Host | x86_64 guests | aarch64 guests | riscv64 guests |
|---|---|---|---|
| Linux x86_64 | Yes | Yes | Yes |
| Linux aarch64 (arm64) | No | Yes | No |
| macOS Apple Silicon | Yes (TCG) | Yes (HVF) | No |
| Windows x86_64 native | Yes | No | No |
| Windows x86_64 WSL | Yes | Yes | Yes |

Hardware acceleration (KVM on Linux, HVF on macOS, WHPX/Hyper-V on Windows) is applied automatically when available.

## Installation

> **CRITICAL: anyvm is a single Python file. Do NOT use `brew install`, `pip install`, `pipx`, or any package manager. Just `curl` the file.**

```bash
# Check if anyvm.py exists locally
ls anyvm.py 2>/dev/null || which anyvm 2>/dev/null

# If not found, download the single file — this is the ONLY installation method
curl -fsSL https://raw.githubusercontent.com/anyvm-org/anyvm/v0.3.9/anyvm.py -o anyvm.py
chmod +x anyvm.py
```

Only QEMU and standard system tools are required as dependencies (no pip packages).

### Install QEMU dependencies

**Linux (Debian/Ubuntu):**
```bash
sudo apt-get --no-install-recommends -y install \
  zstd ovmf xz-utils qemu-utils ca-certificates \
  qemu-system-x86 qemu-system-arm qemu-efi-aarch64 \
  qemu-efi-riscv64 qemu-system-riscv64 u-boot-qemu \
  openssh-client
```

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

# OpenBSD
python3 anyvm.py --os openbsd --release 7.5 --arch aarch64

# Illumos family
python3 anyvm.py --os solaris
python3 anyvm.py --os omnios
python3 anyvm.py --os openindiana
python3 anyvm.py --os tribblix

# Ubuntu Linux
python3 anyvm.py --os ubuntu
python3 anyvm.py --os ubuntu --release 24.04

# GhostBSD (FreeBSD-based desktop)
python3 anyvm.py --os ghostbsd

# Custom resources
python3 anyvm.py --os freebsd --mem 4096 --cpu 4

# Pin a CPU model (e.g. for emulated aarch64)
python3 anyvm.py --os openbsd --arch aarch64 --cpu-type cortex-a72
```

If `--release` is omitted, anyvm auto-selects an available release for that OS.

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
# Sync modes: rsync (default), sshfs, nfs, scp, no/off (disable sync)
# Note: sshfs/nfs are not supported on Windows hosts; rsync needs rsync.exe.
```

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
python3 anyvm.py --os netbsd --builder 2.0.1
```

### Acceleration control

```bash
# Force pure software emulation (no KVM/HVF/WHPX) — slow but generic
python3 anyvm.py --os tribblix --tcg

# Windows: try WHPX acceleration instead of TCG
python3 anyvm.py --os freebsd --whpx

# Expose host PMU (perf/pmcstat/VTune); off by default to avoid #GP crashes
python3 anyvm.py --os ubuntu --enable-pmu -- perf stat ls
```

## Debugging Guide

### VM won't start
1. Check QEMU is installed: `qemu-system-x86_64 --version`
2. Check KVM is available: `ls /dev/kvm` (Linux)
3. Run with `--debug` for verbose output: `python3 anyvm.py --os freebsd --debug`
4. Check disk space — images are ~1-3GB each
5. If it boots but times out, raise the boot timeout: `--boot-timeout-sec 1800`
   (default 600s; OpenBSD/aarch64 defaults to 1200s; TCG mode defaults to 1800s)
6. If acceleration is flaky, force software emulation with `--tcg`

### SSH connection fails
1. Wait for the VM to fully boot (may take 30-60s, much longer under TCG)
2. Use the SSH port/alias shown in anyvm's output (it is auto-assigned, not a fixed 2222)
3. Try manually: `ssh -p <port> -o StrictHostKeyChecking=no -o UserKnownHostsFile=/dev/null root@localhost`
4. Inspect boot via serial console: `python3 anyvm.py --os freebsd --serial 7000` then `nc localhost 7000`

### Networking issues inside VM
1. SSH into the VM and check: `ifconfig` or `ip addr`
2. Test DNS: `ping -c1 google.com`
3. QEMU user-mode networking uses 10.0.2.0/24 — the gateway is 10.0.2.2
4. For IPv6 issues, ensure `--enable-ipv6` is set (off by default)
5. Try a different NIC model if the guest lacks a driver: `--nc e1000`

### Shared folder not syncing
1. Check sync mode compatibility with the guest OS
2. rsync is the most reliable default across all platforms
3. sshfs requires FUSE support in the guest; sshfs/nfs are unavailable on Windows hosts
4. Try `--sync scp` as a fallback, or `--sync no` to disable sync

### VNC Web UI not loading
1. Check if port 6080 is already in use: `ss -tlnp | grep 6080`
2. anyvm auto-increments the web port if busy — check the terminal output (6081, 6082, ...)
3. Try a different VNC display: `--vnc 1` (port 5901)
4. For remote/headless hosts, use a tunnel: `--remote-vnc cf` (or `lhr`/`pinggy`/`serveo`)

### Performance issues
1. Increase memory: `--mem 4096` or `--mem 8192`
2. Increase CPUs: `--cpu 8`
3. Ensure KVM/HVF/WHPX acceleration is active (check `--debug` output for "accel" lines)
4. Use `--disktype virtio` (default; `ide` for DragonFlyBSD)
5. Emulated arches (aarch64/riscv64 on x86 hosts) run under TCG and are inherently slow

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
- All guest images are built by CI in companion repos (e.g., anyvm-org/freebsd-builder) and published as GitHub Releases
- Builder/release fetching honors `GITHUB_TOKEN` / `GH_TOKEN` if set (avoids API rate limits)
