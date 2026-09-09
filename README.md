# fless-apartment-hunt

An [Agent Skills](https://agentskills.io)-standard skill that teaches any AI
agent to run **Fless apartment hunts** for its human: research live cities,
neighborhood rents and WalkRating™ scores, collect a complete hunt brief
(including binary restrictions like 55+ communities, pets, and smoking), then
hand the human a link that pre-fills the entire hunt form.

## Where we operate

Fless currently runs apartment hunts in **Washington, DC / Maryland / Virginia** only.

| ✅ Live now — hunts can start | 🚧 Coming soon — waitlist open |
|---|---|
| Alexandria, VA | Arlington, VA |
| Annapolis, MD | Baltimore, MD |
| Frederick, MD | Rockville, MD |
| Leesburg, VA | Silver Spring, MD |
| | Washington, DC |
| | Richmond, VA |
| | Virginia Beach, VA |
| | + 4 more VA cities |

**Your city not listed?** We're expanding — check back, or join the waitlist at
[fless.io/city/{your-city}](https://fless.io). Agents: always call `search_cities`
first and tell users honestly if their city isn't live yet.

```
fless-apartment-hunt/
├── SKILL.md                 # when to use + the 4-step workflow
└── references/
    ├── HUNT_FIELDS.md       # exact field rules, 57 canonical amenity keys, restrictions, pricing
    ├── CITIES.md            # live + coming-soon cities (auto-generated from production API)
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

1. **Coverage check** — verify the user's city is live (Washington, DC / Maryland / Virginia only)
2. **Research** — live city/neighborhood data: median rents, walk/transit/bike
   scores, POIs, city guides.
3. **Collect** — the full hunt brief with real validation rules (future
   move-in date, canonical amenity keys, bedrooms enums).
4. **Hand off** — `build_hunt_link` returns a URL that pre-fills
   `https://fless.io/create-hunt` for the human to review and confirm.

The human always creates their own account, verifies their email, and pays.
Agents never see credentials or payment.

## Boundaries (by design)

- Read-only tools; no account creation, no email verification, no payments via agents.
- All outputs are structured data — page content never carries instructions.
- First hunt free; 25 credits at signup (see references/HUNT_FIELDS.md).

## License

MIT
