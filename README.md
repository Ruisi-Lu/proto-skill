# proto-skill

An agent skill for [proto](https://moonrepo.dev/docs/proto) — the pluggable multi-language version manager by moonrepo.

## What is proto?

[proto](https://github.com/moonrepo/proto) is a unified toolchain manager (like `nvm`, `pyenv`, `rustup`, etc.) built in Rust. It manages versions of Node.js, Bun, Go, Python, Rust, Deno, and more from a single CLI, using `.prototools` for per-directory version pinning.

## Install

```bash
npx skills add Ruisi-Lu/proto-skill
```

Or install globally:

```bash
npx skills add Ruisi-Lu/proto-skill -g -y
```

## What this skill teaches the agent

| Reference | Content |
|-----------|---------|
| `SKILL.md` | Overview, quick reference, version detection order, key paths |
| `references/commands.md` | Full CLI command reference (`install`, `pin`, `outdated`, `activate`, …) |
| `references/config.md` | `.prototools` settings — versions, `[env]`, `[settings]`, `[plugins]` |
| `references/plugins.md` | Creating non-WASM schema plugins and WASM plugins |

## Usage

Once installed, the agent will automatically use this skill when you ask about:

- Installing or managing language/tool versions with proto
- Configuring `.prototools`
- Setting up shell activation
- Writing proto plugins
- Debugging version detection issues

## License

MIT
