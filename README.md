# Plotwork action

[![ci](https://github.com/plotwork-official/plotwork-action/actions/workflows/ci.yml/badge.svg)](https://github.com/plotwork-official/plotwork-action/actions/workflows/ci.yml)
[![release](https://img.shields.io/github/v/release/plotwork-official/plotwork-action?sort=semver)](https://github.com/plotwork-official/plotwork-action/releases)
[![licence](https://img.shields.io/github/license/plotwork-official/plotwork-action)](LICENSE)

Checks, redraws, validates, and renders [Plotwork](https://plotwork.dev) diagrams in a GitHub workflow. A Plotwork diagram is a `diagram` fence in Markdown, with a committed light and dark SVG under it, or a standalone `.diagram` file. This action fails a pull request whose pictures no longer match their source, puts each problem on its line in the Files changed tab, and writes a job summary that says how to fix it. The diagram engine is bundled into `dist/index.js`: the action installs nothing, makes no network request, needs no token or account, and counts nothing.

## Quick start

Save as `.github/workflows/plotwork.yml`:

```yaml
name: plotwork

on:
  pull_request:
    paths: ['**/*.md', '**/*.diagram']

permissions:
  contents: read

jobs:
  diagrams:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@3d3c42e5aac5ba805825da76410c181273ba90b1 # v7.0.1
        with:
          persist-credentials: false
      - uses: plotwork-official/plotwork-action@v1
```

With no inputs it runs `check` on every Markdown file and every `.diagram` file in the repository.

## Commands

| `command` | Reads | Writes | Fails when |
|---|---|---|---|
| `check` (default) | `paths` and `diagrams` | Nothing | A picture is missing or stale, or a diagram has an error |
| `sync` | `paths` | The Markdown files and their SVGs, in the workspace | A diagram has an error |
| `validate` | `paths` and `diagrams` | Nothing | A diagram has an error (or a warning, with `fail-on: warning`) |
| `render` | `diagrams` | Two SVGs per `.diagram` file in `out-dir` | A diagram has an error |

Every command annotates each problem on its file and line, sets the outputs, and writes a job summary unless `summary` is `false`. `fail-on` changes which problems fail the step.

### check

Fails when a `diagram` fence has no picture under it, when its picture was drawn from other source or by another renderer version, or when its source has an error. It writes nothing.

```yaml
- uses: plotwork-official/plotwork-action@v1
  with:
    paths: |
      README.md
      docs/**/*.md
      !docs/drafts/**
```

A stale fence shows up like this in the log, and as an error on line 12 of the file in the pull request:

```text
::error file=docs/architecture.md,line=12,title=Stale diagram::The picture under this diagram is missing or out of date.%0AFix: run npx @buun_group/plotwork sync docs/architecture.md, or plotwork-official/plotwork-action@v1 with command: sync, then commit the Markdown file and the SVG files.
```

The job summary lists each file with its status and gives one command that redraws every stale picture. That command pins the version of the Plotwork command line built from the same engine as this release, so the pictures it draws pass this check.

### sync

Redraws every stale picture in the runner's workspace: it writes each SVG beside the Markdown file (in `assets/diagrams/`) and the picture block under each fence. It commits nothing and pushes nothing. Pair it with a step that does, such as [peter-evans/create-pull-request](https://github.com/peter-evans/create-pull-request):

```yaml
permissions:
  contents: read

jobs:
  sync:
    runs-on: ubuntu-latest
    permissions:
      contents: write # push the branch
      pull-requests: write # open the pull request
    steps:
      - uses: actions/checkout@3d3c42e5aac5ba805825da76410c181273ba90b1 # v7.0.1
        with:
          persist-credentials: false
      - id: plotwork
        uses: plotwork-official/plotwork-action@v1
        with:
          command: sync
          paths: docs/**/*.md
      - if: steps.plotwork.outputs.stale != '0'
        uses: peter-evans/create-pull-request@5f6978faf089d4d20b00c7766989d076bb2fc7f1 # v8.1.1
        with:
          branch: plotwork/sync
          commit-message: 'docs: redraw stale diagrams'
          title: 'docs: redraw stale diagrams'
          add-paths: docs/**
```

The write permissions belong to the job that opens the pull request, never to the Plotwork step, which needs only `contents: read`. With `convert-mermaid: true`, sync also replaces each `mermaid` fence that converts cleanly with a `diagram` fence and draws it; a fence that does not convert is left as it was and reported as a warning.

### validate

Reports every error and warning in the diagrams it reads, with a line, a column, and a hint. A stale picture is not a problem to it, so it suits a repository that draws pictures elsewhere.

```yaml
- uses: plotwork-official/plotwork-action@v1
  with:
    command: validate
    diagrams: diagrams/**/*.diagram
    fail-on: warning
```

### render

Draws each `.diagram` file to a light and a dark SVG in `out-dir`, keeping its folders: `diagrams/flow.diagram` becomes `<out-dir>/diagrams/flow.svg` and `<out-dir>/diagrams/flow.dark.svg`. Each SVG carries its source, so `npx @buun_group/plotwork read` can give it back. A file with an error writes nothing.

```yaml
- uses: plotwork-official/plotwork-action@v1
  with:
    command: render
    diagrams: diagrams/**/*.diagram
    out-dir: build/diagrams
- uses: actions/upload-artifact@cf430e030ddbb5b0abf93d22962f4752f3646cd9 # v7.0.2
  with:
    name: diagrams
    path: build/diagrams
```

Complete workflows for each command are in [`examples/`](examples).

## Inputs

All inputs are optional.

| Input | Default | Description |
|---|---|---|
| `command` | `check` | `check`, `sync`, `validate`, or `render`. |
| `paths` | `**/*.md` | Markdown files for `check`, `sync`, and `validate`: one glob, file, or folder per line, relative to `working-directory`. A line that starts with `!` leaves out what it matches; a line that starts with `#` is a comment. `node_modules` and `.git` are never searched. |
| `diagrams` | `**/*.diagram` | Standalone `.diagram` files, in the same form. `check` and `validate` report their errors, `render` draws them, `sync` ignores them. Set it to `''` for none. |
| `out-dir` | `plotwork-svg` | Where `render` writes, relative to `working-directory`. It must be inside the repository. |
| `fail-on` | `error` | `error` fails on errors, and on stale pictures in `check`. `warning` fails on warnings as well. `never` only reports. |
| `convert-mermaid` | `false` | When `true`, `check` and `sync` treat each `mermaid` fence that converts to Plotwork as a diagram. |
| `summary` | `true` | Write a job summary with a table of files and how to fix each problem. |
| `working-directory` | `.` | The folder to run in, relative to the repository root. Annotations and the `files` output still name paths from the repository root. |

A mistake in the inputs, such as an unknown command or an `out-dir` outside the repository, fails the step before any file is read, with one error per mistake. A pattern you wrote that matches no file is a warning.

## Outputs

| Output | Description |
|---|---|
| `stale` | Diagrams whose picture was missing or out of date: found by `check`, redrawn by `sync`. `0` for `validate` and `render`. |
| `errors` | Errors found. |
| `warnings` | Warnings found. |
| `files` | A JSON array of paths from the repository root. `check`: the Markdown files that need a sync. `sync`: the files it wrote. `render`: the SVG files it wrote. `validate`: the files with an error. |

```yaml
- id: plotwork
  uses: plotwork-official/plotwork-action@v1
  with:
    fail-on: never
- if: steps.plotwork.outputs.stale != '0'
  env:
    FILES: ${{ steps.plotwork.outputs.files }}
  run: echo "Stale: $FILES"
```

## Permissions

| Command | Needs |
|---|---|
| `check`, `validate`, `render` | `contents: read` |
| `sync` | `contents: read`. The step that commits or opens a pull request needs `contents: write`, and `pull-requests: write` to open one. |

The action reads no token and calls no API. Check out with `persist-credentials: false` unless a later step pushes.

## How it relates to the command line and the MCP server

| Tool | Use it for |
|---|---|
| This action | Checks and redraws in GitHub Actions, with annotations and a job summary. |
| [`@buun_group/plotwork`](https://www.npmjs.com/package/@buun_group/plotwork) | The same engine on your machine or in another CI: `npx @buun_group/plotwork sync --check "docs/**/*.md"`. |
| [`@buun_group/plotwork-mcp`](https://www.npmjs.com/package/@buun_group/plotwork-mcp) | Lets an AI agent write, check, and draw diagrams. |

The three are built from one engine. A release of this action bundles a fixed engine version, and the fix command in its job summary pins the command line release built from the same engine, so a picture redrawn on your machine passes the check in CI.

## Versioning

Releases follow [semantic versioning](https://semver.org). `v1` always points to the latest `v1.x.y` release; `release.yml` moves it when a release is published.

| Change | Release |
|---|---|
| A fix, or an engine update that draws the same pictures | Patch |
| A new input, output, command, or diagram feature | Minor |
| An input removed or renamed, a default changed, or an engine update that changes existing pictures | Major |

Any engine change that draws a different picture makes committed pictures stale, so it is a major release. Pin to the full commit SHA of a release for builds that must never change, and let Dependabot propose updates.

## FAQ

**Does it send my diagrams anywhere?** No. Everything runs in the runner's process. There is no telemetry and no network request.

**Why does the check fail on a picture I just drew?** The picture was drawn by another engine version. Run the fix command from the job summary, which pins the matching command line, or run this action with `command: sync`.

**Can it commit for me?** No. It never holds a token. See [sync](#sync) for pairing it with an action that commits.

**Which Node does it need?** None of yours. It runs on the `node24` runtime that GitHub provides to actions.

**Where are the pictures written?** Beside each Markdown file, in `assets/diagrams/`, as `<name>.svg` and `<name>.dark.svg`. The picture block under each fence shows the right one for the reader's theme on GitHub.

**Can I read the source?** The source is in a private repository. This repository holds the built bundle, its licence and third-party notices, and the tests. See [CONTRIBUTING.md](CONTRIBUTING.md).

**How do I report a vulnerability?** Privately, as [SECURITY.md](SECURITY.md) describes.

## Licence

[MIT](LICENSE), Copyright (c) 2026 Buun Group. The bundle also contains other people's code and data under their own licences, and official cloud provider icons under the providers' own terms; [`dist/licenses/THIRD-PARTY-NOTICES.md`](dist/licenses/THIRD-PARTY-NOTICES.md) lists them all.
