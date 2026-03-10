# proto CLI Commands

Full reference: https://moonrepo.dev/docs/proto/commands

---

## `proto install` (alias: `proto i`)

Install one or all tools.

```bash
# Install all tools from .prototools
proto install

# Install a specific tool (latest)
proto install node
proto install deno 1.31
proto install bun canary

# Pin version to .prototools after install
proto install bun --pin           # pins to ./.prototools
proto install bun --pin global    # pins to ~/.proto/.prototools
proto install bun --pin user      # pins to ~/.prototools

# Force reinstall
proto install node --force

# Build from source (if supported)
proto install go --build

# Pass args to post-install hook
proto install go -- --no-gobin
```

---

## `proto uninstall` (alias: `proto ui`)

Remove a tool version from `~/.proto/tools/<tool>`.

```bash
proto uninstall deno 1.31   # Remove specific version
proto uninstall deno        # Remove all versions
```

---

## `proto pin`

Pin a version in a `.prototools` file without installing.

```bash
proto pin node 22                      # Update ./.prototools
proto pin node 22 --to global          # ~/.proto/.prototools
proto pin node 22 --to user            # ~/.prototools
proto pin node lts --resolve           # Resolve alias → semver first
proto pin node latest --resolve --tool-native  # Pin in package.json devEngines
```

---

## `proto versions` (replaces deprecated `proto list`)

List all installed versions of a tool.

```bash
proto versions node
proto versions node --aliases   # Include aliases
```

---

## `proto outdated`

Check for newer versions of all configured tools.

```bash
proto outdated
```

Output shows: Current / Newest (in range) / Latest / Config file.

---

## `proto run` (alias: `proto r`)

Run a tool binary after version detection. Prefer using shims directly.

```bash
proto run node                  # Auto-detect version
proto run node 22               # Explicit version
proto run bun -- run ./app.ts   # Pass args after --

# Via env var
PROTO_NODE_VERSION=22 proto run node
```

---

## `proto activate`

Activate proto for a shell session with `PATH` and env var injection. Runs on directory change via shell hook. **Recommended over shims for interactive shells.**

```bash
# Setup (add once to shell profile)
eval "$(proto activate bash)"          # ~/.bashrc or ~/.bash_profile
eval "$(proto activate zsh)"           # ~/.zshrc
proto activate fish | source           # ~/.config/fish/config.fish
proto activate pwsh | Out-String | Invoke-Expression   # $PROFILE (PowerShell)

# Flags
proto activate bash --no-shim          # Exclude shims path
proto activate bash --no-bin           # Exclude bin path
proto activate bash --config-mode all  # Include global ~/.proto/.prototools
proto activate bash --no-init          # Wait for cd, skip init trigger
```

---

## `proto upgrade`

Upgrade proto itself to the latest version.

```bash
proto upgrade
proto upgrade --canary   # Canary release
```

---

## `proto clean`

Remove old, unused tool versions and outdated plugins.

```bash
proto clean
proto clean --days 30   # Remove versions unused for 30+ days
```

---

## `proto status`

Show active tool versions for the current directory.

```bash
proto status
```

---

## `proto bin`

Print the path to a tool's binary.

```bash
proto bin node
proto bin node 22
```

---

## `proto setup`

Configure proto in the current shell by modifying the shell profile.

```bash
proto setup
proto setup --shell zsh
```

---

## `proto plugin add / remove`

Manage plugins in `.prototools`.

```bash
proto plugin add my-tool "github:owner/repo"
proto plugin remove my-tool
```

---

## Global options

| Option | Description |
|--------|-------------|
| `--config-mode` / `-c` | `global`, `local`, `upwards` (default), `all` |
| `PROTO_HOME` | Override proto home directory |
| `PROTO_<TOOL>_VERSION` | Override version for a specific tool |
| `PROTO_CONFIG_MODE` | Set resolution mode via env |
