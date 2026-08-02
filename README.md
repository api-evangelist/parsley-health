# Parsley Health

Parsley Health is a physician-led functional medicine practice founded in 2016 by Dr. Robin
Berzin, MD, headquartered in New York, NY. It delivers membership-based primary and specialty
care nationwide via telehealth, with in-person clinics in New York City and Los Angeles.

- Website: https://www.parsleyhealth.com/
- Care and pricing: https://www.parsleyhealth.com/care
- Member application: https://my.parsleyhealth.com/
- GitHub: https://github.com/parsleyhealth
- Secondary market listing: https://forgeglobal.com/parsley-health_stock/

## API posture

**Parsley Health publishes no public API.** Probed 2026-08-02 across every resolvable host:

- No OpenAPI/Swagger at any host root (`/openapi.json`, `/openapi.yaml`, `/swagger.json`,
  `/api-docs`, `/docs`) — all 404 on `www.parsleyhealth.com`.
- No GraphQL endpoint, no MCP server, no A2A agent card, no AsyncAPI, no documented webhooks.
- `api.`, `developer.`, `developers.`, `docs.`, `status.` and `trust.parsleyhealth.com` do not
  resolve in DNS.
- `my.parsleyhealth.com` and `app.parsleyhealth.com` are authenticated single-page apps behind a
  catch-all that answers HTTP 200 `text/html` for every path, including `/.well-known/agent-card.json`
  and `/openapi.json`. Those were rejected as SPA-shell false positives — see
  `well-known/parsley-health-well-known.yml`.
- The public GitHub org holds 12 repositories of internal engineering tooling (Docker images,
  GitHub Actions, lint configs, an Apollo Router fork) — no client SDKs.

## Artifacts

| Path | What it holds |
|---|---|
| `packages/` | Three genuinely first-party npm packages — all lint configs, not API clients |
| `conformance/` | Published healthcare regulatory posture: HIPAA Affiliated Covered Entity, telehealth informed consent, per-state clinical membership terms |
| `security/` | Probed TLS/HSTS/DNSSEC/CAA/SPF/DMARC for the web and member-app hosts |
| `well-known/` | Full `/.well-known/` probe record — zero documents found, SPA false positives labelled |
| `llms/` | Generated `llms.txt` (Parsley publishes none; `/llms.txt` is 404) |
