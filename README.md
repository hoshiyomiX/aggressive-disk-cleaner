<div align="center">

# 🧹 Aggressive Disk Cleaner

**High-performance disk space cleanup for GitHub Actions runners**

*Free up to 30GB+ in under 90 seconds*

[![GitHub Actions](https://img.shields.io/badge/GitHub-Actions-2088FF?style=for-the-badge&logo=githubactions&logoColor=white)](https://github.com/features/actions)
[![Ubuntu](https://img.shields.io/badge/Ubuntu-Latest-E95420?style=for-the-badge&logo=ubuntu&logoColor=white)](https://ubuntu.com/)
[![License](https://img.shields.io/badge/License-MIT-green?style=for-the-badge)](LICENSE)

[Features](#-features) • [Quick Start](#-quick-start) • [Configuration](#️-configuration) • [Cleanup Targets](#-cleanup-targets) • [Examples](#-use-case-examples)

</div>

---

## 📋 Overview

GitHub Actions runners come with ~84GB of available disk space, but pre-installed toolchains consume over 50GB. When building large projects, Docker images, or monorepos, you may encounter "No space left on device" errors.

**Aggressive Disk Cleaner** solves this by intelligently removing unnecessary toolchains and cached files while preserving what your workflow needs.

### Why Use This?

| Problem | Solution |
|---------|----------|
| ❌ Build fails with "no space left on device" | ✅ Free 30GB+ before build starts |
| ❌ Docker image builds run out of space | ✅ Clean Docker cache and unused images |
| ❌ Large monorepo checkout exceeds limit | ✅ Remove unused toolchains (Android, Swift, GHC) |
| ❌ CI/CD pipelines inconsistent | ✅ Predictable, configurable cleanup |

---

## ✨ Features

- 🚀 **Blazing Fast** — Parallel cleanup operations complete in 60-90 seconds
- 🧹 **Aggressive** — Removes up to **30GB+** of unnecessary files
- ⚙️ **Configurable** — Fine-grained control with 25 input options
- 📦 **Flexible** — Use as a reusable workflow or composite action
- 📊 **Transparent** — Before/after disk usage in job summary
- 🔒 **Safe** — Never removes files needed for your specific build

---

## 🚀 Quick Start

### Option 1: Composite Action (Recommended)

Add this step before your build:

```yaml
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: 🧹 Free disk space
        uses: hoshiyomiX/aggressive-disk-cleaner@v1
        
      - name: 📦 Your build step
        run: npm run build
```

### Option 2: Reusable Workflow

```yaml
jobs:
  cleanup:
    uses: hoshiyomiX/aggressive-disk-cleaner/.github/workflows/aggressive-disk-cleaner.yml@v1
    with:
      keep_node: true
      keep_python: true
      
  build:
    needs: cleanup
    runs-on: ubuntu-latest
    steps:
      - run: npm run build
```

---

## ⚙️ Configuration

### Keep Options (Preserve Toolchains)

These options let you **keep** toolchains needed for your build. Set to `true` to preserve, `false` to remove.

| Input | Default | Size Preserved | Description |
|-------|---------|----------------|-------------|
| `keep_apk_build` | `false` | ~7GB | **APK Build Mode** - Keeps Android SDK (build-tools/platforms), Java, Node.js, Gradle cache. Removes NDK to save ~3GB |
| `keep_node` | `true` | ~570MB | Node.js toolchains (18.x, 20.x, 22.x) |
| `keep_python` | `true` | ~1.7GB | Python toolchains (3.9, 3.10, 3.11, 3.12) |
| `keep_go` | `true` | ~1.1GB | Go toolchains (1.21.x, 1.22.x) |
| `keep_java` | `true` | ~1.5GB | Java JDKs (8, 11, 17, 21) |
| `keep_dotnet` | `true` | ~4.2GB | .NET SDK (6.0, 7.0, 8.0) |
| `keep_ruby` | `false` | ~313MB | Ruby toolchains |

### Clean Options (Remove Items)

These options let you **remove** items not needed for your build. Set to `true` to clean, `false` to keep.

#### Large Items (>1GB)

| Input | Default | Size Freed | Description |
|-------|---------|------------|-------------|
| `clean_android` | `true` | ~10GB | Android SDK, NDK, build-tools, platforms |
| `clean_ghc_haskell` | `true` | ~3.7GB | GHC, Haskell toolchain, cabal |
| `clean_swift` | `true` | ~3.2GB | Swift compiler and stdlib |
| `clean_llvm_extra` | `true` | ~1.9GB | Older LLVM versions (keeps latest) |
| `clean_codeql` | `true` | ~1.7GB | CodeQL CLI and queries |
| `clean_azure_cli` | `true` | ~1.2GB | Azure CLI and extensions |
| `clean_powershell` | `true` | ~1.3GB | PowerShell modules |
| `clean_google_cloud` | `true` | ~1GB | Google Cloud SDK |
| `clean_julia` | `true` | ~1GB | Julia language |
| `clean_miniconda` | `true` | ~860MB | Miniconda distribution |
| `clean_docker` | `true` | ~2GB | Docker images, volumes, build cache |

#### Medium Items (100MB-1GB)

| Input | Default | Size Freed | Description |
|-------|---------|------------|-------------|
| `clean_browsers` | `true` | ~1.7GB | Firefox, Chromium, MS Edge |
| `clean_rust` | `true` | ~600MB | Rust toolchains (rustup, cargo) |
| `clean_packer` | `true` | ~680MB | /home/packer caches |
| `clean_skel` | `true` | ~680MB | /etc/skel duplicates |
| `clean_misc` | `true` | ~1.2GB | Docs, AWS CLI, pipx tools |
| `clean_build_caches` | `true` | ~400MB | Gradle, Maven, vcpkg caches |
| `clean_linuxbrew` | `true` | ~188MB | Homebrew for Linux |

#### Special Options

| Input | Default | Size Freed | Description |
|-------|---------|------------|-------------|
| `clean_swap` | `false` | ~3GB | Disable and remove swap file |

---

## 🎯 Cleanup Targets

Based on deep analysis of GitHub Actions `ubuntu-latest` runner:

```
┌─────────────────────────────────────────────────────────────────┐
│                    DISK USAGE BREAKDOWN                         │
├─────────────────────────────────────────────────────────────────┤
│  /usr                    37GB  ████████████████████             │
│  ├─ /usr/local          19GB   ██████████                       │
│  ├─ /usr/share          9.6GB  █████                            │
│  └─ /usr/lib            6.6GB  ███                              │
│                                                                 │
│  /opt                   8.7GB  █████                            │
│  ├─ hostedtoolcache     5.4GB  ███                              │
│  └─ microsoft           3.3GB  ██                               │
│                                                                 │
│  Android SDK            10GB   ██████                           │
│  .NET SDK               4.2GB  ██                               │
│  GHC/Haskell            3.7GB  ██                               │
│  Swift                  3.2GB  ██                               │
│  CodeQL                 1.7GB  █                                │
│  Docker                 2GB    █                                │
└─────────────────────────────────────────────────────────────────┘
```

### Expected Results

| Metric | Before | After | Improvement |
|--------|--------|-------|-------------|
| Disk Used | ~54GB | ~18-24GB | -30GB |
| Disk Available | ~92GB | ~120-128GB | +30GB |
| Cleanup Time | — | 60-90 seconds | — |

---

## 💡 Use Case Examples

### Node.js / JavaScript Projects

```yaml
- name: 🧹 Free disk space for Node.js build
  uses: hoshiyomiX/aggressive-disk-cleaner@v1
  with:
    keep_node: true
    keep_python: false
    keep_go: false
    keep_java: false
    keep_dotnet: false
```

### Python / ML Projects

```yaml
- name: 🧹 Free disk space for Python build
  uses: hoshiyomiX/aggressive-disk-cleaner@v1
  with:
    keep_node: false
    keep_python: true
    keep_go: false
    keep_java: false
    keep_dotnet: false
    clean_miniconda: false  # Keep conda for ML projects
```

### Android Projects

```yaml
- name: 🧹 Free disk space for Android build
  uses: hoshiyomiX/aggressive-disk-cleaner@v1
  with:
    clean_android: false    # Keep Android SDK!
    keep_java: true         # Keep JDK for Android
    keep_dotnet: false
    clean_ghc_haskell: true
    clean_swift: true
```

### APK Builds (Android App / React Native / Capacitor)

```yaml
- name: 🧹 Free disk space for APK build
  uses: hoshiyomiX/aggressive-disk-cleaner@v1
  with:
    keep_apk_build: true    # One-liner: keeps SDK build-tools/platforms, Java, Node.js, Gradle cache
```

This single option automatically:
- ✅ **Keeps** Android SDK build-tools, platforms, cmdline-tools (~7GB)
- ✅ **Keeps** Java JDK (~1.5GB) - Required for Gradle
- ✅ **Keeps** Node.js (~570MB) - For React Native/Capacitor
- ✅ **Keeps** Gradle/Maven cache (~400MB) - Faster builds
- ❌ **Removes** Android NDK (~3GB) - Not needed for most APK builds

> **Note:** Using `keep_apk_build: true` overrides individual `clean_android`, `keep_java`, `keep_node`, and `clean_build_caches` settings.

### Docker / Container Builds

```yaml
- name: 🧹 Free disk space for Docker build
  uses: hoshiyomiX/aggressive-disk-cleaner@v1
  with:
    clean_docker: false     # Keep Docker daemon
    clean_android: true     # Remove unused
    clean_ghc_haskell: true
    clean_swift: true
```

### Go Projects

```yaml
- name: 🧹 Free disk space for Go build
  uses: hoshiyomiX/aggressive-disk-cleaner@v1
  with:
    keep_go: true
    keep_node: false
    keep_python: false
    keep_java: false
    keep_dotnet: false
```

### Java / Kotlin Projects

```yaml
- name: 🧹 Free disk space for Java build
  uses: hoshiyomiX/aggressive-disk-cleaner@v1
  with:
    keep_java: true
    keep_node: false
    keep_python: false
    keep_go: false
    keep_dotnet: false
    clean_build_caches: false  # Keep Gradle/Maven caches
```

### .NET / C# Projects

```yaml
- name: 🧹 Free disk space for .NET build
  uses: hoshiyomiX/aggressive-disk-cleaner@v1
  with:
    keep_dotnet: true
    keep_node: false
    keep_python: false
    keep_go: false
    keep_java: false
```

### Maximum Cleanup (All Tools Removed)

```yaml
- name: 🧹 Maximum disk space cleanup
  uses: hoshiyomiX/aggressive-disk-cleaner@v1
  with:
    keep_node: false
    keep_python: false
    keep_go: false
    keep_java: false
    keep_dotnet: false
    keep_ruby: false
    clean_swap: true
    clean_docker: true
```

---

## 🔧 How It Works

The cleanup process runs in 6 optimized phases:

```
Phase 1: Essential Cleanups (Always Run)
├── APT package cache
├── Temp directories (/tmp, /var/tmp)
├── Runner caches
├── pip caches
├── npm/yarn caches
└── PyPy toolchain

Phase 2: Large Tool Cleanups
├── CodeQL (~1.7GB)
├── Android SDK/NDK (~10GB)
├── Docker images/volumes (~2GB)
├── GHC/Haskell (~3.7GB)
├── Swift (~3.2GB)
└── .NET SDK (if not keeping)

Phase 3: Medium Tool Cleanups
├── PowerShell (~1.3GB)
├── Julia (~1GB)
├── Google Cloud SDK (~1GB)
├── Azure CLI (~1.2GB)
├── Miniconda (~860MB)
├── LLVM extra versions (~1.9GB)
└── Browsers (~1.7GB)

Phase 4: Optional Toolchain Cleanups
├── Node.js (if not keeping)
├── Python (if not keeping)
├── Go (if not keeping)
├── Java (if not keeping)
├── Ruby (if not keeping)
└── Rust (~600MB)

Phase 5: Additional Cleanups
├── /home/packer caches
├── /etc/skel duplicates
├── Linuxbrew
├── Build caches (Gradle/Maven/vcpkg)
└── Misc (docs, AWS CLI, pipx)

Phase 6: Swap Cleanup (Optional)
└── Disable and remove swap file (~3GB)
```

All phases use **parallel bash subshells** for maximum speed.

---

## 📊 Disk Scanner

Analyze disk usage on the runner with the included scanner workflow:

```bash
# Trigger via GitHub CLI
gh workflow run disk-scan.yml

# Or via API
gh api repos/{owner}/{repo}/actions/workflows/disk-scan.yml/dispatches \
  -f ref="main"
```

The scanner provides:
- Complete `/usr` and `/opt` directory breakdown
- All toolchain versions and sizes
- Cleanup recommendations with calculated savings
- Essential vs conditional keep analysis

---

## 🤝 Contributing

Contributions are welcome! Here's how you can help:

1. **Report Issues** — Found a bug or have a suggestion? [Open an issue](../../issues/new)
2. **Submit PRs** — Fork the repo and submit a pull request
3. **Improve Docs** — Help make documentation clearer
4. **Share Results** — Share your cleanup results and configurations

### Development

```bash
# Clone the repository
git clone https://github.com/hoshiyomiX/aggressive-disk-cleaner.git
cd aggressive-disk-cleaner

# Run the disk scanner
gh workflow run disk-scan.yml

# Test the cleaner
gh workflow run aggressive-disk-cleaner.yml
```

---

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

---

## ⚠️ Disclaimer

This action removes pre-installed toolchains from GitHub Actions runners. While it preserves specified toolchains, ensure you:

- Set `keep_*` options correctly for your build requirements
- Set `clean_*` options to `false` for items you need
- Test in a non-critical workflow first
- Review the job summary to verify cleanup results

---

<div align="center">

**[⬆ Back to Top](#-aggressive-disk-cleaner)**

Made with ❤️ by [hoshiyomiX](https://github.com/hoshiyomiX)

If this project helps you, consider giving it a ⭐!

</div>
