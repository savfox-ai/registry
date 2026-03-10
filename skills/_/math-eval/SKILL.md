---
name: math-eval
description: Evaluate math expressions using shell arithmetic and Python.
version: "1.0.0"
metadata:
  savfox:
    emoji: "🧮"
    requires:
      bins:
        - python3
      env: []
    install:
      - id: brew
        kind: brew
        formula: python@3
        bins: [python3]
        label: Homebrew
---

# Math Eval Skill

Evaluate mathematical expressions for testing skill argument passing and output parsing.

## Basic Arithmetic (Shell)

```bash
echo $((2 + 3))
echo $((10 * 5 - 3))
echo $((100 / 7))
```

## Floating Point (Python)

```bash
python3 -c "print({expression})"
```

Examples:
```bash
python3 -c "print(3.14 * 2)"
python3 -c "print(2 ** 10)"
python3 -c "print(round(100 / 3, 4))"
```

## Advanced Math

Square root:
```bash
python3 -c "import math; print(math.sqrt({number}))"
```

Trigonometry:
```bash
python3 -c "import math; print(math.sin(math.radians({degrees})))"
```

Factorial:
```bash
python3 -c "import math; print(math.factorial({n}))"
```

## Random Number

```bash
python3 -c "import random; print(random.randint({min}, {max}))"
```

## Guidelines

- Use shell arithmetic `$(( ))` for simple integer math
- Use Python for floating point, advanced functions, and higher precision
- Always use `print()` to output results for easy parsing
- Wrap expressions carefully to avoid shell interpretation issues
