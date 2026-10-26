# JavaScript PNPM Setup

![Latest Release](https://img.shields.io/github/v/release/p6m-actions/js-pnpm-setup?style=flat-square&label=Latest%20Release&color=blue)

## Description

This GitHub Action sets up PNPM and configures dependency caching for faster workflows. It handles installing PNPM, configuring caching with the actions/cache action, and optionally installing dependencies.

## Usage

```yaml
steps:
  - name: Checkout Code
    uses: actions/checkout@v3

  - name: Setup PNPM with caching
    uses: p6m-actions/js-pnpm-setup@v1
    with:
      node-version: "22"
      install-dependencies: "true"

  # Now you can run PNPM commands
  - name: Build
    run: pnpm build
```

## Inputs

| Input                  | Description                                                        | Required | Default |
| ---------------------- | ------------------------------------------------------------------ | -------- | ------- |
| `node-version`         | Node.js version to use. Empty honors `.nvmrc`, `.node-version`, then `package.json` `engines.node`, then falls back to `22` | No       | `""`    |
| `install-dependencies` | Whether to install dependencies                                    | No       | `true`  |
| `project-path`         | Relative path to the project directory (must contain `package.json` and `pnpm-lock.yaml`) | No       | `.`     |

## Outputs

| Output           | Description               |
| ---------------- | ------------------------- |
| `cache-hit`      | Whether the cache was hit |
| `pnpm-cache-dir` | PNPM cache directory path |

## Examples

### Basic Usage

```yaml
steps:
  - uses: actions/checkout@v3
  - uses: p6m-actions/js-pnpm-setup@v1
  - run: pnpm build
```

### Specify Node Version

```yaml
steps:
  - uses: actions/checkout@v3
  - uses: p6m-actions/js-pnpm-setup@v1
    with:
      node-version: "22"
  - run: pnpm build
```

> **Security floor:** use Node 22.23.4 or later (24.22.1+ / 26.12.1+ on those lines). Node 20 and older are affected by the 2026-10-21 Node security release. A major tag such as `"22"` resolves to the latest 22.x on the runner. A version pinned in `.nvmrc` or `engines.node` overrides the fallback, so keep it at or above the floor.
>
> This action uses `actions/setup-node@v5` and `actions/cache@v5` (Node 24 runtime). Self-hosted runners need Actions runner 2.327.1 or later.

### Setup Without Installing Dependencies

```yaml
steps:
  - uses: actions/checkout@v3
  - uses: p6m-actions/js-pnpm-setup@v1
    with:
      install-dependencies: "false"
  - run: pnpm install --frozen-lockfile
  - run: pnpm build
```

### Using Cache Outputs

```yaml
steps:
  - uses: actions/checkout@v3
  - id: pnpm-setup
    uses: p6m-actions/js-pnpm-setup@v1
  - name: Show cache information
    run: |
      echo "Cache hit: ${{ steps.pnpm-setup.outputs.cache-hit }}"
      echo "PNPM cache directory: ${{ steps.pnpm-setup.outputs.pnpm-cache-dir }}"
```

### Project in a Subdirectory

For repositories where the project is not at the root. The `project-path` must be a relative path within the repository and must contain both `package.json` and `pnpm-lock.yaml`:

```yaml
steps:
  - uses: actions/checkout@v3
  - uses: p6m-actions/js-pnpm-setup@v1
    with:
      project-path: "packages/my-site"
  - name: Build
    working-directory: packages/my-site
    run: pnpm build
```

### Multiple Projects in One Repository

For repositories with multiple independent PNPM projects (not pnpm workspaces using `pnpm-workspace.yaml`), call the action separately for each project. Each project gets its own isolated cache.

> **Note:** This feature is designed for repositories containing multiple separate PNPM projects, each with their own `package.json` and `pnpm-lock.yaml`. For pnpm workspaces, run `pnpm install` from the workspace root instead.

```yaml
steps:
  - uses: actions/checkout@v3

  # Setup and build frontend
  - uses: p6m-actions/js-pnpm-setup@v1
    with:
      project-path: "packages/frontend"
  - name: Build frontend
    working-directory: packages/frontend
    run: pnpm build

  # Setup and build backend
  - uses: p6m-actions/js-pnpm-setup@v1
    with:
      project-path: "packages/backend"
  - name: Build backend
    working-directory: packages/backend
    run: pnpm build
```
