---
name: anyvm
version: "1.0.0"
description: "Run, manage, and debug BSD/Illumos VMs with anyvm + QEMU. Supports FreeBSD, OpenBSD, NetBSD, DragonFlyBSD, Solaris, OmniOS, OpenIndiana, and Haiku across x86_64, aarch64, and riscv64 architectures."
argument-hint: 'anyvm freebsd, anyvm openbsd debug networking, anyvm start solaris vm'
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
    - openbsd
    - netbsd
    - dragonflybsd
    - solaris
    - omnios
    - openindiana
    - haiku
    - bsd
    - virtualization
---

# anyvm Skill

You are an expert at running and debugging BSD and Illumos virtual machines using **anyvm** — a single-file Python tool that bootstraps guest VMs with QEMU.

## When to Activate

Activate this skill when the user wants to:
- Start, stop, or manage a BSD/Illumos/Haiku VM
- Debug issues inside a running VM (networking, packages, services)
- Run commands inside a VM via SSH
- Set up port forwarding, shared folders, or VNC access
- Troubleshoot QEMU or VM boot issues
- Build or test software in a BSD/Solaris environment

## Supported Guest Operating Systems

| Guest OS | x86_64 | aarch64 | riscv64 |
|---|---|---|---|
| FreeBSD (12.4–15.0, desktop: xfce/gnome/kde6) | Yes | Yes | Yes |
| OpenBSD (7.3–7.6+) | Yes | Yes | Yes |
| NetBSD | Yes | Yes | No |
| DragonFlyBSD | Yes | No | No |
| Solaris | Yes | No | No |
| OmniOS | Yes | No | No |
| OpenIndiana | Yes | No | No |
| Haiku | Yes | No | No |

## Installation

> **CRITICAL: anyvm is a single Python file. Do NOT use `brew install`, `pip install`, `pipx`, or any package manager. Just `curl` the file.**

```bash
# Check if anyvm.py exists locally
ls anyvm.py 2>/dev/null || which anyvm 2>/dev/null

# If not found, download the single file — this is the ONLY installation method
curl -fsSL https://raw.githubusercontent.com/anyvm-org/anyvm/v0.3.2/anyvm.py -o anyvm.py
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
python3 anyvm.py --os freebsd --release 15.0 --arch aarch64

# FreeBSD desktop (KDE6, XFCE, or GNOME)
python3 anyvm.py --os freebsd --release 15.0-kde6

# OpenBSD
python3 anyvm.py --os openbsd --release 7.6

# Solaris
python3 anyvm.py --os solaris

# Custom resources
python3 anyvm.py --os freebsd --mem 4096 --cpu 4
```

### Run commands inside VM

**Start a VM and run an initial command:**
```bash
# anyvm starts the VM, runs the command, but the VM keeps running in the background
python3 anyvm.py --os freebsd -- uname -a

# Or start without an initial command
python3 anyvm.py --os freebsd
```

**After the VM is running, always use SSH directly for all subsequent commands:**
```bash
# Use the SSH port shown in anyvm output (default: 2222, or as assigned)
ssh -p 2222 -o StrictHostKeyChecking=no -o UserKnownHostsFile=/dev/null root@localhost uname -a
ssh -p 2222 -o StrictHostKeyChecking=no -o UserKnownHostsFile=/dev/null root@localhost "pkg install -y cmake && cmake --version"
```

> **Important:** The VM does NOT shut down after the initial command completes — it keeps running in the background. Once a VM is started, always use `ssh` directly for any further commands. Do NOT call `python3 anyvm.py --os ... -- command` again — that would try to start a new VM instance.

### Networking

```bash
# Custom SSH port
python3 anyvm.py --os freebsd --ssh-port 2222

# Additional port forwards (host:guest)
python3 anyvm.py --os freebsd -p 8080:80 -p 3306:3306

# UDP port forward
python3 anyvm.py --os freebsd -p udp:5353:53

# Public access (bind to 0.0.0.0)
python3 anyvm.py --os freebsd --public -p 8080:80

# Enable IPv6
python3 anyvm.py --os freebsd --enable-ipv6
```

### Shared Folders

```bash
# Share host directory into VM
python3 anyvm.py --os freebsd -v /home/user/project:/root/project

# Choose sync method
python3 anyvm.py --os freebsd -v /src:/root/src --sync sshfs
# Sync options: rsync (default), sshfs, nfs, scp
```

### Display and Console

```bash
# VNC Web UI (default: http://localhost:6080)
python3 anyvm.py --os freebsd --release 15.0-kde6

# Custom resolution
python3 anyvm.py --os freebsd --release 15.0-kde6 --res 1920x1080

# VNC with password
python3 anyvm.py --os freebsd --release 15.0-kde6 --vnc-password mypass

# Remote VNC tunnel (Cloudflare)
python3 anyvm.py --os freebsd --release 15.0-kde6 --remote-vnc cf

# Serial console mode
python3 anyvm.py --os freebsd --console

# Disable VNC
python3 anyvm.py --os freebsd --vnc off
```

### Snapshot Mode

```bash
# Ephemeral VM — changes are NOT saved to disk
python3 anyvm.py --os freebsd --snapshot -- "pkg install -y nginx && nginx -v"
```

## Debugging Guide

### VM won't start
1. Check QEMU is installed: `qemu-system-x86_64 --version`
2. Check KVM is available: `ls /dev/kvm` (Linux)
3. Try with `--debug` flag for verbose output: `python3 anyvm.py --os freebsd --debug`
4. Check disk space — images are ~1-3GB each

### SSH connection fails
1. Wait for VM to fully boot (may take 30-60 seconds)
2. Check the SSH port shown in the output
3. Try manually: `ssh -p <port> -o StrictHostKeyChecking=no -o UserKnownHostsFile=/dev/null root@localhost`
4. Check serial console for boot errors: `python3 anyvm.py --os freebsd --serial 7000` then `nc localhost 7000`

### Networking issues inside VM
1. SSH into the VM and check: `ifconfig` or `ip addr`
2. Test DNS: `ping -c1 google.com`
3. QEMU user-mode networking uses 10.0.2.0/24 — the gateway is 10.0.2.2
4. For IPv6 issues, ensure `--enable-ipv6` is set

### Shared folder not syncing
1. Check sync mode compatibility with guest OS
2. rsync is most reliable across all platforms
3. sshfs requires FUSE support in the guest
4. Try `--sync scp` as a fallback

### VNC Web UI not loading
1. Check if port 6080 is already in use: `ss -tlnp | grep 6080`
2. anyvm auto-increments the port if busy — check the terminal output
3. Try a different VNC display: `--vnc 1` (port 5901, web UI on 6082)

### Performance issues
1. Increase memory: `--mem 4096` or `--mem 8192`
2. Increase CPUs: `--cpu 8`
3. Ensure KVM/HVF acceleration is active (check `--debug` output for "accel" lines)
4. Use `--disktype virtio` (default) for best I/O performance

## Key File Locations

- **Images**: `./output/{os}/{builder_version}/{os}-{version}.qcow2` (default)
- **UEFI vars**: `./output/{os}/{builder_version}/{os}-{version}-OVMF_VARS.fd`
- **SSH keys**: downloaded alongside images (`.key` files)
- **Custom data dir**: `--data-dir /path/to/storage`
- **Cache dir**: `--cache-dir /path/to/cache`

## Important Notes

- anyvm downloads pre-built images automatically on first run — internet access is required
- Default SSH credentials use key-based auth (keys are bundled with images)
- The VNC Web UI is a built-in feature — no extra software needed
- Use `--snapshot` for disposable testing — it won't modify the base image
- On macOS Apple Silicon, x86_64 VMs run via QEMU TCG (slower, no KVM) while aarch64 VMs use HVF acceleration
- All guest images are built by CI in companion repos (e.g., anyvm-org/freebsd-builder) and published as GitHub Releases
