# Parsley Health

<!-- API-EVANGELIST-PROVENANCE:BEGIN -->
> ### About this repository
>
> **This is not our API.** This repository is an independent, third-party profile of a company's
> **publicly available** API surface, maintained by [API Evangelist](https://apievangelist.com).
> API Evangelist does not operate, host, resell, or support this company's APIs, and is not
> affiliated with or endorsed by the company unless stated on the profile.
>
> **Where the information came from.** Everything here is assembled from material a member of the
> public can reach with a browser and no credentials — the company's own website, developer portal
> and documentation, the specifications it publishes for public use (OpenAPI, AsyncAPI, JSON Schema,
> `apis.json`, `llms.txt` and similar), its public repositories, and its public status, pricing and
> changelog pages. **Nothing here is obtained by breaching a system, defeating an access control, or
> using credentials of any kind.**
>
> **The rating is an independent assessment.** The Kin Score and Agent Readiness rating are
> independently calculated scores of a company's *public* API artifacts, produced by API Evangelist
> against a published rubric. They are not certifications, endorsements, security assessments, or
> audits, and they score published artifacts — not the quality, safety, or security of the software.
>
> **Corrections, re-scores, and removal are free.** No partnership, contract, or purchase is
> required, and you do not need to justify the request.
>
> - **Something wrong?** Open an issue on this repository, or email
>   [info@apievangelist.com](mailto:info@apievangelist.com).
> - **Published something new?** Ask for a re-score and we will re-run the rating.
> - **Want the listing taken down?** Say so and we will honor it. The profile is reduced to your
>   company name, a factual description, and a link to your own site, and the company is recorded as
>   **unrated** — never scored zero for having asked.
>
> **Response times.** Acknowledgement within **one business day**; removal or restriction within
> **two business days**; corrections and re-scores within **five business days**.
>
> **On a security or compliance team?** Email
> [info@apievangelist.com](mailto:info@apievangelist.com) with *security* in the subject line and
> you will get a person, not a form. We will tell you exactly which public URLs this profile was
> built from so your team can see the same surface we did, and we will take the listing down on
> request while you work through it.
>
> Full detail: **[Where this data comes from](https://apievangelist.com/about/where-our-data-comes-from)**
<!-- API-EVANGELIST-PROVENANCE:END -->

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
