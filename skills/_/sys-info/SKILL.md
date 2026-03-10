---
name: sys-info
description: Retrieve system information including OS, CPU, memory, and disk usage.
version: "1.0.0"
metadata:
  savfox:
    emoji: "💻"
    requires:
      bins: []
      env: []
    install: []
---

# Sys Info Skill

Gather system information for diagnostics and testing. All commands use standard utilities available on most Unix-like systems.

## OS Info

```bash
uname -a
```

Distro details (Linux):
```bash
cat /etc/os-release 2>/dev/null || sw_vers 2>/dev/null || echo "Unknown OS"
```

## CPU Info

Core count:
```bash
nproc 2>/dev/null || sysctl -n hw.ncpu 2>/dev/null || echo "unknown"
```

CPU model:
```bash
grep -m1 "model name" /proc/cpuinfo 2>/dev/null || sysctl -n machdep.cpu.brand_string 2>/dev/null
```

## Memory Usage

```bash
free -h 2>/dev/null || vm_stat 2>/dev/null
```

## Disk Usage

```bash
df -h /
```

Current directory size:
```bash
du -sh .
```

## Uptime

```bash
uptime
```

## Environment

Current user:
```bash
whoami
```

Shell:
```bash
echo "$SHELL"
```

PATH directories:
```bash
echo "$PATH" | tr ':' '\n'
```

## Guidelines

- Commands include fallbacks for both Linux and macOS
- On Windows, use these commands under Git Bash or WSL
- Memory and CPU commands may not work in all container environments
- Prefer `free -h` on Linux and `vm_stat` on macOS for memory info
