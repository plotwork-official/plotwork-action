# Changelog

All notable changes to this action are recorded here. The format follows [Keep a Changelog](https://keepachangelog.com/en/1.1.0/), and releases follow [semantic versioning](https://semver.org/spec/v2.0.0.html). release-please writes each new section from the Conventional Commits with the scope `action`; the release workflow copies this file to the public repository without the links into the private one.

## 1.0.0 (2026-10-20)

The stable release of 1.0.0-beta.1, 1.0.0-beta.2, 1.0.0-beta.3, 1.0.0-beta.4, 1.0.0-beta.5, 1.0.0-beta.6, 1.0.0-beta.7, 1.0.0-beta.8, 1.0.0-beta.9, 1.0.0-beta.10, 1.0.0-beta.11, 1.0.0-beta.12, 1.0.0-beta.13, 1.0.0-beta.14. The changes are listed under those versions below.

## 1.0.0-beta.14 (2026-10-20)


### Fixed

* every fetch wrapper gives a default timeout a caller can override, so no outbound call can hang

## 1.0.0-beta.13 (2026-10-19)


### Added

* **action:** bundle the engine whose passive arrow labels ("consumed by", "stored in") draw their verb's icon and whose sequence frames name their operator in plain bold text

## 1.0.0-beta.12 (2026-10-19)


### Fixed

* **api:** the drift dashboard link is the portal's /orgs/:slug/drift path

## 1.0.0-beta.11 (2026-10-18)


### Added

* **action:** generate and sync write one picture per diagram with schemes auto

## 1.0.0-beta.10 (2026-10-18)


### Added

* **action:** bundle the engine that reads icon art outside its core from dist/icons/, covered by dist/checksums.txt

## 1.0.0-beta.9 (2026-10-18)


### Added

* **action:** bundle the engine whose imported arrows show their relationship with an icon and a line colour, and whose frames are all typed
* **action:** bundle the engine whose verb labels draw icons and whose plainly named groups are typed; style suggestions are notices that never fail fail-on: warning


### Fixed

* **action:** bundle the engine whose AsyncAPI pictures draw receives above an application and sends below it

## 1.0.0-beta.8 (2026-10-18)


### Added

* **action:** read Structurizr workspaces, AsyncAPI documents, GitLab CI/CD, and Azure Pipelines in generate

## 1.0.0-beta.7 (2026-10-18)


### Added

* **action:** read CDK cloud assemblies, Helm, Kustomize, and Backstage catalogs in generate

## 1.0.0-beta.6 (2026-10-17)


### Added

* **action:** a personal key's publish that an organisation keeps in its library is reported there, with why

## 1.0.0-beta.5 (2026-10-17)


### Added

* **action:** bundle the engine that reads Terraform plans and state, marks planned changes, and keeps edge labels clear

## 1.0.0-beta.4 (2026-10-17)


### Fixed

* **action:** name the Action in a stale picture's fix at a tag that exists

## 1.0.0-beta.3 (2026-10-16)


### Added

* **action:** since, sarif-file, junit-file, retries, and organisation-key publishing


### Fixed

* **action:** a summary under 1 MiB, a branch rule hint, and never replacing someone's pr-branch

## 1.0.0-beta.2 (2026-10-16)


### Added

* **action,cdn:** repository settings in the Action and data images in the CDN policy
* **action:** add command generate with check and write modes, pull requests, and publishing

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
