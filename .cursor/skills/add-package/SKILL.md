---
name: add-package
description: Add a new package to the homebrew-mew Homebrew tap. Updates registry.json, README.md, and seeds Casks/<name>.rb from the package GitHub release. Use when adding a package, registering a new cask, or wiring a new mewisme tool into homebrew-mew.
---

# Add package to homebrew-mew

Wire a new package into this tap. By default the GitHub repo name under the same owner **is** the package name. Override with optional `repo` when they differ.

## Prerequisites

User must give (or confirm):

| Input | Default |
|---|---|
| `name` | required — Homebrew cask / local file stem (`brew install --cask <name>`); must match `cask "..."` inside the asset |
| `repo` | optional — GitHub repo slug if different from `name` |
| `description` | from cask `desc` if present |
| `asset` | `<name>.rb` |
| `enabled` | `true` |

Package must already publish a Homebrew cask as a release asset (e.g. `discloud-cli.rb` on `mewisme/discloud-go` releases). Autoupdate fetches from `https://github.com/<owner>/<repo\|name>/releases`.

## Checklist

Do every step. Fewest files: only these three touch points.

### 1. `registry.json`

Append under `packages` (keep existing order style; new entry at end is fine):

```json
{
  "name": "<name>",
  "asset": "<name>.rb",
  "enabled": true
}
```

When GitHub repo ≠ package name:

```json
{
  "name": "discloud-cli",
  "repo": "discloud-go",
  "asset": "discloud-cli.rb",
  "enabled": true
}
```

Valid JSON only — trailing commas break CI `jq`.

### 2. Seed `Casks/<name>.rb`

Fetch latest release asset into `Casks/<name>.rb` (local path uses `name`; token inside the file must match) so install works before the next scheduled sync:

```powershell
$owner = (gh repo view --json owner -q .owner.login)  # this tap's owner
$name = "<name>"
$repo = "<repo>"   # defaults to $name
$asset = "<asset>" # e.g. discloud-cli.rb
$url = gh api "repos/$owner/$repo/releases/latest" --jq ".assets[] | select(.name == `"$asset`") | .browser_download_url"
if (-not $url) { throw "Asset $asset missing on $owner/$repo latest release" }
curl.exe -fsSL $url -o "Casks/$name.rb"
```

If fetch fails or asset missing: stop and tell the user the package must ship `<name>.rb` on its latest GitHub release. Do **not** hand-write a fake cask.

Sanity check: file must contain `cask "` (same check CI uses).

Optional: trigger CI sync after registry is pushed:

```powershell
gh workflow run "Auto-update casks"
# package repos can also fire repository_dispatch type sync-package
# with client_payload: { "name": "<name>", "tag": "vX.Y.Z" }
```

### 3. `README.md`

Under **Packages**:

1. Add a table row (alphabetical by package name):

   `| [<name>](https://github.com/<owner>/<name>) | <description> |`

2. Add an install line in the bash block (same alphabetical order):

   `brew install --cask <name>`

Description: use cask `desc`, else a one-line summary from the package README/repo.

### 4. Done check

- [ ] `registry.json` has the package, `enabled: true`
- [ ] `Casks/<name>.rb` exists and contains `cask "`
- [ ] README table + install example updated
- [ ] No other files changed (workflow already reads `registry.json`)

Do **not** edit `.github/workflows/autoupdate.yml` for a normal add.

## Out of scope

- Scoop bucket (`scoop-mew`) — separate repo
- Changing sync schedule or CI
- Committing unless the user asks
