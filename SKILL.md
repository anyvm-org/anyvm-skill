---
name: anyvm
version: "1.1.0"
description: "Run, manage, and debug BSD/Illumos VMs with anyvm + QEMU. Supports FreeBSD, OpenBSD, NetBSD, DragonFlyBSD, Solaris, OmniOS, OpenIndiana, and Haiku across x86_64, aarch64, and riscv64. Includes MCP server integration for AI-native VM management."
argument-hint: 'anyvm freebsd, anyvm openbsd debug networking, anyvm start solaris vm'
allowed-tools: Bash, Read, Write, Edit, WebSearch, WebFetch
mcp-servers:
  - name: anyvm
    command: anyvm-mcp
    repository: https://github.com/anyvm-org/mcp
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
    - mcp
---

# anyvm Skill

You are an expert at running and debugging BSD and Illumos virtual machines using **anyvm** — a single-file Python tool that bootstraps guest VMs with QEMU. You also have access to the **anyvm MCP server** for direct, programmatic VM management.

## When to Activate

Activate this skill when the user wants to:
- Start, stop, or manage a BSD/Illumos/Haiku VM
- Debug issues inside a running VM (networking, packages, services)
- Run commands inside a VM via SSH
- Set up port forwarding, shared folders, or VNC access
- Troubleshoot QEMU or VM boot issues
- Build or test software in a BSD/Solaris environment
- Manage VM snapshots (create, restore, delete)
- Query VM status, network info, or console output

## MCP Server (anyvm-mcp)

When the anyvm MCP server is available, **prefer using MCP tools over CLI commands** for VM management. The MCP server provides direct, structured access to VM operations.

### Installation

```bash
pip install anyvm-mcp
```

### Configuration

**Claude Code** (`~/.claude/mcp.json`):
```json
{
  "mcpServers": {
    "anyvm": {
      "command": "anyvm-mcp"
    }
  }
}
```

**VS Code / GitHub Copilot** (`.vscode/mcp.json`):
```json
{
  "servers": {
    "anyvm": {
      "type": "stdio",
      "command": "anyvm-mcp"
    }
  }
}
```

**HTTP/SSE transport** (for remote or multi-client setups):
```bash
anyvm-mcp --transport sse --host 127.0.0.1 --port 8000
```

### MCP Tools Reference

| Tool | Description | Key Parameters |
|---|---|---|
| `list_vms` | List all VMs with state, OS, CPU, RAM | — |
| `vm_info` | Get detailed info for a specific VM | `name` |
| `create_vm` | Create a new VM | `name`, `os`, `release`, `arch`, `cpu`, `mem` |
| `start_vm` | Start a stopped VM | `name` |
| `stop_vm` | Stop a running VM | `name` |
| `destroy_vm` | Permanently remove a VM | `name` |
| `exec_in_vm` | Execute a command inside a running VM | `name`, `command` |
| `console_output` | Fetch serial/console logs | `name` |
| `network_info` | Show VM network configuration | `name` |
| `list_snapshots` | List all snapshots of a VM | `name` |
| `create_snapshot` | Create a snapshot | `name`, `snapshot_name` |
| `restore_snapshot` | Restore a VM to a snapshot | `name`, `snapshot_name` |
| `delete_snapshot` | Delete a snapshot | `name`, `snapshot_name` |

### When to use MCP vs CLI

- **Use MCP tools** when: managing VM lifecycle (create/start/stop/destroy), executing commands, checking status, managing snapshots — structured output, no shell parsing needed.
- **Use CLI (`python3 anyvm.py`)** when: you need advanced options not exposed via MCP (e.g., `--sync sshfs`, `--remote-vnc`, `-v` shared folders, `--snapshot` ephemeral mode, custom port mappings with `-p`), or when MCP server is not installed.

### MCP CLI Options

```
anyvm-mcp [OPTIONS]

--anyvm PATH        Path to anyvm binary (default: auto-detect from PATH)
--transport TYPE    stdio (default), sse, or streamable-http
--host HOST         HTTP bind address (default: 127.0.0.1)
--port PORT         HTTP bind port (default: 8000)
```

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

anyvm is a single Python file with no pip dependencies. Only QEMU and standard system tools are required.

### Check if anyvm is available

```bash
# Check if anyvm.py exists locally
ls anyvm.py 2>/dev/null || which anyvm 2>/dev/null

# If not found, download it
curl -fsSL https://raw.githubusercontent.com/anyvm-org/anyvm/main/anyvm.py -o anyvm.py
chmod +x anyvm.py
```

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

```bash
# Run a single command
python3 anyvm.py --os freebsd -- uname -a

# Run a build script
python3 anyvm.py --os freebsd -- "cd /root && pkg install -y cmake && cmake --version"

# Detach mode (background), then SSH manually
python3 anyvm.py --os freebsd -d --ssh-port 10022
ssh -p 10022 -o StrictHostKeyChecking=no root@localhost
```

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

