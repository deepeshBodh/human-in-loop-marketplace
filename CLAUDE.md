# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This repository contains the HumanInLoop (HIL) Claude Code Plugin Marketplace - a platform for discovering, sharing, and managing Claude Code plugins for the company HumanInLoop (humaninloop.dev).

## Related Repositories

- **human-in-loop-experiments** (`deepeshBodh/human-in-loop-experiments`): Contains experimental artifacts that will be used as the source material for creating marketplace plugins.

## Development Guidelines

- Use `gh` CLI for all GitHub-related tasks (viewing repos, issues, PRs, etc.)

## Documentation

- **[docs/claude-plugin-documentation.md](docs/claude-plugin-documentation.md)**: Primary reference for Claude Code plugin development. Contains comprehensive technical details on plugin architecture, commands, skills, hooks, MCP integrations, and more.

## Marketplace Structure

```
human-in-loop-marketplace/
├── .claude-plugin/
│   └── marketplace.json      # Marketplace manifest
├── plugins/                  # All marketplace plugins
│   └── example-plugin/       # Example plugin (copy this to create new plugins)
├── docs/                     # Documentation
├── README.md
├── CONTRIBUTING.md
└── LICENSE
```

## Common Commands

```bash
# Add this marketplace to Claude Code
/plugin marketplace add humaninloop/human-in-loop-marketplace

# Install a plugin
/plugin install example-plugin

# View installed plugins
/plugin
```

## Adding New Plugins

1. Copy `plugins/example-plugin/` to `plugins/your-plugin-name/`
2. Update plugin.json, commands, and README with your plugin's info
3. Add entry to `.claude-plugin/marketplace.json`
4. Submit PR
