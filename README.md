# TenkiCloud Actions

First-party GitHub Actions for Tenki workflows.

## Actions

- [`setup-cli`](./setup-cli) installs the `tenki` CLI on Linux and macOS runners.
- [`template-publish`](./template-publish) creates, updates, builds, and publishes sandbox templates through the `tenki` CLI.

## Typical template workflow

Use `setup-cli` first, then `template-publish`.

```yaml
name: Publish sandbox template

on:
  push:
    branches: [main]
    paths:
      - ".tenki/**"

jobs:
  publish:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: TenkiCloud/actions/setup-cli@v1
        with:
          version: latest
      - uses: TenkiCloud/actions/template-publish@v1
        env:
          TENKI_AUTH_TOKEN: ${{ secrets.TENKI_AUTH_TOKEN }}
        with:
          mode: update
          project-id: ${{ secrets.TENKI_PROJECT_ID }}
          template-id: ${{ secrets.TENKI_TEMPLATE_ID }}
          setup-script-path: .tenki/setup.sh
```

## Version pinning

Pin action versions by major tag for patch updates:

```yaml
- uses: TenkiCloud/actions/setup-cli@v1
- uses: TenkiCloud/actions/template-publish@v1
```

Pin the CLI independently:

```yaml
- uses: TenkiCloud/actions/setup-cli@v1
  with:
    version: vX.Y.Z
```

## Auth

`template-publish` reads `TENKI_AUTH_TOKEN` from the job environment. Do not pass the token through `with:`.

Required secrets for most workflows:

- `TENKI_AUTH_TOKEN`: workspace-scoped Tenki API token.
- `TENKI_PROJECT_ID`: project that owns the template.
- `TENKI_WORKSPACE_ID`: required for one-time `mode: create`.
- `TENKI_TEMPLATE_ID`: required after bootstrap for `mode: update`, `build-only`, and `publish-only`.
