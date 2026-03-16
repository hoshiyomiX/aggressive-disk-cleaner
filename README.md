# Aggressive Disk Cleaner

⚡ **Fast and aggressive disk space cleanup for GitHub Actions runners**

[![GitHub Actions](https://img.shields.io/badge/GitHub-Actions-blue?logo=github)](https://github.com/features/actions)
[![License](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)

## Features

- 🚀 **Fast**: Parallel cleanup operations for maximum speed
- 🧹 **Aggressive**: Removes up to 30GB+ of unnecessary files
- ⚙️ **Configurable**: Fine-grained control over what to keep
- 🔧 **Reusable**: Use as a workflow or composite action

## Quick Start

### As a Composite Action

```yaml
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Free disk space
        uses: hoshiyomiX/aggressive-disk-cleaner@main
        
      - name: Your build step
        run: npm run build
```

### With Options

```yaml
- name: Free disk space
  uses: hoshiyomiX/aggressive-disk-cleaner@main
  with:
    skip_android: false    # Remove Android SDK
    skip_docker: false     # Remove Docker images
    keep_node: true        # Keep Node.js toolchains
    keep_python: true      # Keep Python toolchains
    keep_go: true          # Keep Go toolchains
    deep_clean: true       # Remove Swift, Haskell, Julia, etc.
```

### As a Workflow Call

```yaml
jobs:
  clean-and-build:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v4
        
      - name: Run disk cleaner
        uses: hoshiyomiX/aggressive-disk-cleaner/.github/workflows/aggressive-disk-cleaner.yml@main
        with:
          skip_android: false
          skip_docker: false
```

## What Gets Cleaned

### Always Removed
| Item | Size | Description |
|------|------|-------------|
| CodeQL | ~1.7GB | Security analysis toolchain |
| Rust toolchains | ~1.8GB | rustup & cargo (all users) |
| PyPy | ~521MB | Python alternative implementation |
| Ruby toolchains | ~313MB | Ruby versions in toolcache |
| Java caches | Varies | .gradle & .m2 caches |
| .NET caches | Varies | .nuget cache |
| APT cache | ~229MB | Package manager cache |
| Man pages & docs | ~234MB | Documentation files |
| Linuxbrew git | ~500MB+ | Homebrew git objects |
| PowerShell modules | ~100MB+ | Microsoft Graph modules |

### Optional Removal (configurable)
| Item | Size | Input |
|------|------|-------|
| Android SDK/NDK | ~5GB+ | `skip_android: false` |
| Docker images | ~840MB+ | `skip_docker: false` |
| Node.js toolchains | ~571MB | `keep_node: false` |
| Python toolchains | ~1.6GB | `keep_python: false` |
| Go toolchains | ~1GB | `keep_go: false` |

### Deep Clean (when `deep_clean: true`)
| Item | Size | Description |
|------|------|-------------|
| Swift | ~2GB+ | Swift toolchain |
| Haskell/GHC | ~2GB+ | GHC compiler |
| Julia | ~500MB+ | Julia language |
| Firefox | ~287MB | Browser (not needed for builds) |
| Chromium | ~100MB+ | Browser |
| LLVM versions | ~2GB+ | Multiple LLVM versions |

## Expected Results

| Metric | Before | After |
|--------|--------|-------|
| Disk Used | ~54GB | ~25GB |
| Disk Available | ~92GB | ~120GB |
| Cleanup Time | - | ~30-60 seconds |

## Inputs

| Input | Type | Default | Description |
|-------|------|---------|-------------|
| `skip_android` | boolean | `false` | Skip Android SDK cleanup |
| `skip_docker` | boolean | `false` | Skip Docker cleanup |
| `keep_node` | boolean | `true` | Keep Node.js toolchains |
| `keep_python` | boolean | `true` | Keep Python toolchains |
| `keep_go` | boolean | `true` | Keep Go toolchains |
| `deep_clean` | boolean | `true` | Remove large development tools |

## Use Cases

### Node.js Projects
```yaml
- uses: hoshiyomiX/aggressive-disk-cleaner@main
  with:
    keep_python: false
    keep_go: false
```

### Python Projects
```yaml
- uses: hoshiyomiX/aggressive-disk-cleaner@main
  with:
    keep_node: false
    keep_go: false
```

### Mobile Development (Android)
```yaml
- uses: hoshiyomiX/aggressive-disk-cleaner@main
  with:
    skip_android: true
    keep_python: false
    keep_go: false
```

### Docker Builds
```yaml
- uses: hoshiyomiX/aggressive-disk-cleaner@main
  with:
    skip_docker: true
```

### Maximum Cleanup
```yaml
- uses: hoshiyomiX/aggressive-disk-cleaner@main
  with:
    keep_node: false
    keep_python: false
    keep_go: false
    deep_clean: true
```

## How It Works

1. **Parallel Execution**: Uses subshells to run cleanup operations in parallel for speed
2. **Safe Defaults**: Keeps common build toolchains (Node.js, Python, Go) by default
3. **Selective Removal**: Configurable options for project-specific needs
4. **Verification**: Reports disk space before and after cleanup

## Disk Scanner Workflow

This repository also includes a disk scanner workflow to analyze disk usage:

```yaml
# Trigger manually
gh workflow run disk-scan.yml
```

Results are uploaded as artifacts for analysis.

## Contributing

Contributions are welcome! Please feel free to submit issues or pull requests.

## License

MIT License - see [LICENSE](LICENSE) for details.
