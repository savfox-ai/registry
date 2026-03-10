---
name: echo-test
description: Simple echo skill for testing skill loading and execution.
version: "1.0.0"
metadata:
  savfox:
    emoji: "🔔"
    requires:
      bins: []
      env: []
    install: []
---

# Echo Test Skill

A minimal skill for verifying that the skill system loads and executes correctly. All commands use only built-in shell utilities.

## Echo Message

```bash
echo "Hello from echo-test skill!"
```

## Echo with Variables

```bash
MESSAGE="skill test"
echo "Echo test: ${MESSAGE}"
```

## Echo to Stderr

```bash
echo "This goes to stderr" >&2
```

## Echo with Exit Code

Success:
```bash
echo "OK" && exit 0
```

Failure:
```bash
echo "FAIL" >&2 && exit 1
```

## Guidelines

- Use this skill to verify skill discovery, loading, and basic execution
- All commands are portable across Linux, macOS, and Windows (Git Bash)
- No external dependencies required
