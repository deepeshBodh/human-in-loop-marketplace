# Claude Code plugins: Complete technical reference

Claude Code plugins are composable extensions that enhance the agentic coding tool with custom slash commands, agent skills, hooks, and MCP server integrations. This comprehensive reference covers all 11 areas of the plugin system based exclusively on official Anthropic documentation. **Key finding**: plugins use a standardized directory structure with `.claude-plugin/plugin.json` as the required manifest, while all components (commands, skills, hooks) must be placed at the plugin root—not inside the metadata directory.

---

## 1. Plugin architecture

**Summary**: Plugins follow a well-defined directory structure with `.claude-plugin/plugin.json` as the only required file. All component directories exist at the plugin root level. Plugins are copied to a cache directory upon installation, and all internal paths must use the `${CLAUDE_PLUGIN_ROOT}` environment variable.

### Technical details

**Complete directory structure**:
```
my-plugin/
├── .claude-plugin/           # Metadata directory (REQUIRED)
│   └── plugin.json          # Plugin manifest (REQUIRED)
├── commands/                 # Slash command Markdown files
│   ├── deploy.md
│   └── test.md
├── agents/                   # Subagent Markdown files
│   └── code-reviewer.md
├── skills/                   # Agent Skills
│   └── pdf-processor/
│       ├── SKILL.md
│       └── scripts/
├── hooks/                    # Hook configurations
│   └── hooks.json
├── .mcp.json                # MCP server definitions
├── .lsp.json                # LSP server definitions
├── scripts/                 # Utility scripts
│   └── format.sh
├── README.md
└── LICENSE
```

**plugin.json complete schema**:
```json
{
  "name": "plugin-name",           // REQUIRED: kebab-case identifier
  "version": "1.0.0",              // Semantic version
  "description": "Brief plugin description",
  "author": {
    "name": "Author Name",
    "email": "author@example.com",
    "url": "https://github.com/author"
  },
  "homepage": "https://docs.example.com",
  "repository": "https://github.com/author/plugin",
  "license": "MIT",
  "keywords": ["keyword1", "keyword2"],
  "commands": ["./custom/commands/special.md"],
  "agents": "./custom/agents/",
  "skills": "./custom/skills/",
  "hooks": "./config/hooks.json",
  "mcpServers": "./mcp-config.json",
  "outputStyles": "./styles/",
  "lspServers": "./.lsp.json"
}
```

**Loading process**: Claude Code scans for plugins at startup and loads them from three scopes: user (`~/.claude/settings.json`), project (`.claude/settings.json`), and managed (enterprise-controlled). Component paths supplement—not replace—default directories.

**Environment variables available**:
| Variable | Description |
|----------|-------------|
| `${CLAUDE_PLUGIN_ROOT}` | Absolute path to cached plugin directory |
| `${CLAUDE_PROJECT_DIR}` | Project root directory |
| `CLAUDE_CODE_REMOTE` | `"true"` if running remotely |

### Source URLs
- https://code.claude.com/docs/en/plugins-reference
- https://code.claude.com/docs/en/plugins
- https://github.com/anthropics/claude-code/tree/main/plugins

### Gaps
- No formal JSON schema validator provided
- The `outputStyles` and `lspServers` fields lack detailed documentation
- Path resolution behavior for symlinks within plugins is unclear

---

## 2. Commands (slash commands)

**Summary**: Slash commands are Markdown files with YAML frontmatter that define reusable prompts. Commands support arguments via `$ARGUMENTS` (all) and `$1`, `$2`, `$3` (positional), dynamic content injection with `!`backticks`` for bash and `@path` for files, and are inherently multi-turn since they execute within ongoing conversations.

### Technical details

**Command file format**:
```markdown
---
description: Brief description for /help and SlashCommand tool
allowed-tools: Bash(git add:*), Read, Write, Edit
argument-hint: [pr-number] [priority]
model: claude-3-5-haiku-20241022
disable-model-invocation: false
---

# Optional Heading

Review PR #$1 with priority $2.

## Context from Git
!`git diff HEAD~1`

## Files to Review
@src/main.py

Focus on security, performance, and code style.
```

**Frontmatter options**:
| Option | Type | Description | Default |
|--------|------|-------------|---------|
| `description` | string | Shows in /help; used by SlashCommand tool for auto-invocation | First line |
| `allowed-tools` | string | Comma-separated tool list | Inherits |
| `argument-hint` | string | Shows during autocomplete (e.g., `[file] [mode]`) | None |
| `model` | string | Specific model to use | Inherits |
| `disable-model-invocation` | boolean | Prevent SlashCommand tool from calling this | `false` |

**Argument handling**:
- `$ARGUMENTS` — All text after the command name
- `$1`, `$2`, `$3`... — Positional arguments by index
- Arguments are space-separated; usage: `/deploy production fast`

**Namespaced commands**: Plugin commands use format `/plugin-name:command-name`:
```
/my-plugin:hello          # From my-plugin
/formatter:format-code    # From formatter plugin
```

**Location hierarchy**:
| Scope | Location | Display |
|-------|----------|---------|
| Project | `.claude/commands/` | (project) |
| User | `~/.claude/commands/` | (user) |
| Plugin | `<plugin>/commands/` | Namespaced |

### Source URLs
- https://code.claude.com/docs/en/slash-commands
- https://code.claude.com/docs/en/plugins-reference

### Gaps
- No documentation on maximum argument count
- Multi-word quoted arguments behavior undocumented
- SlashCommand tool character budget (default 15,000) lacks configuration guidance

---

## 3. Skills (agent skills)

**Summary**: Skills differ from commands by being **model-invoked**—Claude autonomously decides when to use them based on the skill's description. Skills use a progressive disclosure system: only metadata loads initially, with the full `SKILL.md` body loaded on-demand. Skills can include bundled scripts and resources, and support tool restrictions via `allowed-tools`.

### Technical details

**Skill vs command comparison**:
| Aspect | Skills | Commands |
|--------|--------|----------|
| Invocation | Model-invoked automatically | User-invoked via `/command` |
| Trigger | Task context matching | Manual typing |
| Format | Directory with SKILL.md | Single Markdown file |
| Resources | Can bundle scripts, references | Standalone |

**SKILL.md complete format**:
```markdown
---
name: pdf-processor
description: |
  Extract text and tables from PDF files, fill forms, merge documents.
  Use when working with PDF files or .pdf extensions.
license: MIT
allowed-tools: Bash, Read, Write
metadata:
  version: "1.0.0"
  author: "developer-name"
---

# PDF Processor

## Instructions
1. Check if the file is a valid PDF
2. Use the bundled extraction script for text
3. Return formatted output

## Examples
- "Extract text from contract.pdf" → Triggers this skill
- "Convert report.pdf to markdown" → Triggers this skill
```

**YAML frontmatter fields**:
| Field | Required | Description |
|-------|----------|-------------|
| `name` | Yes | Must match directory name (lowercase, hyphenated) |
| `description` | Yes | What it does + when to use (~100 words max) |
| `license` | No | License identifier |
| `allowed-tools` | No | Comma-separated tool list (Claude Code only) |
| `metadata` | No | Custom key-value pairs |

**Progressive disclosure**:
1. **Level 1**: Name + description loaded at startup into system prompt
2. **Level 2**: Full SKILL.md read via Bash tool when skill is relevant
3. **Level 3**: Bundled resources loaded as needed

**Recommended directory structure**:
```
skill-name/
├── SKILL.md           # Required
├── LICENSE.txt        # Optional
├── scripts/           # Executable code
│   └── processor.py
├── references/        # Documentation
│   └── api-guide.md
└── assets/            # Templates, data files
    └── template.xlsx
```

**Triggering optimization**: Write descriptions with keywords users would mention. Poor: "Helps with documents". Better: "Extract text from PDF files, fill forms, merge documents. Use when working with .pdf files."

### Source URLs
- https://www.anthropic.com/engineering/equipping-agents-for-the-real-world-with-agent-skills
- https://github.com/anthropics/skills/blob/main/agent_skills_spec.md
- https://www.anthropic.com/news/skills

### Gaps
- Exact token budget for skill metadata in system prompt undocumented
- Interaction between multiple overlapping skills unclear
- Skill debugging/testing tools not provided

---

## 4. Hooks

**Summary**: Hooks execute shell commands or LLM prompts in response to **10 lifecycle events**. Hooks can block operations, modify tool inputs, inject context, and control Claude's behavior. All matching hooks run in parallel with a default **60-second timeout**.

### Technical details

**hooks.json complete schema**:
```json
{
  "description": "Optional description",
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Write|Edit",
        "hooks": [
          {
            "type": "command",
            "command": "${CLAUDE_PLUGIN_ROOT}/scripts/validate.sh",
            "timeout": 30
          }
        ]
      }
    ],
    "Stop": [
      {
        "hooks": [
          {
            "type": "prompt",
            "prompt": "Evaluate if all tasks are complete: $ARGUMENTS",
            "timeout": 15
          }
        ]
      }
    ]
  }
}
```

**All hook events**:
| Event | Uses Matcher? | Can Block? | Description |
|-------|---------------|------------|-------------|
| `PreToolUse` | Yes | Yes | Before tool calls |
| `PermissionRequest` | Yes | Yes | On permission dialogs |
| `PostToolUse` | Yes | No | After tool completes |
| `UserPromptSubmit` | No | Yes | Before processing user prompt |
| `Notification` | Yes | No | On notifications |
| `Stop` | No | Yes | When Claude attempts to stop |
| `SubagentStop` | No | Yes | When subagent attempts to stop |
| `PreCompact` | Yes | No | Before compaction |
| `SessionStart` | Yes | No | At session start |
| `SessionEnd` | No | No | At session end |

**Hook types**:
- `"type": "command"` — Runs any shell command/script
- `"type": "prompt"` — Sends prompt to fast LLM (Haiku)

**Exit code behavior**:
| Exit Code | Result |
|-----------|--------|
| 0 | Success; JSON output parsed for decisions |
| 2 | Blocking error; stderr shown to Claude |
| Other | Non-blocking error; execution continues |

**Decision control via JSON output** (PreToolUse example):
```json
{
  "hookSpecificOutput": {
    "hookEventName": "PreToolUse",
    "permissionDecision": "allow|deny|ask",
    "permissionDecisionReason": "Explanation",
    "updatedInput": { "field": "modified_value" }
  }
}
```

**Matcher patterns**:
- Simple: `Write` matches Write tool only
- Regex: `Write|Edit` or `Notebook.*`
- `*` or `""` or omitted = match all

### Source URLs
- https://code.claude.com/docs/en/hooks
- https://code.claude.com/docs/en/hooks-guide
- https://github.com/anthropics/claude-code/tree/main/examples/hooks

### Gaps
- JSON output schema for each event type not fully documented
- Hook priority/ordering when multiple hooks from different sources match
- Maximum concurrent hooks limit not specified

---

## 5. MCP integration

**Summary**: Plugins can bundle MCP servers that start automatically when the plugin is enabled. Configuration uses `.mcp.json` at plugin root or inline in `plugin.json`. Both stdio (local) and HTTP (remote) transports are supported with full environment variable expansion and OAuth authentication.

### Technical details

**.mcp.json complete schema**:
```json
{
  "mcpServers": {
    "local-database": {
      "command": "${CLAUDE_PLUGIN_ROOT}/servers/db-server",
      "args": ["--config", "${CLAUDE_PLUGIN_ROOT}/config.json"],
      "env": {
        "DB_PATH": "${CLAUDE_PLUGIN_ROOT}/data",
        "SECRET": "${USER_SECRET}"
      },
      "cwd": "${CLAUDE_PLUGIN_ROOT}"
    },
    "remote-api": {
      "type": "http",
      "url": "${API_URL:-https://api.example.com}/mcp",
      "headers": {
        "Authorization": "Bearer ${API_KEY}"
      }
    }
  }
}
```

**Configuration fields**:
| Field | Type | Description |
|-------|------|-------------|
| `command` | string | Executable path (stdio) |
| `args` | string[] | Command arguments |
| `env` | object | Environment variables |
| `cwd` | string | Working directory |
| `type` | string | Transport: `"stdio"`, `"http"`, `"sse"` |
| `url` | string | Server URL (HTTP/SSE) |
| `headers` | object | Auth headers (HTTP) |

**Environment variable expansion**:
- `${VAR}` — Expands to environment variable value
- `${VAR:-default}` — Uses default if VAR unset

**Inline configuration** (in plugin.json):
```json
{
  "name": "my-plugin",
  "mcpServers": {
    "plugin-tools": {
      "command": "${CLAUDE_PLUGIN_ROOT}/server",
      "args": ["--port", "8080"]
    }
  }
}
```

**Authentication methods**:
1. **OAuth 2.0**: For remote servers, use `/mcp` command to authenticate
2. **Headers**: API keys via headers field
3. **Environment**: Credentials via env variables

**Important**: Restart Claude Code required after enabling/disabling plugins with MCP servers.

### Source URLs
- https://code.claude.com/docs/en/mcp
- https://code.claude.com/docs/en/plugins-reference

### Gaps
- MCP server startup timeout not documented
- Server crash recovery behavior unclear
- Token limits for MCP output (default 25,000) not configurable per-server

---

## 6. Local storage and file access

**Summary**: Plugins inherit Claude Code's permission model—write access is restricted to the working directory and subdirectories, while read access extends more broadly. Plugins are copied to a cache directory on installation and cannot reference files outside their structure. Sandboxing uses OS-level enforcement (bubblewrap on Linux, Seatbelt on macOS).

### Technical details

**File access rules**:
| Operation | Default Permission | Scope |
|-----------|-------------------|-------|
| Write | Project only | Working directory + subdirectories |
| Read | Broader | Project + system libraries |
| Create directory | Permitted | Within project scope |

**Plugin isolation**:
- Plugins copied to cache at install time
- Cannot reference `../` paths outside plugin structure
- `${CLAUDE_PLUGIN_ROOT}` provides absolute path to cached location

**Sandboxing**:
- **Linux**: bubblewrap for process isolation
- **macOS**: Seatbelt sandbox enforcement
- Child processes inherit sandbox boundaries
- Network access controlled via proxy

**Storing plugin data**:
```
plugin-name/
├── .claude-plugin/plugin.json
├── config/               # Plugin configuration
├── data/                 # Plugin data storage
└── scripts/              # Executable scripts
```

Use `${CLAUDE_PLUGIN_ROOT}/data/` for plugin-specific storage referenced via environment variable.

**Permission modes**:
| Mode | Description |
|------|-------------|
| `default` | Prompts on first use |
| `acceptEdits` | Auto-accepts file edits |
| `plan` | Read-only analysis |
| `bypassPermissions` | Skips all prompts (dangerous) |

### Source URLs
- https://code.claude.com/docs/en/security
- https://code.claude.com/docs/en/sandboxing
- https://code.claude.com/docs/en/iam

### Gaps
- Maximum file size for plugin data storage
- Cache location path not documented
- Cleanup behavior when plugin uninstalled

---

## 7. Marketplace and distribution

**Summary**: Plugin marketplaces are curated collections hosted on GitHub repositories or local directories. Users add marketplaces via `/plugin marketplace add` and install plugins with `/plugin install`. Auto-update is supported per-marketplace. **No formal review process exists**—distribution is decentralized with user-driven trust.

### Technical details

**marketplace.json complete schema**:
```json
{
  "$schema": "https://anthropic.com/claude-code/marketplace.schema.json",
  "name": "my-marketplace",
  "version": "1.0.0",
  "description": "Collection description",
  "owner": {
    "name": "Organization Name",
    "email": "support@example.com"
  },
  "plugins": [
    {
      "name": "plugin-name",
      "description": "What the plugin does",
      "source": "./plugins/plugin-name",
      "category": "development",
      "version": "1.0.0",
      "author": {
        "name": "Author",
        "email": "author@example.com"
      }
    }
  ]
}
```

**Installation commands**:
```bash
# Add marketplace from GitHub
/plugin marketplace add anthropics/claude-code

# Add local marketplace
/plugin marketplace add ./my-local-marketplace

# Install plugin
/plugin install plugin-name

# Install specific plugin from marketplace
/plugin install plugin-name@marketplace-name
```

**Configuration in settings.json**:
```json
{
  "extraKnownMarketplaces": {
    "team-plugins": {
      "source": {
        "source": "github",
        "repo": "org/internal-plugins"
      }
    }
  },
  "enabledPlugins": {
    "plugin-name@team-plugins": true
  }
}
```

**Auto-update**: Per-marketplace toggle; plugins update at session start when enabled.

**Enterprise deployment**: Use managed settings locations:
- macOS/Linux: `/etc/claude-code/`
- Windows: `C:\ProgramData\ClaudeCode\`

### Source URLs
- https://www.anthropic.com/news/claude-code-plugins
- https://code.claude.com/docs/en/plugin-marketplaces
- https://github.com/anthropics/claude-code/blob/main/.claude-plugin/marketplace.json

### Gaps
- Schema URL returns 404 (known issue #9686)
- Version constraint syntax (semver ranges) undocumented
- Conflict resolution when same plugin exists in multiple marketplaces

---

## 8. Development and testing

**Summary**: Development uses a local marketplace workflow—create a directory marketplace, add it to Claude Code, then iterate with uninstall/reinstall cycles. Debugging uses `claude --debug` and the `/hooks` menu. The `plugin-dev` toolkit provides validation scripts.

### Technical details

**Development workflow**:
```bash
# 1. Create development structure
mkdir dev-marketplace
cd dev-marketplace
mkdir -p .claude-plugin my-plugin/.claude-plugin

# 2. Create marketplace manifest
cat > .claude-plugin/marketplace.json << 'EOF'
{
  "name": "dev-marketplace",
  "owner": { "name": "Developer" },
  "plugins": [
    {
      "name": "my-plugin",
      "source": "./my-plugin",
      "description": "Development plugin"
    }
  ]
}
EOF

# 3. Create plugin manifest
cat > my-plugin/.claude-plugin/plugin.json << 'EOF'
{ "name": "my-plugin" }
EOF

# 4. Add and install
claude
/plugin marketplace add ./dev-marketplace
/plugin install my-plugin@dev-marketplace
```

**Iteration cycle**:
```bash
/plugin uninstall my-plugin@dev-marketplace
/plugin install my-plugin@dev-marketplace
# Then restart Claude Code
```

**Debugging tools**:
```bash
# Verbose startup logging
claude --debug

# Check hook registration
/hooks

# View plugin status
/plugin
```

**Validation scripts** (from plugin-dev toolkit):
```bash
./validate-hook-schema.sh hooks/hooks.json
./test-hook.sh my-hook.sh test-input.json
./hook-linter.sh my-hook.sh
```

**Common issues and solutions**:
| Issue | Cause | Solution |
|-------|-------|----------|
| Plugin not loading | Invalid JSON | Validate syntax |
| Commands missing | Wrong directory | Place `commands/` at root, not in `.claude-plugin/` |
| Hooks not firing | Not executable | `chmod +x script.sh` |
| Path errors | Absolute paths | Use relative paths with `./` |
| Changes not applied | Cached copy | Uninstall, reinstall, restart |

### Source URLs
- https://code.claude.com/docs/en/plugins
- https://code.claude.com/docs/en/plugins-reference
- https://github.com/anthropics/claude-code/tree/main/plugins/plugin-dev

### Gaps
- No hot-reload capability documented
- Unit testing framework for plugin code
- CI/CD integration guidance missing

---

## 9. Best practices and examples

**Summary**: Anthropic provides several official example plugins including plugin-dev toolkit, agent-sdk-dev, code-review, and hookify. Key patterns include using `${CLAUDE_PLUGIN_ROOT}` for all paths, proper frontmatter in all Markdown files, and separating concerns across components.

### Technical details

**Official example plugins** (github.com/anthropics/claude-code/plugins):

| Plugin | Purpose | Key Features |
|--------|---------|--------------|
| plugin-dev | Build plugins | 8-phase creation workflow, validators |
| agent-sdk-dev | SDK development | Interactive setup, verification agents |
| code-review | PR reviews | Multi-agent review with scoring |
| pr-review-toolkit | PR analysis | 6 specialized analyzer agents |
| hookify | Hook creation | Markdown-based hook configuration |
| ralph-loop | Iterative dev | Self-referential AI loops |

**Recommended patterns**:

1. **Path handling**: Always use `${CLAUDE_PLUGIN_ROOT}` in hooks and MCP configs
2. **Error handling**: Return exit code 2 for blocking errors with descriptive stderr
3. **Security**: Quote shell variables, validate inputs, skip sensitive files
4. **Structure**: Keep `.claude-plugin/` minimal—only plugin.json inside

**Command best practice**:
```markdown
---
description: Comprehensive code review with security focus
allowed-tools: Read, Grep, Glob, Bash(git diff:*)
argument-hint: [file-pattern]
---

## Context
!`git status`

## Review Checklist
Review @$ARGUMENTS for:
1. Security vulnerabilities
2. Performance issues
3. Code style
```

**Hook best practice**:
```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Write|Edit",
        "hooks": [{
          "type": "command",
          "command": "\"${CLAUDE_PLUGIN_ROOT}/scripts/format.sh\" \"$CLAUDE_PROJECT_DIR\"",
          "timeout": 30
        }]
      }
    ]
  }
}
```

### Source URLs
- https://github.com/anthropics/claude-code/tree/main/plugins
- https://github.com/anthropics/skills
- https://github.com/anthropics/claude-plugins-official

### Gaps
- No style guide for plugin authoring
- Performance benchmarking guidance missing
- Accessibility considerations not documented

---

## 10. Limitations and constraints

**Summary**: Plugins cannot write outside the project directory, access blocked network domains, or bypass the permission system. Hooks have a **60-second timeout** by default. MCP permissions don't support wildcards. Malicious hook modifications are mitigated by startup snapshots requiring explicit review.

### Technical details

**What plugins CANNOT do**:
- ❌ Write to parent directories without explicit permission
- ❌ Reference files outside the copied plugin structure
- ❌ Access unapproved network domains (when sandboxed)
- ❌ Use wildcard (`*`) in MCP tool permissions
- ❌ Bypass startup hook snapshots for current session
- ❌ Execute commands in blocklist (curl, wget by default)

**Resource constraints**:
| Resource | Limit |
|----------|-------|
| Hook timeout | 60 seconds default (configurable) |
| MCP output | 25,000 tokens max |
| SlashCommand | 15,000 characters |

**Crash handling**:
| Hook Event | Exit 2 Behavior |
|------------|-----------------|
| PreToolUse | Blocks tool call |
| PermissionRequest | Denies permission |
| PostToolUse | Shows error (tool already ran) |
| Stop | Blocks stoppage |

**Security model**:
- Hooks snapshotted at startup; external modifications warned
- Changes require review in `/hooks` menu
- Enterprise can block non-managed hooks with `allowManagedHooksOnly`
- Scripts must be executable (`chmod +x`)

**Sandboxing enforcement**:
- Linux: bubblewrap
- macOS: Seatbelt
- Windows: Not yet supported

### Source URLs
- https://code.claude.com/docs/en/security
- https://code.claude.com/docs/en/sandboxing
- https://code.claude.com/docs/en/iam

### Gaps
- Memory limits for plugin execution
- Concurrent plugin count limits
- Behavior when multiple plugins conflict

---

## 11. Platform integration

**Summary**: Claude Code is built on the Claude API and uses the same infrastructure. The **Claude Agent SDK** (formerly Claude Code SDK) provides programmatic access to the same agent loop, tools, and context management. Plugins inherit parent authentication—no separate billing—and all usage is billed through standard API token pricing.

### Technical details

**API relationship**:
- Claude Code uses Claude API with ANTHROPIC_API_KEY
- Supports third-party providers: AWS Bedrock, Google Vertex AI, Microsoft Foundry
- Agent SDK provides identical capabilities programmatically

**Agent SDK integration**:
```python
# Python SDK with plugin support
from claude_agent_sdk import Agent

agent = Agent(
    setting_sources=["project"],  # Enables plugins
    plugins=["plugin-name"]
)
```

```typescript
// TypeScript SDK
const agent = new Agent({
  settingSources: ['project'],
  plugins: ['plugin-name']
});
```

**Billing**:
| Model | Input | Output |
|-------|-------|--------|
| Claude Opus 4.5 | $5/MTok | $25/MTok |
| Claude Sonnet 4.5 | $3/MTok | $15/MTok |
| Claude Haiku 4 | $0.25/MTok | $1.25/MTok |

Consumer plans (Pro $20/mo, Max $100/mo) do NOT reduce API costs for organizations.

**Platform features accessible to plugins**:
- Built-in tools: Read, Write, Edit, Bash, Glob, Grep
- Web tools: WebFetch, WebSearch
- MCP connector for remote servers
- Subagent orchestration
- Prompt caching (automatic)

**Authentication flow**:
- Plugins inherit parent Claude Code authentication
- MCP servers use OAuth for remote connections
- No separate plugin-level authentication

### Source URLs
- https://platform.claude.com/docs/en/agent-sdk/overview
- https://www.anthropic.com/engineering/building-agents-with-the-claude-agent-sdk
- https://www.anthropic.com/pricing

### Gaps
- Custom tool creation from plugins (vs MCP) undocumented
- Rate limit behavior for plugin-heavy workflows
- Multi-tenant plugin architecture considerations

---

## Consolidated reference

### Complete plugin.json schema

```json
{
  "name": "plugin-name",                    // REQUIRED: kebab-case
  "version": "1.0.0",                       // Semantic version
  "description": "Brief description",       // What the plugin does
  "author": {
    "name": "Author Name",
    "email": "email@example.com",
    "url": "https://github.com/author"
  },
  "homepage": "https://docs.example.com",
  "repository": "https://github.com/author/plugin",
  "license": "MIT",
  "keywords": ["keyword1", "keyword2"],
  "commands": "./commands/",                // String or array of paths
  "agents": "./agents/",
  "skills": "./skills/",
  "hooks": "./hooks/hooks.json",            // Path or inline object
  "mcpServers": "./.mcp.json",              // Path or inline object
  "outputStyles": "./styles/",
  "lspServers": "./.lsp.json"
}
```

### Complete SKILL.md template

```markdown
---
name: skill-name                           # REQUIRED: matches directory name
description: |                             # REQUIRED: what + when
  Clear description of what this skill does.
  Include keywords users would mention.
  Specify when Claude should use this skill.
license: MIT
allowed-tools: Bash, Read, Write           # Claude Code only
metadata:
  version: "1.0.0"
  author: "developer-name"
  category: "development"
---

# Skill Name

## Purpose
Explain the skill's primary function.

## Instructions
1. Step-by-step guidance for Claude
2. Clear, actionable instructions
3. Include edge case handling

## Examples
- "User query example" → Expected behavior
- "Another example" → Expected behavior

## Guidelines
- Important consideration 1
- Important consideration 2
```

### Complete hooks.json schema

```json
{
  "description": "Hook configuration description",
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Write|Edit",
        "hooks": [
          {
            "type": "command",
            "command": "${CLAUDE_PLUGIN_ROOT}/scripts/validate.sh",
            "timeout": 30
          }
        ]
      }
    ],
    "PostToolUse": [
      {
        "matcher": "Bash",
        "hooks": [
          {
            "type": "command",
            "command": "echo 'Command completed' >> ${CLAUDE_PROJECT_DIR}/log.txt",
            "timeout": 10
          }
        ]
      }
    ],
    "Stop": [
      {
        "hooks": [
          {
            "type": "prompt",
            "prompt": "Check if all tasks are complete: $ARGUMENTS",
            "timeout": 15
          }
        ]
      }
    ],
    "SessionStart": [
      {
        "matcher": "startup",
        "hooks": [
          {
            "type": "command",
            "command": "${CLAUDE_PLUGIN_ROOT}/scripts/init.sh"
          }
        ]
      }
    ]
  }
}
```

### Complete marketplace.json schema

```json
{
  "$schema": "https://anthropic.com/claude-code/marketplace.schema.json",
  "name": "marketplace-name",
  "version": "1.0.0",
  "description": "Marketplace description",
  "owner": {
    "name": "Organization Name",
    "email": "support@example.com",
    "url": "https://example.com"
  },
  "plugins": [
    {
      "name": "plugin-name",
      "description": "Plugin description",
      "source": "./plugins/plugin-name",
      "category": "development",
      "version": "1.0.0",
      "author": {
        "name": "Author Name",
        "email": "author@example.com"
      }
    }
  ]
}
```

### Full-featured plugin directory structure

```
enterprise-plugin/
├── .claude-plugin/
│   └── plugin.json              # Required manifest
├── commands/
│   ├── deploy.md                # /enterprise-plugin:deploy
│   ├── test.md                  # /enterprise-plugin:test
│   └── status.md                # /enterprise-plugin:status
├── agents/
│   ├── security-reviewer.md
│   ├── performance-tester.md
│   └── compliance-checker.md
├── skills/
│   ├── code-review/
│   │   ├── SKILL.md
│   │   └── scripts/
│   │       └── analyzer.py
│   └── documentation/
│       ├── SKILL.md
│       └── references/
│           └── style-guide.md
├── hooks/
│   └── hooks.json
├── .mcp.json
├── .lsp.json
├── scripts/
│   ├── pre-commit.sh
│   ├── format.py
│   └── validate.js
├── config/
│   └── settings.json
├── data/
│   └── .gitkeep
├── README.md
├── LICENSE
└── CHANGELOG.md
```

---

## Conclusion

Claude Code's plugin architecture provides a comprehensive extension system built on five core components: **slash commands** for user-invoked shortcuts, **agent skills** for model-invoked capabilities, **hooks** for lifecycle event handling, **MCP servers** for external tool integration, and **subagents** for specialized tasks. The system prioritizes composability through standardized Markdown formats with YAML frontmatter, security through sandboxing and permission controls, and distribution through decentralized GitHub-based marketplaces. 

Key constraints include the 60-second hook timeout, write restrictions to project scope only, and the requirement to restart Claude Code after MCP changes. The progressive disclosure model for skills—loading metadata first, then full content on-demand—represents an elegant solution to context window management. For teams, the managed settings system and enterprise policy controls enable organizational governance without sacrificing developer flexibility.