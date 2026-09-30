# Changelog

## 2.0.0 (2026-09-30) fless-apartment-hunt
- **BREAKING: approval gate.** Hunts NEVER auto-email anymore. Every new hunt
  (including guest conversion) returns an INSTANT matched-building list
  (`matched_count`, `matched_buildings[]`) right after creation, then parks at
  the gate: `review_state` = `awaiting_user_approval` (individuals) or
  `awaiting_realtor_curation` (realtor hunts).
- New review surface: `GET /hunts/{id}/review`, `GET /hunts/{id}/review/status`,
  `POST /hunts/{id}/review/decision`. Outreach fires ONLY after the explicit
  decision; FAIL-CLOSED: buildings the user leaves untouched are removed, not
  approved.
- Legacy hunts (created before the gate, review_state NULL) stay ungated and
  answer review reads with 409 (nothing to review).
- SKILL.md: new "What's new in 2.0.0" section, rewritten after-handoff flow
  (Step 4), updated intro + rules so every outreach claim matches gate
  behavior. AI.md now documents the review endpoints and the 409 legacy
  semantics; ERRORS.md adds the 409 gate errors + `SUBSCRIPTION_REQUIRED`
  (402 trial_ended) + `UNAUTHORIZED`.
- MCP server version 1.3.0 (19 tools: 11 anonymous + 8 authenticated
  realtor_* tools; see the separate fless-realtor skill released the same day).

## 1.0.0 (2026-09-30) fless-realtor
- **Initial release of the second skill.** Authenticated `realtor_*` MCP tools
  for licensed agents on Fless for Realtors: API key auth
  (`Authorization: Bearer fless_ak_...`, keys created at fless.io/realtor under
  API keys, full key shown exactly once).
- The eight tools: `realtor_list_clients`, `realtor_create_client`,
  `realtor_create_hunt`, `realtor_list_candidate_buildings`,
  `realtor_curate_hunt`, `realtor_add_building_to_hunt`,
  `realtor_send_to_client`, `realtor_get_hunt_status`.
- Two-gate funnel documented: realtor curates (never emails), then sends the
  list to the client (`awaiting_client_approval`); ONLY the client's decision
  releases outreach. Realtor-removed buildings never reach the client.
- Trial/subscription semantics: 15-day free trial, $49.99/mo flat, unlimited
  hunts while subscribed; expired trials get `SUBSCRIPTION_REQUIRED` on
  create-client / create-hunt / send-to-client (curation edits + reads stay
  ungated).
- Structured errors: UNAUTHORIZED / NOT_FOUND / SUBSCRIPTION_REQUIRED /
  INVALID_STATE / CLIENT_EXISTS / ALREADY_IN_HUNT / AMBIGUOUS_MATCH.
- Anonymous MCP toolset unchanged.

## 1.3.0 (2026-09-19)
- **New `submit_error_report` MCP tool**: agents report errors/problems with tools or data
- REST endpoint `POST /api/v1/agent/error-reports` for skill-only users
- Admin triage: Error reports section in Agent Analytics (status workflow, notes)
- MCP server version 1.2.0 (11 tools)
- New `agent_error_reports` table (migration 086, additive-only)
- Security: strict enums + regex validation, injection-phrase blocklist, 5 req/hr/IP rate limit, no PII collected

## 1.2.0 (2026-09-12)
- **New `request_city` MCP tool**: agents can request Fless expansion to new cities
- **New `get_city_request_stats` MCP tool**: see which cities have the most demand
- REST endpoints: `POST /api/v1/agent/city-requests` + `GET /api/v1/agent/city-requests/stats`
- Inline city request form on /ai-agents page
- MCP server version bumped to 1.1.0 (10 tools now)
- Security: strict input validation, 5 req/hr rate limit, anonymous (no PII)

## 1.1.0 (2026-09-08)
- **Added explicit city coverage section** to README and SKILL.md: Fless operates in Washington, DC / Maryland / Virginia only
- **New "Coverage check" workflow step** in SKILL.md: agents now verify the target city is live before collecting a brief
- **CITIES.md now auto-generates from the production API** (was dev DB; fixed Suffolk, VA drift)
- Coverage table on GitHub README distinguishes live vs coming-soon cities
- Updated frontmatter description to include coverage area

## 1.0.0 (2026-09-05)
- Initial release: fless-apartment-hunt skill (SKILL.md + 4 references).
- MCP server live at https://mcp.fless.io/mcp (8 read-only tools).
- Well-known discovery at https://fless.io/.well-known/agent-skills/index.json.
- Setup guides for 9 assistants at https://fless.io/ai-agents.