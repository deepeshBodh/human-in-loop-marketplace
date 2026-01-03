# HumanInLoop Claude Code Plugin Marketplace

Official Claude Code plugin marketplace for [HumanInLoop](https://humaninloop.dev).

## Quick Start

### Add the Marketplace

```bash
/plugin marketplace add deepeshBodh/human-in-loop-marketplace
```

### Install Plugins

```bash
# Install constitution plugin first (required by humaninloop)
/plugin install humaninloop-constitution
/humaninloop-constitution:setup

# Install main workflow plugin
/plugin install humaninloop
```

### Browse Installed Plugins

```bash
/plugin
```

## Available Plugins

| Plugin | Description | Commands |
|--------|-------------|----------|
| [humaninloop](./plugins/humaninloop) | Specification-driven development workflow: specify → plan → tasks → implement | `/humaninloop:specify`, `/humaninloop:plan`, `/humaninloop:tasks`, `/humaninloop:analyze`, `/humaninloop:checklist`, `/humaninloop:implement` |
| [humaninloop-constitution](./plugins/humaninloop-constitution) | Project constitution setup for HumanInLoop workflows | `/humaninloop-constitution:setup` |

### humaninloop

Multi-agent specification-driven development workflow with integrated quality validation.

**Agents:** 14 specialized agents for spec writing, validation, planning, task generation, and research
**Commands:** `/humaninloop:specify`, `/humaninloop:plan`, `/humaninloop:tasks`, `/humaninloop:analyze`, `/humaninloop:checklist`, `/humaninloop:implement`
**Skills:** 3 model-invoked skills for authoring and analysis
**Requires:** `humaninloop-constitution` plugin

#### Skills

| Skill | Trigger Phrases | Description |
|-------|-----------------|-------------|
| `iterative-analysis` | "brainstorm", "deep analysis", "let's think through" | Progressive questioning with 2-3 options per question and synthesis |
| `authoring-requirements` | "functional requirements", "FR-", "RFC 2119", "MUST SHOULD MAY" | Write FR-XXX format requirements with validation |
| `authoring-user-stories` | "user story", "Given When Then", "P1", "P2", "P3" | Write prioritized user stories with acceptance scenarios |

### humaninloop-constitution

Project constitution setup defining core principles, governance rules, and development standards.

**Commands:** `/humaninloop-constitution:setup`

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
│   ├── humaninloop/               # Main workflow plugin
│   │   ├── agents/                # 14 multi-agent workflow agents
│   │   ├── commands/              # specify, plan, tasks, analyze, checklist, implement
│   │   ├── skills/                # 3 model-invoked authoring skills
│   │   ├── check-modules/         # Validation check modules
│   │   ├── scripts/               # Shell utilities
│   │   └── templates/             # Workflow templates
│   └── humaninloop-constitution/  # Constitution setup plugin
│       ├── commands/              # setup command
│       └── templates/             # Constitution template
├── specs/                         # Feature specifications
│   ├── completed/                 # Shipped features
│   ├── in-progress/               # Currently implementing
│   └── planned/                   # Future features
├── docs/
│   ├── decisions/                 # Architecture Decision Records
│   └── claude-plugin-documentation.md
├── CHANGELOG.md                   # Release history
├── ROADMAP.md                     # Vision and planned features
├── RELEASES.md                    # Release philosophy
├── CONTRIBUTING.md
└── LICENSE
```

## License

MIT - See [LICENSE](./LICENSE)
