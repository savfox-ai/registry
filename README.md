# Savfox AI Registry

This repository is a metadata registry for Savfox skills.

The current model is organized around three layers:

- `realm`: a publisher namespace or organization
- `flock`: a curated distribution of skills inside a realm
- `skills`: the index of individual skill records inside a flock

## Repository Layout

```text
.
├── LICENSE
├── README.md
├── docs/
│   ├── directory-structure.md
│   └── metadata-format.md
├── schemas/
│   ├── common.schema.json
│   ├── flock.schema.json
│   ├── realm.schema.json
│   └── skills.schema.json
└── realms/
    └── {realm-id}/
        ├── realm.json
        └── flocks/
            └── {flock-slug}/
                ├── flock.json
                └── skills.json
```

## Metadata Files

- `realms/{realm-id}/realm.json`
  Defines namespace ownership, branding, discovery metadata, contacts, and defaults.
- `realms/{realm-id}/flocks/{flock-slug}/flock.json`
  Defines one flock's identity, lifecycle status, source repository, compatibility, and index location.
- `realms/{realm-id}/flocks/{flock-slug}/skills.json`
  Defines the list of skills exposed by that flock.

## Schemas

All sample metadata files include a `$schema` field and validate against Draft 2020-12 schemas in [`schemas/`](schemas).

- [`schemas/realm.schema.json`](schemas/realm.schema.json)
- [`schemas/flock.schema.json`](schemas/flock.schema.json)
- [`schemas/skills.schema.json`](schemas/skills.schema.json)

## Documentation

- [`docs/directory-structure.md`](docs/directory-structure.md)
- [`docs/metadata-format.md`](docs/metadata-format.md)

## License

This project is licensed under the [Apache License 2.0](LICENSE).
