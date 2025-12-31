# Example Plugin

A simple example plugin demonstrating the Claude Code plugin structure.

## Installation

```bash
# First, add the HumanInLoop marketplace (if not already added)
/plugin marketplace add humaninloop/human-in-loop-marketplace

# Install this plugin
/plugin install example-plugin
```

## Commands

### `/example-plugin:hello [name]`

Displays a greeting message. Optionally include a name to personalize the greeting.

**Examples:**
- `/example-plugin:hello` - Generic greeting
- `/example-plugin:hello World` - Greets "World"

## Plugin Structure

```
example-plugin/
├── .claude-plugin/
│   └── plugin.json      # Plugin manifest
├── commands/
│   └── hello.md         # Slash command
├── README.md            # This file
└── LICENSE              # MIT license
```

## License

MIT
