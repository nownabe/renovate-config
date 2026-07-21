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

- Based on `config:recommended`; enables the OpenSSF Scorecard badge and the Dependency Dashboard
- Semantic commits: runtime dependencies use type `deps`, everything else (CI actions, container images, infra, tool-version managers, dev/optional/peer dependencies, lockfile-only updates) is demoted to `chore`. Scoped per manager, e.g. `deps(npm):`, `deps(go):`, `chore(github-actions):`, `chore(docker):`, `chore(terraform):`, `chore(mise):`
- Labels all PRs with `renovate`; `deps/security` for vulnerability fixes and `deps/major` for major GitHub Actions and mise updates
- Automerges updates after CI passes; major GitHub Actions and mise updates disable automerge and require manual review
- Commits via the GitHub API (`platformCommit`) so commits are signed by the GitHub App, satisfying `required_signatures` rulesets
- Waits 3 days after a release before creating a PR (supply-chain safety); vulnerability fixes are created immediately and assigned to `nownabe`
- Checks the OSV database in addition to GitHub's vulnerability alerts
- Pins Docker image tags to digests
- Groups non-major GitHub Actions and mise tool updates
- Interprets all schedules in JST (`Asia/Tokyo`)
- Caps concurrent PRs at 5 and disables the hourly limit, so PRs are created on every Renovate run
- Updates versions annotated with `# renovate: datasource=... depName=...` in Dockerfiles, workflow env vars, and Makefiles (`customManagers:*Versions` presets)
- Custom manager that updates the mise version pinned in workflow files on lines annotated with `# renovate:mise-version`
- Runs `mise trust && mise lock` after `mise.toml` updates (requires self-hosted Renovate with those commands in `allowedCommands`; see [renovatebot/renovate#40568](https://github.com/renovatebot/renovate/issues/40568))
- Auto-migrates deprecated Renovate config options via `configMigration`

Rules for managers a repository doesn't use (e.g. mise) are no-ops, so the single default preset is safe to use everywhere.

## Validation

```sh
npx --yes --package renovate -- renovate-config-validator --strict default.json renovate.json5
```
