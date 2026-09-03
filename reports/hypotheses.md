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
