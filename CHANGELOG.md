# Changelog

All notable changes to this action are recorded here. The format follows [Keep a Changelog](https://keepachangelog.com/en/1.1.0/), and releases follow [semantic versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

The first release, planned as `v1.0.0`.

### Added

- `check`: fails on a missing or stale picture and on an error in a diagram, with one annotation per problem on its file and line.
- `sync`: redraws stale pictures in the workspace and commits nothing.
- `validate`: reports errors and warnings in Markdown fences and `.diagram` files.
- `render`: draws each `.diagram` file to a light and a dark SVG in `out-dir`.
- Inputs `command`, `paths`, `diagrams`, `out-dir`, `fail-on`, `convert-mermaid`, `summary`, and `working-directory`.
- Outputs `stale`, `errors`, `warnings`, and `files`.
- A job summary with a table of files and the command that fixes stale pictures.
- The engine of `@buun_group/plotwork` 0.1.0, bundled into `dist/index.js` for the `node24` runtime.

[Unreleased]: https://github.com/plotwork-official/plotwork-action/commits/main
