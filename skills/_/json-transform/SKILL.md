---
name: json-transform
description: Transform and query JSON data using jq and Python.
version: "1.0.0"
metadata:
  savfox:
    emoji: "🔄"
    requires:
      bins:
        - jq
      env: []
    install:
      - id: brew
        kind: brew
        formula: jq
        bins: [jq]
        label: Homebrew
      - id: apt
        kind: apt
        package: jq
        bins: [jq]
        label: APT
      - id: choco
        kind: choco
        package: jq
        bins: [jq]
        label: Chocolatey
---

# JSON Transform Skill

Transform, query, and manipulate JSON data. Useful for testing data pipeline skills and validating JSON processing.

## Pretty Print

```bash
echo '{json}' | jq .
cat file.json | jq .
```

## Extract Fields

Single field:
```bash
echo '{json}' | jq '.fieldName'
```

Nested field:
```bash
echo '{json}' | jq '.parent.child'
```

## Filter Arrays

Select items matching condition:
```bash
echo '{json}' | jq '[.items[] | select(.active == true)]'
```

First N items:
```bash
echo '{json}' | jq '.items[:5]'
```

## Map and Transform

Extract specific fields from array:
```bash
echo '{json}' | jq '[.users[] | {name: .name, email: .email}]'
```

Add computed field:
```bash
echo '{json}' | jq '[.items[] | . + {total: (.price * .quantity)}]'
```

## Aggregate

Count:
```bash
echo '{json}' | jq '.items | length'
```

Sum:
```bash
echo '{json}' | jq '[.items[].amount] | add'
```

Group by:
```bash
echo '{json}' | jq 'group_by(.category) | map({key: .[0].category, count: length})'
```

## Convert Formats

JSON to CSV:
```bash
echo '{json}' | jq -r '.users[] | [.id, .name, .email] | @csv'
```

JSON to TSV:
```bash
echo '{json}' | jq -r '.users[] | [.id, .name, .email] | @tsv'
```

## Merge JSON Files

```bash
jq -s '.[0] * .[1]' file1.json file2.json
```

## Guidelines

- Always use `jq .` to validate JSON before complex queries
- Use `-r` flag for raw string output (no quotes)
- Use `-e` flag to set exit code based on output (useful for conditionals)
- For very large files, use `jq --stream` for memory-efficient processing
- Wrap jq filters in single quotes to prevent shell interpretation
