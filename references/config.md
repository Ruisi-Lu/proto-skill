# `.prototools` Configuration

Full reference: https://moonrepo.dev/docs/proto/config

`.prototools` is a TOML file used to pin tool versions and configure proto settings. It can exist at:

| Location key | Path |
|---|---|
| `local` | `./.prototools` (current directory) |
| `user` | `~/.prototools` |
| `global` | `~/.proto/.prototools` |

**Recommendation:**
- Default/fallback versions → `global`
- Project-specific versions → `local`
- Developer settings → `user`

---

## Pinning versions

```toml
node = "22"           # Partial version — resolves to latest 22.x.x
npm = "10.7.0"        # Exact version
go = "~1.22"          # Tilde range
rust = "stable"       # Alias
bun = "latest"
python = "3.12"
proto = "0.44.0"      # Pin proto version itself
```

---

## `[env]` — Per-directory environment variables

Variables are injected for all tools and when `proto activate` is used.
They do **not** override existing shell variables.

```toml
[env]
DEBUG = "*"
PORT = "3000"
# Remove an inherited variable:
CI = false
# Variable substitution:
MY_PATH = "${HOME}/custom"

# Load from a dotenv file:
file = ".env"
```

---

## `[settings]` — proto behaviour

```toml
[settings]
# Auto-install missing version when proto run is used (requires shim)
auto-install = true

# Auto-clean unused tools in background
auto-clean = true

# Control which built-in plugins are enabled
builtin-plugins = true           # All (default)
builtin-plugins = false          # Disable all
builtin-plugins = ["node", "bun"] # Only these

# Duration in seconds to cache downloaded plugins (default: 30 days)
cache-duration = 86400

# Version detection strategy
# "first-available" (default) | "prefer-prototools" | "only-prototools"
detect-strategy = "prefer-prototools"

# Auto-pin resolved 'latest' alias after install
# "global" | "local" | "user"
pin-latest = "local"

# Opt out of anonymous telemetry
telemetry = false

# Enable lockfile (.protolock)
unstable-lockfile = true
```

---

## `[plugins]` — Register custom tools

```toml
[plugins]
# GitHub release (TOML schema plugin)
my-cli = "github:owner/repo"

# GitHub with specific file
my-cli = "github:owner/repo/path/to/schema.toml"

# Absolute URL
my-cli = "https://example.com/tool/schema.toml"

# Local file (for development)
my-cli = "file://./path/to/schema.toml"
```

After adding a plugin, install with:
```bash
proto install my-cli
```

---

## Resolution mode

Controls which `.prototools` files are loaded per command.

```bash
# Via flag
proto install --config-mode all

# Via env var
PROTO_CONFIG_MODE=all proto install
```

| Mode | Files loaded |
|------|-------------|
| `local` | Only `./prototools` |
| `global` | Only `~/.proto/.prototools` |
| `upwards` | Traverse up from cwd (default for most commands) |
| `upwards-global` / `all` | Traverse up + global |

---

## Environment-specific config

When `PROTO_ENV=production`, proto also loads `.prototools.production` (merged on top of `.prototools`).

```bash
PROTO_ENV=staging proto install
```

Files loaded (highest to lowest priority):
```
./.prototools.staging
./.prototools
~/.prototools.staging
~/.prototools
~/.proto/.prototools
```
