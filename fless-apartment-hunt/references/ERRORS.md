# Error catalog and recovery

Every Fless agent surface returns the same error shape:

```json
{"error": {"code": "...", "message": "...", "hint": "...", "docs_url": "https://fless.io/hunt-how-to.md", "retryable": false}}
```

| Code | Meaning | Recovery |
|---|---|---|
| `CITY_NOT_FOUND` | Unknown city id/slug/name | List cities (`search_cities` / cities listing) and retry |
| `COMING_SOON_CITY` | City hasn't launched | Offer the waitlist page `https://fless.io/city/{slug}` instead of a hunt |
| `INVALID_MOVE_IN_DATE_PAST` | move_in_date not future | Ask for a later date |
| `PRICE_RANGE_INVALID` | min ≥ max | Ask for a corrected budget |
| `INVALID_REQUEST` | Bad enum/format/value | Read `message` — it names the field; fix and retry |
| `MISSING_FIELDS` | Tool argument absent | Supply the named field |
| `NOT_FOUND` / `CITY_NOT_FOUND` / `NEIGHBORHOOD_NOT_FOUND` / `POST_NOT_FOUND` / `ARTICLE_NOT_FOUND` (markdown) | No rendition at that URL | Use the listed alternates |
| `RATE_LIMITED` | Too many requests | Wait for `Retry-After` seconds, then retry |
| `LINK_EXPIRED` | Hunt link older than 7 days or unknown | Rebuild via `build_hunt_link` |
| `AUTHENTICATION_REQUIRED` | Anonymous access to a private endpoint | Only public endpoints are agent-accessible; no workaround |

Agent-driven error reporting: every failed validation is logged by Fless with
the field and code, so fixing your brief per the `hint` also improves the
published docs. When a fix contradicts this skill's reference files, trust the
live `hint` and re-read `https://fless.io/hunt-how-to.md` (it is regenerated
from the same source of truth).
