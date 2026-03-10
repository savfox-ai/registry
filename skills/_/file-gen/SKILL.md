---
name: file-gen
description: Generate test files and sample data for development and testing.
version: "1.0.0"
metadata:
  savfox:
    emoji: "📝"
    requires:
      bins: []
      env: []
    install: []
---

# File Gen Skill

Generate various types of test files and sample data. Useful for testing file I/O, parsing, and data processing skills.

## Generate JSON File

```bash
cat > test-data.json << 'EOF'
{
  "users": [
    {"id": 1, "name": "Alice", "email": "alice@example.com", "active": true},
    {"id": 2, "name": "Bob", "email": "bob@example.com", "active": false},
    {"id": 3, "name": "Charlie", "email": "charlie@example.com", "active": true}
  ],
  "metadata": {
    "generated": "test",
    "count": 3
  }
}
EOF
```

## Generate CSV File

```bash
cat > test-data.csv << 'EOF'
id,name,email,score,department
1,Alice,alice@example.com,95,Engineering
2,Bob,bob@example.com,87,Marketing
3,Charlie,charlie@example.com,92,Engineering
4,Diana,diana@example.com,88,Design
5,Eve,eve@example.com,91,Marketing
EOF
```

## Generate YAML File

```bash
cat > test-config.yaml << 'EOF'
server:
  host: localhost
  port: 8080
database:
  url: postgres://localhost/testdb
  pool_size: 5
logging:
  level: debug
  format: json
EOF
```

## Generate TOML File

```bash
cat > test-config.toml << 'EOF'
[package]
name = "test-project"
version = "0.1.0"
edition = "2021"

[dependencies]
serde = { version = "1.0", features = ["derive"] }
tokio = { version = "1", features = ["full"] }
EOF
```

## Generate Random Text File

```bash
head -c 1024 /dev/urandom | base64 > random-data.txt
```

## Generate Numbered Lines

```bash
seq 1 100 | while read n; do echo "Line $n: test data $(date +%s%N)"; done > lines.txt
```

## Guidelines

- All generated files use safe, predictable test data
- Files are created in the current working directory
- Use `cat > file << 'EOF'` (quoted EOF) to prevent variable expansion
- Clean up test files after use with `rm -f <filename>`
