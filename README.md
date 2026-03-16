# Aggressive Disk Cleaner

⚡ **Fast and aggressive disk space cleanup for GitHub Actions runners**

[![GitHub Actions](https://img.shields.io/badge/GitHub-Actions-blue?logo=github)](https://github.com/features/actions)
[![License](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)

## Features

- 🚀 **Fast**: Parallel cleanup operations in 6 phases
- 🧹 **Aggressive**: Removes up to **30GB+** of unnecessary files
- ⚙️ **Configurable**: Fine-grained control over what to keep/clean
- 🔧 **Flexible**: Use as workflow or composite action
- 📊 **Reporting**: Before/after disk usage comparison

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

### Maximum Cleanup (Recommended)

```yaml
- name: Free disk space aggressively
  uses: hoshiyomiX/aggressive-disk-cleaner@main
  with:
    # Keep only essential toolchains
    keep_node: true
    keep_python: true
    keep_go: true
    keep_java: false
    keep_dotnet: false
    keep_ruby: false
    
    # Clean everything else
    clean_android: true       # ~10GB
    clean_docker: true        # ~2GB
    clean_codeql: true        # ~1.7GB
    clean_ghc_haskell: true   # ~3.7GB
    clean_swift: true         # ~3.2GB
    clean_powershell: true    # ~1.3GB
    clean_browsers: true      # ~920MB
    clean_swap: true          # ~3GB
```

## Cleanup Targets

Based on deep scan analysis of GitHub Actions runner:

### 🔴 Large Items (>1GB)

| Item | Size | Input | Default |
|------|------|-------|---------|
| Android SDK/NDK | ~10GB | `clean_android` | `true` |
| Haskell/GHC | ~3.7GB | `clean_ghc_haskell` | `true` |
| Swift toolchain | ~3.2GB | `clean_swift` | `true` |
| .NET SDK | ~4.2GB | `keep_dotnet` | `true` |
| CodeQL | ~1.7GB | `clean_codeql` | `true` |
| LLVM extra | ~1.9GB | `clean_llvm_extra` | `true` |
| PowerShell | ~1.3GB | `clean_powershell` | `true` |
| Azure CLI | ~1.2GB | `clean_azure_cli` | `true` |
| Google Cloud SDK | ~1GB | `clean_google_cloud` | `true` |
| Julia | ~1GB | `clean_julia` | `true` |

### 🟡 Medium Items (100MB-1GB)

| Item | Size | Input | Default |
|------|------|-------|---------|
| Docker images | ~2GB | `clean_docker` | `true` |
| Python toolchains | ~1.7GB | `keep_python` | `true` |
| Java JDKs | ~1.5GB | `keep_java` | `true` |
| Rust toolchains | ~600MB | `clean_rust` | `true` |
| Node.js toolchains | ~570MB | `keep_node` | `true` |
| Go toolchains | ~1.1GB | `keep_go` | `true` |
| Miniconda | ~860MB | `clean_miniconda` | `true` |
| Browsers | ~920MB | `clean_browsers` | `true` |
| /home/packer | ~680MB | `clean_packer` | `true` |
| /etc/skel duplicates | ~680MB | `clean_skel` | `true` |

### 🟢 Small Items (<100MB)

| Item | Size | Input | Default |
|------|------|-------|---------|
| Linuxbrew | ~188MB | `clean_linuxbrew` | `true` |
| vcpkg | ~189MB | `clean_vcpkg` | `true` |
| Gradle/Maven | varies | `clean_gradle_maven` | `true` |
| Man pages/docs | ~234MB | `clean_docs` | `true` |
| Ruby toolchains | ~313MB | `keep_ruby` | `false` |

## Expected Results

| Metric | Before | After |
|--------|--------|-------|
| Disk Used | ~54GB | ~18-24GB |
| Disk Available | ~92GB | ~120-128GB |
| **Total Freed** | - | **~30-36GB** |
| Cleanup Time | - | ~60-90 seconds |

## Inputs Reference

### Keep Options (Toolchains to Preserve)

| Input | Default | Description |
|-------|---------|-------------|
| `keep_node` | `true` | Keep Node.js toolchains |
| `keep_python` | `true` | Keep Python toolchains |
| `keep_go` | `true` | Keep Go toolchains |
| `keep_java` | `true` | Keep Java JDKs |
| `keep_dotnet` | `true` | Keep .NET SDK |
| `keep_ruby` | `false` | Keep Ruby toolchains |

### Clean Options (Items to Remove)

| Input | Default | Size Freed |
|-------|---------|------------|
| `clean_android` | `true` | ~10GB |
| `clean_docker` | `true` | ~2GB |
| `clean_codeql` | `true` | ~1.7GB |
| `clean_ghc_haskell` | `true` | ~3.7GB |
| `clean_swift` | `true` | ~3.2GB |
| `clean_powershell` | `true` | ~1.3GB |
| `clean_julia` | `true` | ~1GB |
| `clean_google_cloud` | `true` | ~1GB |
| `clean_azure_cli` | `true` | ~1.2GB |
| `clean_miniconda` | `true` | ~860MB |
| `clean_llvm_extra` | `true` | ~1.9GB |
| `clean_browsers` | `true` | ~920MB |
| `clean_rust` | `true` | ~600MB |
| `clean_packer` | `true` | ~680MB |
| `clean_skel` | `true` | ~680MB |
| `clean_linuxbrew` | `true` | ~188MB |
| `clean_vcpkg` | `true` | ~189MB |
| `clean_gradle_maven` | `true` | varies |
| `clean_docs` | `true` | ~234MB |
| `clean_swap` | `false` | ~3GB |

## Use Case Examples

### Node.js Projects
```yaml
- uses: hoshiyomiX/aggressive-disk-cleaner@main
  with:
    keep_node: true
    keep_python: false
    keep_go: false
    keep_java: false
    keep_dotnet: false
```

### Python Projects
```yaml
- uses: hoshiyomiX/aggressive-disk-cleaner@main
  with:
    keep_node: false
    keep_python: true
    keep_go: false
    keep_java: false
    keep_dotnet: false
```

### Android Projects
```yaml
- uses: hoshiyomiX/aggressive-disk-cleaner@main
  with:
    clean_android: false  # Keep Android SDK
    clean_docker: false   # If using Docker builds
```

### Docker Builds
```yaml
- uses: hoshiyomiX/aggressive-disk-cleaner@main
  with:
    clean_docker: false   # Keep Docker
```

### Maximum Cleanup (All Tools Removed)
```yaml
- uses: hoshiyomiX/aggressive-disk-cleaner@main
  with:
    keep_node: false
    keep_python: false
    keep_go: false
    keep_java: false
    keep_dotnet: false
    keep_ruby: false
    clean_swap: true
```

## How It Works

1. **Phase 1**: Essential cleanups (APT, temp, caches)
2. **Phase 2**: Large tool removal (Android, CodeQL, GHC, Swift)
3. **Phase 3**: Medium tool removal (PowerShell, Julia, Cloud SDKs)
4. **Phase 4**: Optional toolchains (Node, Python, Go, Java)
5. **Phase 5**: Additional cleanups (Packer, Skel, Linuxbrew)
6. **Phase 6**: Swap file cleanup

All phases use parallel operations for maximum speed.

## Disk Scanner

Run deep analysis of runner disk usage:

```bash
gh workflow run disk-scan.yml
```

Results include:
- Complete /usr and /opt breakdown
- All toolchain versions and sizes
- Cleanup recommendations with calculated savings
- Essential vs conditional keep analysis

## Contributing

Contributions welcome! Please submit issues or pull requests.

## License

MIT License
