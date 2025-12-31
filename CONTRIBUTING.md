# Contributing to HumanInLoop Marketplace

Thank you for your interest in contributing a plugin to the HumanInLoop marketplace!

## Submitting a Plugin

### 1. Create Your Plugin

Copy the example plugin as a starting point:

```bash
cp -r plugins/example-plugin plugins/your-plugin-name
```

Then update the values in your copied plugin.

### 2. Required Files

Your plugin must include:

```
your-plugin-name/
├── .claude-plugin/
│   └── plugin.json      # REQUIRED: Plugin manifest
├── commands/            # Slash commands (optional)
│   └── *.md
├── agents/              # Subagents (optional)
│   └── *.md
├── skills/              # Agent skills (optional)
│   └── skill-name/
│       └── SKILL.md
├── hooks/               # Hooks (optional)
│   └── hooks.json
├── README.md            # REQUIRED: Plugin documentation
└── LICENSE              # REQUIRED: License file
```

### 3. Plugin Manifest (`plugin.json`)

```json
{
  "name": "your-plugin-name",
  "version": "1.0.0",
  "description": "Brief description of what your plugin does",
  "author": {
    "name": "Your Name",
    "email": "your@email.com"
  },
  "license": "MIT",
  "keywords": ["relevant", "keywords"]
}
```

### 4. Submit a Pull Request

1. Fork this repository
2. Add your plugin to the `plugins/` directory
3. Add an entry to `.claude-plugin/marketplace.json`:

```json
{
  "name": "your-plugin-name",
  "description": "Brief description",
  "source": "./plugins/your-plugin-name",
  "category": "category-name",
  "version": "1.0.0",
  "author": {
    "name": "Your Name"
  }
}
```

4. Submit a pull request

## Plugin Guidelines

### Naming

- Use kebab-case: `my-plugin-name`
- Be descriptive but concise
- Avoid generic names like `utils` or `helpers`

### Categories

Use one of these categories:
- `development` - Dev tools, linting, formatting
- `productivity` - Workflow automation
- `documentation` - Doc generation, READMEs
- `testing` - Test runners, coverage
- `deployment` - CI/CD, deployment helpers
- `examples` - Example/demo plugins

### Quality Checklist

- [ ] Plugin has a clear, single purpose
- [ ] README explains what the plugin does and how to use it
- [ ] All commands have descriptions in frontmatter
- [ ] No hardcoded paths (use `${CLAUDE_PLUGIN_ROOT}`)
- [ ] Scripts are executable (`chmod +x`)
- [ ] License file included

## Documentation

See [docs/claude-plugin-documentation.md](./docs/claude-plugin-documentation.md) for the complete Claude Code plugin technical reference.

## Questions?

Open an issue if you have questions about contributing.
