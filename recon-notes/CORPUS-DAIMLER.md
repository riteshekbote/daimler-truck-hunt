# Daimler Truck — deep surface corpus (daimlertruck.com)

_Generated 2026-09-03 | wildcard-cleaned corpus | read-only passive_

## Cohort
- live HTTPS (GET /): 16 of 16 dedicated

- `as.api.daimlertruck.com`  [HTTP 404]  40.81.218.168
- `authz.as.api.daimlertruck.com`  [HTTP 404]  4.214.165.88
- `authz.dev.na.api.daimlertruck.com`  [HTTP 404]  4.154.193.7
- `authz.eu.api.daimlertruck.com`  [HTTP 404]  9.163.222.35
- `authz.na.api.daimlertruck.com`  [HTTP 404]  20.72.227.176
- `authz.tst.as.api.daimlertruck.com`  [HTTP 404]  20.18.197.184
- `authz.tst.eu.api.daimlertruck.com`  [HTTP 404]  20.76.97.47
- `authz.tst.na.api.daimlertruck.com`  [HTTP 404]  20.72.235.110
- `dev.na.api.daimlertruck.com`  [HTTP 404]  20.80.145.22
- `developer.as.api.daimlertruck.com`  [HTTP 200]  4.214.165.88
- `developer.dev.na.api.daimlertruck.com`  [HTTP 200]  4.154.193.7
- `developer.eu.api.daimlertruck.com`  [HTTP 200]  9.163.222.35
- `developer.na.api.daimlertruck.com`  [HTTP 200]  20.72.227.176
- `developer.tst.eu.api.daimlertruck.com`  [HTTP 200]  20.76.97.47
- `developer.tst.na.api.daimlertruck.com`  [HTTP 200]  20.72.235.110
- `eu.api.daimlertruck.com`  [HTTP 404]  20.76.15.251

## Technology / fingerprint notes
- API gateways (`as.api`, `eu.api`, `dev.na.api`) run **Azure API Management (APIM)** — error envelope `{origin:"config", section:"backend", reason:"OperationNotFound"}` confirms APIM.
- Hosted on Azure public IPs (40.81.218.168, 4.214.x, 9.163.x, 20.x = Microsoft ranges).
- Developer portals (`developer.<region>.api`) are **Next.js** apps (x-powered-by: Next.js).
- All developer-portal non-root paths redirect to login: `307 ?callbackUrl=%2Fapi` — identity-gated (OAuth/B2C style).
- No unauthenticated OpenAPI/Swagger disclosure found on developer portals (gated), and none on the APIM gateways via common paths.
- `authz.<region>.api` return empty 404 at root — likely separate authz service, operation-path bound.
- Region matrix: as / eu / na, plus tst/dev tiers (e.g. developer.tst.eu.api, dev.na.api).

## API / spec paths probed (unauth, read-only)
- as.api / eu.api / dev.na.api GET / -> 404 APIM 'Unknown operation requested' (gateway up, needs valid operation + subscription key).
- developer.*.api /swagger /openapi.json /api-docs -> 307 login gate.
- authz.*.api GET / -> empty 404.

> Honest framing: these are surface-map observations from passive enumeration. No vulnerability is claimed. All HTTP probes were read-only GET to `/` and common static paths. Scope must be confirmed with the program before any active testing.
