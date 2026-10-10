# Security policy

## Supported versions

| Version | Supported |
|---|---|
| `v1` (latest `1.x.y`) | Yes |
| Older releases | No. Move to the latest `v1`. |

## Reporting a vulnerability

Report it privately through [GitHub private vulnerability reporting](https://github.com/plotwork-official/plotwork-action/security/advisories/new). Do not open a public issue.

Include the release or commit you used, the workflow or input that shows the problem, and what an attacker could gain. We reply within five working days, keep you informed while we fix it, and credit you in the advisory unless you ask us not to.

## What the action can reach

The action runs in the runner's process with the permissions of the job. It reads the files its inputs name and, for `sync` and `render`, writes inside the workspace. It never reads a token, never calls an API, and makes no network request. A path that leaves the repository, through `..` or an absolute path, is refused. Diagram text is parsed, never run as code.
