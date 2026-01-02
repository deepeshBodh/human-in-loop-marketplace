---
name: scaffold-workflow
description: Domain knowledge for scaffolding feature branches and directories. Provides script reference, naming conventions, and integration patterns. EXCLUSIVE TO scaffold-agent - do not invoke from other agents.
---

# Scaffold Workflow Skill

## Purpose

Domain knowledge for creating feature infrastructure in the HumanInLoop specification workflow. This skill provides reference information about scripts, naming conventions, and patterns—NOT procedures.

> **Architecture Note**: Procedures belong in agents, not skills. The scaffold-agent contains the step-by-step operating procedure. This skill provides the domain knowledge the agent needs.

## Script Reference

The skill provides a single consolidated script: `scripts/create-new-feature.sh`

### create-new-feature.sh

Create the complete feature scaffold (branch + directory + template).

```bash
./scripts/create-new-feature.sh --json "Add user authentication with OAuth2"
# Output: {"BRANCH_NAME":"005-user-auth","SPEC_FILE":"specs/005-user-auth/spec.md","FEATURE_NUM":"005"}
```

**Options**:
- `--json`: Output in JSON format (required for agent integration)
- `--number N`: Override auto-detected feature number
- `--short-name <name>`: Override generated branch slug

**What it does**:
1. Generates a clean branch slug from the feature description
2. Determines the next sequential feature number
3. Creates git branch (if git repository detected)
4. Creates feature directory at `specs/{BRANCH_NAME}/`
5. Copies spec template to `specs/{BRANCH_NAME}/spec.md`
6. Exports `HUMANINLOOP_FEATURE` environment variable

## Naming Conventions

### Branch Name Generation

Rules for generating clean branch slugs from feature descriptions:

- Filters common stop words (I, want, add, the, to, for, with, create, build, implement, etc.)
- Preserves technical terms and acronyms (OAuth2, API, JWT, SSO, CRUD, REST, GraphQL)
- Uses lowercase with hyphens as separators
- Limits output to 3-4 meaningful words
- Minimum 3 characters per word (except recognized acronyms)

| Input | Generated Slug |
|-------|----------------|
| "I want to add user authentication" | `user-authentication` |
| "Implement OAuth2 integration for the API" | `oauth2-integration-api` |
| "Create a dashboard for analytics" | `dashboard-analytics` |
| "Add task priority levels" | `task-priority-levels` |

### Feature Number Detection

Rules for determining sequential feature numbers:

- Fetches all remote branches (if git repository detected)
- Scans both local/remote branches for `###-*` pattern
- Scans specs directory for existing feature directories
- Returns the maximum found + 1
- Zero-pads to 3 digits
- Starts at `001` if no existing features found

## Error Patterns

Common error scenarios and appropriate responses:

| Error Type | Detection | Response |
|------------|-----------|----------|
| Script not found | Check if script exists before execution | Report missing dependency with path |
| Git error | Check git command exit status | Report specific git error message |
| Permission error | Check file operation results | Report path with permission issue |
| Partial failure | Track each step completion | Document partial state, attempt cleanup |
| Duplicate branch | Check if branch already exists | Increment feature number and retry |
| Template not found | Check if template file exists | Create empty file as fallback, warn |

## Integration Reference

### Template Paths

Templates are located in the shared templates directory:
- `${CLAUDE_PLUGIN_ROOT}/templates/index-template.md` (unified workflow state)
- `${CLAUDE_PLUGIN_ROOT}/templates/spec-template.md`
- `${CLAUDE_PLUGIN_ROOT}/templates/checklist-template.md`

### Environment Variables

- `CLAUDE_PLUGIN_ROOT`: Path to the humaninloop plugin directory
- `SKILL_ROOT`: Path to this skill's directory (for script access)
- `HUMANINLOOP_FEATURE`: Set by create-new-feature.sh to the branch name

### Output Directory Structure

```
specs/{BRANCH_NAME}/
├── spec.md                    # Feature specification
├── checklists/                # Quality validation checklists
└── .workflow/
    └── index.md               # Unified workflow state
```
