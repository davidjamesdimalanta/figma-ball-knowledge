![figma-ball-knowledge](banner.png)

# figma-ball-knowledge

**Designers → [Download the latest release](https://github.com/davidjamesdimalanta/figma-ball-knowledge/releases/latest)** — grab `figma-ball-knowledge.skill` and drop it into Claude Desktop.

**Developers → install in Claude Code:**

```bash
claude plugin install figma-ball-knowledge@figma-ball-knowledge
```

(Also runs in Cursor and Codex — see [Install](#install) below.)

---

## What it is

A Claude skill suite that makes AI agents work with Figma the way a designer would — using your design system, not hardcoded values.

It's an **orchestrator** that routes each task to capability files on demand: reading existing design state, frame and layout creation, component instances, token binding, documentation, vector work, file management. Before writing anything, it runs a **discovery audit** against your file — local variables, remote library variables, published components, text and paint styles — then emits an execution plan you can correct before it touches the canvas.

Not a framework, not a code-gen wrapper. Claude writes plain `use_figma` scripts against the Figma MCP; these skills are the reference library and orchestration that make those scripts reliable.

## Why use it

The new wave of AI design tools (Lovable, Bolt, v0, Magic Patterns, Google Stitch, Claude artifacts) assumes a greenfield. If your org already runs on Figma — with years of tokens, components, and documentation — none of them have a reliable way to port back in.

Figma's own MCP closes the loop, but without design-domain context agents fail in predictable ways:

- Hardcoded hex fills instead of token bindings.
- Frames with broken auto-layout (`fill` inside a `hug` collapses to 10px).
- Raw frames where a published component exists.
- Long tool-call loops that exhaust your token budget before producing usable output.

`figma-ball-knowledge` encodes the Figma-specific knowledge the agent was missing, so you get:

- **Figma-native output** — components from your library, fills bound to your tokens, auto-layout that holds up.
- **Fewer wasted tokens** — the agent reaches correct tool calls faster, so sessions stay inside usage limits.
- **No black-box writes** — every write is preceded by a plan (target, component map, token sources, build phases) you can correct.
- **Portable across hosts** — Claude Desktop, Claude Code, Cursor, and Codex, same skill files.

Built for designers who want the speed of agents without abandoning the design system they've already invested in.

## Install

Supported hosts: **Claude Desktop**, **Claude Code**, **Cursor**, **Codex**. All four use the same skill files — only the install path and tool-name mapping differ.

### Claude Desktop (no CLI needed)

**Step 1 — Download the skill file.**
Go to the [latest release](https://github.com/davidjamesdimalanta/figma-ball-knowledge/raw/main/figma-ball-knowledge.skill) and download `figma-ball-knowledge.skill`.

**Step 2 — Add it to Claude Desktop.**
Open Claude Desktop → click your profile icon → **Customize** → **Skills** → upload `figma-ball-knowledge.skill`.

**Step 3 — Connect the Figma MCP server.**
In Claude Desktop → **Settings** → **Integrations** → add the Figma MCP server URL `https://mcp.figma.com/mcp`. See [Figma's MCP documentation](https://help.figma.com/hc/en-us/articles/32132100833559) for details.

**Step 4 — Start a new conversation** and describe your Figma task. Claude will pick up the skill automatically.

---

### Claude Code (CLI)

This plugin is installed via the `figma-ball-knowledge` marketplace.

**Step 1 — Add the marketplace** (once, in `~/.claude/settings.json`):

```json
{
  "extraKnownMarketplaces": {
    "figma-ball-knowledge": {
      "source": {
        "source": "github",
        "repo": "davidjamesdimalanta/figma-ball-knowledge"
      }
    }
  }
}
```

**Step 2 — Install the plugin:**

```bash
claude plugin install figma-ball-knowledge@figma-ball-knowledge
```

**Step 3 — Connect the Figma MCP server.** The skill calls `use_figma`, `search_design_system`, `get_metadata`, `get_screenshot`, `get_design_context`, `get_variable_defs`. See [Figma's MCP documentation](https://help.figma.com/hc/en-us/articles/32132100833559) for setup.

---

### Cursor

Cursor loads the skill as an auto-attached rule — it activates when you mention Figma, paste a `figma.com` URL, or describe a Figma task.

**Step 1 — Add the rule to your project.**

Copy `.cursor/rules/figma-ball-knowledge.mdc` and the `plugins/figma-ball-knowledge/skills/figma-ball-knowledge/` directory into your project, preserving paths. The rule references the skill files by relative path.

The quickest way is to clone the repo and symlink:

```bash
git clone https://github.com/davidjamesdimalanta/figma-ball-knowledge.git
# From your project root:
ln -s <path-to-clone>/.cursor/rules/figma-ball-knowledge.mdc .cursor/rules/figma-ball-knowledge.mdc
ln -s <path-to-clone>/plugins/figma-ball-knowledge/skills/figma-ball-knowledge plugins/figma-ball-knowledge/skills/figma-ball-knowledge
```

**Step 2 — Configure the Figma MCP server.**

Add to `.cursor/mcp.json` (project) or `~/.cursor/mcp.json` (global):

```json
{
  "mcpServers": {
    "figma": {
      "url": "https://mcp.figma.com/mcp"
    }
  }
}
```

Restart Cursor. Confirm the Figma MCP tools appear in Settings → MCP.

**Step 3 — Describe a Figma task** (e.g. paste a `figma.com/design/...` URL). The rule auto-attaches and Cursor follows the orchestrator.

Full details: [`plugins/figma-ball-knowledge/skills/figma-ball-knowledge/references/cursor-tools.md`](plugins/figma-ball-knowledge/skills/figma-ball-knowledge/references/cursor-tools.md)

---

### Codex

Tell Codex:

```
Fetch and follow instructions from https://raw.githubusercontent.com/davidjamesdimalanta/figma-ball-knowledge/main/.codex/INSTALL.md
```

Or install manually:

```bash
git clone https://github.com/davidjamesdimalanta/figma-ball-knowledge.git ~/.codex/figma-ball-knowledge
mkdir -p ~/.agents/skills
ln -s ~/.codex/figma-ball-knowledge/plugins/figma-ball-knowledge/skills/figma-ball-knowledge ~/.agents/skills/figma-ball-knowledge
```

Then add the Figma MCP server to `~/.codex/config.toml`:

```toml
[mcp_servers.figma]
url = "https://mcp.figma.com/mcp"
```

Restart Codex. Full details: [`.codex/INSTALL.md`](.codex/INSTALL.md)

## Usage

### Claude Desktop

Just describe your task naturally — the skill triggers automatically:

```
Recreate the screen at figma.com/design/<key>/<name>?node-id=<id>
```

```
Swap every instance of the old Button component with the new one in this file.
```

```
Apply our color tokens to all hardcoded fills in the file.
```

### Claude Code

Type `/figma-ball-knowledge` before your request, or just mention a Figma file:

```
/figma-ball-knowledge
Recreate the screen at figma.com/design/<key>/<name>?node-id=<id>
as a mobile-first React mockup. Match token usage to the existing system.
```

The orchestrator runs discovery, resolves the URL, reads the relevant capability files, and emits an execution plan before any write.

### Cursor

Describe the Figma task naturally. The rule auto-attaches on Figma-related prompts:

```
Recreate figma.com/design/<key>/<name>?node-id=<id> as a mobile-first mockup.
```

Questions come back as plain-text chat messages (no `AskUserQuestion` tool on Cursor — see `references/cursor-tools.md`).

### Codex

Same — describe the task naturally. Codex auto-activates the skill based on its description:

```
Apply our color tokens to all hardcoded fills in figma.com/design/<key>/<name>
```

Questions come back as plain text. See `references/codex-tools.md` for the full tool mapping.

## Directory layout

```
figma-ball-knowledge/
  .claude-plugin/
    marketplace.json                  Claude Code marketplace manifest
  .codex/
    INSTALL.md                        Codex install instructions
  .cursor/
    rules/
      figma-ball-knowledge.mdc        Cursor auto-attached rule
  plugins/
    figma-ball-knowledge/
      .claude-plugin/plugin.json      plugin manifest
      skills/
        figma-ball-knowledge/
          SKILL.md                    orchestrator; read first
          figma-read.md               reading existing design state
          figma-frames.md             frame and layout creation
          figma-components.md         component import, creation, hierarchy
          figma-tokens.md             variable and style binding, WCAG
          figma-vectors.md            vector geometry, shapes, boolean ops
          figma-documentation.md      style guides, Code Connect, token export
          figma-files.md              file and page management
          figma-code.md               Figma Plugin API constraints and patterns
          figma-personal-workflow.md  designer preferences capture protocol
          references/
            tool-reference.md         every MCP tool, grouped read/write
            cursor-tools.md           Cursor tool mapping (AskUserQuestion → plain text)
            codex-tools.md            Codex tool mapping (AskUserQuestion → plain text)
            component-swap.md         swap-every-instance workflow
            component-reactions.md    prototype reactions with CONDITIONAL pattern
            vector-effects-exports.md gradients, shadows, blur, node exports
          scripts/
            README.md                 script library contract
            discovery-audit.js        the discovery audit script
            rightedge-placement.js    rightEdge placement boilerplate
            wcag-contrast.js          contrast utilities
            bind-fill.js              token binding wrappers
            token-audit.js            hardcoded fill finder
            component-swap.js         swap script with guards
```

## Requirements

- A supported host: **Claude Desktop**, **Claude Code**, **Cursor**, or **Codex**.
- The Figma MCP server connected to that host (see each install section above).
- Figma plan with library publishing if `search_design_system` should return remote components and variables.

## Contributing

PRs that widen the capability files are welcome when they document a Figma platform behavior that applies to every file, not a one-off quirk. If a script in `scripts/` has broken with a Figma API change, fix it and update the capability file that references it.

## License

MIT.
