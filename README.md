# Savfox AI Skill Registry

The official registry for distributing and discovering [Savfox](https://github.com/savfox-ai/savfox) AI skills.

## Structure

```text
skills.json              # Registry index
docs/
  metadata-format.md     # Detailed _meta.toml and index documentation
skills/
  {namespace}/
    {skill-slug}/
      _meta.toml         # Canonical bundle metadata
      SKILL.md           # Main skill entry file
      ...                # Optional bundled assets/templates/scripts
```

## Metadata

`_meta.toml` now uses a versioned schema with explicit sections for:

- package identity
- authorship
- discovery metadata
- source location
- runtime requirements
- links

The source section supports both bundled skills and external git repositories, including a git ref (`branch`, `tag`, or `commit`) and a `subdir` for skills that live below the repository root.

See [docs/metadata-format.md](docs/metadata-format.md) for the full schema, validation rules, and examples.

## Index

`skills.json` is also versioned now. Each entry includes the registry path plus a source summary so clients do not need to infer where the skill actually lives.

## License

This project is licensed under the [Apache License 2.0](LICENSE).
