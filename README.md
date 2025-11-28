# ArchLens Render Diff on PR

A GitHub Action that automatically generates and posts architectural diff diagrams on pull requests. It compares the module dependencies between your PR branch and the base branch, highlighting new, deleted, or modified dependencies.

## Features

- Automatically analyzes Python module dependencies
- Generates visual diff diagrams showing architectural changes
- Only posts images for views that have actual changes
- Comments on PRs with the architectural diff visualization

## Usage

Add this to your workflow file (e.g., `.github/workflows/archlens.yml`):

```yaml
name: ArchLens Diff

on:
  pull_request:
    branches: [main, master]

jobs:
  archlens:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3

      - uses: archlens/Render-Diff-on-PR@main
        with:
          BRANCH_NAME: archlens-diagrams
```

## Inputs

| Input | Description | Required | Default |
|-------|-------------|----------|---------|
| `config-path` | Path to the ArchLens config file | No | `archlens.json` |
| `render-diff` | Whether to render diff (true) or full diagram (false) | No | `true` |
| `BRANCH_NAME` | Name for the orphan branch to store diagrams | Yes | - |
| `comment-on-no-changes` | Post a comment when there are no architectural changes | No | `true` |

## Configuration

Create an `archlens.json` file in your repository:

```json
{
  "name": "my-project",
  "rootFolder": "src",
  "github": {
    "url": "https://github.com/owner/repo",
    "branch": "main"
  },
  "saveLocation": "./diagrams/",
  "views": {
    "top-level": {
      "packages": [{"path": "*", "depth": 1}]
    }
  }
}
```

## How It Works

1. The action installs ArchLens and analyzes your codebase
2. It compares the local (PR) module dependencies with the remote (base branch)
3. For each view with changes, it generates a diff diagram highlighting:
   - **Green**: New packages or dependencies
   - **Red**: Deleted packages or dependencies
   - Dependency count changes (e.g., `5 (+2)`)
4. Diagrams are stored in an orphan branch and linked in a PR comment
5. If no architectural changes are detected, it posts a message indicating so (configurable)

## Example Output

When changes are detected:
> **ArchLens** detected architectural changes in the following views:
>
> ![diff](diagram-url)

When no changes are detected:
> **ArchLens** - No architecturally relevant changes to the existing views

## License

MIT
