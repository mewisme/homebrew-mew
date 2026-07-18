---
name: add-package
description: Add a new package to the homebrew-mew Homebrew tap. Updates registry.json, README.md, and seeds Casks/<name>.rb from the package GitHub release. Use when adding a package, registering a new cask, or wiring a new mewisme tool into homebrew-mew.
---

# Add package to homebrew-mew

Wire a new package into this tap. Repo name under the same GitHub owner **is** the package name.

## Prerequisites

User must give (or confirm):

| Input | Default |
|---|---|
| `name` | required — GitHub repo / Homebrew cask name |
| `description` | from cask `desc` if present |
| `asset` | `<name>.rb` |
| `enabled` | `true` |

Package must already publish a Homebrew cask as a release asset (e.g. `agentrule.rb` on `mewisme/agentrule` releases). Autoupdate fetches from `https://github.com/<owner>/<name>/releases`.

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

Valid JSON only — trailing commas break CI `jq`.

### 2. Seed `Casks/<asset>`

Fetch latest release asset into `Casks/<name>.rb` so install works before the next scheduled sync:

```powershell
$owner = (gh repo view --json owner -q .owner.login)  # this tap's owner
$name = "<name>"
$asset = "$name.rb"
$url = gh api "repos/$owner/$name/releases/latest" --jq ".assets[] | select(.name == `"$asset`") | .browser_download_url"
if (-not $url) { throw "Asset $asset missing on $owner/$name latest release" }
curl.exe -fsSL $url -o "Casks/$asset"
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
