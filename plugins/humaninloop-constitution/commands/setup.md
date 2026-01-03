---
description: Create or update the project constitution using the Principal Architect agent.
handoffs:
  - label: Build Specification
    agent: humaninloop:specify
    prompt: Build a feature specification governed by the constitution. I want to build...
---

## User Input

```text
$ARGUMENTS
```

## Workflow

1. **Check for existing constitution**
   ```bash
   cat .humaninloop/memory/constitution.md 2>/dev/null
   ```

2. **Detect project context**
   - Check `package.json`, `pyproject.toml`, `pubspec.yaml`, etc. for tech stack
   - Check if `CLAUDE.md` exists

3. **Invoke Principal Architect agent**

   Pass context directly in the prompt:

   ```
   Task(
     subagent_type: "humaninloop-constitution:principal-architect",
     prompt: "
       Create a constitution for [PROJECT_NAME].

       User request: [USER_INPUT or 'Set up project governance']

       Tech stack: [DETECTED_STACK]

       CLAUDE.md exists: [Yes/No]

       [If amending: 'Existing constitution:' + content]

       Write the constitution to .humaninloop/memory/constitution.md
       Use the authoring-constitution skill for structure and quality criteria.
     ",
     description: "Create project constitution"
   )
   ```

4. **After agent completes**
   - If CLAUDE.md exists, sync relevant sections using `syncing-claude-md` skill patterns
   - Report result to user

5. **Suggest commit**
   ```
   docs: create constitution v1.0.0
   ```
