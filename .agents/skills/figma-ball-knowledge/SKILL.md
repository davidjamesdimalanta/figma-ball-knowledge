---
name: figma-ball-knowledge
description: "Use when the user mentions Figma, pastes a figma.com URL or node-id, or asks to recreate a screen, build frames, import components, apply design tokens, swap component instances, audit a design system, bind variables, generate Code Connect, create FigJam diagrams, conduct competitive analysis, synthesize research for design decisions, or do any design work in a Figma file. Covers design research, reading existing design state, frame and auto-layout creation, library components, variables and styles, vectors, documentation, and file management via the Figma MCP."
metadata:
  mcp-server: figma
---

# Figma Ball Knowledge: Orchestrator

Read this skill first on any Figma request. It parses the designer's intent, determines whether research or discovery is needed first, runs the appropriate audit against the Figma MCP, resolves any URL targets, and progressively discloses the capability files the task actually needs. You do not need to read every capability file up front.

> **Designer-first principle:** This skill treats the designer as the primary stakeholder. Outputs are structured for design decision-making, not engineering handoff. When research informs a build, lead with the recommendation and the "So What?" — supporting data follows.

---

## Global Rule: AskUserQuestion

Any time you need to ask the designer a question — for clarification, ambiguity resolution, preference confirmation, or page selection — use the `AskUserQuestion` tool. This applies at every step without exception.

**Non-Claude hosts:** `AskUserQuestion` is a Claude Code / Claude Desktop tool. When running under Cursor, Codex, or any other host that lacks this tool, ask the question in plain text and present options as a numbered list if the skill specifies multiple choice. See `references/cursor-tools.md` (Cursor) or `references/codex-tools.md` (Codex) for the full tool mapping.

---

## Capability Files

These are reference libraries, not sequential steps. Read the ones the current task needs. Multiple files are read together, not exclusively.

| File | API surface covered |
|---|---|
| `figma-research.md` | Strategic research: competitive audits, PESTLE/SWOT, BLUF synthesis, "So What?" test, quality gates |
| `figma-read.md` | Reading existing design state: get_design_context, get_metadata, get_variable_defs, source parsing |
| `figma-frames.md` | Frame and layout creation: createFrame, auto-layout, rightEdge placement, section pattern |
| `figma-components.md` | Component instances, library import, component creation, bottom-up build order |
| `figma-tokens.md` | Variable and style binding, token architecture, WCAG, migration |
| `figma-vectors.md` | Vectors, shapes, boolean ops, gradients, image fills |
| `figma-documentation.md` | Style guide specimens, Code Connect, token export, FigJam diagrams, writing quality standards |
| `figma-files.md` | File creation, page management |
| `figma-code.md` | Figma Plugin API constraints: font loading, async lookup, sizing lifecycle, binding patterns |
| `figma-personal-workflow.md` | Capturing and applying designer preferences |

## Workflow References

Longer workflows live alongside their parent capability files. Read these when their specific workflow applies.

| File | When to read |
|---|---|
| `references/component-swap.md` | Replacing every instance of a deprecated component across a file |
| `references/component-reactions.md` | Wiring prototype reactions to boolean variables (filter chips, toggle states) |
| `references/vector-effects-exports.md` | Applying effects, gradients, or setting up node exports |
| `references/cursor-tools.md` | Tool-name translation when running under Cursor |
| `references/codex-tools.md` | Tool-name translation when running under Codex |

## Bundled Scripts

| File | Contents |
|---|---|
| `scripts/discovery-audit.js` | Discovery audit script. Paste into `use_figma`. |
| `scripts/rightedge-placement.js` | rightEdge frame placement boilerplate |
| `scripts/wcag-contrast.js` | getLuminance + contrastRatio utilities |
| `scripts/bind-fill.js` | bindFill, bindStroke, safeAppend utilities |
| `scripts/token-audit.js` | Hardcoded fill finder + token gap logger |
| `scripts/component-swap.js` | Full instance swap + master deletion protocol |

---

## Preference Check

Before any tool call, check whether a `## Designer Workflow Preferences` section exists in any capability file relevant to the task. If preferences are present, load them into working memory — they override defaults for this session.

---

## Discovery Audit

Run once per session at first contact with a file. Do not repeat while edits are in progress.

The full audit script is in `scripts/discovery-audit.js`. Paste it into a `use_figma` call. Call these together in the same response:

**Call 1 — Local audit** (`use_figma` — paste `scripts/discovery-audit.js`): returns local variable collections, text/paint/effect styles.

**Call 2 — Published library components:** `search_design_system("")`

**Call 3 — Remote library variables and styles:**
`search_design_system("", { includeVariables: true, includeStyles: true })`

> `getLocalVariableCollectionsAsync()` only returns variables created in this file. Files using a shared/published library will have remote variables invisible to Call 1. Call 3 surfaces them. Merge both results before building the Session Context.

**Call 4 — File/node structure:** `get_metadata(resolvedNodeId ?? fileRoot)`

**Call 5 — Visual baseline** (only if a target node-id was provided): `get_screenshot(resolvedNodeId)`

After these return, synthesize a **Session Context**:

```
SESSION CONTEXT
Target node              : [name, type, page] — or — [no URL target, full file]
Local variable collections: [N collections, M variables total]
Remote library variables  : [N from search_design_system includeVariables]
Text styles : [N]   Paint styles : [N]   Effect styles : [N]
Library components       : [N from search_design_system]
File state               : [empty | partial system | mature system]
```

File state classification:
- `empty` — 0 variable collections, 0 styles
- `partial system` — some tokens or styles exist, gaps present
- `mature system` — 2+ variable collections with meaningful coverage, text and paint styles present

---

## URL Target Resolution

1. Extract `node-id` from the URL. Hyphens in the URL (`node-id=21-173`) become colons internally (`21:173`). Confirm the node's page, name, and type from the `get_metadata` result.
2. If the node is a page (canvas), output will be a new frame placed on that page. If it is a frame, output will be placed alongside it.
3. If the URL has no `node-id`, surface all top-level frames and ask which page to work on using `AskUserQuestion`.

Node ID is the only authoritative target reference. Never match by name.

---

## Context Extraction

Scan the message for structured signals and hold them as **Project Context**:

| Category | Signals | Name |
|---|---|---|
| Device / platform | "mobile", "iOS", "desktop", width in px | `device` |
| Code framework | "React", "Tailwind", "SwiftUI" | `framework` |
| Brand / visual | hex codes, "our brand color" | `brandColors` |
| Fidelity intent | "rough", "high-fi", "production-ready" | `fidelityIntent` |
| Product domain | "fintech", "SaaS", "health" | `domain` |
| Screen / component type | named screen, named component | `targetType` |
| Source reference | URL to existing design to recreate/translate | `sourceRef` |
| Research signals | "competitive", "best-in-class", "industry standard", "landscape" | `researchNeeded` |
| Stakeholder context | "present to", "share with team", "executive review" | `audienceType` |

---

## Capability Selection (Progressive Disclosure)

Read the task and the Session Context. Mark every capability the task requires, then read all marked files before writing any code.

| Capability | Select when the task requires… |
|---|---|
| `figma-research` | New product feature with no precedent, competitive analysis, stakeholder synthesis, research-informed design direction, any signal in the `researchNeeded` context |
| `figma-read` | Source reference provided, recreation/translation task, audit, reading existing tokens or structure |
| `figma-frames` | Creating any new frame, screen, section, or layout scaffold |
| `figma-components` | Any component instance, library import, component creation, or UI element that may exist in the design system |
| `figma-tokens` | Applying, migrating, or creating design system color, spacing, or typography tokens |
| `figma-vectors` | Icons, shapes, paths, boolean ops, gradients, image fills, effects, or exports |
| `figma-documentation` | Style guide, Code Connect, token export, FigJam diagram, dev handoff artifact, research report formatting |
| `figma-files` | New file or new page creation |

Multiple capabilities are the norm. A screen recreation with a mature design system typically needs figma-read + figma-frames + figma-components + figma-tokens. A new product feature with strategic ambiguity typically needs figma-research + figma-frames + figma-components + figma-tokens.

Always read `figma-code.md` before writing any `use_figma` script. Its constraints apply universally.

---

## Execution Plan (BLUF Format)

Before any write call, emit this plan. Fires every time. The plan leads with the recommendation (BLUF — Bottom Line Up Front) so the designer can confirm direction before any technical work begins.

| Field | Value |
|---|---|
| **Recommendation** | [One sentence: what will be built and why — the "So What?" for this task] |
| **Task** | [one-sentence description] |
| **Target** | New frame at rightEdge + 200 on page `[name]` (node `[id]`) — or — editing `[specific property]` on node `[id]` |
| **Capabilities** | [list of selected capability files] |
| **Research synthesis** | [BLUF summary — recommendation, impact, confidence] _(omit if figma-research not selected)_ |
| **Design requirements** | [Ordered list of insight → requirement mappings driving this build] _(omit if figma-research not selected)_ |
| **Component map** | `[element]` → `[component key]` / `[element]` → raw frame if no match _(omit if figma-components not selected)_ |
| **Token sources** | [variable collection names to use] _(omit if figma-tokens not selected)_ |
| **Sections** | [ordered list of build phases] |
| **Ambiguity** | [one question if any execution decision is genuinely unclear — otherwise "none"] |

The plan is visible to the designer before any write. It naturally invites correction. If the ambiguity line contains a question, deliver it using `AskUserQuestion` and wait for the answer before proceeding.

> **BLUF principle:** The recommendation field answers "What should we build?" in one sentence. If the designer reads nothing else, this sentence gives them enough to approve or redirect. Supporting evidence follows in the research synthesis and design requirements fields.

---

## Personal Workflow Layer

When a designer states a preference mid-session, offer to save it using `AskUserQuestion`:
> "Want me to save that as a workflow preference so I follow it automatically going forward?"

When the same answer appears twice across separate sessions, offer proactively:
> "You have picked [answer] twice now. Want me to save that so I stop asking?"

See `figma-personal-workflow.md` for how to persist preferences and which sections of each file are safe to edit.

---

## Tool Reference

Every tool this skill calls, plus the rules that apply to every `use_figma` script, lives in `references/tool-reference.md`. Read that file once per session, or whenever a tool name appears that you do not immediately recognize.

---

## Quality Gates for Research-Informed Tasks

When `figma-research` is selected, enforce these gates before presenting the execution plan:

1. **Circular Verification Ban**: Do not present information the designer already provided as a research "discovery."
2. **Source Diversity**: When citing external references, draw from at least 3 unique domains. No single source exceeds 40% of the reasoning.
3. **Grounded Abstractions**: Every recommendation must reference a specific observation or data point — never "this will enhance user engagement" without a concrete basis.
4. **Recency Flag**: Data older than 18 months on fast-moving topics (UI trends, platform capabilities) must be flagged.
5. **Point of View Required**: Every synthesis must state which approach is superior for this specific context. "There are advantages to both" is not a conclusion.

---

## Writing Quality Standards

All outputs visible to the designer — execution plans, research syntheses, annotations, documentation — follow these standards:

- **Lead with the action.** State what to do before explaining why.
- **Ground abstractions in specifics.** Not "optimize the onboarding flow" but "move the CTA above the fold to capture the 40% of users on 6.5-inch screens."
- **Vary rhythm.** Follow a detailed analysis with a short declarative sentence. Monotonous cadence signals mechanical output.
- **Earn every adjective.** Remove "fascinating," "remarkable," or "innovative" unless the specific evidence that earned it is in the same sentence.
- **End sections with implications, not summaries.** Do not restate what was just said — move forward into what it means for the next decision.
- **Maintain a point of view.** A skilled researcher advocates for the user through the lens of business impact. Neutral observations without recommendations fail the "So What?" test.

---

## Anti-Patterns

- Running the discovery audit again while edits are in progress.
- Resolving a target node by name instead of node-id.
- Running two `use_figma` write calls simultaneously.
- Using `search_design_system` as the only discovery step — it misses all local assets.
- Running only Call 1 for variable discovery and skipping Call 3 — remote library variables are invisible to `getLocalVariableCollectionsAsync()`.
- Using `get_variable_defs` to discover available tokens — it only returns already-bound tokens.
- Skipping `get_screenshot` after any write.
- Building an entire screen or component set in one `use_figma` call.
- Clearing an existing frame's children and rebuilding inside it. A destination URL means "place new content here" — always as a new frame. Never empty and repurpose an existing one.
- Creating frames as top-level page children intending to reparent them later. `appendChild()` silently fails when moving nodes across parents, producing orphaned frames. Build directly inside the final wrapper from the start.
- Building UI elements (buttons, chips, nav bars) as raw frames when matching library components exist. The component map in the execution plan must be completed before building.
- Reading `node.vectorPaths` without error handling on any VECTOR node from an unknown source.
- Selecting only one capability when the task clearly needs several.
- Emitting an execution plan with no ambiguity and then asking clarifying questions anyway — plan first, then build.
- Jumping to Figma builds on new product features without first assessing whether research is needed.
- Presenting research without a point of view — every synthesis requires a recommendation.
- Using ambient enthusiasm ("fascinating opportunity") in place of grounded evidence.
- Delivering a competitive audit as a feature list without "So What?" implications.
