# figma-ball-knowledge for Codex

Install guide for using figma-ball-knowledge with OpenAI Codex.

## Prerequisites

- OpenAI Codex CLI
- Node.js (for `npx`)
- A Figma account with access to the Figma MCP server

## Install

### Step 1 — Add the skill

```bash
npx skills add davidjamesdimalanta/figma-ball-knowledge
```

This copies the skill to `~/.agents/skills/figma-ball-knowledge/` and creates symlinks for all detected agents automatically.

### Step 2 — Configure the Figma MCP server

Add Figma to `~/.codex/config.toml`:

```toml
[mcp_servers.figma]
url = "https://mcp.figma.com/mcp"
```

See [Figma's MCP documentation](https://help.figma.com/hc/en-us/articles/32132100833559) for authentication.

### Step 3 — Restart Codex

Skills are discovered at startup. After restarting, ask Codex to do any Figma task — the skill activates automatically based on its description.

## How it works

Codex scans `~/.agents/skills/` at startup, parses each `SKILL.md` frontmatter, and loads a skill when the user's request matches its description.

The skill itself is written using Claude Code tool names. The translation lives in `references/codex-tools.md` — most importantly, `AskUserQuestion` becomes a plain-text question. Codex reads the tool mapping alongside `SKILL.md`.

## Updating

```bash
npx skills update figma-ball-knowledge
```

## Uninstalling

```bash
npx skills remove figma-ball-knowledge
```

## Troubleshooting

**Skill not activating.** Verify the install: `ls ~/.agents/skills/figma-ball-knowledge/SKILL.md`. Restart Codex after installing.

**Figma tools missing.** Check that the MCP server is reachable and your Codex config parses cleanly. Run a trivial Figma command and watch for an MCP connection error.

---

<details>
<summary><b>Manual install (no npx)</b></summary>

### Step 1 — Clone the repo

```bash
git clone https://github.com/davidjamesdimalanta/figma-ball-knowledge.git ~/.codex/figma-ball-knowledge
```

### Step 2 — Symlink the skill into Codex's skill directory

```bash
mkdir -p ~/.agents/skills
ln -s ~/.codex/figma-ball-knowledge/plugins/figma-ball-knowledge/skills/figma-ball-knowledge ~/.agents/skills/figma-ball-knowledge
```

**Windows (PowerShell, no Developer Mode required):**

```powershell
New-Item -ItemType Directory -Force -Path "$env:USERPROFILE\.agents\skills"
cmd /c mklink /J "$env:USERPROFILE\.agents\skills\figma-ball-knowledge" "$env:USERPROFILE\.codex\figma-ball-knowledge\plugins\figma-ball-knowledge\skills\figma-ball-knowledge"
```

### Updating (manual)

```bash
cd ~/.codex/figma-ball-knowledge && git pull
```

### Uninstalling (manual)

```bash
rm ~/.agents/skills/figma-ball-knowledge
rm -rf ~/.codex/figma-ball-knowledge
```

**Windows:**

```powershell
Remove-Item "$env:USERPROFILE\.agents\skills\figma-ball-knowledge"
Remove-Item -Recurse "$env:USERPROFILE\.codex\figma-ball-knowledge"
```

</details>
