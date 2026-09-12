# Changelog

## 1.2.0 (2026-09-12)
- **New `request_city` MCP tool** — agents can request Fless expansion to new cities
- **New `get_city_request_stats` MCP tool** — see which cities have the most demand
- REST endpoints: `POST /api/v1/agent/city-requests` + `GET /api/v1/agent/city-requests/stats`
- Inline city request form on /ai-agents page
- MCP server version bumped to 1.1.0 (10 tools now)
- Security: strict input validation, 5 req/hr rate limit, anonymous (no PII)

## 1.1.0 (2026-09-08)
- **Added explicit city coverage section** to README and SKILL.md — Fless operates in Washington, DC / Maryland / Virginia only
- **New "Coverage check" workflow step** in SKILL.md — agents now verify the target city is live before collecting a brief
- **CITIES.md now auto-generates from the production API** (was dev DB — fixed Suffolk, VA drift)
- Coverage table on GitHub README distinguishes live vs coming-soon cities
- Updated frontmatter description to include coverage area

## 1.0.0 (2026-09-05)
- Initial release: fless-apartment-hunt skill (SKILL.md + 4 references).
- MCP server live at https://mcp.fless.io/mcp (8 read-only tools).
- Well-known discovery at https://fless.io/.well-known/agent-skills/index.json.
- Setup guides for 9 assistants at https://fless.io/ai-agents.
