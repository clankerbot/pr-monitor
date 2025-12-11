# PR Monitor

A GitHub Action that waits for all other workflow checks to complete and reports aggregate status. Use this as a single required check in branch protection rules instead of listing every workflow.

## Why?

Instead of maintaining a list of required checks that needs updating every time you add a workflow, use PR Monitor as your single required check. It will:

- Wait for all other workflows to complete
- Fail if any workflow fails
- Pass if all workflows pass
- Handle docs-only PRs gracefully (no other workflows = pass)

## Usage

Create `.github/workflows/pr-monitor.yml`:

```yaml
name: PR Monitor

on:
  pull_request:
    types: [opened, synchronize, reopened]
    branches:
      - main

jobs:
  monitor:
    name: 'Check All Workflows'
    runs-on: ubuntu-latest
    steps:
      - uses: clankerbot/pr-monitor@v1
```

Then set "Check All Workflows" as your only required check in branch protection.

## Inputs

| Input | Description | Default |
|-------|-------------|---------|
| `excluded-jobs` | Comma-separated job names to exclude | `''` |
| `pre-sleep` | Seconds to wait before checking | `10` |
| `check-interval` | Seconds between status checks | `5` |
| `timeout` | Maximum minutes to wait | `10` |
| `github-token` | GitHub token for API access | `${{ github.token }}` |

## Example with exclusions

```yaml
- uses: clankerbot/pr-monitor@v1
  with:
    excluded-jobs: 'deploy-preview,notify-slack'
    timeout: '15'
```

## How it works

1. Waits `pre-sleep` seconds for other workflows to start
2. Polls the GitHub Checks API every `check-interval` seconds
3. Excludes itself and any jobs in `excluded-jobs`
4. Fails immediately if any check fails
5. Passes when all checks complete successfully
6. Times out after `timeout` minutes if checks are still running

## License

MIT
