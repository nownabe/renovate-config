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
- Semantic commits with type `deps` (scoped per manager, e.g. `deps(github-actions):`, `deps(npm):`, `deps(mise):`)
- Labels all PRs with `renovate` (`deps/major` for majors, `deps/security` for vulnerability fixes)
- Automerges non-major updates after CI passes; major updates require manual review
- Commits via the GitHub API (`platformCommit`) so commits are signed by the GitHub App, satisfying `required_signatures` rulesets
- Waits 3 days after a release before creating a PR (supply-chain safety); vulnerability fixes are created immediately
- Checks the OSV database in addition to GitHub's vulnerability alerts
- Pins Docker image tags to digests
- Groups non-major GitHub Actions and mise tool updates; no schedule or hourly limits, so PRs are created on every Renovate run (daily via [ghac](https://github.com/nownabe/ghac))
- Updates versions annotated with `# renovate: datasource=... depName=...` in Dockerfiles, workflow env vars, and Makefiles (`customManagers:*Versions` presets)
- Custom manager that updates the mise version pinned in workflow files on lines annotated with `# renovate:mise-version`
- Runs `mise lock` after `mise.toml` updates (requires self-hosted Renovate with `mise lock` in `allowedCommands`; see [renovatebot/renovate#40568](https://github.com/renovatebot/renovate/issues/40568))
- Auto-migrates deprecated Renovate config options via `configMigration`

Rules for managers a repository doesn't use (e.g. mise) are no-ops, so the single default preset is safe to use everywhere.

## Validation

```sh
npx --yes --package renovate renovate-config-validator default.json5 renovate.json5
```
