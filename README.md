# Agent context check

Grades what your AI coding agents are told (AGENTS.md, CLAUDE.md, Cursor rules, Copilot instructions,
GEMINI.md, MCP configs) on every pull request, and fails only on problems **the pull request introduces**.
Existing debt never blocks a merge.

It finds dead paths, undefined scripts, the wrong package manager, duplicated and conflicting instructions,
hidden Unicode, secrets in context and unpinned MCP servers. Powered by [threadctx](https://www.npmjs.com/package/threadctx).

## Use

`.github/workflows/agent-context.yml`:

```yaml
name: Agent context
on: pull_request
permissions:
  contents: read
  pull-requests: write     # grade-change comment
  security-events: write   # findings in GitHub code scanning
jobs:
  agent-context:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@3d3c42e5aac5ba805825da76410c181273ba90b1 # v7.0.1
      - uses: threadctx-dev/action@v1
```

Pin to a release commit SHA (see Releases) if your policy requires it.

## Inputs

| Input | Default | |
|---|---|---|
| `fail-on` | `blocker` | Fail when a **new** finding is at or above `blocker`, `warning` or `info`; `none` never fails |
| `comment` | `true` | One comment per pull request, updated on every push |
| `sarif` | `true` | Upload to GitHub code scanning (needs `security-events: write`) |
| `path` | `.` | Directory to scan |
| `version` | pinned | threadctx version to run |

Outputs: `grade`, `score`, `new-findings`.

## What leaves your runner

Nothing goes to us. The scan runs on the runner; the comment and SARIF go to your own repository through
your workflow's token. The action has no dependencies beyond the runner's Node and `gh`, and the one
third-party step (`github/codeql-action/upload-sarif`) is pinned by commit.

## Run it locally

```bash
npx threadctx scan
```

Licence: Apache-2.0.
