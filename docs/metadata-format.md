# Metadata Format

This repository uses JSON metadata with schema version `1` for the current `realm -> flock -> skills` model.

All metadata files can reference local schemas with the `$schema` property:

- `schemas/realm.schema.json`
- `schemas/flock.schema.json`
- `schemas/skills.schema.json`

## Model Overview

### `realm.json`

`realm.json` describes the owner namespace of one realm.

Typical responsibilities:

- namespace identity
- maintainers and contact points
- branding and discovery metadata
- default publishing policy for child flocks

### `flock.json`

`flock.json` describes one curated skill collection under a realm.

Typical responsibilities:

- flock identity and version
- lifecycle and visibility
- canonical source repository
- compatibility range
- where to find the `skills.json` index

### `skills.json`

`skills.json` is the per-flock skill index.

Typical responsibilities:

- enumerate skills in the flock
- expose display metadata for list UIs
- define the canonical source for each skill
- provide optional runtime and compatibility hints

## File Relationship

```text
realms/{realm-id}/realm.json
  └── flocks/{flock-slug}/flock.json
        └── skills.json
              └── items[]
```

The recommended identifier relationship is:

- `realm.json.id == {realm-id}`
- `flock.json.realm_id == realm.json.id`
- `flock.json.slug == {flock-slug}`
- `skills.json.realm_id == realm.json.id`
- `skills.json.flock_slug == flock.json.slug`
- `skills.json.items[].id == {realm-id}/{flock-slug}/{skill-slug}`

## `realm.json`

### Required fields

| Field | Type | Notes |
| --- | --- | --- |
| `schema_version` | integer | Must be `1`. |
| `id` | string | Realm identifier. Allows `_` or a lowercase slug. |
| `name` | string | Display name. |
| `description` | string | Human-readable namespace description. |
| `maintainers` | array | At least one maintainer. |

### Recommended fields

| Field | Type | Purpose |
| --- | --- | --- |
| `tagline` | string | Short one-line summary for directory UIs. |
| `visibility` | string | `public`, `unlisted`, or `private`. |
| `verified` | boolean | Whether the registry operator verified ownership. |
| `branding` | object | Icon, accent color, optional banner/logo URLs. |
| `discovery` | object | Categories, keywords, and tags. |
| `contacts` | object | Public, security, and discussion contacts. |
| `policies` | object | Submission, security, and code-of-conduct URLs. |
| `links` | object | Docs, homepage, issue tracker, and related URLs. |

## `flock.json`

### Required fields

| Field | Type | Notes |
| --- | --- | --- |
| `schema_version` | integer | Must be `1`. |
| `realm_id` | string | Parent realm identifier. |
| `slug` | string | Flock slug, lowercase with `-` or `_`. |
| `name` | string | Display name. |
| `description` | string | Short collection summary. |
| `version` | string | Semver release such as `1.2.0`. |
| `status` | string | `draft`, `active`, `experimental`, `deprecated`, or `archived`. |
| `license` | string | SPDX-style license string or explicit label. |
| `maintainers` | array | Maintainer list for the flock. |
| `source` | object | Canonical origin of the flock metadata/content. |
| `index` | object | Must include `skills_path`. |

### Recommended fields

| Field | Type | Purpose |
| --- | --- | --- |
| `visibility` | string | Discovery visibility. |
| `categories` | array | Higher-level discovery grouping. |
| `keywords` | array | Search keywords. |
| `branding` | object | Flock-specific icon or color overrides. |
| `compatibility` | object | Supported Savfox versions and platforms. |
| `featured_skills` | array | Ordered list of promoted skill slugs. |
| `links` | object | Homepage, repository, docs, issues, etc. |
| `verification` | object | Security verification status and scan results. |

### `source` variants

`source.kind` supports two modes:

1. `git`
   Use when the flock is sourced from a Git repository.

```json
{
  "kind": "git",
  "url": "https://github.com/example/flock-name.git",
  "ref": {
    "type": "branch",
    "value": "main"
  },
  "subdir": "."
}
```

2. `registry`
   Use when the content is bundled directly in this repository or another checked-in registry tree.

```json
{
  "kind": "registry",
  "path": "realms/_/flocks/flock_name"
}
```

## `skills.json`

### Root object

| Field | Type | Notes |
| --- | --- | --- |
| `schema_version` | integer | Must be `1`. |
| `realm_id` | string | Parent realm identifier. |
| `flock_slug` | string | Parent flock slug. |
| `generated_at` | string | Optional RFC 3339 timestamp. |
| `items` | array | Skill records. Can be empty for a new flock. |

### Skill item required fields

| Field | Type | Notes |
| --- | --- | --- |
| `id` | string | Fully qualified ID: `{realm}/{flock}/{skill}`. |
| `slug` | string | Skill slug. |
| `name` | string | Display name. |
| `summary` | string | Short list view description. |
| `version` | string | Semver release. |
| `status` | string | Lifecycle status. |
| `license` | string | SPDX-style or explicit license. |
| `source` | object | Canonical source locator for the skill. |

### Skill item recommended fields

| Field | Type | Purpose |
| --- | --- | --- |
| `description` | string | Longer explanation than `summary`. |
| `categories` | array | Discovery grouping. |
| `keywords` | array | Search terms. |
| `maintainers` | array | Skill-specific maintainers. |
| `entry` | object | Entry file format and relative path. |
| `compatibility` | object | Supported Savfox versions and platforms. |
| `runtime` | object | Required binaries, env vars, network/sandbox hints. |
| `links` | object | Homepage, repository, docs, issues, examples. |
| `verification` | object | Security verification status and scan results. |

## Verification

The `verification` object tracks security review status for flocks and individual skills. When a user submits a git URL, the server clones the repository, scans for skills, groups them into flocks, and marks each as `unverified`. Verification proceeds through automated scan modules and optional manual review.

### Verification fields

| Field | Type | Required | Notes |
| --- | --- | --- | --- |
| `status` | string | yes | Current verification state (see below). |
| `scans` | array | no | Per-module scan results. |
| `verified_at` | string | no | RFC 3339 timestamp of last verification. |
| `verified_by` | string | no | `auto-scan` or `manual-review`. |
| `reviewer_id` | string | no | ID of the human reviewer, if applicable. |
| `notes` | string | no | Free-text notes from the reviewer or scan pipeline. |

### Verification status values

| Status | Meaning |
| --- | --- |
| `unverified` | Initial state — no scans have run. |
| `pending` | Scans are queued or in progress. |
| `passed` | All automated scans passed. |
| `failed` | One or more scans failed. |
| `manual-approved` | A human reviewer approved the content. |
| `manual-rejected` | A human reviewer rejected the content. |

### Typical state transitions

```text
unverified → pending → passed
                     → failed → manual-approved
                              → manual-rejected
```

A flock or skill starts as `unverified`. When scan modules are queued, it transitions to `pending`. After all scans finish, it becomes `passed` or `failed`. A `failed` result can be escalated for manual review, resulting in `manual-approved` or `manual-rejected`.

### Scan result fields

Each entry in the `scans` array describes one scan module's output.

| Field | Type | Required | Notes |
| --- | --- | --- | --- |
| `module` | string | yes | Lowercase module ID (e.g. `license-check`, `secret-scan`). |
| `status` | string | yes | `queued`, `running`, `passed`, `failed`, `skipped`, or `error`. |
| `severity` | string | no | `none`, `low`, `medium`, `high`, or `critical`. |
| `message` | string | no | Human-readable detail about the result. |
| `scanned_at` | string | no | RFC 3339 timestamp when the scan completed. |

## Shared Conventions

### Slugs and identifiers

- Realm IDs allow `_` for the default/example realm.
- Flock and skill slugs should use lowercase letters, digits, `-`, or `_`.

### Paths

- Use forward slashes in relative paths.
- Paths must stay inside the logical source root.
- Avoid absolute paths and parent traversal (`..`).

### Source references

- `git` sources must include a URL, ref type/value, and `subdir`.
- `registry` sources must include a relative `path`.
- `entry.path` is interpreted relative to `source.subdir` for `git`, and relative to `source.path` for `registry`.

### Compatibility

`compatibility` is intentionally lightweight:

- `savfox`: semver range or other supported version expression
- `platforms`: `any`, `windows`, `linux`, `macos`, or `web`
- `architectures`: `any`, `x64`, or `arm64`

### Runtime hints

`runtime` is intended for client-side warnings and preflight checks.

Supported keys:

- `bins`
- `env`
- `network`
- `sandbox`
- `memory_mb`

## Validation Rules

Clients or CI should reject metadata when:

- `schema_version` is not `1`
- required strings are empty
- a slug or fully qualified ID is malformed
- `version` is not valid semver
- a URL or email field is invalid
- a relative path is absolute or escapes upward
- `source.kind = "git"` but URL, ref, or `subdir` is missing
- `skills.json.realm_id` or `skills.json.flock_slug` conflicts with its parent directory

## Optional Future Expansion

The current format does not require local skill bundle folders, but the structure is compatible with a future convention such as:

```text
realms/{realm-id}/flocks/{flock-slug}/skills/{skill-slug}/
  SKILL.md
  assets/
  templates/
  scripts/
```

If that convention is adopted later, `skills.json.items[].source.kind = "registry"` can point to the bundle directory without changing the rest of the model.
