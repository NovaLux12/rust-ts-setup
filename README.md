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

## Jobs it replaces

- PresenceJam-Desktop CI: `Frontend (npm build + ts-rs codegen)` + `Rust (cargo check)` + `Rust clippy`
