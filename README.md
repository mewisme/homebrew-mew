# homebrew-mew

Homebrew tap for [mewisme](https://github.com/mewisme) packages.

## Install

```bash
brew tap mewisme/mew
brew install --cask <package>
```

## Packages

| Package | Version | Description |
|---|---|---|
| [agentrule](https://github.com/mewisme/agentrule) | 0.1.4 | CLI to install agent instruction rules across Cursor, Claude, Codex, and more |
| [codemcp](https://github.com/mewisme/codemcp) | 0.3.1 | A secure, workspace-bound MCP bridge connecting ChatGPT, Claude, and other AI agents to your machine. |
| [discloud-cli](https://github.com/mewisme/discloud-go) | 0.3.6 | CLI client for DisCloud (Discord-backed file storage) |

```bash
brew install --cask agentrule
brew install --cask codemcp
brew install --cask discloud-cli
```

Casks sync daily from each package's GitHub release asset (`*.rb`).
