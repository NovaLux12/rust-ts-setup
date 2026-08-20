# rust-ts-setup

Composite GitHub Action for Rust + TypeScript monorepos.

Sets up Node.js, Rust, caching, then runs `npm ci`, `cargo check`, `npm run build`, and `npm run codegen`.

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

### Inputs

| Input | Description | Default | Required |
|-------|-------------|---------|----------|
| `node-version` | Node.js version | `20` | no |
| `rust-channel` | Rust toolchain channel | `stable` | no |
| `cache-key` | Optional cache key prefix | _(empty)_ | no |

### What it does

1. `actions/setup-node@v4` with npm cache (`**/package-lock.json`)
2. `dtolnay/rust-toolchain@stable` with selected channel
3. `Swatinem/rust-cache@v2` keyed on `Cargo.lock`
4. `npm ci`
5. `cargo fetch` + `cargo check --all-targets`
6. `npm run build`
7. `npm run codegen` (only when PR label `ts-rs` or commit message contains `ts-rs`)

## Development

```bash
# Validate action definition
python3 -c "import yaml; yaml.safe_load(open('action.yml'))"
cat action.yml
```

## Release

Push a semver tag to create a GitHub Release (via `.github/workflows/release.yml`):

```bash
git tag v0.1.0
git push origin v0.1.0
```

## Replaces

Replaces repetitive per-repo setup steps (Node/Rust toolchain + cache + the four standard commands) with one shared action. Update the action once to update every consuming repo.

## License

MIT
