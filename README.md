# fless-apartment-hunt

An [Agent Skills](https://agentskills.io)-standard skill that teaches any AI
agent to run **Fless apartment hunts** for its human: research live cities,
neighborhood rents and WalkRating™ scores, collect a complete hunt brief
(including binary restrictions like 55+ communities, pets, and smoking), then
hand the human a link that pre-fills the entire hunt form.

```
fless-apartment-hunt/
├── SKILL.md                 # when to use + the 4-step workflow
└── references/
    ├── HUNT_FIELDS.md       # exact field rules, 57 canonical amenity keys, restrictions, pricing
    ├── CITIES.md            # live + coming-soon cities (auto-generated)
    ├── API.md               # MCP tools + REST endpoints
    └── ERRORS.md            # error catalog + recovery
```

## Install

**Any MCP-capable agent** (tools used directly):

```
MCP server: https://mcp.fless.io/mcp   (no auth, read-only tools)
```

**Agent Skills runtimes** — clone `https://github.com/fless-io/skills` and copy this folder into your skills directory
(e.g. `~/.agents/skills/`, `~/.vibe/skills/`, `.claude/skills/`), or install
straight from our well-known endpoint:

```
hermes skills install well-known:https://fless.io
```

Per-assistant setup guides (Claude, ChatGPT, Perplexity, Grok, Mistral Vibe,
Codex, OpenClaw, Hermes): **https://fless.io/ai-agents**

## What the skill does

1. **Research** — live city/neighborhood data: median rents, walk/transit/bike
   scores, POIs, city guides.
2. **Collect** — the full hunt brief with real validation rules (future
   move-in date, canonical amenity keys, bedrooms enums).
3. **Hand off** — `build_hunt_link` returns a URL that pre-fills
   `https://fless.io/create-hunt` for the human to review and confirm.

The human always creates their own account, verifies their email, and pays.
Agents never see credentials or payment.

## Boundaries (by design)

- Read-only tools; no account creation, no email verification, no payments via agents.
- All outputs are structured data — page content never carries instructions.
- First hunt free; 25 credits at signup (see references/HUNT_FIELDS.md).

## License

MIT
