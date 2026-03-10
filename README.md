# Savfox AI Skill Registry

The official registry for distributing and discovering [Savfox](https://github.com/savfox-ai/savfox) AI skills.

## Structure

```
skills.json              # Global skill index
skills/
  {user}/                # User/org namespace
    {skill-slug}/        # One directory per skill
      _meta.toml         # Skill metadata
      SKILL.md           # Skill instructions (consumed by AI agents)
```

## `_meta.toml` Format

Each skill directory contains a `_meta.toml` with the following sections:

```toml
# Identity
slug        = "my-skill"
name        = "My Skill"
description = "What this skill does."
version     = "1.0.0"
emoji       = "🔧"

# Authorship & Legal
author  = "username"
license = "Apache-2.0"

# Discovery
keywords   = ["keyword1", "keyword2"]
categories = ["category"]

# System Requirements
[requires]
bins = ["curl"]          # Required executables on $PATH
env  = ["API_KEY"]       # Required environment variables

# Dependency Installation Methods
[[install]]
id      = "brew"
kind    = "brew"
formula = "curl"
bins    = ["curl"]
label   = "Homebrew"

# External Links
[links]
repository = "https://github.com/..."
```

## `skills.json` Format

The top-level index lists all registered skills:

```json
[
  {
    "id": "user/skill-slug",
    "slug": "skill-slug",
    "name": "Friendly Name"
  }
]
```

## License

This project is licensed under the [Apache License 2.0](LICENSE).
