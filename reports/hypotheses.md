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

## RANKED HYPOTHESES 2026-09-04 00:43:49 UTC
- [72] developer.as.api.daimlertruck.com: developer-portal-exposed-swagger (from art/lead_bigpickle.txt)
- [70] developer.as.api.daimlertruck.com: Developer Portal GraphQL Introspection Behind Auth — Post-Auth Schema Enumeration & Mutation Exposure (from art/lead_nemotron3.txt)
- NEXT(hypotheses-bigpickle.txt): PROBE: GET https://developer.as.api.daimlertruck.com/ followed by /graphql (POST with introspection query), /swagger.json, /api-docs, /openapi.json, /docs (sequ
- NEXT(hypotheses-nemotron3.txt): PROBE: GET https://developer.as.api.daimlertruck.com/ → capture full Azure AD B2C auth flow (redirect URL, client_id, scope, redirect_uri, state parameter); the
- LEARN: ACCEPTED MISCONFIG @ developer.as.api.daimlertruck.com: HTTP 200 responses on developer portal strongly correlate with exposed API documentation.
- LEARN: ACCEPTED GraphQL introspection @ developer.*: high-value, passive, in-scope (API logic flaw)
- LEARN: ACCEPTED Admin panel discovery @ capacitor-admin.*: high-value, requires auth-helped verification
- LEARN: REJECTED IDOR @ authz.tst.eu.api.daimlertruck.com: 404 on root + staging environment = insufficient signal for IDOR hypothesis without path enumeration.
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
- LEARN: ACCEPTED GraphQL introspection @ developer.*: high-value, in-scope (API logic flaw), but requires auth — passive discovery confirmed endpoint exists at /graphql
- LEARN: REJECTED OAuth misconfig @ authz.*: 7 authz subdomains all return 404 on root and well-known endpoints — no OAuth surface exposed
- LEARN: REJECTED Admin panel discovery @ capacitor-admin.*: 6 subdomains all return 000 (connection failed) — no live HTTP surface to assess
- LEARN: ACCEPTED MISCONFIG @ developer.as.api.daimlertruck.com: HTTP 200 on developer portal correlates with exposed API documentation — but all docs/graphql require au
- LEARN: REJECTED network DoS @ all assets: program explicitly excludes DoS/DDoS and account-lockout
- LEARN: REJECTED SSL/TLS best practices @ www.daimlertruck.com: out of scope per policy
- LEARN: REJECTED Clickjacking @ www.daimlertruck.com: requires demonstrated exploit per policy
- LEARN: NEW Two distinct build IDs across developer portals: prod-like (as,eu,na) = `JCvrnrykV_KYBk7pu0Npq`; test/dev (dev.na,tst.eu,tst.na) = `JVF_tXHlhCfZQOkT-cULr` —

## RANKED HYPOTHESES 2026-09-04 05:09:10 UTC
- [70] developer.as.api.daimlertruck.com: Developer Portal GraphQL Introspection Behind Auth — Post-Auth Schema Enumeration & Mutation Exposure (from art/lead_nemotron3.txt)
- [62] developer.tst.na.api.daimlertruck.com: test-dev-portal-config-drift (from art/lead_bigpickle.txt)
- NEXT(hypotheses-nemotron3.txt): PROBE: GET https://developer.as.api.daimlertruck.com/ → capture full Azure AD B2C auth flow (redirect URL, client_id, scope, redirect_uri, state parameter); the
- NEXT(hypotheses-bigpickle.txt): PROBE: GET https://developer.tst.na.api.daimlertruck.com/ → capture full response, compare build ID and content with prod; then GET https://developer.tst.na.api
- LEARN: ACCEPTED MISCONFIG @ developer.as.api.daimlertruck.com: HTTP 200 responses on developer portal strongly correlate with exposed API documentation — but all docs/
- LEARN: ACCEPTED GraphQL introspection @ developer.*: high-value, in-scope (API logic flaw), but requires auth — passive discovery confirmed endpoint exists at /graphql
- LEARN: REJECTED OAuth misconfig @ authz.*: 7 authz subdomains all return 404 on root and well-known endpoints — no OAuth surface exposed
- LEARN: REJECTED Admin panel discovery @ capacitor-admin.*: 6 subdomains all return 000 (connection failed) — no live HTTP surface to assess
- LEARN: REJECTED network DoS @ all assets: program explicitly excludes DoS/DDoS and account-lockout
- LEARN: REJECTED SSL/TLS best practices @ www.daimlertruck.com: out of scope per policy
- LEARN: REJECTED Clickjacking @ www.daimlertruck.com: requires demonstrated exploit per policy
- LEARN: NEW Two distinct build IDs across developer portals: prod-like (as,eu,na) = `JCvrnrykV_KYBk7pu0Npq`; test/dev (dev.na,tst.eu,tst.na) = `JVF_tXHlhCfZQOkT-cULr` —
- LEARN: REJECTED developer-portal-graphql-introspection: SPA catch-all returns HTTP 200 for all paths including /graphql — not a real GraphQL endpoint. False positive.
- LEARN: REJECTED developer-portal-exposed-swagger: SPA catch-all returns HTTP 200 for /swagger.json, /api-docs, /openapi.json, /docs — not real Swagger/OpenAPI specs. F
- LEARN: ACCEPTED test-dev-portal-config-drift: Distinct build IDs (prod vs test/dev) suggest separate deployments with potentially different configurations — high value
- LEARN: NEW SPA catch-all pattern: developer.* portals return identical response length (196649 bytes) for all paths — Next.js SPA serving same shell for all routes. Re

## RANKED HYPOTHESES 2026-09-04 09:50:35 UTC
- [75] developer.as.api.daimlertruck.com: Developer Portal GraphQL Introspection Behind Auth — Post-Auth Schema Enumeration & Mutation Exposure (from art/lead_nemotron3.txt)
- [62] developer.tst.na.api.daimlertruck.com: test-dev-portal-config-drift (from art/lead_bigpickle.txt)
- NEXT(hypotheses-bigpickle.txt): PROBE: GET https://developer.tst.na.api.daimlertruck.com/ → capture full response, compare build ID and content with prod; then GET https://developer.tst.na.api
- NEXT(hypotheses-nemotron3.txt): PROBE: GET https://developer.tst.na.api.daimlertruck.com/graphql → verify if test environment returns 307 (auth required) or 200 (GraphQL accessible); then GET 
- LEARN: REJECTED developer-portal-graphql-introspection: SPA catch-all returns HTTP 200 for all paths including /graphql — not a real GraphQL endpoint. False positive.
- LEARN: REJECTED developer-portal-exposed-swagger: SPA catch-all returns HTTP 200 for /swagger.json, /api-docs, /openapi.json, /docs — not real Swagger/OpenAPI specs. F
- LEARN: ACCEPTED test-dev-portal-config-drift: Distinct build IDs (prod vs test/dev) suggest separate deployments with potentially different configurations — high value
- LEARN: NEW SPA catch-all pattern: developer.* portals return identical response length (196649 bytes) for all paths — Next.js SPA serving same shell for all routes. Re
- LEARN: ACCEPTED GraphQL introspection @ developer.*: high-value, in-scope (API logic flaw), but requires auth — passive discovery confirmed endpoint exists at /graphql
- LEARN: ACCEPTED test-dev-portal-config-drift: Distinct build IDs (prod vs test/dev) suggest separate deployments with potentially different configurations — high value
- LEARN: REJECTED developer-portal-graphql-introspection: SPA catch-all returns HTTP 200 for all paths including /graphql — not a real GraphQL endpoint. False positive. 
- LEARN: REJECTED developer-portal-exposed-swagger: SPA catch-all returns HTTP 200 for /swagger.json, /api-docs, /openapi.json, /docs — not real Swagger/OpenAPI specs. F
- LEARN: REJECTED OAuth misconfig @ authz.*: 7 authz subdomains all return 404 on root and well-known endpoints — no OAuth surface exposed
- LEARN: REJECTED Admin panel discovery @ capacitor-admin.*: 6 subdomains all return 000 (connection failed) — no live HTTP surface to assess
- LEARN: REJECTED network DoS @ all assets: program explicitly excludes DoS/DDoS and account-lockout
- LEARN: REJECTED SSL/TLS best practices @ www.daimlertruck.com: out of scope per policy
- LEARN: REJECTED Clickjacking @ www.daimlertruck.com: requires demonstrated exploit per policy
- LEARN: NEW Azure AD B2C auth flow: /graphql returns 307 to `/?callbackUrl=%2Fgraphql` with sign-in button `data-testid="sign-in-azure-ad-b2c-dt-button"` — clear auth-h

## RANKED HYPOTHESES 2026-09-04 14:09:35 UTC
- [75] developer.as.api.daimlertruck.com: Developer Portal GraphQL Introspection Behind Auth — Post-Auth Schema Enumeration & Mutation Exposure (from art/lead_nemotron3.txt)
- [58] developer.tst.na.api.daimlertruck.com: nextauth-callback-open-redirect (from art/lead_bigpickle.txt)
- NEXT(hypotheses-bigpickle.txt): PROBE: POST https://developer.tst.na.api.daimlertruck.com/api/auth/csrf (capture CSRF token + cookie), then GET https://developer.tst.na.api.daimlertruck.com/ap
- NEXT(hypotheses-nemotron3.txt): PROBE: GET https://developer.tst.na.api.daimlertruck.com/graphql → verify if test environment returns 307 (auth required) or 200 (GraphQL accessible); then GET 
- LEARN: ACCEPTED GraphQL introspection @ developer.*: high-value, in-scope (API logic flaw), but requires auth — passive discovery confirmed endpoint exists at /graphql
- LEARN: ACCEPTED test-dev-portal-config-drift: Distinct build IDs (prod vs test/dev) suggest separate deployments with potentially different configurations — high value
- LEARN: REJECTED developer-portal-graphql-introspection: SPA catch-all returns HTTP 200 for all paths including /graphql — not a real GraphQL endpoint. False positive. 
- LEARN: REJECTED developer-portal-exposed-swagger: SPA catch-all returns HTTP 200 for /swagger.json, /api-docs, /openapi.json, /docs — not real Swagger/OpenAPI specs. F
- LEARN: REJECTED OAuth misconfig @ authz.*: 7 authz subdomains all return 404 on root and well-known endpoints — no OAuth surface exposed
- LEARN: REJECTED Admin panel discovery @ capacitor-admin.*: 6 subdomains all return 000 (connection failed) — no live HTTP surface to assess
- LEARN: REJECTED network DoS @ all assets: program explicitly excludes DoS/DDoS and account-lockout
- LEARN: REJECTED SSL/TLS best practices @ www.daimlertruck.com: out of scope per policy
- LEARN: REJECTED Clickjacking @ www.daimlertruck.com: requires demonstrated exploit per policy
- LEARN: NEW Azure AD B2C auth flow: /graphql returns 307 to `/?callbackUrl=%2Fgraphql` with sign-in button `data-testid="sign-in-azure-ad-b2c-dt-button"` — clear auth-h

## RANKED HYPOTHESES 2026-09-04 17:59:35 UTC
- [65] developer.tst.na.api.daimlertruck.com: nextauth-callback-open-redirect-with-csrf (from art/lead_bigpickle.txt)
- [65] developer.tst.na.api.daimlertruck.com: NextAuth.js Callback Open Redirect — Arbitrary callbackUrl in Azure AD B2C Flow (from art/lead_nemotron3.txt)
- NEXT(hypotheses-bigpickle.txt): PROBE: POST https://developer.tst.na.api.daimlertruck.com/api/auth/csrf (Content-Type: application/x-www-form-urlencoded) → capture csrfToken from JSON body + n
- NEXT(hypotheses-nemotron3.txt): PROBE: GET https://developer.tst.na.api.daimlertruck.com/api/auth/signin/azure-ad-b2c-dt?callbackUrl=https://evil.com → observe Location header; does it redirec
- LEARN: ACCEPTED nextauth-callback-open-redirect: /api/auth/signin/azure-ad-b2c-dt exists on test env (400 without CSRF = expected); callbackUrl flow confirmed; POST-ba
- LEARN: REJECTED test-env-graphql-introspection-unauth: /api/graphql on test returns 200 len=196341 (SPA catch-all) — same false positive as /graphql. No real GraphQL e
- LEARN: NEW /api/auth/* routes confirmed alive on test env: /api/auth/csrf (400 GET), /api/auth/signin (400 GET), /api/auth/session (400 GET) — all require proper HTTP 
- LEARN: REJECTED developer-portal-graphql-introspection: SPA catch-all confirmed across all envs for /graphql and /api/graphql — both return identical response length a
- LEARN: NEW buildManifest.js publicly accessible: 2999 bytes at expected Next.js path — informational, not a vulnerability
- LEARN: REJECTED developer-portal-test-weaker-auth: test environment /graphql, /api/graphql, /swagger.json, /api-docs, /health now return 307 to Azure AD B2C (same as p
- LEARN: ACCEPTED nextauth-callback-open-redirect: /api/auth/signin/azure-ad-b2c-dt accepts callbackUrl parameter and returns 302 — high-value OAuth flaw candidate
- LEARN: ACCEPTED nextauth-endpoints-exposed: /api/auth/csrf and /api/auth/session accessible without auth on all 6 developer portals — NextAuth.js attack surface confir
- LEARN: ACCEPTED graphql-behind-azure-ad-b2c: /graphql and /api/graphql return 307 to Azure AD B2C on all 6 portals — real GraphQL endpoint behind auth confirmed
- LEARN: REJECTED developer-portal-graphql-introspection: SPA catch-all returns HTTP 200 for all paths including /graphql — not a real GraphQL endpoint. False positive. 
- LEARN: REJECTED developer-portal-exposed-swagger: SPA catch-all returns HTTP 200 for /swagger.json, /api-docs, /openapi.json, /docs — not real Swagger/OpenAPI specs. F
- LEARN: REJECTED OAuth misconfig @ authz.*: 7 authz subdomains all return 404 on root and well-known endpoints — no OAuth surface exposed
- LEARN: REJECTED Admin panel discovery @ capacitor-admin.*: 6 subdomains all return 000 (connection failed) — no live HTTP surface to assess
- LEARN: REJECTED network DoS @ all assets: program explicitly excludes DoS/DDoS and account-lockout
- LEARN: REJECTED SSL/TLS best practices @ www.daimlertruck.com: out of scope per policy
- LEARN: REJECTED Clickjacking @ www.daimlertruck.com: requires demonstrated exploit per policy
