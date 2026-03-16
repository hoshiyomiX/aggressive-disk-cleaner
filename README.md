# Aggressive Disk Cleaner

Fast and aggressive disk space cleanup workflow for GitHub Actions runners.

## Purpose

This repository provides GitHub Actions workflows to:
1. **Scan** - Analyze disk usage on GitHub Actions runners
2. **Clean** - Aggressively free up disk space for build processes

## Workflows

### 1. Disk Scanner (`disk-scan.yml`)
Scans the runner's disk to identify space usage patterns.

### 2. Disk Cleaner (Coming Soon)
Aggressively removes unnecessary files to maximize free disk space.

## Target Cleanup Items

Based on typical GitHub Actions runner patterns, the cleaner targets:

- **Toolchain caches**: Python, Node.js, Go, Rust, Java, Ruby, .NET
- **Language caches**: pip, npm, cargo, gradle, maven, nuget, gem
- **System caches**: APT, Docker images, Snap packages
- **Unused files**: Locales, documentation, man pages

## Usage

```yaml
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Free disk space
        uses: hoshiyomiX/aggressive-disk-cleaner@main
```

## License

MIT
