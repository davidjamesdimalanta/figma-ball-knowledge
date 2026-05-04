---
name: figma-research
description: "Capability reference for structured design research workflows. Use when starting a new project, entering a discovery phase, conducting competitive analysis, building a research synthesis for stakeholder review, or translating desk research insights into actionable design requirements. Covers competitive audits, PESTLE/SWOT frameworks, BLUF synthesis, the 'So What?' test, and anti-hallucination quality gates."
metadata:
  mcp-server: figma
---

# Figma Research — Strategic Design Research & Synthesis

Select this capability when: a new project needs a research foundation, a competitive audit is required, the task involves translating research findings into design requirements, or stakeholders need a structured synthesis before design work begins.

This capability runs **before** any frame, component, or token work. Its outputs feed directly into the execution plan's component map, fidelity intent, and design requirements.

---

## The Research-First Principle

Design decisions that skip research become expensive guesses. This capability ensures that every design task with strategic ambiguity goes through a structured research phase before committing to Figma work.

**When to trigger research:**
- New product screens with no existing design precedent in the file
- Feature requests that reference competitor capabilities
- Stakeholder requests that include market-facing language ("competitive," "best-in-class," "industry standard")
- Design system overhauls that affect multiple products or brands
- Any task where the designer says "I need to understand the landscape first"

**When to skip research and go straight to building:**
- Iteration on an existing screen with clear direction
- Token migration or refactoring tasks
- Bug fixes or alignment corrections
- Component swaps with a defined replacement

---

## Competitive Audit Matrix

A structured comparison of 2–3 direct competitors and 1–2 indirect competitors. Direct competitors solve the same problem for the same audience. Indirect competitors solve the same problem differently or solve a related problem for the same audience.

### Building the Matrix

For each competitor, collect:

| Dimension | Data Points | Strategic Implication |
|---|---|---|
| Core value proposition | Primary problem solved | Identifies white space or saturation |
| Target audience | Demographics, psychographics | Reveals underserved segments |
| Feature set | Key capabilities, differentiators | Establishes table stakes vs. unique value |
| Design language | Layout patterns, component density, interaction style | Informs fidelity and visual direction |
| Accessibility posture | WCAG compliance, contrast ratios, motion sensitivity | Establishes minimum accessibility bar |
| User sentiment | App store reviews, social mentions, support threads | Surfaces specific failures to solve |

### Translating Audit to Design Direction

Each row in the matrix must pass the "So What?" test (see below). A row without a design implication is data, not insight.

```
COMPETITIVE INSIGHT → DESIGN REQUIREMENT
Competitor X uses bottom-sheet navigation → Consider thumb-zone-first layout for mobile
All competitors lack dark mode → Dark mode is a differentiation opportunity, not table stakes
Users complain about onboarding length → Progressive disclosure; defer non-essential setup
```

---

## Strategic Frameworks

### PESTLE Analysis (External Environment)

Scan for external forces that affect design decisions before they become constraints mid-build:

| Factor | Design Impact |
|---|---|
| **P**olitical | Government regulation affecting feature availability by region |
| **E**conomic | Pricing pressure that affects freemium vs. premium feature gating |
| **S**ocio-cultural | Accessibility expectations, dark mode as norm, inclusive design mandates |
| **T**echnological | Platform constraints (iOS vs. Android capabilities, browser support) |
| **L**egal | Data privacy (GDPR, CCPA) affecting form design and data collection flows |
| **E**nvironmental | Sustainability expectations affecting animation density and data transfer |

### SWOT Synthesis

Filter competitive and environmental findings into an internal strategic model:

- **Strengths**: Design system maturity, existing component coverage, brand recognition
- **Weaknesses**: Token gaps, accessibility gaps, platform inconsistencies
- **Opportunities**: Unserved segments, competitor failures, emerging platform features
- **Threats**: Regulatory changes, competitor convergence, user expectation shifts

SWOT output feeds directly into the execution plan's ambiguity field and the design requirements section.

---

## The "So What?" Test

Every insight must answer three questions before it enters the execution plan:

1. **What did we find?** — The observation, grounded in specific evidence
2. **So what?** — The business or user implication, quantified when possible
3. **Now what?** — The concrete design requirement

| Observation | So What? | Design Requirement |
|---|---|---|
| Users spend 30s navigating the login screen | High friction at entry → 5% bounce in first minute | Implement biometric login; add "Continue as Guest" |
| Competitors default to dark mode | Dark mode is table stakes for 18–25 demographic | System-default to dark; ensure all components pass WCAG in high contrast |
| Users report feeling "overwhelmed" by settings | Information density blocks feature discovery | Progressive disclosure: hide advanced behind "More" menu |

Insights that cannot complete all three columns are observations, not requirements. Surface them as context but do not let them drive build decisions.

---

## BLUF (Bottom Line Up Front) Synthesis

When presenting research to stakeholders or when the designer asks for a summary, always lead with the recommendation. Supporting evidence follows.

### BLUF Structure

```
RESEARCH SYNTHESIS — [Project/Feature Name]
─────────────────────────────────────────────
RECOMMENDATION: [One sentence — what to build and why]
IMPACT: [Quantified business or user impact]
CONFIDENCE: [High | Medium | Low] — based on source quality and coverage
─────────────────────────────────────────────
KEY FINDINGS:
1. [Finding → So What? → Design requirement]
2. [Finding → So What? → Design requirement]
3. [Finding → So What? → Design requirement]
─────────────────────────────────────────────
RISKS & UNKNOWNS:
- [Risk 1 — mitigation strategy]
- [Risk 2 — mitigation strategy]
─────────────────────────────────────────────
METHODOLOGY: [Sources consulted, analysis frameworks used]
```

The recommendation comes first because stakeholders spend less than 60 seconds on a report. Data that does not support a conclusion or mitigate a risk belongs in an appendix, not the synthesis.

---

## Quality Gates (Anti-Hallucination)

When research involves LLM-assisted synthesis, enforce these gates before delivering any findings:

### Mandatory Gates

1. **Circular Verification Ban**: Never use information the designer already provided as a "discovery." Research must surface new value.
2. **Source Diversity**: Use at least 5 unique domains. No single source exceeds 25% of citation share.
3. **Official Source Threshold**: At least 30% of data from official documentation, academic sources, or primary platform guidelines.
4. **Recency Check**: Data older than 18 months on fast-moving topics (UI trends, platform capabilities) must be flagged as potentially stale.
5. **Information Black Box**: When no public information is found, state "NO PUBLIC INFORMATION AVAILABLE" — never speculate.

### Detecting Mechanical Registers in Output

Before delivering any research synthesis, scan for these LLM writing markers and revise:

| Marker | What It Looks Like | Fix |
|---|---|---|
| Three-part list compulsion | Every point organized into exactly three items | Break the pattern — use 2, 4, or 5 items when appropriate |
| Abstract noun preference | "strategic initiatives" instead of concrete actions | Replace with the specific action: "move the CTA above the fold" |
| Summary conclusion | Restating what was just said at the end of every section | End with the implication or the next question, not a recap |
| Ambient enthusiasm | "fascinating," "remarkable" without specific cause | Remove or replace with the specific evidence that earned the adjective |
| Perfect paragraph structure | Topic-support-conclusion in every paragraph | Vary rhythm — follow a deep analysis with a short declarative punch |
| Absence of opinion | "There are advantages to both approaches" | State which approach is superior for this specific context and why |

Research output must have a point of view. Neutral summaries that avoid recommendation fail the primary objective of actionable research.

---

## Integration with the Execution Plan

When the research capability is selected, the execution plan gains two additional fields:

| Field | Value |
|---|---|
| **Research synthesis** | BLUF summary (2–3 sentences: recommendation, impact, confidence) |
| **Design requirements** | Ordered list of "So What?" → requirement mappings that will drive the build |

These fields appear before the component map and token sources, ensuring research informs technical decisions rather than following them.

---

## Anti-Patterns

- Jumping to Figma frames before completing competitive analysis on a new product feature.
- Presenting a competitive audit as a feature list without "So What?" implications.
- Using ambient enthusiasm ("this is a fascinating opportunity") instead of grounded evidence.
- Delivering research that avoids a recommendation — every synthesis must have a clear point of view.
- Citing a single source as the basis for a design requirement — triangulate with at least two independent sources.
- Letting PESTLE or SWOT analysis exist as a standalone deliverable — it must feed into the execution plan's design requirements.
- Treating desk research as a one-time gate — research insights should be revisited when design assumptions are challenged during iteration.
- Smoothing qualitative data by removing hesitations, contradictions, or idiomatic user language — these are signals, not noise.
