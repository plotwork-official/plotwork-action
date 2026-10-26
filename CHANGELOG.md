# Changelog

All notable changes to this action are recorded here. The format follows [Keep a Changelog](https://keepachangelog.com/en/1.1.0/), and releases follow [semantic versioning](https://semver.org/spec/v2.0.0.html). release-please writes each new section from the Conventional Commits with the scope `action`; the release workflow copies this file to the public repository without the links into the private one.

## 1.5.0-beta.1 (2026-10-26)


### Added

* a split import's whole as one diagram, and combining a series
* **api:** what big imports draw, chosen by the organisation and each member
* **app:** diagram page, folders, board and editor UI pass
* diagram series keep the parts of a split import together
* diagram style settings, editor line menus and bar animation, docs and checks
* **diagrams:** edges=, fit=, align and main=on shape how a flow is drawn
* **diagrams:** spacing=compact|normal|roomy on the flow header
* **quality:** look hints offer spacing=, and agents are told to read them


### Fixed

* **diagrams:** an organisation read that reaches the wrong address says so instead of blaming the key

## 1.4.0 (2026-10-26)

The stable release of 1.4.0-beta.1. The changes are listed under those versions below.

## 1.4.0-beta.1 (2026-10-26)


### Added

* **api:** GET /meta/terraform-provider/versions lists the provider's Registry versions

## 1.3.1-beta.1 (2026-10-26)


### Fixed

* **release:** a typed Terraform pin so the site compiles on stable, type checks before a pin PR merges, and selfhost carries its public workflow

## 1.3.0 (2026-10-26)

The stable release of 1.3.0-beta.1, 1.3.0-beta.2. The changes are listed under those versions below.

## 1.3.0-beta.2 (2026-10-25)


### Added

* **docs:** code highlighting for every fence language, and guide films with the guide-film skill


### Fixed

* **diagrams:** pictures alone that differ are not drift

## 1.3.0-beta.1 (2026-10-24)


### Added

* published diagrams carry their source by default, and organisations keep diagrams editable
* source in published diagrams by default, and every key of a system identity in the console

## 1.2.0 (2026-10-23)

The stable release of 1.2.0-beta.1, 1.2.0-beta.2, 1.2.0-beta.3, 1.2.0-beta.4, 1.2.0-beta.5. The changes are listed under those versions below.

## 1.2.0-beta.5 (2026-10-23)


### Added

* **action:** send each source's format and the default branch with a run report
* **brand:** the new mark on the public faces: MCP server icon, registry listing, Action badge and READMEs
* **diagrams:** import graphql, protobuf, serverless, dockerfile, nginx, dbt and workspaces, and draw the icon each file names
* **orgs:** organisation and personal legend settings, applied to hosted calls and CI

## 1.2.0-beta.4 (2026-10-22)


### Added

* **action:** publish sends scheme, the light, dark or auto picture
* **settings:** defaults.theme is the colour theme, defaults.scheme the pictures

## 1.2.0-beta.3 (2026-10-22)


### Added

* **app:** Keys becomes Keys & CI, with API keys and Trusted publishers as two parts, /ci as a second address, and every mention renamed

## 1.2.0-beta.2 (2026-10-22)


### Added

* **action:** the outputs signed-in-as and drift-url, so a later step can check a keyless sign-in and where the run was sent


### Fixed

* **action:** a diagram that matches its files is In sync, and a run not sent to the drift dashboard says the trusted publisher sign-in failed too

## 1.2.0-beta.1 (2026-10-22)


### Added

* **action:** sign in as a trusted publisher with no API key
* one SVG per diagram by default
* one SVG per diagram for new setups, with a notice ahead of the default change

## 1.1.0 (2026-10-21)

The stable release of 1.1.0-beta.1, 1.1.0-beta.2. The changes are listed under those versions below.

## 1.1.0-beta.2 (2026-10-21)


### Fixed

* **release:** committed pins name stable releases only (Action v1.0.0, CLI 0.1.0, MCP 0.1.0), and a beta Action build names the matching CLI beta at build time

## 1.1.0-beta.1 (2026-10-20)


### Added

* **action:** bundle the engine that warns W700 to W710 for what looks like a credential in a diagram's source, with the value masked, so fail-on: warning fails on a key in a label
* **action:** bundle the engine where an icon= that names nothing is warning W208 and draws the default icon, with SAP and plain glyphs for ServiceNow, Workday, Zscaler and CrowdStrike

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
