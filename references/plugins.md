# proto Plugins

Full reference: https://moonrepo.dev/docs/proto/plugins

proto supports two plugin types:

| Type | Use case |
|------|---------|
| **Non-WASM (schema)** | Simple CLIs / pre-built binaries. JSON, TOML, or YAML. |
| **WASM** | Advanced logic, version inference, custom lifecycle hooks. Rust → WASM. |

---

## Non-WASM (Schema) Plugin

Ideal for distributing a pre-built binary that proto should download and manage.

### Minimal `schema.toml`

```toml
name = "My Tool"
type = "cli"   # "language" | "dependency-manager" | "package-manager" | "cli"

[platform.linux]
download-file = "mytool-{arch}-unknown-linux-{libc}.tar.gz"
archive-prefix = "mytool-linux"
exe-path = "bin/mytool"
checksum-file = "mytool-{arch}-unknown-linux-{libc}.sha256"

[platform.macos]
download-file = "mytool-{arch}-apple-darwin.tar.xz"
archive-prefix = "mytool-macos"
exe-path = "bin/mytool"
checksum-file = "mytool-{arch}-apple-darwin.sha256"

[platform.windows]
download-file = "mytool-{arch}-pc-windows-msvc.zip"
archive-prefix = "mytool-windows"
exe-path = "bin/mytool.exe"
checksum-file = "mytool-{arch}-pc-windows-msvc.sha256"

[install]
download-url = "https://github.com/owner/repo/releases/download/v{version}/{download_file}"
checksum-url = "https://github.com/owner/repo/releases/download/v{version}/{checksum_file}"

[install.arch]
aarch64 = "arm64"
x86_64 = "x64"

[install.exes.mytool]
exe-path = "bin/mytool"
primary = true
```

### Available tokens

| Token | Value |
|-------|-------|
| `{version}` | Full semver, e.g. `1.2.3` |
| `{versionMajor}` | Major only, e.g. `1` |
| `{versionMinor}` | Minor only, e.g. `2` |
| `{versionPatch}` | Patch only, e.g. `3` |
| `{arch}` | CPU arch (`x86_64`, `aarch64`, …) |
| `{os}` | OS (`linux`, `macos`, `windows`) |
| `{libc}` | Linux libc (`gnu` or `musl`) |

### Executable options (`[install.exes.<name>]`)

```toml
[install.exes.mytool]
exe-path = "bin/mytool"
primary = true                  # Mark as primary executable
no-bin = false                  # Create symlink in ~/.proto/bin
no-shim = false                 # Create shim in ~/.proto/shims
shim-before-args = ["--verbose"] # Prepend args in shim
shim-after-args = []
shim-env-vars = { MY_VAR = "1" }
```

### Global packages

```toml
[packages]
globals-lookup-dirs = ["$MYTOOL_HOME/bin", "$HOME/.mytool/bin"]
globals-prefix = "mytool-"
```

---

## Enabling a Plugin in `.prototools`

```toml
[plugins]
# GitHub release asset (recommended for distribution)
mytool = "github:owner/repo"

# Specific file in repo
mytool = "github:owner/repo/path/to/schema.toml"

# Remote URL
mytool = "https://example.com/mytool.toml"

# Local development
mytool = "file://./schema.toml"
```

```bash
proto install mytool
```

---

## Publishing to proto's Registry

1. Fork [github.com/moonrepo/proto](https://github.com/moonrepo/proto)
2. Add an entry to `registry/` (see existing entries for format)
3. Open a pull request

Published plugins become discoverable via `proto plugin search`.

---

## WASM Plugin (Advanced)

For complex tools that need custom logic (version inference, lifecycle hooks):

- Written in Rust, compiled to WASM
- Uses the [`proto_pdk`](https://github.com/moonrepo/proto/tree/master/crates/pdk) crate
- Hook into: `load_versions`, `resolve_version`, `detect_version_files`, `install_hook`, `post_install`, and more

See: https://moonrepo.dev/docs/proto/wasm-plugin
