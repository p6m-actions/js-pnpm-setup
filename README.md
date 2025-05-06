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
      node-version: "18"
      install-dependencies: "true"

  # Now you can run PNPM commands
  - name: Build
    run: pnpm build
```

## Inputs

| Input                  | Description                     | Required | Default |
| ---------------------- | ------------------------------- | -------- | ------- |
| `node-version`         | Node.js version to use          | No       | '18'    |
| `install-dependencies` | Whether to install dependencies | No       | 'true'  |

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
      node-version: "20"
  - run: pnpm build
```

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
