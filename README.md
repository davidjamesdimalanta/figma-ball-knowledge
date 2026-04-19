# figma-ball-knowledge

Figma workflow skills for Claude Code. Designed for Figma designers working through an LLM — type `/figma-ball-knowledge` in Claude Code and the orchestrator parses your intent, runs discovery against the Figma MCP server, and progressively discloses only the capability files the current task needs.

Not a framework, not a code-gen wrapper. Claude writes plain `use_figma` scripts against the Figma MCP; these skills are the reference library and orchestration that make those scripts reliable across a growing set of design workflows.

## What it does

The orchestrator (`figma-ball-knowledge`) handles every Figma request end-to-end:

1. **Discovery audit** — one batched parallel round: local variable collections, published library components, remote library variables, file metadata, and a visual baseline screenshot.
2. **URL target resolution** — converts any `node-id` in a `figma.com` URL to the internal form and confirms the page, name, and type.
3. **Capability selection** — reads only the capability files needed for the task: `figma-read`, `figma-frames`, `figma-components`, `figma-tokens`, `figma-vectors`, `figma-documentation`, `figma-files`. Always reads `figma-code` before any write.
4. **Execution plan** — prints the plan (target, capabilities, component map, token sources, ordered sections, any ambiguity question) before any write. You can correct it before Claude touches the canvas.
5. **Build** — writes one section per `use_figma` call, with `get_screenshot` between phases.

## Supported workflows

- **Screen and component recreation** from a reference URL (Figma-to-Figma or web-to-Figma translation).
- **Applying, migrating, or creating design system tokens** — color, spacing, typography — with WCAG contrast checks.
- **Component instance management** — creating components, swapping every instance of a deprecated component, wiring prototype reactions to boolean variables.
- **Style guide and documentation generation** — token specimens, Code Connect mappings, FigJam diagrams, token exports for dev handoff.
- **Vector work** — icons, shapes, boolean ops, gradients, image fills, effects, node exports.
- **File and page creation** — new files, new pages inside an existing file.
- **Reading existing design state** for audits, refactor scoping, or translation to code.

## Install

This plugin is installed via the `figma-ball-knowledge` marketplace.

**Step 1 — Add the marketplace** (once, in `~/.claude/settings.json`):

```json
{
  "extraKnownMarketplaces": {
    "figma-ball-knowledge": {
      "source": {
        "source": "npm",
        "package": "figma-ball-knowledge"
      }
    }
  }
}
```

Or, if you prefer to source directly from GitHub:

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

## Usage

Type `/figma-ball-knowledge` in Claude Code before your request, or just mention a Figma file in your prompt:

```
/figma-ball-knowledge
Recreate the screen at figma.com/design/<key>/<name>?node-id=<id>
as a mobile-first React mockup. Match token usage to the existing system.
```

The orchestrator runs discovery, resolves the URL, reads the relevant capability files, and emits an execution plan before any write.

## Directory layout

```
figma-ball-knowledge/
  .claude-plugin/
    marketplace.json                  marketplace manifest
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

- Claude Code (or any Anthropic-skill host that loads plugins with skills).
- The Figma MCP server connected to that client.
- Figma plan with library publishing if `search_design_system` should return remote components and variables.

## Contributing

PRs that widen the capability files are welcome when they document a Figma platform behavior that applies to every file, not a one-off quirk. If a script in `scripts/` has broken with a Figma API change, fix it and update the capability file that references it.

## License

MIT.
