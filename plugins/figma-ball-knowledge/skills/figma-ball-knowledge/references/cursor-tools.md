# Cursor Tool Mapping

This skill is written using Claude Code tool names. When running under Cursor, use the equivalents below.

## Tool Translation

| Skill references | Cursor equivalent |
|---|---|
| `AskUserQuestion` | Ask the user in plain text. Present options as a numbered list when the skill asks for multiple choice. |
| `use_figma` | Same — invoked through the Figma MCP server. |
| `search_design_system` | Same — invoked through the Figma MCP server. |
| `get_metadata`, `get_screenshot`, `get_design_context`, `get_variable_defs` | Same — all invoked through the Figma MCP server. |
| `Read`, `Write`, `Edit` (files) | Use Cursor's native file tools. |
| `Bash` (run commands) | Use Cursor's native terminal tool. |

## AskUserQuestion → Plain Text

Whenever `SKILL.md` or a capability file says "ask using `AskUserQuestion`", do this instead:

1. State the question plainly in the chat.
2. If the skill specifies options, present them as a numbered list. Include a short label for each.
3. Wait for the user's reply before continuing.

**Example — page selection (from URL Target Resolution):**

Skill says:
> "If the URL has no `node-id`, surface all top-level frames and ask which page to work on using `AskUserQuestion`."

Cursor behavior:
```
The URL has no node-id. Here are the top-level frames in this file:

  1. Home — page "Desktop"
  2. Settings — page "Desktop"
  3. Onboarding — page "Mobile"

Which one should I work on? Reply with the number or the frame name.
```

**Example — preference capture (from Personal Workflow Layer):**

Skill says:
> "Want me to save that as a workflow preference so I follow it automatically going forward?"

Cursor behavior: ask that sentence in plain text and wait for yes/no.

## Figma MCP Setup for Cursor

Add the Figma MCP server to `.cursor/mcp.json` (project) or `~/.cursor/mcp.json` (global):

```json
{
  "mcpServers": {
    "figma": {
      "url": "https://mcp.figma.com/mcp"
    }
  }
}
```

Restart Cursor. Confirm the Figma MCP tools appear in the MCP panel under Settings → MCP.

See [Figma's MCP documentation](https://help.figma.com/hc/en-us/articles/32132100833559) for authentication details.

## What Stays The Same

Everything non-tool-specific in the skill applies verbatim: the Discovery Audit sequence, URL Target Resolution rules, Capability Selection logic, Execution Plan format, and all anti-patterns.
