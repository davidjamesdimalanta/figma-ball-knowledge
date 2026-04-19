# Scripts

Reusable `use_figma` snippets. Paste these into a `use_figma` call when the pattern applies, or inline your own variant for one-off work.

## Contract

Every script must:

1. Start with a block comment containing a one-line description, the capability it belongs to (for example `figma-tokens`, `figma-components`), and a minimal paste-in example.
2. Be idempotent where possible — running twice should leave the file the same as running once, unless the script is an accumulating mutator.
3. Return structured data via `return`, not `console.log`. Console output is invisible inside `use_figma`.
4. Use `await figma.getNodeByIdAsync(id)`, never the synchronous form. See `../figma-code.md`.
5. Append child nodes to their parent before setting `layoutSizingHorizontal/Vertical = 'FILL'`. See `../figma-code.md`.

## Current scripts

| File | Capability | Purpose |
|---|---|---|
| `discovery-audit.js` | discovery | Local variable collections and styles in one call. Use for the initial audit. |
| `rightedge-placement.js` | `figma-frames` | Computes rightEdge for new frame placement, avoids overlap. |
| `wcag-contrast.js` | `figma-tokens` | `getLuminance` and `contrastRatio` helpers for WCAG checks. |
| `bind-fill.js` | `figma-tokens` | `bindFill`, `bindStroke`, `safeAppend` wrappers for the namespace binding API. |
| `token-audit.js` | `figma-tokens` | Finds hardcoded fills and logs token gaps before a migration pass. |
| `component-swap.js` | `figma-components` | Full instance swap and master deletion protocol. See `../references/component-swap.md`. |

## Anti-patterns

- Writing a script that calls `figma.getNodeById` synchronously. Use the async form.
- Writing a script that reports via `console.log`. Use `return JSON.stringify(...)`.
