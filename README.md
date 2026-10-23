<p align="center">
  <a href="https://plotwork.dev">
    <picture>
      <source media="(prefers-color-scheme: dark)" srcset="https://plotwork.dev/brand/logo/plotwork-lockup-on-dark.svg">
      <img alt="Plotwork" src="https://plotwork.dev/brand/logo/plotwork-lockup-on-light.svg" width="260">
    </picture>
  </a>
</p>

# Plotwork action

[![ci](https://github.com/plotwork-official/plotwork-action/actions/workflows/ci.yml/badge.svg)](https://github.com/plotwork-official/plotwork-action/actions/workflows/ci.yml)
[![release](https://img.shields.io/github/v/release/plotwork-official/plotwork-action?sort=semver)](https://github.com/plotwork-official/plotwork-action/releases)
[![licence](https://img.shields.io/github/license/plotwork-official/plotwork-action)](LICENSE)

Checks, redraws, validates, and renders [Plotwork](https://plotwork.dev) diagrams in a GitHub workflow. A Plotwork diagram is a `diagram` fence in Markdown, with a committed light and dark SVG under it, or a standalone `.diagram` file. This action fails a pull request whose pictures no longer match their source, puts each problem on its line in the Files changed tab, and writes a job summary that says how to fix it. The diagram engine is bundled into `dist/index.js`: the action installs nothing and counts nothing. By default it makes no network request and needs no token or account; only `generate` in write mode talks to GitHub with the workflow's token, and to Plotwork when you turn on `publish`.

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
| `generate` | The `sources` of `plotwork.config.json` | Nothing in check mode; with `mode: write`, the diagrams, then a pull request or a commit | A diagram drifted from its files, or a diagram has an error |

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
::error file=docs/architecture.md,line=12,title=Stale diagram::The picture under this diagram is missing or out of date.%0AFix: run plotwork-official/plotwork-action@v1 with command: sync, then commit the Markdown file and the SVG files.
```

The job summary lists each file with its status and says how to redraw every stale picture: run this action with `command: sync` and the same `paths`, then commit what it wrote. Once the Plotwork command line is on npm, the summary and the annotation also give an `npx` command that pins the version built from the same engine as this release, so the pictures it draws pass this check.

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

### generate

A complete working example, which is also the smoke test of every release, is [plotwork-examples](https://github.com/plotwork-official/plotwork-examples).

Makes each diagram that the `sources` of `plotwork.config.json` name from its files (Terraform, Bicep, ARM templates, Pulumi, CloudFormation, OpenAPI, Docker Compose, Kubernetes, Prisma, Drizzle, SQL, GitHub Actions workflows, CSV, JSON, folder listings, git logs), applies its overrides file, and fails on drift with what changed: nodes and edges added, removed, and changed. With `mode: write` it writes the diagrams and, by `on-drift`, opens or edits one pull request from `pr-branch` (the default), commits to the branch the run is on (`commit`; never on its own commit), or only fails (`fail`). With `publish: true` and an API key in `PLOTWORK_API_KEY`, each source that has a `publish` section goes to a stable link or an organisation library; only the generated diagram source is sent, never the files it was made from. See [Diagrams as code](https://docs.plotwork.dev/diagrams-as-code).

A pull request check:

```yaml
name: diagrams

on:
  pull_request:

permissions:
  contents: read

jobs:
  drift:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@3d3c42e5aac5ba805825da76410c181273ba90b1 # v7.0.1
        with:
          persist-credentials: false
      - uses: plotwork-official/plotwork-action@v1
        with:
          command: generate
```

Regenerate on main, or nightly, and open a pull request:

```yaml
name: diagrams-regenerate

on:
  push:
    branches: [main]
  schedule:
    - cron: '0 4 * * *'

permissions:
  contents: read

jobs:
  regenerate:
    runs-on: ubuntu-latest
    permissions:
      contents: write # push the pull request branch
      pull-requests: write # open or edit the pull request
    steps:
      - uses: actions/checkout@3d3c42e5aac5ba805825da76410c181273ba90b1 # v7.0.1
      - uses: plotwork-official/plotwork-action@v1
        with:
          command: generate
          mode: write
```

The repository must allow GitHub Actions to create pull requests (Settings, Actions, General). A pull request opened with the workflow's own token starts no workflow; pass a GitHub App or personal token in `github-token` to run its checks.

Publish to an organisation:

```yaml
name: diagrams-publish

on:
  push:
    branches: [main]

permissions:
  contents: read

jobs:
  publish:
    runs-on: ubuntu-latest
    permissions:
      contents: write
      pull-requests: write
    steps:
      - uses: actions/checkout@3d3c42e5aac5ba805825da76410c181273ba90b1 # v7.0.1
      - uses: plotwork-official/plotwork-action@v1
        with:
          command: generate
          mode: write
          publish: true
        env:
          PLOTWORK_API_KEY: ${{ secrets.PLOTWORK_API_KEY }}
```

Publishing with an organisation key needs no other setting: the diagrams go to that organisation's library, which has no public address, so the `published` output gives each one's `svgUrl` for the key and its members. A key works only on the service that made it; set `PLOTWORK_API_URL` for another API, such as a preview.

#### Changed only, and reports

`since` runs only the sources whose files, overrides file, or diagram changed since a git ref, and every source when the settings file changed or a file was deleted. It needs the history, so check out with `fetch-depth: 0`. Keep a nightly run without it: a new release can draw pictures differently with no file change pointing at it.

`sarif-file` writes a SARIF 2.1.0 report (rule `plotwork/drift` for each drifted diagram, and one rule for each diagnostic code) for `github/codeql-action/upload-sarif`, which shows drift in the Security tab. `junit-file` writes a JUnit XML report with one test case for each source, for a test view.

```yaml
name: diagrams

on:
  pull_request:

permissions:
  contents: read

jobs:
  drift:
    runs-on: ubuntu-latest
    permissions:
      contents: read
      security-events: write # upload the SARIF report
    steps:
      - uses: actions/checkout@3d3c42e5aac5ba805825da76410c181273ba90b1 # v7.0.1
        with:
          persist-credentials: false
          fetch-depth: 0
      - uses: plotwork-official/plotwork-action@v1
        with:
          command: generate
          since: ${{ github.event.pull_request.base.sha }}
          sarif-file: plotwork.sarif
          junit-file: plotwork.junit.xml
      - if: ${{ !cancelled() }}
        uses: github/codeql-action/upload-sarif@24c54180a607b1449ed407dd24f251e4e9147c8d # v4.38.3
        with:
          sarif_file: plotwork.sarif
```

A reusable workflow that a platform team calls from every repository is [plotwork.yml](https://github.com/plotwork-official/plotwork-examples/blob/main/.github/workflows/plotwork.yml) in plotwork-examples, and [Enterprise rollout](https://docs.plotwork.dev/enterprise-rollout) covers required checks, many teams in one repository, and air-gapped runners.

Complete workflows for each command are in [`examples/`](examples).

## Inputs

All inputs are optional.

| Input | Default | Description |
|---|---|---|
| `command` | `check` | `check`, `sync`, `validate`, `render`, or `generate`. |
| `paths` | `**/*.md` | Markdown files for `check`, `sync`, and `validate`: one glob, file, or folder per line, relative to `working-directory`. A line that starts with `!` leaves out what it matches; a line that starts with `#` is a comment. `node_modules` and `.git` are never searched. |
| `diagrams` | `**/*.diagram` | Standalone `.diagram` files, in the same form. `check` and `validate` report their errors, `render` draws them, `sync` ignores them. Set it to `''` for none. |
| `out-dir` | `plotwork-svg` | Where `render` writes, relative to `working-directory`. It must be inside the repository. |
| `fail-on` | `error` | `error` fails on errors, and on stale pictures in `check`. `warning` fails on warnings as well. `never` only reports. |
| `convert-mermaid` | `false` | When `true`, `check` and `sync` treat each `mermaid` fence that converts to Plotwork as a diagram. |
| `config` | `''` | The repository settings file, relative to `working-directory`. Every command applies its header defaults, and `check` and `validate` apply its style rules. Empty looks for `plotwork.config.json`, then `.plotwork/config.json`, from `working-directory` up to the repository root. One file per run. `off` applies none. |
| `rules-org` | `''` | An organisation id or slug whose saved style rules `check` and `validate` apply instead of the file's rules. They are read from the Plotwork API with the organisation key in the `PLOTWORK_API_KEY` environment variable. The file's defaults still apply. Apart from `generate` in write mode, this is the only network request the action makes, and only when set. |
| `mode` | `check` | `generate` only. `check` fails on drift and writes nothing; `write` makes the diagrams again and does what `on-drift` says. |
| `on-drift` | `pr` | `generate` in write mode. `pr` pushes to `pr-branch` and opens or edits one pull request; `commit` commits to the branch the run is on, and does nothing on its own commit; `fail` only fails. |
| `pr-branch` | `plotwork/diagrams` | The branch `pr` pushes to. The action owns it: each run replaces it with one commit on the branch the run is on. |
| `pr-title` | `docs: regenerate diagrams from their sources` | The pull request's title. |
| `commit-message` | `docs: regenerate diagrams from their sources` | The commit message; the action adds the trailer `Plotwork-Generated: true`. |
| `publish` | `false` | In write mode, publish each source that has a `publish` section with the key in `PLOTWORK_API_KEY`. Without a key it is skipped with a notice. |
| `github-token` | `${{ github.token }}` | The token that pushes and opens the pull request. |
| `since` | `''` | `generate` only. A git ref, such as `origin/main` or `${{ github.event.pull_request.base.sha }}`: only the sources whose files, overrides, or diagram changed since it run, and every source when the settings file changed. Needs `fetch-depth: 0`. Empty runs every source. |
| `sarif-file` | `''` | `generate` only. A file inside the repository to write a SARIF report to, for `github/codeql-action/upload-sarif`. Empty writes none. |
| `junit-file` | `''` | `generate` only. A file inside the repository to write a JUnit XML report to, one test case for each source. Empty writes none. |
| `report-file` | `''` | `generate` only. A file to write the versioned JSON report to ([schema](https://docs.plotwork.dev/schemas/plotwork.report.json), `schemaVersion` 1): the run, and per source its files, status, changes by meaning, counts, problems, and CODEOWNERS owners. Empty writes none. |
| `comment` | `auto` | `generate` only, on `pull_request` runs. `auto` writes one comment when a diagram drifted or has an error (the change table, pictures before and after, the owners, and how to fix it) and edits that same comment on later runs, saying so when everything is in step again. `always` writes it on every run, `never` writes none. Needs `pull-requests: write`; when the token cannot comment, as on a pull request from a fork, a notice says so and the job summary has everything. |
| `mention-owners` | `false` | `generate` only. When `true`, the comment names the CODEOWNERS owners of each drifted diagram as @-mentions, which notifies them. `false` names them without notifying anyone. |
| `report-drift` | `false` | `generate` only. When `true`, sends the run's metadata (repository, ref, commit, and per source its name, out path, status, change counts, and owners; never a diagram or a file) to the organisation's drift dashboard on Plotwork, with the organisation key in `PLOTWORK_API_KEY`. Pro and Enterprise. A refusal is a notice and fails nothing. |
| `summary` | `true` | Write a job summary with a table of files and how to fix each problem. |
| `working-directory` | `.` | The folder to run in, relative to the repository root. Annotations and the `files` output still name paths from the repository root. |

A mistake in the inputs, such as an unknown command or an `out-dir` outside the repository, fails the step before any file is read, with one error per mistake. A pattern you wrote that matches no file is a warning.

## Outputs

| Output | Description |
|---|---|
| `stale` | Diagrams whose picture was missing or out of date: found by `check`, redrawn by `sync`, drifted from their files in `generate`. `0` for `validate` and `render`. |
| `errors` | Errors found. |
| `warnings` | Warnings found. |
| `files` | A JSON array of paths from the repository root. `check`: the Markdown files that need a sync. `sync`: the files it wrote. `render`: the SVG files it wrote. `validate`: the files with an error. `generate`: the diagrams that drifted in check mode, the files written in write mode. |
| `changes` | `generate`: a JSON array, one entry per diagram that drifted, with its source name, its file, the number of changes, and the sentence that names them, such as `Added 1 node (logs).` |
| `pull-request` | `generate` with `on-drift: pr`: the address of the pull request opened or edited, or empty. Once nothing drifts, the pull request it opened before is closed with a comment and its branch deleted. |
| `published` | `generate` with `publish`: a JSON array of what was published, each with `name`, `kind` (`personal` or `organization`), `url`, `stableLink` (or null), `svgUrl` (an organisation's picture, served to the key; or null), `id`, and `organization`. |
| `comment-url` | `generate` on a pull request: the address of the comment it wrote or edited, or empty. |
| `drift-url` | `generate` with `report-drift`: the address of the organisation's drift dashboard the run was sent to, or empty. |
| `signed-in-as` | A JSON object with the organisation and publisher a run with no key signed in as, as a trusted publisher, such as `{"organization":"acme","publisher":"Platform CI"}`; empty when the run used a key or did not sign in. |

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
| `generate` | `contents: read` in check mode. In write mode `contents: write`, and `pull-requests: write` with `on-drift: pr`, plus "Allow GitHub Actions to create and approve pull requests". |
| `generate` with `comment` | `pull-requests: write`, to write and edit the pull request comment. |

Only `generate` in write mode reads a token (`github-token`) and calls GitHub's API; with `publish` it also calls the Plotwork API with `PLOTWORK_API_KEY`. Both are masked and never printed. Check out with `persist-credentials: false` unless a later step pushes.

## How it relates to the command line and the MCP server

| Tool | Use it for |
|---|---|
| This action | Checks and redraws in GitHub Actions, with annotations and a job summary. |
| [`@buun_group/plotwork`](https://www.npmjs.com/package/@buun_group/plotwork) | The same engine on your machine or in another CI: `npx @buun_group/plotwork sync --check "docs/**/*.md"`. |
| [`@buun_group/plotwork-mcp`](https://www.npmjs.com/package/@buun_group/plotwork-mcp) | Lets an AI agent write, check, and draw diagrams. |

The three are built from one engine. A release of this action bundles a fixed engine version. Once the command line release built from the same engine is on npm, the fix in the job summary pins it, so a picture redrawn on your machine passes the check in CI.

## Versioning

Releases follow [semantic versioning](https://semver.org). They are built and published from the Plotwork monorepo by a workflow, never by hand.

| Ref | What it holds | Use it for |
|---|---|---|
| `v1` | The latest stable `v1.x.y` release. `release.yml` moves it when a stable release is published, and only then. | Most workflows. |
| `v1.2.3` | One stable release. Never moved or deleted. | A workflow that changes only when you change it. |
| `v1.3.0-beta.2` | One prerelease, marked as a pre-release on GitHub and never the latest release. `v1` does not move for it. | Trying a change before it is stable. |
| `main` | The latest stable release. It moves only with a stable release. | Reading the code that `v1` runs. |
| `next` | Every change on its way to the next release, beta or not. It can be ahead of every tag. | Nothing in production; use a beta tag. |

| Change | Release |
|---|---|
| A fix, or an engine update that draws the same pictures | Patch |
| A new input, output, command, or diagram feature | Minor |
| An input removed or renamed, a default changed, or an engine update that changes existing pictures | Major |

Any engine change that draws a different picture makes committed pictures stale, so it is a major release. Pin to the full commit SHA of a release for builds that must never change, and let Dependabot propose updates.

## Verify

Every release has `dist/checksums.txt`, the SHA-256 of the bundle and its notices. In a checkout of the release:

```bash
(cd dist && sha256sum --check --strict checksums.txt)
```

Every release after `v1.0.0-beta.6` also has a signed build provenance attestation for `dist/index.js` and `dist/checksums.txt`, made by this repository's `release.yml` with GitHub artifact attestations and recorded in the public Sigstore transparency log. With the GitHub CLI:

```bash
gh attestation verify dist/index.js --repo plotwork-official/plotwork-action
```

To require this repository's release workflow and the tag as well:

```bash
gh attestation verify dist/index.js \
  --repo plotwork-official/plotwork-action \
  --signer-workflow plotwork-official/plotwork-action/.github/workflows/release.yml \
  --source-ref refs/tags/v1.2.3
```

The attestation proves the file is the one in the release tag and was signed by this repository's release workflow. The bundle is built in the private Plotwork monorepo, so the attestation does not describe that build; each commit message names the monorepo commit it was built from.

## FAQ

**Does it send my diagrams anywhere?** Not by default. Everything runs in the runner's process, with no telemetry. Only `generate` with `publish: true` sends anything: the generated diagram source, to the Plotwork API, with your key. The files it was made from never leave the runner.

**Why does the check fail on a picture I just drew?** The picture was drawn by another engine version. Run this action with `command: sync`, which draws with the same engine as the check, or the pinned command from the job summary once it gives one.

**Can it commit for me?** Only `generate` in write mode, with the workflow's token: a pull request (the default) or a commit to the branch. `sync` never holds a token; see [sync](#sync) for pairing it with an action that commits.

**Which Node does it need?** None of yours. It runs on the `node24` runtime that GitHub provides to actions.

**Where are the pictures written?** Beside each Markdown file, in `assets/diagrams/`, as `<name>.svg` and `<name>.dark.svg`. The picture block under each fence shows the right one for the reader's theme on GitHub.

**Can I read the source?** The source is in a private repository. This repository holds the built bundle, its licence and third-party notices, and the tests. See [CONTRIBUTING.md](CONTRIBUTING.md).

**How do I report a vulnerability?** Privately, as [SECURITY.md](SECURITY.md) describes.

## Licence

[MIT](LICENSE), Copyright (c) 2026 Buun Group. The bundle also contains other people's code and data under their own licences, and official cloud provider icons under the providers' own terms; [`dist/licenses/THIRD-PARTY-NOTICES.md`](dist/licenses/THIRD-PARTY-NOTICES.md) lists them all.
