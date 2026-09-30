# API reference (realtor surface)

Base: `https://fless.io` · MCP: `https://mcp.fless.io/mcp` (Streamable HTTP,
JSON-RPC 2.0). Realtor tools require `Authorization: Bearer fless_ak_<key>`
on EVERY call (create the key at https://fless.io/realtor under API keys;
the full key is shown exactly once). `https://www.fless.io/mcp` also works.

## MCP tools (realtor, authenticated)

| Tool | In → Out |
|---|---|
| `realtor_list_clients` `{}` | accepted clients (id, name, email) + pending invitations (email, expires) |
| `realtor_create_client` `{email, first_name, last_name}` | invitation created + emailed (7-day accept link) |
| `realtor_create_hunt` `{name, city_id, client_user_id?|client_email?, min_price?, max_price?, bedrooms?, move_in_date?, proximity_criteria?}` | `{hunt_id, matched_count, review_state}`; hunt parks at `awaiting_realtor_curation` |
| `realtor_list_candidate_buildings` `{hunt_id}` | counts + buildings `[{id (hunt_building), building_id, name, address, distance_miles, eligible, realtor_status}]` |
| `realtor_curate_hunt` `{hunt_id, removals?: [{id, reason?}], notes?: [{id, internal_notes?, client_notes?}]}` | `{hunt_id, review_state, included, removed, notes_applied, sent: false}`; applies WITHOUT sending (send=false path); not gated. Reasons are recorded in the decision audit trail |
| `realtor_add_building_to_hunt` `{hunt_id, query}` | inventory search by name; single match added `{added: true, hunt_building_id, building: {building_id, name, address, distance_miles}, realtor_status: "manual_realtor", eligible}`; ambiguous → top 3 named, nothing added; every match already present → `ALREADY_IN_HUNT` |
| `realtor_send_to_client` `{hunt_id}` | every non-removed building included → `{hunt_id, review_state: "awaiting_client_approval", included, removed, sent: true}`; gated |
| `realtor_get_hunt_status` `{hunt_id}` | `{hunt_id, review_state, counts: {total, eligible, contacted}, mode, client_name, next_step}` |

Anonymous research tools (`search_cities`, `get_city_overview`,
`get_neighborhood`, `get_hunt_requirements`, `build_hunt_link`,
`check_hunt_link`, `search`, `fetch`, `request_city`,
`get_city_request_stats`, `submit_error_report`) work for everyone and need
no key; they are documented in the public `fless-apartment-hunt` skill.

Tool errors come back as `isError: true` results whose `structuredContent`
carries `{error: {code, message, hint, docs_url, retryable}}`.

## The two-gate funnel

`realtor_create_hunt` NEVER emails buildings. The hunt sits at the realtor
curation gate; `realtor_send_to_client` moves it to the client approval
gate; only the CLIENT's approval decision releases outreach. Curating
without sending (`realtor_curate_hunt`) never advances the funnel or gates.

## REST equivalents (authenticated realtor, same dashboard rules)

- `POST /api/v1/realtor/api-keys` `{name}` → 201 `{id, name, key, created_at}` (key shown once)
- `GET /api/v1/realtor/api-keys` → `[{id, name, key_prefix, last_used_at, revoked_at, created_at}]`
- `DELETE /api/v1/realtor/api-keys/{id}` → soft revoke
- `POST /api/v1/realtor/clients` · `GET /api/v1/realtor/clients`
- `POST /api/v1/realtor/hunts` (realtor hunt, same instant matched list + gate as POST /hunts/) · `GET /api/v1/realtor/hunts`
- `GET /api/v1/hunts/{hunt_id}/review` · `GET /api/v1/hunts/{hunt_id}/review/status`
- `POST /api/v1/hunts/{hunt_id}/review/realtor-decision` (curation + send)
- `POST /api/v1/hunts/{hunt_id}/review/decision` (the CLIENT gate: approvals/removals/approve_all_remaining, `confirm: true` required, FAIL-CLOSED: untouched buildings are removed, then `review_state: "released"` and outreach dispatches to the approved buildings). Only the hunt's client may call it; the realtor gets 403 on behalf of the client.
- `GET /api/v1/hunts/{hunt_id}/review/buildings/search?q=` ·
  `POST /api/v1/hunts/{hunt_id}/review/buildings` (manual add) ·
  `DELETE /api/v1/hunts/{hunt_id}/review/buildings/{hunt_building_id}` (manual-remove your own adds)

## Error catalog

| Code | Meaning | Recovery |
|---|---|---|
| `UNAUTHORIZED` | Missing/invalid/revoked API key (or key owner not an active realtor) | Create a key at fless.io/realtor under API keys; send as Authorization Bearer |
| `SUBSCRIPTION_REQUIRED` | Expired trial advancing the funnel (create client / create hunt / send) | Subscribe under Billing at fless.io/realtor ($49.99/mo flat, 15 day free trial) |
| `NOT_FOUND` | Unknown hunt/client, or one that belongs to another realtor (no existence leak) | List first (realtor_list_clients, realtor_get_hunt_status) |
| `INVALID_STATE` | Wrong review_state (409 class): curating a released hunt, sending twice, no matched buildings yet, manual add outside a gate | Read realtor_get_hunt_status, then act on the actual state |
| `INVALID_REQUEST` | Field problem (bad date, price order, enum) | Read message; it names the field |
| `CLIENT_EXISTS` | Invite email that is already one of your clients | Use realtor_list_clients, then create their hunt |
| `ALREADY_IN_HUNT` | Add-building where every inventory match is present | Check realtor_list_candidate_buildings |
| `AMBIGUOUS_MATCH` | Several inventory matches (top 3 named, nothing added) | Retry with a more specific query |
| `CITY_NOT_FOUND` / `COMING_SOON_CITY` | Hunt city unknown or not launched | search_cities; offer the waitlist page |
| `MISSING_FIELDS` | Required tool argument absent | Supply the named field |
| `RATE_LIMITED` | Too many requests | Wait for Retry-After, then retry |

## Pricing (state only when asked)

$49.99/mo flat, 15 day free trial. Unlimited hunts while subscribed. First
hunt for consumers is free under the public funnel (25 signup credits;
hunts cost 5 credits); never mix the two pricings in one message.

## Limits

Anonymous: ~120 requests/min/IP (429 + Retry-After). Client invitations are
valid 7 days. Hunt links (public funnel) live 7 days.