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
│   └── marketplace.json           # Marketplace manifest
├── plugins/
│   ├── humaninloop/               # Main workflow plugin (specify → plan)
│   │   ├── .claude-plugin/
│   │   │   └── plugin.json
│   │   ├── agents/                # Multi-agent workflow agents
│   │   ├── commands/              # /humaninloop:specify, /humaninloop:plan
│   │   ├── check-modules/         # Validation check modules
│   │   ├── scripts/               # Shell utilities
│   │   └── templates/             # Workflow templates
│   └── humaninloop-constitution/  # Constitution setup plugin
│       ├── .claude-plugin/
│       │   └── plugin.json
│       ├── commands/              # /humaninloop-constitution:setup
│       └── templates/
├── docs/                          # Documentation
├── README.md
├── CONTRIBUTING.md
└── LICENSE
```

## Available Plugins

| Plugin | Description | Commands |
|--------|-------------|----------|
| `humaninloop` | Specification-driven development workflow | `/humaninloop:specify`, `/humaninloop:plan` |
| `humaninloop-constitution` | Project constitution setup | `/humaninloop-constitution:setup` |

## Common Commands

```bash
# Add this marketplace to Claude Code
/plugin marketplace add deepeshBodh/human-in-loop-marketplace

# Install constitution plugin first (required by humaninloop)
/plugin install humaninloop-constitution
/humaninloop-constitution:setup

# Install main workflow plugin
/plugin install humaninloop

# View installed plugins
/plugin
```

## Adding New Plugins

1. Copy `plugins/humaninloop-constitution/` as a template for simpler plugins
2. Update plugin.json, commands, and README with your plugin's info
3. Add entry to `.claude-plugin/marketplace.json`
4. Submit PR
