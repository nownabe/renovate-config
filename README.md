# renovate-config

Shared [Renovate](https://docs.renovatebot.com/) presets for github.com/nownabe repositories.

## Usage

Add the following to `renovate.json5` (or `renovate.json`, `.github/renovate.json5`, etc.) in your repository:

```json5
{
  $schema: "https://docs.renovatebot.com/renovate-schema.json",
  extends: ["github>nownabe/renovate-config"],
}
```

## What the default preset does

- Extends `config:recommended` and enables the OpenSSF Scorecard badge and the Dependency Dashboard
- Semantic commits with type `deps` (scoped per manager, e.g. `deps(github-actions):`, `deps(mise):`)
- Labels all PRs with `renovate` (`deps/major` for majors, `deps/security` for vulnerability fixes)
- Automerges non-major updates after CI passes; major updates require manual review
- Waits 3 days after a release before creating a PR (supply-chain safety); vulnerability fixes are created immediately
- Groups non-major GitHub Actions and mise tool updates, scheduled weekly (before 6am on Monday, JST)
- Custom manager that updates the mise version pinned in workflow files on lines annotated with `# renovate:mise-version`
- Runs `mise lock` after `mise.toml` updates (requires self-hosted Renovate with `postUpgradeTasks` allowed; see [renovatebot/renovate#40568](https://github.com/renovatebot/renovate/issues/40568))

Rules for managers a repository doesn't use (e.g. mise) are no-ops, so the single default preset is safe to use everywhere.

## Validation

```sh
npx --yes --package renovate renovate-config-validator default.json5 renovate.json5
```
