# anyvm-skill

AI agent skill for [anyvm](https://github.com/anyvm-org/anyvm) — run, manage, and debug BSD/Illumos VMs with natural language.

Works with [Claude Code](https://claude.com/claude-code), GitHub Copilot, and other AI coding assistants that support skill/instruction files.

## What it does

This skill teaches your AI assistant how to use anyvm, so you can say things like:

- "Start a FreeBSD 15.0 VM with 4GB RAM"
- "Run my test suite on OpenBSD"
- "Debug why the VM can't connect to the network"
- "Set up a Solaris VM with port 8080 forwarded"
- "Share my project folder into a NetBSD VM"

The assistant will know the correct commands, flags, troubleshooting steps, and best practices — no need to memorize the CLI.

## Supported VMs

| Guest OS | x86_64 | aarch64 | riscv64 |
|---|---|---|---|
| FreeBSD (12.4–15.0, desktop: xfce/gnome/kde6) | :white_check_mark: | :white_check_mark: | :white_check_mark: |
| OpenBSD (7.3–7.6+) | :white_check_mark: | :white_check_mark: | :white_check_mark: |
| NetBSD | :white_check_mark: | :white_check_mark: | |
| DragonFlyBSD | :white_check_mark: | | |
| Solaris | :white_check_mark: | | |
| OmniOS | :white_check_mark: | | |
| OpenIndiana | :white_check_mark: | | |
| Haiku | :white_check_mark: | | |

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
- Starting VMs with any supported OS, version, and architecture
- Running commands inside VMs via SSH
- Detach/background mode
- Snapshot mode for ephemeral testing

### Networking
- SSH port forwarding
- Custom TCP/UDP port mapping
- Public access binding
- IPv6 configuration

### Shared Folders
- Host-to-guest directory sharing
- Multiple sync backends: rsync, sshfs, nfs, scp

### Display & Console
- Built-in VNC Web UI
- Remote VNC tunnels (Cloudflare, Localhost.run, Pinggy, Serveo)
- Serial console access
- Custom resolution and VGA settings

### Troubleshooting
- VM boot failures
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

## Contributing

Issues and PRs welcome at [github.com/anyvm-org/anyvm-skill](https://github.com/anyvm-org/anyvm-skill).

## License

MIT
