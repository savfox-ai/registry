# Directory Structure

This document describes the directory layout used by the current registry.

## Current Tree

```text
.
├── LICENSE
├── README.md
├── docs/
│   ├── directory-structure.md
│   └── metadata-format.md
├── realms/
│   ├── _/
│   │   ├── realm.json
│   │   └── flocks/
│   │       └── flock_name/
│   │           ├── flock.json
│   │           └── skills.json
│   └── savfox/
└── schemas/
    ├── common.schema.json
    ├── flock.schema.json
    ├── realm.schema.json
    └── skills.schema.json
```

## Path Semantics

### Repository root

- `README.md`
  Short overview of the registry model and links to docs/schemas.
- `LICENSE`
  Repository-wide license.
- `docs/`
  Human-readable format and layout documentation.
- `schemas/`
  JSON Schema definitions used by editors, validators, and CI.
- `realms/`
  The registry content tree.

### `realms/{realm-id}/`

Each directory under `realms/` represents one publisher namespace.

Expected contents:

- `realm.json`
  Namespace metadata.
- `flocks/`
  Child flocks that belong to this realm.

### `realms/{realm-id}/flocks/{flock-slug}/`

Each directory under `flocks/` represents one discoverable flock.

Expected contents:

- `flock.json`
  Flock metadata and source definition.
- `skills.json`
  Flat index of skills published by the flock.

## Responsibility Split

Use the three metadata files for distinct concerns:

- `realm.json`
  Publisher identity and policies.
- `flock.json`
  Release metadata for one curated collection.
- `skills.json`
  Item-level records for discoverable skills.

This split keeps list queries cheap:

- list realms without walking every flock
- list flocks without scanning every skill
- list skills without cloning every external source

## Why There Is No Root `skills.json`

The previous repository layout used a top-level `skills.json` plus a `skills/` tree.

The current structure scopes indexes to the flock level instead:

- ownership stays explicit
- multi-tenant publishing becomes simpler
- different flocks can use different release cadences
- registries can aggregate or mirror selected flocks later

## `realms/_/`

`realms/_/` is the reference/example realm in the current tree.

It shows the minimum working layout:

- one `realm.json`
- one sample flock
- one sample `skills.json`

## `realms/savfox/`

The `realms/savfox/` directory is currently present as a placeholder for an official realm namespace.

It can be populated later with:

- `realm.json`
- `flocks/`
- additional branded metadata

## Recommended Growth Pattern

When the registry expands, keep the same layering:

```text
realms/
  {realm-id}/
    realm.json
    flocks/
      {flock-slug}/
        flock.json
        skills.json
```

If local skill bundles are added later, keep them below the flock directory so ownership remains obvious:

```text
realms/{realm-id}/flocks/{flock-slug}/skills/{skill-slug}/
  SKILL.md
  assets/
  templates/
  scripts/
```
