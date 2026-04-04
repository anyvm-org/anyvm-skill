---
name: anyvm
version: "1.2.0"
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

You are an expert at running and debugging BSD and Illumos virtual machines using **anyvm MCP server**. **Always use MCP tools for VM management** — they provide direct, structured access to all VM operations.

## When to Activate

Activate this skill when the user wants to:
- Start, stop, or manage a BSD/Illumos/Haiku VM
- Debug issues inside a running VM (networking, packages, services)
- Run commands inside a VM via SSH
- Troubleshoot QEMU or VM boot issues
- Build or test software in a BSD/Solaris environment
- Manage VM snapshots (create, restore, delete)
- Query VM status, network info, or console output

## Installation

```bash
# Check if anyvm-mcp is installed
which anyvm-mcp 2>/dev/null || ls ~/.local/bin/anyvm-mcp 2>/dev/null

# If not found, install it (bundles anyvm CLI automatically)
pipx install anyvm-mcp

# QEMU is also required
# macOS:
brew install qemu
# Linux (Debian/Ubuntu):
sudo apt-get --no-install-recommends -y install qemu-system-x86 qemu-system-arm qemu-utils ovmf
```

## MCP Tools Reference

**Always use these MCP tools instead of CLI commands.**

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
| `network_info` | Show IP, MAC, NIC configuration | `name` |
| `list_snapshots` | List all snapshots of a VM | `name` |
| `create_snapshot` | Create a snapshot | `name`, `snapshot_name` |
| `restore_snapshot` | Restore a VM to a snapshot | `name`, `snapshot_name` |
| `delete_snapshot` | Delete a snapshot | `name`, `snapshot_name` |

### Typical Workflows

**Start a FreeBSD VM and run a command:**
1. `create_vm` with `name: "myvm"`, `os: "freebsd"`
2. `start_vm` with `name: "myvm"`
3. `exec_in_vm` with `name: "myvm"`, `command: "uname -a"`

**Snapshot workflow:**
1. `create_snapshot` with `name: "myvm"`, `snapshot_name: "before-change"`
2. `exec_in_vm` — make changes
3. `restore_snapshot` if something goes wrong

**Check VM status:**
1. `list_vms` to see all VMs
2. `vm_info` for details on a specific VM
3. `network_info` to check networking
4. `console_output` to read boot/serial logs

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

## Debugging Guide

### VM won't start
1. Check QEMU is installed: `qemu-system-x86_64 --version`
2. Check KVM is available: `ls /dev/kvm` (Linux)
3. Use `console_output` MCP tool to check boot logs
4. Check disk space — images are ~1-3GB each

### SSH / exec_in_vm fails
1. Wait for VM to fully boot (may take 30-60 seconds)
2. Use `vm_info` to check VM state
3. Use `console_output` to check for boot errors
4. Use `network_info` to verify networking is up

### Networking issues inside VM
1. `exec_in_vm` with `command: "ifconfig"` or `command: "ip addr"`
2. `exec_in_vm` with `command: "ping -c1 google.com"` to test DNS
3. QEMU user-mode networking uses 10.0.2.0/24 — gateway is 10.0.2.2

### Performance issues
1. Create VM with more resources: `cpu: 4`, `mem: 4096`
2. On macOS Apple Silicon, aarch64 VMs use HVF acceleration (fast), x86_64 VMs use TCG (slower)

## Important Notes

- anyvm downloads pre-built images automatically on first run — internet access is required
- Default SSH credentials use key-based auth (keys are bundled with images)
- On macOS Apple Silicon, x86_64 VMs run via QEMU TCG (slower) while aarch64 VMs use HVF acceleration
- All guest images are built by CI in companion repos (e.g., anyvm-org/freebsd-builder) and published as GitHub Releases
