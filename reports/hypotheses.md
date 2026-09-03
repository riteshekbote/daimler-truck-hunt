# Hypotheses (ranked)

## RANKED HYPOTHESES 2026-09-02 21:45:21 UTC

## RANKED HYPOTHESES 2026-09-02 23:56:20 UTC

## RANKED HYPOTHESES 2026-09-03 03:42:29 UTC

## RANKED HYPOTHESES 2026-09-03 08:46:04 UTC

## RANKED HYPOTHESES 2026-09-03 13:25:19 UTC

## RANKED HYPOTHESES 2026-09-03 17:17:27 UTC
- [75] developer.as.api.daimlertruck.com: Developer Portal GraphQL Introspection & Mutation Exposure (from art/lead_nemotron3.txt)
- [72] developer.as.api.daimlertruck.com: developer-portal-exposed-swagger (from art/lead_bigpickle.txt)
- NEXT(hypotheses-bigpickle.txt): PROBE: GET https://developer.as.api.daimlertruck.com/ followed by /swagger.json, /api-docs, /openapi.json, /docs (sequential, 1 req/sec, passive GET only) to co
- NEXT(hypotheses-nemotron3.txt): PROBE: GET https://developer.as.api.daimlertruck.com/ → capture response body, headers, tech stack; then POST https://developer.as.api.daimlertruck.com/graphql 
- LEARN: ACCEPTED MISCONFIG @ developer.as.api.daimlertruck.com: HTTP 200 responses on developer portal strongly correlate with exposed API documentation.
- LEARN: REJECTED IDOR @ authz.tst.eu.api.daimlertruck.com: 404 on root + staging environment = insufficient signal for IDOR hypothesis without path enumeration.
- LEARN: REJECTED network DoS @ all assets: program explicitly excludes DoS/DDoS and account-lockout
- LEARN: REJECTED SSL/TLS best practices @ www.daimlertruck.com: out of scope per policy
- LEARN: REJECTED Clickjacking @ www.daimlertruck.com: requires demonstrated exploit per policy
- LEARN: ACCEPTED GraphQL introspection @ developer.*: high-value, passive, in-scope (API logic flaw)
- LEARN: ACCEPTED Admin panel discovery @ capacitor-admin.*: high-value, requires auth-helped verification
- LEARN: ACCEPTED OAuth misconfig @ authz.*: high-value if endpoints exist, passive discovery first

## RANKED HYPOTHESES 2026-09-03 20:13:47 UTC
- [75] developer.as.api.daimlertruck.com: developer-portal-graphql-introspection (from art/lead_bigpickle.txt)
- [70] developer.as.api.daimlertruck.com: Developer Portal GraphQL Introspection Behind Auth — Post-Auth Schema Enumeration & Mutation Exposure (from art/lead_nemotron3.txt)
- NEXT(hypotheses-bigpickle.txt): PROBE: GET https://developer.as.api.daimlertruck.com/ followed by /graphql (POST with introspection query), /swagger.json, /api-docs, /openapi.json, /docs (sequ
- NEXT(hypotheses-nemotron3.txt): PROBE: GET https://developer.as.api.daimlertruck.com/ → capture full Azure AD B2C auth flow (redirect URL, client_id, scope, redirect_uri); then AUTH_HELPED: PO
- LEARN: ACCEPTED MISCONFIG @ developer.as.api.daimlertruck.com: HTTP 200 responses on developer portal strongly correlate with exposed API documentation.
- LEARN: ACCEPTED GraphQL introspection @ developer.*: high-value, passive, in-scope (API logic flaw)
- LEARN: ACCEPTED Admin panel discovery @ capacitor-admin.*: high-value, requires auth-helped verification
- LEARN: REJECTED IDOR @ authz.tst.eu.api.daimlertruck.com: 404 on root + staging environment = insufficient signal for IDOR hypothesis without path enumeration.
- LEARN: ACCEPTED GraphQL introspection @ developer.*: high-value, in-scope (API logic flaw), but requires auth — passive discovery confirmed endpoint exists at /graphql
- LEARN: REJECTED OAuth misconfig @ authz.*: 7 authz subdomains all return 404 on root and well-known endpoints — no OAuth surface exposed
- LEARN: REJECTED Admin panel discovery @ capacitor-admin.*: 6 subdomains all return 000 (connection failed) — no live HTTP surface to assess
- LEARN: ACCEPTED MISCONFIG @ developer.as.api.daimlertruck.com: HTTP 200 on developer portal correlates with exposed API documentation — but all docs/graphql require au
- LEARN: REJECTED network DoS @ all assets: program explicitly excludes DoS/DDoS and account-lockout
- LEARN: REJECTED SSL/TLS best practices @ www.daimlertruck.com: out of scope per policy

## RANKED HYPOTHESES 2026-09-03 22:31:26 UTC
- [72] developer.as.api.daimlertruck.com: developer-portal-exposed-swagger (from art/lead_bigpickle.txt)
- [70] developer.as.api.daimlertruck.com: Developer Portal GraphQL Introspection Behind Auth — Post-Auth Schema Enumeration & Mutation Exposure (from art/lead_nemotron3.txt)
- NEXT(hypotheses-bigpickle.txt): PROBE: GET https://developer.as.api.daimlertruck.com/ followed by GET https://developer.as.api.daimlertruck.com/swagger.json, GET https://developer.as.api.daiml
- NEXT(hypotheses-nemotron3.txt): PROBE: GET https://developer.as.api.daimlertruck.com/ → capture full Azure AD B2C auth flow (redirect URL, client_id, scope, redirect_uri, state parameter); the
- LEARN: ACCEPTED MISCONFIG @ developer.as.api.daimlertruck.com: HTTP 200 responses on developer portal strongly correlate with exposed API documentation.
- LEARN: ACCEPTED GraphQL introspection @ developer.*: high-value, in-scope (API logic flaw), but requires auth — passive discovery confirmed endpoint exists at /graphql
- LEARN: REJECTED OAuth misconfig @ authz.*: 7 authz subdomains all return 404 on root and well-known endpoints — no OAuth surface exposed.
- LEARN: REJECTED Admin panel discovery @ capacitor-admin.*: 6 subdomains all return 000 (connection failed) — no live HTTP surface to assess.
- LEARN: ACCEPTED GraphQL introspection @ developer.*: high-value, in-scope (API logic flaw), but requires auth — passive discovery confirmed endpoint exists at /graphql
- LEARN: REJECTED OAuth misconfig @ authz.*: 7 authz subdomains all return 404 on root and well-known endpoints — no OAuth surface exposed
- LEARN: REJECTED Admin panel discovery @ capacitor-admin.*: 6 subdomains all return 000 (connection failed) — no live HTTP surface to assess
- LEARN: ACCEPTED MISCONFIG @ developer.as.api.daimlertruck.com: HTTP 200 on developer portal correlates with exposed API documentation — but all docs/graphql require au
- LEARN: REJECTED network DoS @ all assets: program explicitly excludes DoS/DDoS and account-lockout
- LEARN: REJECTED SSL/TLS best practices @ www.daimlertruck.com: out of scope per policy
- LEARN: REJECTED Clickjacking @ www.daimlertruck.com: requires demonstrated exploit per policy
- LEARN: NEW Two distinct build IDs across developer portals: prod-like (as,eu,na) = `JCvrnrykV_KYBk7pu0Npq`; test/dev (dev.na,tst.eu,tst.na) = `JVF_tXHlhCfZQOkT-cULr` —
