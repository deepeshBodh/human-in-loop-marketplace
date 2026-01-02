# HumanInLoop Claude Code Plugin Marketplace

Official Claude Code plugin marketplace for [HumanInLoop](https://humaninloop.dev).

## Quick Start

### Add the Marketplace

```bash
/plugin marketplace add deepeshBodh/human-in-loop-marketplace
```

### Install Plugins

```bash
# Install in dependency order:

# 1. Constitution (governance, no dependencies)
/plugin install humaninloop-constitution
/humaninloop-constitution:setup

# 2. Core (shared skills and agents)
/plugin install humaninloop-core

# 3. Workflow plugins (depend on core)
/plugin install humaninloop-specs  # Specification workflow
/plugin install humaninloop        # Implementation workflow
```

### Browse Installed Plugins

```bash
/plugin
```

## Architecture

The plugin ecosystem follows a **hexagonal architecture** with three layers:

- **Skills** (innermost): Pure domain knowledge—atomic, composable, no dependencies
- **Agents** (middle): Procedures + context + judgment—compose skills via `skills:` declarations
- **Workflows** (outermost): Orchestration + state management—own all persistent state

Dependencies point inward: Workflows → Agents → Skills. See [ADR-005](./docs/decisions/005-hexagonal-agent-architecture.md).

## Available Plugins

| Plugin | Description | Commands |
|--------|-------------|----------|
| [humaninloop-constitution](./plugins/humaninloop-constitution) | Project constitution setup | `/humaninloop-constitution:setup` |
| [humaninloop-core](./plugins/humaninloop-core) | Shared skills and agents (foundation) | (skills only) |
| [humaninloop-specs](./plugins/humaninloop-specs) | Specification workflow | `/humaninloop-specs:specify`, `/humaninloop-specs:checklist` |
| [humaninloop](./plugins/humaninloop) | Implementation workflow | `/humaninloop:plan`, `/humaninloop:tasks`, `/humaninloop:analyze`, `/humaninloop:implement` |

### humaninloop-constitution

Project constitution setup defining core principles, governance rules, and development standards.

**Commands:** `/humaninloop-constitution:setup`
**Dependencies:** None (install first)

### humaninloop-core

Domain-agnostic skills and shared agents that provide standalone value and are used by workflow plugins.

**Skills:** iterative-analysis, context-patterns, brownfield-patterns, validation-expertise, and more
**Agents:** codebase-discovery, validator-agent
**Dependencies:** humaninloop-constitution

### humaninloop-specs

Specification workflow with multi-agent quality validation.

**Commands:** `/humaninloop-specs:specify`, `/humaninloop-specs:checklist`
**Dependencies:** humaninloop-core, humaninloop-constitution

### humaninloop

Implementation workflow: plan → tasks → implement.

**Commands:** `/humaninloop:plan`, `/humaninloop:tasks`, `/humaninloop:analyze`, `/humaninloop:implement`
**Dependencies:** humaninloop-core, humaninloop-constitution

## Contributing

Want to add your plugin to the marketplace? See [CONTRIBUTING.md](./CONTRIBUTING.md) for guidelines.

## Documentation

- [Claude Code Plugin Documentation](./docs/claude-plugin-documentation.md) - Complete technical reference for building plugins
- [Changelog](./CHANGELOG.md) - Release history
- [Roadmap](./ROADMAP.md) - Vision and planned features
- [Architecture Decisions](./docs/decisions/) - ADRs explaining key technical choices
- [Release Philosophy](./RELEASES.md) - Versioning strategy and release guidelines

## Repository Structure

```
human-in-loop-marketplace/
├── .claude-plugin/
│   └── marketplace.json           # Marketplace manifest
├── plugins/
│   ├── humaninloop-constitution/  # Constitution setup (install first)
│   │   ├── commands/
│   │   └── templates/
│   ├── humaninloop-core/          # Shared skills & agents (install second)
│   │   ├── agents/                # codebase-discovery, validator-agent
│   │   └── skills/                # Domain-agnostic skills
│   ├── humaninloop-specs/         # Specification workflow
│   │   ├── agents/
│   │   ├── commands/
│   │   ├── skills/
│   │   └── templates/
│   └── humaninloop/               # Implementation workflow
│       ├── agents/
│       ├── commands/
│       ├── check-modules/         # Validation check modules (workflow config)
│       ├── skills/
│       └── templates/
├── specs/                         # Feature specifications
│   ├── completed/
│   ├── in-progress/
│   └── planned/
├── docs/
│   ├── decisions/                 # Architecture Decision Records
│   ├── agent-skills-documentation.md
│   └── claude-plugin-documentation.md
├── CHANGELOG.md
├── ROADMAP.md
├── RELEASES.md
├── CONTRIBUTING.md
└── LICENSE
```

## License

MIT - See [LICENSE](./LICENSE)
