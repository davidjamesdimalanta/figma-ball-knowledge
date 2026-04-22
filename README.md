<img src="banner.png" alt="figma-ball-knowledge" width="100%" />

# figma-ball-knowledge

For agents, its a Claude skill suite that exposes the right way to interact with Figma MCP. For designers, its a set of rules that tells agents how to ask and parse designer intent.

### Get it

[**↓ Download the latest release**](https://github.com/davidjamesdimalanta/figma-ball-knowledge/releases/latest) — for Claude Desktop (no terminal needed).

Or for Claude Code:

```bash
claude plugin install figma-ball-knowledge@figma-ball-knowledge
```

Also runs in Cursor and Codex. See [Install](#install).

---

## What it is

An **orchestrator** that routes each Figma task to a capability file on demand — reading, frames, components, tokens, vectors, documentation, files.

Before writing anything, it runs a **discovery audit** against your file (local variables, remote library variables, published components, text and paint styles) and emits an execution plan you can correct before it touches the canvas.

## Why use it

The new wave of AI design tools (Lovable, Bolt, v0, Stitch, Claude artifacts) assumes a greenfield. If your org already runs on Figma, none of them port back in reliably.

Figma's own MCP closes the loop — but without design-domain context, agents fail in predictable ways:

- Hardcoded hex fills instead of token bindings.
- Auto-layout collapses (`fill` inside a `hug` defaults to 10px).
- Raw frames where a published component already exists.
- Tool-call loops that burn your token budget before producing anything usable.

`figma-ball-knowledge` encodes the Figma-specific knowledge the agent was missing. You get:

- **Design-system-native output** — library components, token-bound fills, auto-layout that holds.
- **Fewer wasted tokens** — correct tool calls first try.
- **No black-box writes** — every write is preceded by a plan you can correct.
- **One skill, four hosts** — Claude Desktop, Claude Code, Cursor, Codex.

## Install

All four hosts use the same skill files. Pick yours.

<details>
<summary><b>Claude Desktop</b> — no terminal needed</summary>

1. Download [`figma-ball-knowledge.skill`](https://github.com/davidjamesdimalanta/figma-ball-knowledge/releases/latest).
2. In Claude Desktop → profile icon → **Customize** → **Skills** → upload the file.
3. **Settings** → **Integrations** → add Figma MCP: `https://mcp.figma.com/mcp`. ([docs](https://help.figma.com/hc/en-us/articles/32132100833559))
4. Start a new conversation and describe your Figma task.

</details>

<details>
<summary><b>Claude Code</b> — CLI</summary>

```bash
npx skills add davidjamesdimalanta/figma-ball-knowledge
```

See [Figma MCP setup](https://help.figma.com/hc/en-us/articles/32132100833559).

</details>

<details>
<summary><b>Cursor</b> — auto-attached rule</summary>

```bash
npx skills add davidjamesdimalanta/figma-ball-knowledge
mkdir -p .cursor/rules
ln -s ../../.agents/skills/figma-ball-knowledge/references/cursor-rule.mdc .cursor/rules/figma-ball-knowledge.mdc
```

Add Figma MCP to `.cursor/mcp.json`:

```json
{ "mcpServers": { "figma": { "url": "https://mcp.figma.com/mcp" } } }
```

Restart Cursor. Full details: [`references/cursor-tools.md`](plugins/figma-ball-knowledge/skills/figma-ball-knowledge/references/cursor-tools.md).

</details>

<details>
<summary><b>Codex</b></summary>

```bash
npx skills add davidjamesdimalanta/figma-ball-knowledge
```

Add Figma MCP to `~/.codex/config.toml`:

```toml
[mcp_servers.figma]
url = "https://mcp.figma.com/mcp"
```

See [Figma MCP docs](https://help.figma.com/hc/en-us/articles/32132100833559). For manual install without npx, see [`.codex/INSTALL.md`](.codex/INSTALL.md).

</details>

## Usage

Start every request with `/figma-ball-knowledge` to invoke the skill. Without the prefix, the host won't load it.

```
/figma-ball-knowledge
Recreate figma.com/design/<key>/<name>?node-id=<id> as a mobile-first mockup.
```

```
/figma-ball-knowledge
Swap every instance of the old Button component with the new one.
```

```
/figma-ball-knowledge
Apply our color tokens to all hardcoded fills in this file.
```

On Cursor and Codex, clarifying questions come back as plain chat (no `AskUserQuestion` tool — see [`cursor-tools.md`](plugins/figma-ball-knowledge/skills/figma-ball-knowledge/references/cursor-tools.md) or [`codex-tools.md`](plugins/figma-ball-knowledge/skills/figma-ball-knowledge/references/codex-tools.md)).

## Requirements

- A supported host: Claude Desktop, Claude Code, Cursor, or Codex.
- The Figma MCP server connected to that host.
- Figma plan with library publishing if `search_design_system` should return remote components and variables.

<details>
<summary><b>Directory layout</b></summary>

```
figma-ball-knowledge/
  .claude-plugin/marketplace.json       Claude Code marketplace manifest
  .codex/INSTALL.md                     Codex install instructions (manual path)
  .cursor/rules/figma-ball-knowledge.mdc Cursor rule for dev-in-repo use
  plugins/figma-ball-knowledge/
    .claude-plugin/plugin.json          plugin manifest
    skills/figma-ball-knowledge/        source of truth for the skill
      SKILL.md                          orchestrator; read first
      figma-read.md                     reading existing design state
      figma-frames.md                   frame and layout creation
      figma-components.md               component import, creation, hierarchy
      figma-tokens.md                   variable and style binding, WCAG
      figma-vectors.md                  vector geometry, shapes, boolean ops
      figma-documentation.md            style guides, Code Connect, token export
      figma-files.md                    file and page management
      figma-code.md                     Figma Plugin API constraints
      figma-personal-workflow.md        designer preferences capture
      references/                       tool mappings, workflow recipes, cursor-rule.mdc
      scripts/                          discovery audit, WCAG, swap guards
  .agents/skills/figma-ball-knowledge/  active install after npx skills add
```

</details>

## Contributing

PRs that widen the capability files are welcome when they document a Figma platform behavior that applies to every file, not a one-off quirk. If a script in `scripts/` has broken with a Figma API change, fix it and update the capability file that references it.

## License

MIT.
