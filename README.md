# rust-ts-setup

[![CI](https://github.com/NovaLux12/rust-ts-setup/actions/workflows/ci.yml/badge.svg)](https://github.com/NovaLux12/rust-ts-setup/actions/workflows/ci.yml) [![Release](https://github.com/NovaLux12/rust-ts-setup/actions/workflows/release.yml/badge.svg)](https://github.com/NovaLux12/rust-ts-setup/actions/workflows/release.yml)

Composite GitHub Action for Rust + TypeScript monorepos. Sets up Node.js, Rust, caching, then runs `npm ci`, `cargo check`, `npm run build`, and conditional `npm run codegen`.

## Usage

```yaml
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: NovaLux12/rust-ts-setup@v1
        with:
          node-version: '20'
          rust-channel: 'stable'
```

With custom versions and cache prefix:

```yaml
      - uses: NovaLux12/rust-ts-setup@v1
        with:
          node-version: '22'
          rust-channel: '1.82'
          cache-key: 'my-app'
```

Pin to a release tag or commit:

```yaml
      - uses: NovaLux12/rust-ts-setup@v1.2.3
      - uses: NovaLux12/rust-ts-setup@a1b2c3d  # commit SHA
```

### Inputs

| Input | Description | Default | Required |
|-------|-------------|---------|----------|
| `node-version` | Node.js version (`actions/setup-node`) | `20` | no |
| `rust-channel` | Rust toolchain channel (`dtolnay/rust-toolchain`) | `stable` | no |
| `cache-key` | Optional cache key prefix (`Swatinem/rust-cache`) | `""` (uses `rust-ts`) | no |

All inputs map directly to `action.yml`. `cache-key` is prepended to the `Cargo.lock` hash key; omit it for the default `rust-ts-<hash>`.

### What it does

1. `actions/setup-node@v4` with npm cache (`**/package-lock.json`) at `node-version`
2. `dtolnay/rust-toolchain@stable` at `rust-channel`
3. `Swatinem/rust-cache@v2` keyed on `cache-key` + `Cargo.lock` hash
4. `npm ci`
5. `cargo fetch` + `cargo check --all-targets`
6. `npm run build`
7. `npm run codegen` — only when PR label `ts-rs` or commit message contains `ts-rs`

## Development

```bash
# validate YAML
yamllint -d "{extends: relaxed, rules: {line-length: {max: 120}, document-start: disable}}" action.yml action/action.yml
actionlint

# fallback without yamllint/actionlint
python3 -c "import yaml; yaml.safe_load(open('action.yml'))"
cat action.yml
python3 -c "import yaml; yaml.safe_load(open('action/action.yml'))"
```

CI validates `action.yml` and workflows on every push/PR via `yamllint`/`actionlint` (see `.github/workflows/ci.yml`).

## Release

Push a semver tag to create a GitHub Release (via `.github/workflows/release.yml`):

```bash
git tag v0.1.0
git push origin v0.1.0
```

## Replaces

Replaces repetitive per-repo setup steps (Node/Rust toolchain + cache + the four standard commands) with one shared action. Update the action once to update every consuming repo.

## License

MIT — see [LICENSE](LICENSE).
