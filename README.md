# HumanInLoop Claude Code Plugin Marketplace

Official Claude Code plugin marketplace for [HumanInLoop](https://humaninloop.dev).

## Quick Start

### Add the Marketplace

```bash
/plugin marketplace add humaninloop/human-in-loop-marketplace
```

### Browse Available Plugins

```bash
/plugin
```

### Install a Plugin

```bash
/plugin install example-plugin
```

## Available Plugins

| Plugin | Description | Category |
|--------|-------------|----------|
| [example-plugin](./plugins/example-plugin) | A simple example plugin demonstrating the plugin structure | examples |

## Contributing

Want to add your plugin to the marketplace? See [CONTRIBUTING.md](./CONTRIBUTING.md) for guidelines.

## Documentation

- [Claude Code Plugin Documentation](./docs/claude-plugin-documentation.md) - Complete technical reference for building plugins

## Repository Structure

```
human-in-loop-marketplace/
├── .claude-plugin/
│   └── marketplace.json      # Marketplace manifest
├── plugins/                  # All marketplace plugins
│   └── example-plugin/       # Example plugin (copy to create new plugins)
├── docs/                     # Documentation
├── README.md                 # This file
├── CONTRIBUTING.md           # Contribution guidelines
└── LICENSE                   # MIT license
```

## License

MIT - See [LICENSE](./LICENSE)
