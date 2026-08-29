# homebrew-mew

Homebrew tap for [mewisme](https://github.com/mewisme) packages.

## Install

```bash
brew tap mewisme/mew
brew install --cask <package>
```

## Packages

| Package | Description |
|---|---|
| [agentrule](https://github.com/mewisme/agentrule) | CLI to install agent instruction rules across Cursor, Claude, Codex, and more |
| [discloud-cli](https://github.com/mewisme/discloud-go) | CLI client for DisCloud (Discord-backed file storage) |
| [chatgpt-mcp](https://github.com/mewisme/chatgpt-mcp) | Self-hosted MCP runtime with local tools, upstream MCP aggregation, and OpenAI Secure MCP Tunnel |

```bash
brew install --cask agentrule
brew install --cask discloud-cli
brew install --cask chatgpt-mcp
```

Casks sync daily from each package's GitHub release asset (`*.rb`).
