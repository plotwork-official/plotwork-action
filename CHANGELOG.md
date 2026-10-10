# Changelog

All notable changes to this action are recorded here. The format follows [Keep a Changelog](https://keepachangelog.com/en/1.1.0/), and releases follow [semantic versioning](https://semver.org/spec/v2.0.0.html). release-please writes each new section from the Conventional Commits with the scope `action`; the release workflow copies this file to the public repository without the links into the private one.

## 1.0.0-beta.1

The first prerelease. The stable `v1.0.0` follows from `main`.

### Added

* `check`: fails on a missing or stale picture and on an error in a diagram, with one annotation per problem on its file and line.
* `sync`: redraws stale pictures in the workspace and commits nothing.
* `validate`: reports errors and warnings in Markdown fences and `.diagram` files.
* `render`: draws each `.diagram` file to a light and a dark SVG in `out-dir`.
* Inputs `command`, `paths`, `diagrams`, `out-dir`, `fail-on`, `convert-mermaid`, `summary`, and `working-directory`.
* Outputs `stale`, `errors`, `warnings`, and `files`.
* A job summary with a table of files, and a fix that runs this action with `command: sync`.
* The Plotwork engine, bundled into `dist/index.js` for the `node24` runtime.
