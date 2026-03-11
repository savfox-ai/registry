# Metadata Format

This registry uses schema version `2` for both per-skill metadata (`_meta.toml`) and the top-level registry index (`skills.json`).

## Goals

- Make skill metadata explicit and versioned.
- Support skills bundled directly in this registry.
- Support skills whose canonical source lives in an external git repository.
- Allow skills to live in a subdirectory instead of the git repository root.
- Keep enough information in `skills.json` for simple clients to browse without scanning the whole tree.

## `_meta.toml`

Each skill folder must contain `_meta.toml` at the bundle root.

### Required shape

```toml
schema_version = 2

[package]
kind = "skill"
slug = "echo-test"
name = "Echo Test"
description = "Simple echo skill for testing skill loading and execution."
version = "1.0.0"
license = "Apache-2.0"

[author]
name = "_"

[source]
kind = "bundle"
subdir = "."
```

### Sections

#### `schema_version`

- Required.
- Current value: `2`.

#### `[package]`

- Required.
- `kind`: currently `skill`.
- `slug`: canonical machine name. Lowercase letters, digits, and `-` only.
- `name`: display name.
- `description`: short summary used for registry display and search.
- `version`: semver string such as `1.0.0`.
- `license`: SPDX-style license identifier or another explicit license label.
- `icon`: optional short icon or emoji used by clients for richer presentation.

#### `[author]`

- Required.
- `name`: human-readable author or maintainer label for the bundle content.

#### `[discovery]`

- Optional.
- `keywords`: free-form search keywords.
- `categories`: higher-level grouping values.

#### `[source]`

- Required.
- `kind`: `bundle` or `git`.
- `subdir`: relative path to the skill root inside the source tree.
  - Use `"."` when the skill root is the source root.
  - Use forward slashes.
  - Must be relative.

For bundled skills stored directly in this registry:

```toml
[source]
kind = "bundle"
subdir = "."
```

For git-backed skills:

```toml
[source]
kind = "git"
subdir = "packages/skills/echo-test"

[source.git]
url = "https://github.com/acme/agent-monorepo.git"

[source.git.ref]
kind = "tag"
value = "skills/echo-test/v1.4.0"
```

#### Git source rules

When `source.kind = "git"`:

- `[source.git]` is required.
- `url` is required.
- `[source.git.ref]` is required.
- `source.git.ref.kind` must be one of:
  - `branch`
  - `tag`
  - `commit`
- `source.git.ref.value` is the branch name, tag name, or commit hash.

Example with a branch:

```toml
[source]
kind = "git"
subdir = "skills/sys-info"

[source.git]
url = "https://github.com/acme/platform-tools.git"

[source.git.ref]
kind = "branch"
value = "main"
```

Example with a commit hash:

```toml
[source]
kind = "git"
subdir = "skills/json-transform"

[source.git]
url = "https://github.com/acme/data-skills.git"

[source.git.ref]
kind = "commit"
value = "6c4d6fc7c9e9385cb6ab8e4dc1e9587f9cbd7e4f"
```

#### `[runtime]`

- Optional.
- `bins`: executables expected on `PATH`.
- `env`: environment variables the skill expects.

Installation hints can be declared with `[[runtime.install]]`:

```toml
[runtime]
bins = ["jq"]
env = []

[[runtime.install]]
id = "brew"
kind = "brew"
package = "jq"
bins = ["jq"]
label = "Homebrew"
```

Each install record supports:

- `id`: optional stable identifier inside the metadata file.
- `kind`: installer type such as `brew`, `apt`, `choco`, `pip`, `cargo`, or `custom`.
- `package`: optional package/formula name.
- `command`: optional explicit install command for custom installers.
- `bins`: optional binaries satisfied by this install method.
- `label`: optional display label.

At least one of `package` or `command` should be provided for each install record.

#### `[links]`

- Optional.
- Free-form string map for repository, homepage, docs, issues, examples, etc.

Example:

```toml
[links]
repository = "https://github.com/savfox-ai/registry"
documentation = "https://example.com/skills/echo-test"
```

## Bundled Skill Example

```toml
schema_version = 2

[package]
kind = "skill"
slug = "echo-test"
name = "Echo Test"
description = "Simple echo skill for testing skill loading and execution."
version = "1.0.0"
license = "Apache-2.0"
icon = "🔔"

[author]
name = "_"

[discovery]
keywords = ["test", "echo", "debug", "hello-world"]
categories = ["testing"]

[source]
kind = "bundle"
subdir = "."

[runtime]
bins = []
env = []

[links]
repository = "https://github.com/savfox-ai/registry"
```

## `skills.json`

The registry index is a JSON object:

```json
{
  "schema_version": 2,
  "items": [
    {
      "id": "_/echo-test",
      "namespace": "_",
      "slug": "echo-test",
      "name": "Echo Test",
      "description": "Simple echo skill for testing skill loading and execution.",
      "version": "1.0.0",
      "registry_path": "skills/_/echo-test",
      "metadata_path": "skills/_/echo-test/_meta.toml",
      "source": {
        "kind": "bundle",
        "subdir": "."
      }
    }
  ]
}
```

### Index field meanings

- `schema_version`: current index schema version.
- `items`: registry entries.
- `id`: `{namespace}/{slug}`.
- `namespace`: owner or grouping directory in this registry.
- `slug`: canonical package slug.
- `name`: display name.
- `description`: short summary for list UIs.
- `version`: latest published metadata version for that entry.
- `registry_path`: folder path inside this repository.
- `metadata_path`: `_meta.toml` location.
- `source`: source summary copied from `_meta.toml`.

For git-backed entries, `source` includes the git URL, ref kind/value, and `subdir`.

## Validation Rules

Clients and servers should reject metadata when any of the following are true:

- `schema_version` is not `2`
- `package.slug` is invalid
- `package.version` is not valid semver
- required strings are empty
- `source.subdir` is absolute or escapes upward with `..`
- `source.kind = "git"` but git URL/ref is missing
- `source.kind != "git"` but git-specific fields are present in a contradictory way

## Compatibility Guidance

- New publishers should treat `_meta.toml` as the canonical source for slug, name, version, and description.
- Registry clients should prefer `_meta.toml` over inferred values from folder names.
- Servers can still keep a fallback path for older bundles that only contain markdown frontmatter, but new submissions should ship `_meta.toml`.
