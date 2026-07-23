# anyvm-skill

AI agent skill for [anyvm](https://github.com/anyvm-org/anyvm) — run, manage, and debug BSD, Illumos, Linux, Android, GNU Hurd, and Plan 9 VMs with natural language.

Works with [Claude Code](https://claude.com/claude-code), GitHub Copilot, and other AI coding assistants that support skill/instruction files.

## What it does

This skill teaches your AI assistant how to use anyvm, so you can say things like:

- "Start a FreeBSD 15.0 VM with 4GB RAM"
- "Run my test suite on OpenBSD"
- "Spin up an Ubuntu 24.04 VM and build my project"
- "Test my code on Ubuntu s390x and powerpc64"
- "Boot a BlissOS (Android) VM and show me the desktop"
- "Spin up a GNU Hurd or Plan 9 VM to try it out"
- "Debug why the VM can't connect to the network"
- "Set up a Solaris VM with port 8080 forwarded"
- "Share my project folder into a NetBSD VM"

The assistant will know the correct commands, flags, troubleshooting steps, and best practices — no need to memorize the CLI.

## Supported VMs

Architecture columns: x86_64 / aarch64 / riscv64 / powerpc64 / sparc64 / s390x.

| Guest OS | x86_64 | aarch64 | riscv64 | ppc64 | sparc64 | s390x |
|---|---|---|---|---|---|---|
| FreeBSD (12.4–15.0, desktop: xfce/gnome/kde6) | :white_check_mark: | :white_check_mark: | :white_check_mark: | :white_check_mark: | | |
| OpenBSD (7.3–7.9, desktops: xfce/gnome/kde6/mate/lxqt/lumina/enlightenment) | :white_check_mark: | :white_check_mark: | :white_check_mark: | | :white_check_mark: | |
| NetBSD | :white_check_mark: | :white_check_mark: | :white_check_mark: | | :white_check_mark: | |
| DragonFlyBSD | :white_check_mark: | | | | | |
| MidnightBSD | :white_check_mark: | | | | | |
| GhostBSD (desktop: MATE/xfce/gershwin) | :white_check_mark: | | | | | |
| Solaris | :white_check_mark: | | | | | |
| OmniOS | :white_check_mark: | | | | | |
| OpenIndiana | :white_check_mark: | | | | | |
| Tribblix | :white_check_mark: | | | | | |
| Haiku | :white_check_mark: | | | | | |
| Ubuntu (e.g. 24.04) | :white_check_mark: | :white_check_mark: | :white_check_mark: | :white_check_mark: | | :white_check_mark: |
| BlissOS (Android-x86; 14/15/16 = Android 11/12L/13) | :white_check_mark: | | | | | |
| GNU Hurd (Debian; also 32-bit i386) | :white_check_mark: | | | | | |
| Plan 9 (9front; telnet + 9P, not SSH) | :white_check_mark: | | | | | |

## Installation

### Claude Code

```bash
# Clone to your skills directory
git clone https://github.com/anyvm-org/anyvm-skill.git ~/.claude/skills/anyvm
```

Or add it as a project skill:

```bash
cd your-project
git clone https://github.com/anyvm-org/anyvm-skill.git .claude/skills/anyvm
```

### GitHub Copilot

Copy `SKILL.md` to your repository as `.github/copilot-instructions.md`, or append its contents to your existing instructions file.

### Other AI Assistants

Copy the contents of `SKILL.md` into your assistant's system prompt or instruction file.

## What the skill covers

### VM Lifecycle
- Starting VMs with any supported OS, version, and architecture (x86_64, aarch64, riscv64, powerpc64, sparc64, s390x)
- Desktop releases (FreeBSD/OpenBSD/GhostBSD graphical variants) and the BlissOS Android desktop
- Running commands inside VMs via SSH (with auto-generated SSH aliases); Plan 9 uses a telnet console instead
- Detach/background and console/foreground modes
- Snapshot mode for ephemeral testing
- Local `--qcow2` images and pinned `--builder` versions

### Networking
- SSH port forwarding (auto-assigned host port) and named SSH aliases
- Custom TCP/UDP port mapping
- Public access binding (`--public`, `--public-ssh`, `--public-vnc`)
- Network card model selection (`--nc`)
- IPv6 configuration

### Shared Folders
- Host-to-guest directory sharing
- Multiple sync backends: rsync, sshfs, nfs (bundled user-space server), sys-nfs, scp (or off)
- Guest-aware defaults (BlissOS = scp, Plan 9 = 9P, Hurd = no sshfs)

### Display & Console
- Built-in VNC Web UI
- Remote VNC tunnels (Cloudflare, Localhost.run, Pinggy, Serveo)
- Serial console access and QEMU monitor exposure
- Custom resolution and VGA settings

### Acceleration & Boot
- Automatic KVM/HVF/WHPX, or forced software emulation (`--tcg`)
- Configurable boot timeouts (`--boot-timeout-sec`)
- Optional host PMU passthrough (`--enable-pmu`)

### Troubleshooting
- VM boot failures and timeouts
- SSH connection issues
- Guest networking problems
- Shared folder sync issues
- VNC Web UI debugging
- Performance tuning

## File Structure

```
anyvm-skill/
├── SKILL.md      # The skill definition (main file)
├── README.md     # This file
└── LICENSE        # MIT License
```

## Requirements

The skill itself has no dependencies. To actually run VMs, you need:

- **[anyvm](https://github.com/anyvm-org/anyvm)** — single Python file, no pip install needed
- **QEMU** — the VM hypervisor
- **Python 3** — to run anyvm.py

## Examples

Once the skill is installed, your AI assistant can handle conversations like:

> **You:** I need to test my C library on FreeBSD and OpenBSD
>
> **Assistant:** *(starts a FreeBSD VM, compiles and runs tests, then does the same on OpenBSD, reports results)*

> **You:** The VM's network isn't working
>
> **Assistant:** *(checks ifconfig inside the VM, verifies DNS, tests gateway connectivity, suggests fixes)*

> **You:** Set up a FreeBSD desktop I can access from my browser
>
> **Assistant:** *(starts FreeBSD with KDE6, configures VNC Web UI, provides the URL)*

> **You:** Does my build still work on Ubuntu s390x and powerpc64?
>
> **Assistant:** *(boots Ubuntu under QEMU for each big-endian/IBM-Z arch, builds and runs the test suite, reports per-arch results)*

## Contributing

Issues and PRs welcome at [github.com/anyvm-org/anyvm-skill](https://github.com/anyvm-org/anyvm-skill).

## License

MIT
