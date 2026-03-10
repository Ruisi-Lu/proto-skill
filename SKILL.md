---
name: proto
description: Guide for proto — the pluggable multi-language version manager by moonrepo. Use when installing tools, pinning versions, configuring .prototools, writing plugins, or troubleshooting version detection.
version: 0.1.0
license: MIT
---

# proto

**Always check [moonrepo.dev/docs/proto](https://moonrepo.dev/docs/proto) for the latest API.**

proto is a pluggable, multi-language version manager (toolchain) built in Rust. It manages versions of programming languages and tooling (Node.js, Bun, Go, Python, Rust, Deno, …) via a single CLI, using a `.prototools` TOML config file for per-directory version pinning.

## References

- [./references/commands.md](./references/commands.md) — CLI command reference
- [./references/config.md](./references/config.md) — `.prototools` configuration
- [./references/plugins.md](./references/plugins.md) — Creating and publishing plugins

## When to Use

| Situation | Action |
|-----------|--------|
| Install / upgrade a language or tool | `proto install <tool> [version]` |
| Pin a version for a project | `proto pin <tool> <version>` |
| Check for outdated tools | `proto outdated` |
| Set up shell activation | `proto activate <shell>` |
| Add a custom tool via plugin | Edit `[plugins]` in `.prototools` |
| Create a new plugin | Use the non-WASM schema or WASM |
| Detect why wrong version is running | See version detection order |

## Quick Reference

### Install proto itself

```powershell
# Windows (PowerShell admin)
irm https://moonrepo.dev/install/proto.ps1 | iex
```

```bash
# macOS / Linux / WSL
bash <(curl -fsSL https://moonrepo.dev/install/proto.sh)
```

### Common commands

```bash
proto install node          # Install latest Node.js
proto install node 22       # Install specific version
proto install               # Install all tools in .prototools

proto pin node 22 --resolve # Pin resolved version locally
proto pin node 22 --to global

proto outdated              # Check for newer versions
proto upgrade               # Upgrade proto itself

proto run node -- --version # Run a tool (or just use shim: node --version)
proto versions node         # List installed versions
proto uninstall node 20     # Remove a specific version
```

### Minimal `.prototools`

```toml
node = "22"
npm = "10"
go = "~1.22"
rust = "stable"

[settings]
auto-install = true
pin-latest = "local"
```

### Shell activation (recommended over shims)

```bash
# Add to end of ~/.zshrc / ~/.bashrc
eval "$(proto activate bash)"   # bash
eval "$(proto activate zsh)"    # zsh
proto activate fish | source    # fish
```

```powershell
# Add to $PROFILE
proto activate pwsh | Out-String | Invoke-Expression
```

## Version Detection Order

proto resolves versions in this order (first match wins):

1. Explicit CLI argument: `proto run node 22.0.0`
2. Environment variable: `PROTO_NODE_VERSION=22.0.0`
3. `.prototools` (traversing up from cwd)
4. Tool-native files (`.nvmrc`, `package.json#devEngines`, etc.)
5. Global pin at `~/.proto/.prototools`

## Key Paths

| Path | Purpose |
|------|---------|
| `~/.proto/` | proto home directory (`$PROTO_HOME`) |
| `~/.proto/tools/<tool>/<version>/` | Installed tool binaries |
| `~/.proto/shims/` | Shim wrappers (added to `PATH`) |
| `~/.proto/bin/` | Symlinks for pinned tools |
| `~/.proto/.prototools` | Global version pins |
| `./.prototools` | Project-local version pins |
| `~/.prototools` | User-level config |
