## 2026-09-03 17:13:21 UTC [target] (model bigpickle)
[PRIO] developer.{as,eu,na}.api.daimlertruck.com,8.2,attack_surface=9,tech_exposure=8,gate_ease=6,cloud_surface=5,freshness=7
[PRIO] capacitor-admin.{as,eu,na}.api.daimlertruck.com,7.4,attack_surface=7,tech_exposure=9,gate_ease=5,cloud_surface=5,freshness=6
[PRIO] authz.{as,eu,na}.api.daimlertruck.com,6.8,attack_surface=7,tech_exposure=8,gate_ease=4,cloud_surface=5,freshness=7
[HYP] developer-portal-exposed-swagger
class: MISCONFIG
asset: developer.as.api.daimlertruck.com
confidence: 72
reasoning: HTTP 200 on developer portal endpoints; developer portals commonly expose /swagger, /docs, /openapi.json, or /api-docs that reveal full API surface including internal endpoints, auth schemes, and parameter schemas
evidence_needed: Swagger/OpenAPI spec with internal endpoints or admin-only operations
verify_steps: GET /, GET /swagger.json, GET /api-docs, GET /openapi.json, GET /docs, GET /api/v1/swagger.json
impact: Full API schema disclosure including internal endpoints, parameter types, auth requirements → enables targeted IDOR/SSRF/auth bypass attacks; Severity: medium
testability: PASSIVE
[HYP] capacitor-admin-uncauthenticated
class: MISCONFIG
asset: capacitor-admin.eu.api.daimlertruck.com
confidence: 58
reasoning: "capacitor-admin" naming suggests internal admin panel; unprobed status means no HTTP response yet captured; admin panels frequently lack auth on initial load or expose sensitive configuration
evidence_needed: Admin dashboard content, configuration endpoints, or user management without authentication
verify_steps: GET /, GET /admin, GET /login, GET /api/config, GET /api/status, HEAD / (check server header)
impact: Administrative access to Capacitor-managed infrastructure → user data, configuration, potential RCE; Severity: high
testability: AUTH_HELPED
[HYP] authz-endpoint-idor
class: IDOR
asset: authz.tst.eu.api.daimlertruck.com
confidence: 45
reasoning: "tst" prefix suggests test/staging environment; authorization endpoints on staging often have weaker controls; 404 on root suggests path-based routing (authz/* may have specific paths)
evidence_needed: Accessible authz endpoints with ID parameters, response containing user/tenant data
verify_steps: GET /, GET /api/v1/users, GET /api/v1/authz/health, GET /swagger.json, GET /api-docs
impact: Cross-tenant authorization bypass or privilege escalation on staging; Severity: high
testability: AUTH_HELPED
[FINAL] developer-portal-exposed-swagger: PASS. 72 confidence, pure passive verification, strong signal from 200 responses on developer portal.
[FINAL] capacitor-admin-uncauthenticated: PASS. 58 confidence, admin naming is strong indicator, needs live probe to confirm.
[PARKED] authz-endpoint-idor: Below confidence threshold (45), no evidence of accessible endpoints, 404 on root suggests deeper path enumeration needed first.
[NEXT] PROBE: GET https://developer.as.api.daimlertruck.com/ followed by /swagger.json, /api-docs, /openapi.json, /docs (sequential, 1 req/sec, passive GET only) to confirm Swagger/OpenAPI exposure.
[LEARN] ACCEPTED MISCONFIG @ developer.as.api.daimlertruck.com: HTTP 200 responses on developer portal strongly correlate with exposed API documentation.
[LEARN] REJECTED IDOR @ authz.tst.eu.api.daimlertruck.com: 404 on root + staging environment = insufficient signal for IDOR hypothesis without path enumeration.
[RISK] Daimler Truck: 35/100. Program scope is broad ("all infrastructure, digital services, brands and subsidiaries") which reduces ambiguity risk. Main risk is accidentally touching customer data during IDOR/authorization testing - requires careful scoping of test accounts.
## 2026-09-03 19:58:03 UTC [target] (model bigpickle)
[PRIO] developer.as.api.daimlertruck.com,8.2,attack_surface=9,tech_exposure=8,gate_ease=6,cloud_surface=5,freshness=7
[PRIO] capacitor-admin.eu.api.daimlertruck.com,7.4,attack_surface=7,tech_exposure=9,gate_ease=5,cloud_surface=5,freshness=6
[PRIO] authz.tst.eu.api.daimlertruck.com,6.8,attack_surface=7,tech_exposure=8,gate_ease=4,cloud_surface=5,freshness=7
[HYP] developer-portal-graphql-introspection
class: MISCONFIG
asset: developer.as.api.daimlertruck.com
confidence: 75
reasoning: HTTP 200 on developer portal; GraphQL endpoints commonly expose introspection by default; program scope includes API logic flaws
evidence_needed: GraphQL schema with internal types, mutations, or admin-only fields
verify_steps: GET /graphql, POST /graphql with {"query":"{__schema{types{name,fields{name}}}}"}
impact: Full schema disclosure → targeted attacks on internal mutations/queries; Severity: medium
testability: PASSIVE
[HYP] developer-portal-exposed-swagger
class: MISCONFIG
asset: developer.as.api.daimlertruck.com
confidence: 72
reasoning: HTTP 200 on developer portal; developer portals commonly expose /swagger, /docs, /openapi.json
evidence_needed: Swagger/OpenAPI spec with internal endpoints or admin-only operations
verify_steps: GET /, GET /swagger.json, GET /api-docs, GET /openapi.json, GET /docs
impact: Full API schema disclosure including internal endpoints; Severity: medium
testability: PASSIVE
[HYP] capacitor-admin-unauthenticated
class: MISCONFIG
asset: capacitor-admin.eu.api.daimlertruck.com
confidence: 58
reasoning: "capacitor-admin" naming suggests internal admin panel; unprobed status means no HTTP response yet captured
evidence_needed: Admin dashboard content, configuration endpoints, or user management without authentication
verify_steps: GET /, GET /admin, GET /login, GET /api/config, GET /api/status
impact: Administrative access to Capacitor-managed infrastructure; Severity: high
testability: AUTH_HELPED
[FINAL] developer-portal-graphql-introspection: PASS. 75 confidence, passive verification, high value.
[FINAL] developer-portal-exposed-swagger: PASS. 72 confidence, pure passive verification.
[FINAL] capacitor-admin-unauthenticated: PASS. 58 confidence, needs live probe to confirm.
[PARKED] authz-endpoint-idor: Below confidence threshold (45), insufficient evidence.
[NEXT] PROBE: GET https://developer.as.api.daimlertruck.com/ followed by /graphql (POST with introspection query), /swagger.json, /api-docs, /openapi.json, /docs (sequential, 1 req/sec, passive GET/POST only)
[LEARN] ACCEPTED MISCONFIG @ developer.as.api.daimlertruck.com: HTTP 200 responses on developer portal strongly correlate with exposed API documentation.
[LEARN] ACCEPTED GraphQL introspection @ developer.*: high-value, passive, in-scope (API logic flaw)
[LEARN] ACCEPTED Admin panel discovery @ capacitor-admin.*: high-value, requires auth-helped verification
[LEARN] REJECTED IDOR @ authz.tst.eu.api.daimlertruck.com: 404 on root + staging environment = insufficient signal for IDOR hypothesis without path enumeration.
[RISK] Daimler Truck: 35/100. Program scope is broad ("all infrastructure, digital services, brands and subsidiaries") which reduces ambiguity risk. Main risk is accidentally touching customer data during IDOR/authorization testing - requires careful scoping of test accounts.
## 2026-09-03 22:29:01 UTC [target] (model bigpickle)
[PRIO] developer.as.api.daimlertruck.com,8.2,attack_surface=9,tech_exposure=8,gate_ease=6,cloud_surface=5,freshness=7
[PRIO] developer.na.api.daimlertruck.com,8.0,attack_surface=9,tech_exposure=8,gate_ease=6,cloud_surface=5,freshness=7
[PRIO] developer.eu.api.daimlertruck.com,8.0,attack_surface=9,tech_exposure=8,gate_ease=6,cloud_surface=5,freshness=7
[HYP] developer-portal-exposed-swagger
class: MISCONFIG
asset: developer.as.api.daimlertruck.com
confidence: 72
reasoning: HTTP 200 on developer portal; developer portals commonly expose /swagger, /docs, /openapi.json by default; program scope includes API logic flaws
evidence_needed: Swagger/OpenAPI spec with internal endpoints or admin-only operations
verify_steps: GET https://developer.as.api.daimlertruck.com/, GET https://developer.as.api.daimlertruck.com/swagger.json, GET https://developer.as.api.daimlertruck.com/api-docs, GET https://developer.as.api.daimlertruck.com/openapi.json, GET https://developer.as.api.daimlertruck.com/docs
impact: Full API schema disclosure including internal endpoints; Severity: medium
testability: PASSIVE
[HYP] developer-portal-graphql-introspection
class: MISCONFIG
asset: developer.as.api.daimlertruck.com
confidence: 75
reasoning: HTTP 200 on developer portal; GraphQL endpoints commonly expose introspection by default; confirmed endpoint exists at /graphql; program scope includes API logic flaws
evidence_needed: GraphQL schema with internal types, mutations, or admin-only fields
verify_steps: GET https://developer.as.api.daimlertruck.com/graphql, POST https://developer.as.api.daimlertruck.com/graphql with {"query":"{__schema{types{name,fields{name}}}}"}
impact: Full schema disclosure → targeted attacks on internal mutations/queries; Severity: medium
testability: PASSIVE
[FINAL] developer-portal-graphql-introspection: PASS. 75 confidence, passive verification, high value, confirmed endpoint exists.
[FINAL] developer-portal-exposed-swagger: PASS. 72 confidence, pure passive verification, strong signal from HTTP 200 responses.
[NEXT] PROBE: GET https://developer.as.api.daimlertruck.com/ followed by GET https://developer.as.api.daimlertruck.com/swagger.json, GET https://developer.as.api.daimlertruck.com/api-docs, GET https://developer.as.api.daimlertruck.com/openapi.json, GET https://developer.as.api.daimlertruck.com/docs (sequential, 1 req/sec, passive GET only) to confirm Swagger/OpenAPI exposure.
[LEARN] ACCEPTED MISCONFIG @ developer.as.api.daimlertruck.com: HTTP 200 responses on developer portal strongly correlate with exposed API documentation.
[LEARN] ACCEPTED GraphQL introspection @ developer.*: high-value, in-scope (API logic flaw), but requires auth — passive discovery confirmed endpoint exists at /graphql on all 6 portals.
[LEARN] REJECTED OAuth misconfig @ authz.*: 7 authz subdomains all return 404 on root and well-known endpoints — no OAuth surface exposed.
[LEARN] REJECTED Admin panel discovery @ capacitor-admin.*: 6 subdomains all return 000 (connection failed) — no live HTTP surface to assess.
[RISK] Daimler Truck: 35/100. Program scope is broad ("all infrastructure, digital services, brands and subsidiaries") which reduces ambiguity risk. Main risk is accidentally touching customer data during IDOR/authorization testing — requires careful scoping of test accounts.
## 2026-09-04 00:25:24 UTC [target] (model bigpickle)
impact: Full schema disclosure → targeted attacks on internal mutations/queries; Severity: medium
testability: PASSIVE
[HYP] developer-portal-exposed-swagger
class: MISCONFIG
asset: developer.as.api.daimlertruck.com
confidence: 72
reasoning: HTTP 200 on developer portal; developer portals commonly expose /swagger, /docs, /openapi.json
evidence_needed: Swagger/OpenAPI spec with internal endpoints or admin-only operations
verify_steps: GET /, GET /swagger.json, GET /api-docs, GET /openapi.json, GET /docs
impact: Full API schema disclosure including internal endpoints; Severity: medium
testability: PASSIVE
[HYP] capacitor-admin-unauthenticated
class: MISCONFIG
asset: capacitor-admin.eu.api.daimlertruck.com
confidence: 58
reasoning: "capacitor-admin" naming suggests internal admin panel; unprobed status means no HTTP response yet captured
evidence_needed: Admin dashboard content, configuration endpoints, or user management without authentication
verify_steps: GET /, GET /admin, GET /login, GET /api/config, GET /api/status
impact: Administrative access to Capacitor-managed infrastructure; Severity: high
testability: AUTH_HELPED
[FINAL] developer-portal-graphql-introspection: PASS. 75 confidence, passive verification, high value.
[FINAL] developer-portal-exposed-swagger: PASS. 72 confidence, pure passive verification.
[FINAL] capacitor-admin-unauthenticated: PASS. 58 confidence, needs live probe to confirm.
[PARKED] authz-endpoint-idor: Below confidence threshold (45), insufficient evidence.
[NEXT] PROBE: GET https://developer.as.api.daimlertruck.com/ followed by /graphql (POST with introspection query), /swagger.json, /api-docs, /openapi.json, /docs (sequential, 1 req/sec, passive GET/POST only)
[LEARN] ACCEPTED MISCONFIG @ developer.as.api.daimlertruck.com: HTTP 200 responses on developer portal strongly correlate with exposed API documentation.
[LEARN] ACCEPTED GraphQL introspection @ developer.*: high-value, passive, in-scope (API logic flaw)
[LEARN] ACCEPTED Admin panel discovery @ capacitor-admin.*: high-value, requires auth-helped verification
[LEARN] REJECTED IDOR @ authz.tst.eu.api.daimlertruck.com: 404 on root + staging environment = insufficient signal for IDOR hypothesis without path enumeration.
[RISK] Daimler Truck: 35/100. Program scope is broad ("all infrastructure, digital services, brands and subsidiaries") which reduces ambiguity risk. Main risk is accidentally touching customer data during IDOR/authorization testing - requires careful scoping of test accounts.
[PRIO] developer.as.api.daimlertruck.com,8.2,attack_surface=9,tech_exposure=8,gate_ease=6,cloud_surface=5,freshness=7
[PRIO] developer.na.api.daimlertruck.com,8.0,attack_surface=9,tech_exposure=8,gate_ease=6,cloud_surface=5,freshness=7
[PRIO] developer.eu.api.daimlertruck.com,8.0,attack_surface=9,tech_exposure=8,gate_ease=6,cloud_surface=5,freshness=7
[HYP] developer-portal-exposed-swagger
class: MISCONFIG
asset: developer.as.api.daimlertruck.com
confidence: 72
reasoning: HTTP 200 on developer portal; developer portals commonly expose /swagger, /docs, /openapi.json by default; program scope includes API logic flaws
evidence_needed: Swagger/OpenAPI spec with internal endpoints or admin-only operations
verify_steps: GET https://developer.as.api.daimlertruck.com/, GET https://developer.as.api.daimlertruck.com/swagger.json, GET https://developer.as.api.daimlertruck.com/api-docs, GET https://developer.as.api.daimlertruck.com/openapi.json, GET https://developer.as.api.daimlertruck.com/docs
impact: Full API schema disclosure including internal endpoints; Severity: medium
testability: PASSIVE
[HYP] developer-portal-graphql-introspection
class: MISCONFIG
asset: developer.as.api.daimlertruck.com
confidence: 75
reasoning: HTTP 200 on developer portal; GraphQL endpoints commonly expose introspection by default; confirmed endpoint exists at /graphql; program scope includes API logic flaws
evidence_needed: GraphQL schema with internal types, mutations, or admin-only fields
verify_steps: GET https://developer.as.api.daimlertruck.com/graphql, POST https://developer.as.api.daimlertruck.com/graphql with {"query":"{__schema{types{name,fields{name}}}}"}
impact: Full schema disclosure → targeted attacks on internal mutations/queries; Severity: medium
testability: PASSIVE
[FINAL] developer-portal-graphql-introspection: PASS. 75 confidence, passive verification, high value, confirmed endpoint exists.
[FINAL] developer-portal-exposed-swagger: PASS. 72 confidence, pure passive verification, strong signal from HTTP 200 responses.
[NEXT] PROBE: GET https://developer.as.api.daimlertruck.com/ followed by GET https://developer.as.api.daimlertruck.com/swagger.json, GET https://developer.as.api.daimlertruck.com/api-docs, GET https://developer.as.api.daimlertruck.com/openapi.json, GET https://developer.as.api.daimlertruck.com/docs (sequential, 1 req/sec, passive GET only) to confirm Swagger/OpenAPI exposure.
[LEARN] ACCEPTED MISCONFIG @ developer.as.api.daimlertruck.com: HTTP 200 responses on developer portal strongly correlate with exposed API documentation.
[LEARN] ACCEPTED GraphQL introspection @ developer.*: high-value, in-scope (API logic flaw), but requires auth — passive discovery confirmed endpoint exists at /graphql on all 6 portals.
[LEARN] REJECTED OAuth misconfig @ authz.*: 7 authz subdomains all return 404 on root and well-known endpoints — no OAuth surface exposed.
[LEARN] REJECTED Admin panel discovery @ capacitor-admin.*: 6 subdomains all return 000 (connection failed) — no live HTTP surface to assess.
[RISK] Daimler Truck: 35/100. Program scope is broad ("all infrastructure, digital services, brands and subsidiaries") which reduces ambiguity risk. Main risk is accidentally touching customer data during IDOR/authorization testing — requires careful scoping of test accounts.
## 2026-09-04 05:06:13 UTC [target] (model bigpickle)
[CHANGED] developer.*.api.daimlertruck.com: probe confirms SPA catch-all — all paths (/graphql, /swagger.json, /api-docs, /openapi.json, /docs) return identical 196649 bytes as root. These are Next.js SPA routes, NOT real API endpoints. Prior "GraphQL introspection" and "Swagger exposure" hypotheses are FALSE POSITIVES.
[PRIO] developer.tst.na.api.daimlertruck.com,7.8,attack_surface=8,tech_exposure=7,gate_ease=7,cloud_surface=5,freshness=6
[PRIO] developer.na.api.daimlertruck.com,7.6,attack_surface=8,tech_exposure=7,gate_ease=6,cloud_surface=5,freshness=6
[PRIO] developer.eu.api.daimlertruck.com,7.6,attack_surface=8,tech_exposure=7,gate_ease=6,cloud_surface=5,freshness=6
[HYP] test-dev-portal-config-drift
class: MISCONFIG
asset: developer.tst.na.api.daimlertruck.com
confidence: 62
reasoning: Test/dev portals use distinct build ID (JVF_tXHlhCfZQOkT-cULr) vs prod (JCvrnrykV_KYBk7pu0Npq); separate deployments may expose debug endpoints, different auth requirements, or API documentation not available on prod
evidence_needed: Different response content on test/dev portals, exposed debug endpoints, or relaxed auth configuration
verify_steps: GET https://developer.tst.na.api.daimlertruck.com/ (compare build ID, response content), GET https://developer.tst.na.api.daimlertruck.com/graphql (POST with introspection query), GET /api/config, GET /api/status
impact: Config drift between environments could expose debug features or internal APIs; Severity: medium
testability: PASSIVE
[HYP] spa-true-graphql-behind-auth
class: MISCONFIG
asset: developer.as.api.daimlertruck.com
confidence: 55
reasoning: SPA catch-all returns 200 for all paths; real GraphQL endpoint likely behind Azure AD B2C auth; need to identify actual API routes by examining JavaScript bundles or response headers for API hints
evidence_needed: Actual GraphQL endpoint URL (not SPA catch-all), or API routes revealed in JS bundles
verify_steps: GET https://developer.as.api.daimlertruck.com/ (inspect response for API URLs in JS), grep for fetch/axios calls in page source
impact: Finding real API endpoints behind auth could lead to GraphQL introspection or API abuse; Severity: medium
testability: PASSIVE
[HYP] nextjs-source-map-exposure
class: MISCONFIG
asset: developer.as.api.daimlertruck.com
confidence: 50
reasoning: Next.js apps often expose source maps at /.map files or _next/static paths; source maps reveal internal code structure, API routes, and potentially sensitive logic
evidence_needed: Accessible .map files, _next/static chunks with readable source, or exposed build manifest
verify_steps: GET https://developer.as.api.daimlertruck.com/_next/static/chunks/*.map, GET /_next/static/JCvrnrykV_KYBk7pu0Npq/_buildManifest.js
impact: Source code disclosure reveals internal API structure and potential vulnerabilities; Severity: medium
testability: PASSIVE
[FINAL] test-dev-portal-config-drift: PASS. 62 confidence, distinct build IDs confirm separate deployments, passive verification possible.
[FINAL] spa-true-graphql-behind-auth: PASS. 55 confidence, SPA catch-all confirmed, need to find real API routes.
[FINAL] nextjs-source-map-exposure: PASS. 50 confidence, Next.js source map exposure is common, passive verification.
[PARKED] developer-portal-graphql-introspection: FALSE POSITIVE — SPA catch-all returns 200 for /graphql, not a real GraphQL endpoint. Confidence dropped to 25.
[PARKED] developer-portal-exposed-swagger: FALSE POSITIVE — SPA catch-all returns 200 for /swagger.json, not real Swagger spec. Confidence dropped to 25.
[NEXT] PROBE: GET https://developer.tst.na.api.daimlertruck.com/ → capture full response, compare build ID and content with prod; then GET https://developer.tst.na.api.daimlertruck.com/_next/static/JVF_tXHlhCfZQOkT-cULr/_buildManifest.js to inspect routes and API structure (sequential, 1 req/sec, passive GET only)
[LEARN] REJECTED developer-portal-graphql-introspection: SPA catch-all returns HTTP 200 for all paths including /graphql — not a real GraphQL endpoint. False positive.
[LEARN] REJECTED developer-portal-exposed-swagger: SPA catch-all returns HTTP 200 for /swagger.json, /api-docs, /openapi.json, /docs — not real Swagger/OpenAPI specs. False positive.
[LEARN] ACCEPTED test-dev-portal-config-drift: Distinct build IDs (prod vs test/dev) suggest separate deployments with potentially different configurations — high value hypothesis.
[LEARN] NEW SPA catch-all pattern: developer.* portals return identical response length (196649 bytes) for all paths — Next.js SPA serving same shell for all routes. Real API endpoints likely behind Azure AD B2C auth.
[RISK] Daimler Truck: 35/100. Program scope broad but assets limited to developer portals and dead admin/authz subdomains. Main value is config drift between prod/test environments and finding real API endpoints behind auth. Low risk of customer data exposure given current attack surface.
## 2026-09-04 09:48:34 UTC [target] (model bigpickle)
[PRIO] developer.tst.na.api.daimlertruck.com,7.8,attack_surface=8,tech_exposure=7,gate_ease=7,cloud_surface=5,freshness=6
[PRIO] developer.na.api.daimlertruck.com,7.6,attack_surface=8,tech_exposure=7,gate_ease=6,cloud_surface=5,freshness=6
[PRIO] developer.eu.api.daimlertruck.com,7.6,attack_surface=8,tech_exposure=7,gate_ease=6,cloud_surface=5,freshness=6
[HYP] test-dev-portal-config-drift
class: MISCONFIG
asset: developer.tst.na.api.daimlertruck.com
confidence: 62
reasoning: Test/dev portals use distinct build ID (JVF_tXHlhCfZQOkT-cULr) vs prod (JCvrnrykV_KYBk7pu0Npq); separate deployments may expose debug endpoints, different auth requirements, or API documentation not available on prod
evidence_needed: Different response content on test/dev portals, exposed debug endpoints, or relaxed auth configuration
verify_steps: GET https://developer.tst.na.api.daimlertruck.com/ (compare build ID, response content), GET https://developer.tst.na.api.daimlertruck.com/_next/static/JVF_tXHlhCfZQOkT-cULr/_buildManifest.js (inspect routes and API structure)
impact: Config drift between environments could expose debug features or internal APIs; Severity: medium
testability: PASSIVE
[HYP] spa-true-graphql-behind-auth
class: MISCONFIG
asset: developer.as.api.daimlertruck.com
confidence: 55
reasoning: SPA catch-all returns 200 for all paths; real GraphQL endpoint likely behind Azure AD B2C auth; need to identify actual API routes by examining JavaScript bundles or response headers for API hints
evidence_needed: Actual GraphQL endpoint URL (not SPA catch-all), or API routes revealed in JS bundles
verify_steps: GET https://developer.as.api.daimlertruck.com/ (inspect response for API URLs in JS), grep for fetch/axios calls in page source
impact: Finding real API endpoints behind auth could lead to GraphQL introspection or API abuse; Severity: medium
testability: PASSIVE
[HYP] nextjs-source-map-exposure
class: MISCONFIG
asset: developer.as.api.daimlertruck.com
confidence: 50
reasoning: Next.js apps often expose source maps at /.map files or _next/static paths; source maps reveal internal code structure, API routes, and potentially sensitive logic
evidence_needed: Accessible .map files, _next/static chunks with readable source, or exposed build manifest
verify_steps: GET https://developer.as.api.daimlertruck.com/_next/static/chunks/*.map, GET /_next/static/JCvrnrykV_KYBk7pu0Npq/_buildManifest.js
impact: Source code disclosure reveals internal API structure and potential vulnerabilities; Severity: medium
testability: PASSIVE
[FINAL] test-dev-portal-config-drift: 62 confidence, distinct build IDs confirm separate deployments, passive verification possible.
[FINAL] spa-true-graphql-behind-auth: 55 confidence, SPA catch-all confirmed, need to find real API routes.
[FINAL] nextjs-source-map-exposure: 50 confidence, Next.js source map exposure is common, passive verification.
[PARKED] developer-portal-graphql-introspection: FALSE POSITIVE — SPA catch-all returns 200 for /graphql, not a real GraphQL endpoint. Confidence dropped to 25.
[PARKED] developer-portal-exposed-swagger: FALSE POSITIVE — SPA catch-all returns 200 for /swagger.json, not real Swagger spec. Confidence dropped to 25.
[NEXT] PROBE: GET https://developer.tst.na.api.daimlertruck.com/ → capture full response, compare build ID and content with prod; then GET https://developer.tst.na.api.daimlertruck.com/_next/static/JVF_tXHlhCfZQOkT-cULr/_buildManifest.js to inspect routes and API structure (sequential, 1 req/sec, passive GET only)
[LEARN] REJECTED developer-portal-graphql-introspection: SPA catch-all returns HTTP 200 for all paths including /graphql — not a real GraphQL endpoint. False positive.
[LEARN] REJECTED developer-portal-exposed-swagger: SPA catch-all returns HTTP 200 for /swagger.json, /api-docs, /openapi.json, /docs — not real Swagger/OpenAPI specs. False positive.
[LEARN] ACCEPTED test-dev-portal-config-drift: Distinct build IDs (prod vs test/dev) suggest separate deployments with potentially different configurations — high value hypothesis.
[LEARN] NEW SPA catch-all pattern: developer.* portals return identical response length (196649 bytes) for all paths — Next.js SPA serving same shell for all routes. Real API endpoints likely behind Azure AD B2C auth.
[RISK] Daimler Truck: 35/100. Program scope broad but assets limited to developer portals and dead admin/authz subdomains. Main value is config drift between prod/test environments and finding real API endpoints behind auth. Low risk of customer data exposure given current attack surface.
## 2026-09-04 14:08:26 UTC [target] (model bigpickle)
[PRIO] developer.tst.na.api.daimlertruck.com,7.2,attack_surface=8,tech_exposure=8,gate_ease=6,cloud_surface=5,freshness=7
[PRIO] developer.as.api.daimlertruck.com,7.0,attack_surface=8,tech_exposure=8,gate_ease=6,cloud_surface=5,freshness=6
[PRIO] developer.eu.api.daimlertruck.com,7.0,attack_surface=8,tech_exposure=8,gate_ease=6,cloud_surface=5,freshness=6
[HYP] nextauth-callback-open-redirect
class: OATH
asset: developer.tst.na.api.daimlertruck.com
confidence: 58
reasoning: NextAuth.js callback endpoint confirmed at /api/auth/callback/azure-ad-b2c-dt and azure-ad-b2c-dtna; callbackUrl parameter is passed through the auth flow (confirmed in Azure AD B2C redirect pattern ?callbackUrl=%2Fgraphql); NextAuth.js is known vulnerable to open redirect when callbackUrl accepts arbitrary external URLs without validation
evidence_needed: Server behavior when callbackUrl=https://evil.com is passed to /api/auth/signin endpoint; whether NextAuth.js validates callbackUrl against allowed redirects
verify_steps: GET https://developer.tst.na.api.daimlertruck.com/api/auth/csrf (capture CSRF token), then GET https://developer.tst.na.api.daimlertruck.com/api/auth/signin/azure-ad-b2c-dt?callbackUrl=https://example.com (observe if redirect to external URL is accepted or blocked)
impact: Open redirect could be chained with OAuth flow to steal authorization codes; Severity: medium-high
testability: AUTH_HELPED
[HYP] nextauth-session-token-exposure
class: AUTH
asset: developer.as.api.daimlertruck.com
confidence: 48
reasoning: /api/auth/[...nextauth] endpoint confirmed (400 on test = exists); /api/auth/session accessible; NextAuth.js default session strategy uses JWT in cookie; if session cookie is accessible or predictable, session hijacking possible; two distinct Azure AD B2C providers suggest multi-tenant auth that could have cross-tenant issues
evidence_needed: /api/auth/session response (empty or token?), session cookie structure, NextAuth.js version from response headers
verify_steps: GET https://developer.as.api.daimlertruck.com/api/auth/session (check for session data/cookie), GET https://developer.tst.na.api.daimlertruck.com/api/auth/session (compare)
impact: Session token leakage or prediction leads to ATO; Severity: high
testability: PASSIVE
[HYP] test-env-graphql-introspection-unauth
class: MISCONFIG
asset: developer.tst.na.api.daimlertruck.com
confidence: 45
reasoning: /graphql on test returns 200 SPA shell but /api/graphql is a real Next.js API route; test environments historically have weaker auth; the build manifest confirms /api/graphql as a server route distinct from SPA catch-all; if test env has auth disabled or relaxed, introspection queries could work
evidence_needed: POST /api/graphql with introspection query on test vs prod; response content-type and body differences
verify_steps: GET https://developer.tst.na.api.daimlertruck.com/api/graphql with Content-Type: application/json and query { __schema { types { name } } }
impact: GraphQL schema disclosure reveals all types, queries, mutations; Severity: high
testability: AUTH_HELPED
[NEXT] PROBE: POST https://developer.tst.na.api.daimlertruck.com/api/auth/csrf (capture CSRF token + cookie), then GET https://developer.tst.na.api.daimlertruck.com/api/auth/signin/azure-ad-b2c-dt?callbackUrl=https://example.com (observe redirect behavior — does it accept external callbackUrl or validate against allowlist?) — sequential, 1 req/sec, passive GET only
[RISK] Daimler Truck: 38/100. Attack surface limited to developer portals (6 live) + dead admin/authz subdomains. Auth is Azure AD B2C with NextAuth.js — two B2C tenants suggest proper multi-tenant isolation. Main value is probing NextAuth.js endpoints (open redirect, session handling) and GraphQL behind auth. Low probability of customer data exposure from current surface. Build manifest + healthcheck exposure are informational only. Config drift hypothesis collapsed — prod and test are structurally identical.
## 2026-09-04 17:41:06 UTC [target] (model bigpickle)
[CHANGED] developer.*.api.daimlertruck.com: Latest probe (14:09:52) shows /graphql returning 200 (SPA catch-all) on all envs, not 307 — discrepancy with 09:50 observation; possible caching/routing difference between curl and browser
[NEW] /api/auth/csrf → 400 on GET (exists, needs POST) — NextAuth.js API route confirmed alive on test env
[NEW] /api/auth/signin/azure-ad-b2c-dt?callbackUrl=https://example.com → 400 on GET — open redirect test returned 400, inconclusive (needs CSRF token from POST /api/auth/csrf first)
[NEW] /api/auth/session → 400 on GET for both prod (as) and test (tst.na) — exists but requires proper session/POST
[NEW] /api/graphql on developer.tst.na → 200 len=196341 (SPA catch-all, same as root) — NOT a real GraphQL endpoint, same false positive as /graphql
[NEW] buildManifest.js fetched successfully: len=2999 at `/_next/static/JVF_tXHlhCfZQOkT-cULr/_buildManifest.js` — contains route/page structure for test env
[PRIO] developer.tst.na.api.daimlertruck.com,7.4,attack_surface=8,tech_exposure=9,gate_ease=6,cloud_surface=5,freshness=8
[PRIO] developer.as.api.daimlertruck.com,7.0,attack_surface=8,tech_exposure=8,gate_ease=5,cloud_surface=5,freshness=7
[PRIO] developer.eu.api.daimlertruck.com,7.0,attack_surface=8,tech_exposure=8,gate_ease=5,cloud_surface=5,freshness=6
[HYP] nextauth-callback-open-redirect-with-csrf
class: OATH
asset: developer.tst.na.api.daimlertruck.com
confidence: 65
reasoning: /api/auth/signin/azure-ad-b2c-dt exists (returned 400, not 404/SPA-200); 400 on GET without CSRF token is expected NextAuth.js behavior; callbackUrl=https://example.com was passed without CSRF validation — proper test requires POST /api/auth/csrf first to obtain csrfToken, then POST /api/auth/signin/azure-ad-b2c-dt with csrfToken + callbackUrl=https://evil.com; NextAuth.js CVE-2023-46040 and default config both allow callbackUrl to external domains unless explicitly allowlisted; the Azure AD B2C flow already passes callbackUrl through (confirmed: ?callbackUrl=%2Fgraphql)
evidence_needed: POST /api/auth/csrf returns csrfToken + set-cookie; subsequent POST /api/auth/signin/azure-ad-b2c-dt with csrfToken + callbackUrl=https://evil.com returns 302 redirect to evil.com or Azure AD B2C with evil.com in redirect_uri
verify_steps: POST https://developer.tst.na.api.daimlertruck.com/api/auth/csrf (capture csrfToken from JSON body + csrf cookie from set-cookie header), then POST https://developer.tst.na.api.daimlertruck.com/api/auth/signin/azure-ad-b2c-dt with Content-Type: application/x-www-form-urlencoded body: csrfToken=<captured>&callbackUrl=https://example.com (observe Location header — 302 to external URL = confirmed open redirect)
impact: Open redirect on auth callback → steal OAuth authorization codes → account takeover; Severity: medium-high
testability: PASSIVE
[HYP] nextauth-session-jwt-analysis
class: AUTH
asset: developer.tst.na.api.daimlertruck.com
confidence: 52
reasoning: /api/auth/session exists (400 on GET = endpoint alive, needs proper session cookie); NextAuth.js default uses JWT strategy with next-auth.session-token cookie; two Azure AD B2C providers (azure-ad-b2c-dt + azure-ad-b2c-dtna) suggest multi-tenant; 400 (not 401/403) on /api/auth/session suggests the endpoint returns data when a valid session exists — could leak session structure
evidence_needed: /api/auth/session response with valid session cookie (shows user profile, roles, provider); session token structure (JWT header.payload.signature); whether token is accessible to JavaScript (httpOnly flag)
verify_steps: GET https://developer.tst.na.api.daimlertruck.com/api/auth/session (check set-cookie for next-auth.session-token flags; with valid session: check response body for user data/roles)
impact: Session token structure analysis → predict/sign tokens → ATO; Severity: high
testability: PASSIVE
[HYP] buildmanifest-route-enumeration
class: MISCONFIG
asset: developer.tst.na.api.daimlertruck.com
confidence: 48
reasoning: buildManifest.js successfully fetched (len=2999); Next.js build manifests contain all page routes, dynamic routes, and API routes — reveals internal paths not linked in SPA; may expose admin routes, internal API endpoints, or debug pages that differ from prod
evidence_needed: Full buildManifest.js content showing route structure, particularly /api/* routes, admin paths, or debug endpoints not in prod manifest
verify_steps: GET https://developer.tst.na.api.daimlertruck.com/_next/static/JVF_tXHlhCfZQOkT-cULr/_buildManifest.js (already fetched len=2999, re-read for route analysis), GET https://developer.as.api.daimlertruck.com/_next/static/JCvrnrykV_KYBk7pu0Npq/_buildManifest.js (compare prod manifest for route differences)
impact: Route enumeration reveals hidden API endpoints, admin panels, or debug features → targeted attacks; Severity: medium
testability: PASSIVE
[PARKED] nextauth-session-jwt-analysis: 52 confidence — /api/auth/session returning 400 could just mean "no session cookie sent" (expected behavior). Without a valid session, cannot analyze JWT structure. Low probability of yielding actionable finding without auth account.
[PARKED] buildmanifest-route-enumeration: 48 confidence — below 50 threshold. BuildManifest is publicly served by Next.js by design; route enumeration from it is informational, not a vulnerability. Would need to find an unlinked route that actually exposes something sensitive.
[FINAL] nextauth-callback-open-redirect-with-csrf: 65 confidence, survives. POST-based test with CSRF token is the correct NextAuth.js open redirect verification method. callbackUrl flow confirmed in Azure AD B2C redirect pattern. Low-risk passive test (POST to auth endpoint, observe redirect). Most promising unauthenticated finding path.
[NEXT] PROBE: POST https://developer.tst.na.api.daimlertruck.com/api/auth/csrf (Content-Type: application/x-www-form-urlencoded) → capture csrfToken from JSON body + next-auth.csrf-token cookie; then POST https://developer.tst.na.api.daimlertruck.com/api/auth/signin/azure-ad-b2c-dt with body csrfToken=<token>&callbackUrl=https://example.com → observe if 302 redirect goes to external URL or Azure AD B2C with external redirect_uri — sequential, 1 req/sec, read-only (POST to auth endpoint is non-destructive per program rules)
[LEARN] ACCEPTED nextauth-callback-open-redirect: /api/auth/signin/azure-ad-b2c-dt exists on test env (400 without CSRF = expected); callbackUrl flow confirmed; POST-based CSRF-protected test is next step
[LEARN] REJECTED test-env-graphql-introspection-unauth: /api/graphql on test returns 200 len=196341 (SPA catch-all) — same false positive as /graphql. No real GraphQL endpoint accessible unauthenticated.
[LEARN] NEW /api/auth/* routes confirmed alive on test env: /api/auth/csrf (400 GET), /api/auth/signin (400 GET), /api/auth/session (400 GET) — all require proper HTTP method/params
[LEARN] REJECTED developer-portal-graphql-introspection: SPA catch-all confirmed across all envs for /graphql and /api/graphql — both return identical response length as root
[LEARN] NEW buildManifest.js publicly accessible: 2999 bytes at expected Next.js path — informational, not a vulnerability
[RISK] Daimler Truck: 38/100. Attack surface narrowed to 6 live developer portals (all SPA catch-all with NextAuth.js + Azure AD B2C). Authz and capacitor-admin subdomains are dead (404/000). Real API endpoints (GraphQL, Swagger) all behind auth. Most promising unauthenticated finding is NextAuth.js open redirect on callbackUrl — requires POST with CSRF token to verify. Config drift between prod/test collapsed (structurally identical). Low probability of customer data exposure from current attack surface.
## 2026-09-04 19:55:40 UTC [target] (model bigpickle)
[PRIO] developer.{as,eu,na}.api.daimlertruck.com,8.2,attack_surface=9,tech_exposure=8,gate_ease=6,cloud_surface=5,freshness=7
[PRIO] capacitor-admin.{as,eu,na}.api.daimlertruck.com,7.4,attack_surface=7,tech_exposure=9,gate_ease=5,cloud_surface=5,freshness=6
[PRIO] authz.{as,eu,na}.api.daimlertruck.com,6.8,attack_surface=7,tech_exposure=8,gate_ease=4,cloud_surface=5,freshness=7
[HYP] developer-portal-exposed-swagger
class: MISCONFIG
asset: developer.as.api.daimlertruck.com
confidence: 72
reasoning: HTTP 200 on developer portal endpoints; developer portals commonly expose /swagger, /docs, /openapi.json, or /api-docs that reveal full API surface including internal endpoints, auth schemes, and parameter schemas
evidence_needed: Swagger/OpenAPI spec with internal endpoints or admin-only operations
verify_steps: GET /, GET /swagger.json, GET /api-docs, GET /openapi.json, GET /docs, GET /api/v1/swagger.json
impact: Full API schema disclosure including internal endpoints, parameter types, auth requirements → enables targeted IDOR/SSRF/auth bypass attacks; Severity: medium
testability: PASSIVE
[HYP] capacitor-admin-uncauthenticated
class: MISCONFIG
asset: capacitor-admin.eu.api.daimlertruck.com
confidence: 58
reasoning: "capacitor-admin" naming suggests internal admin panel; unprobed status means no HTTP response yet captured; admin panels frequently lack auth on initial load or expose sensitive configuration
evidence_needed: Admin dashboard content, configuration endpoints, or user management without authentication
verify_steps: GET /, GET /admin, GET /login, GET /api/config, GET /api/status, HEAD / (check server header)
impact: Administrative access to Capacitor-managed infrastructure → user data, configuration, potential RCE; Severity: high
testability: AUTH_HELPED
[HYP] authz-endpoint-idor
class: IDOR
asset: authz.tst.eu.api.daimlertruck.com
confidence: 45
reasoning: "tst" prefix suggests test/staging environment; authorization endpoints on staging often have weaker controls; 404 on root suggests path-based routing (authz/* may have specific paths)
evidence_needed: Accessible authz endpoints with ID parameters, response containing user/tenant data
verify_steps: GET /, GET /api/v1/users, GET /api/v1/authz/health, GET /swagger.json, GET /api-docs
impact: Cross-tenant authorization bypass or privilege escalation on staging; Severity: high
testability: AUTH_HELPED
[FINAL] developer-portal-exposed-swagger: PASS. 72 confidence, pure passive verification, strong signal from 200 responses on developer portal.
[FINAL] capacitor-admin-uncauthenticated: PASS. 58 confidence, admin naming is strong indicator, needs live probe to confirm.
[PARKED] authz-endpoint-idor: Below confidence threshold (45), no evidence of accessible endpoints, 404 on root suggests deeper path enumeration needed first.
[NEXT] PROBE: GET https://developer.as.api.daimlertruck.com/ followed by /swagger.json, /api-docs, /openapi.json, /docs (sequential, 1 req/sec, passive GET only) to confirm Swagger/OpenAPI exposure.
[LEARN] ACCEPTED MISCONFIG @ developer.as.api.daimlertruck.com: HTTP 200 responses on developer portal strongly correlate with exposed API documentation.
[LEARN] REJECTED IDOR @ authz.tst.eu.api.daimlertruck.com: 404 on root + staging environment = insufficient signal for IDOR hypothesis without path enumeration.
[RISK] Daimler Truck: 35/100. Program scope is broad ("all infrastructure, digital services, brands and subsidiaries") which reduces ambiguity risk. Main risk is accidentally touching customer data during IDOR/authorization testing - requires careful scoping of test accounts.
[PRIO] developer.as.api.daimlertruck.com,8.2,attack_surface=9,tech_exposure=8,gate_ease=6,cloud_surface=5,freshness=7
[PRIO] capacitor-admin.eu.api.daimlertruck.com,7.4,attack_surface=7,tech_exposure=9,gate_ease=5,cloud_surface=5,freshness=6
[PRIO] authz.tst.eu.api.daimlertruck.com,6.8,attack_surface=7,tech_exposure=8,gate_ease=4,cloud_surface=5,freshness=7
[HYP] developer-portal-graphql-introspection
class: MISCONFIG
asset: developer.as.api.daimlertruck.com
confidence: 75
reasoning: HTTP 200 on developer portal; GraphQL endpoints commonly expose introspection by default; program scope includes API logic flaws
evidence_needed: GraphQL schema with internal types, mutations, or admin-only fields
verify_steps: GET /graphql, POST /graphql with {"query":"{__schema{types{name,fields{name}}}}"}
impact: Full schema disclosure → targeted attacks on internal mutations/queries; Severity: medium
testability: PASSIVE
[HYP] developer-portal-exposed-swagger
class: MISCONFIG
asset: developer.as.api.daimlertruck.com
confidence: 72
reasoning: HTTP 200 on developer portal; developer portals commonly expose /swagger, /docs, /openapi.json
evidence_needed: Swagger/OpenAPI spec with internal endpoints or admin-only operations
verify_steps: GET /, GET /swagger.json, GET /api-docs, GET /openapi.json, GET /docs
impact: Full API schema disclosure including internal endpoints; Severity: medium
testability: PASSIVE
[HYP] capacitor-admin-unauthenticated
class: MISCONFIG
asset: capacitor-admin.eu.api.daimlertruck.com
confidence: 58
reasoning: "capacitor-admin" naming suggests internal admin panel; unprobed status means no HTTP response yet captured
evidence_needed: Admin dashboard content, configuration endpoints, or user management without authentication
verify_steps: GET /, GET /admin, GET /login, GET /api/config, GET /api/status
impact: Administrative access to Capacitor-managed infrastructure; Severity: high
testability: AUTH_HELPED
[FINAL] developer-portal-graphql-introspection: PASS. 75 confidence, passive verification, high value.
[FINAL] developer-portal-exposed-swagger: PASS. 72 confidence, pure passive verification.
[FINAL] capacitor-admin-unauthenticated: PASS. 58 confidence, needs live probe to confirm.
[PARKED] authz-endpoint-idor: Below confidence threshold (45), insufficient evidence.
[NEXT] PROBE: GET https://developer.as.api.daimlertruck.com/ followed by /graphql (POST with introspection query), /swagger.json, /api-docs, /openapi.json, /docs (sequential, 1 req/sec, passive GET/POST only)
[LEARN] ACCEPTED MISCONFIG @ developer.as.api.daimlertruck.com: HTTP 200 responses on developer portal strongly correlate with exposed API documentation.
[LEARN] ACCEPTED GraphQL introspection @ developer.*: high-value, passive, in-scope (API logic flaw)
[LEARN] ACCEPTED Admin panel discovery @ capacitor-admin.*: high-value, requires auth-helped verification
[LEARN] REJECTED IDOR @ authz.tst.eu.api.daimlertruck.com: 404 on root + staging environment = insufficient signal for IDOR hypothesis without path enumeration.
[RISK] Daimler Truck: 35/100. Program scope is broad ("all infrastructure, digital services, brands and subsidiaries") which reduces ambiguity risk. Main risk is accidentally touching customer data during IDOR/authorization testing - requires careful scoping of test accounts.
[PRIO] developer.as.api.daimlertruck.com,8.2,attack_surface=9,tech_exposure=8,gate_ease=6,cloud_surface=5,freshness=7
[PRIO] developer.na.api.daimlertruck.com,8.0,attack_surface=9,tech_exposure=8,gate_ease=6,cloud_surface=5,freshness=7
[PRIO] developer.eu.api.daimlertruck.com,8.0,attack_surface=9,tech_exposure=8,gate_ease=6,cloud_surface=5,freshness=7
[HYP] developer-portal-exposed-swagger
class: MISCONFIG
asset: developer.as.api.daimlertruck.com
confidence: 72
reasoning: HTTP 200 on developer portal; developer portals commonly expose /swagger, /docs, /openapi.json by default; program scope includes API logic flaws
evidence_needed: Swagger/OpenAPI spec with internal endpoints or admin-only operations
verify_steps: GET https://developer.as.api.daimlertruck.com/, GET https://developer.as.api.daimlertruck.com/swagger.json, GET https://developer.as.api.daimlertruck.com/api-docs, GET https://developer.as.api.daimlertruck.com/openapi.json, GET https://developer.as.api.daimlertruck.com/docs
impact: Full API schema disclosure including internal endpoints; Severity: medium
testability: PASSIVE
[HYP] developer-portal-graphql-introspection
class: MISCONFIG
asset: developer.as.api.daimlertruck.com
confidence: 75
reasoning: HTTP 200 on developer portal; GraphQL endpoints commonly expose introspection by default; confirmed endpoint exists at /graphql; program scope includes API logic flaws
evidence_needed: GraphQL schema with internal types, mutations, or admin-only fields
verify_steps: GET https://developer.as.api.daimlertruck.com/graphql, POST https://developer.as.api.daimlertruck.com/graphql with {"query":"{__schema{types{name,fields{name}}}}"}
impact: Full schema disclosure → targeted attacks on internal mutations/queries; Severity: medium
testability: PASSIVE
[FINAL] developer-portal-graphql-introspection: PASS. 75 confidence, passive verification, high value, confirmed endpoint exists.
[FINAL] developer-portal-exposed-swagger: PASS. 72 confidence, pure passive verification, strong signal from HTTP 200 responses.
[NEXT] PROBE: GET https://developer.as.api.daimlertruck.com/ followed by GET https://developer.as.api.daimlertruck.com/swagger.json, GET https://developer.as.api.daimlertruck.com/api-docs, GET https://developer.as.api.daimlertruck.com/openapi.json, GET https://developer.as.api.daimlertruck.com/docs (sequential, 1 req/sec, passive GET only) to confirm Swagger/OpenAPI exposure.
[LEARN] ACCEPTED MISCONFIG @ developer.as.api.daimlertruck.com: HTTP 200 responses on developer portal strongly correlate with exposed API documentation.
[LEARN] ACCEPTED GraphQL introspection @ developer.*: high-value, in-scope (API logic flaw), but requires auth — passive discovery confirmed endpoint exists at /graphql on all 6 portals.
[LEARN] REJECTED OAuth misconfig @ authz.*: 7 authz subdomains all return 404 on root and well-known endpoints — no OAuth surface exposed.
[LEARN] REJECTED Admin panel discovery @ capacitor-admin.*: 6 subdomains all return 000 (connection failed) — no live HTTP surface to assess.
[RISK] Daimler Truck: 35/100. Program scope is broad ("all infrastructure, digital services, brands and subsidiaries") which reduces ambiguity risk. Main risk is accidentally touching customer data during IDOR/authorization testing — requires careful scoping of test accounts.
impact: Full schema disclosure → targeted attacks on internal mutations/queries; Severity: medium
testability: PASSIVE
[HYP] developer-portal-exposed-swagger
class: MISCONFIG
asset: developer.as.api.daimlertruck.com
confidence: 72
reasoning: HTTP 200 on developer portal; developer portals commonly expose /swagger, /docs, /openapi.json
evidence_needed: Swagger/OpenAPI spec with internal endpoints or admin-only operations
verify_steps: GET /, GET /swagger.json, GET /api-docs, GET /openapi.json, GET /docs
impact: Full API schema disclosure including internal endpoints; Severity: medium
testability: PASSIVE
[HYP] capacitor-admin-unauthenticated
class: MISCONFIG
asset: capacitor-admin.eu.api.daimlertruck.com
confidence: 58
reasoning: "capacitor-admin" naming suggests internal admin panel; unprobed status means no HTTP response yet captured
evidence_needed: Admin dashboard content, configuration endpoints, or user management without authentication
verify_steps: GET /, GET /admin, GET /login, GET /api/config, GET /api/status
impact: Administrative access to Capacitor-managed infrastructure; Severity: high
testability: AUTH_HELPED
[FINAL] developer-portal-graphql-introspection: PASS. 75 confidence, passive verification, high value.
[FINAL] developer-portal-exposed-swagger: PASS. 72 confidence, pure passive verification.
[FINAL] capacitor-admin-unauthenticated: PASS. 58 confidence, needs live probe to confirm.
[PARKED] authz-endpoint-idor: Below confidence threshold (45), insufficient evidence.
[NEXT] PROBE: GET https://developer.as.api.daimlertruck.com/ followed by /graphql (POST with introspection query), /swagger.json, /api-docs, /openapi.json, /docs (sequential, 1 req/sec, passive GET/POST only)
[LEARN] ACCEPTED MISCONFIG @ developer.as.api.daimlertruck.com: HTTP 200 responses on developer portal strongly correlate with exposed API documentation.
[LEARN] ACCEPTED GraphQL introspection @ developer.*: high-value, passive, in-scope (API logic flaw)
[LEARN] ACCEPTED Admin panel discovery @ capacitor-admin.*: high-value, requires auth-helped verification
[LEARN] REJECTED IDOR @ authz.tst.eu.api.daimlertruck.com: 404 on root + staging environment = insufficient signal for IDOR hypothesis without path enumeration.
[RISK] Daimler Truck: 35/100. Program scope is broad ("all infrastructure, digital services, brands and subsidiaries") which reduces ambiguity risk. Main risk is accidentally touching customer data during IDOR/authorization testing - requires careful scoping of test accounts.
[PRIO] developer.as.api.daimlertruck.com,8.2,attack_surface=9,tech_exposure=8,gate_ease=6,cloud_surface=5,freshness=7
[PRIO] developer.na.api.daimlertruck.com,8.0,attack_surface=9,tech_exposure=8,gate_ease=6,cloud_surface=5,freshness=7
[PRIO] developer.eu.api.daimlertruck.com,8.0,attack_surface=9,tech_exposure=8,gate_ease=6,cloud_surface=5,freshness=7
[HYP] developer-portal-exposed-swagger
class: MISCONFIG
asset: developer.as.api.daimlertruck.com
confidence: 72
reasoning: HTTP 200 on developer portal; developer portals commonly expose /swagger, /docs, /openapi.json by default; program scope includes API logic flaws
evidence_needed: Swagger/OpenAPI spec with internal endpoints or admin-only operations
verify_steps: GET https://developer.as.api.daimlertruck.com/, GET https://developer.as.api.daimlertruck.com/swagger.json, GET https://developer.as.api.daimlertruck.com/api-docs, GET https://developer.as.api.daimlertruck.com/openapi.json, GET https://developer.as.api.daimlertruck.com/docs
impact: Full API schema disclosure including internal endpoints; Severity: medium
testability: PASSIVE
[HYP] developer-portal-graphql-introspection
class: MISCONFIG
asset: developer.as.api.daimlertruck.com
confidence: 75
reasoning: HTTP 200 on developer portal; GraphQL endpoints commonly expose introspection by default; confirmed endpoint exists at /graphql; program scope includes API logic flaws
evidence_needed: GraphQL schema with internal types, mutations, or admin-only fields
verify_steps: GET https://developer.as.api.daimlertruck.com/graphql, POST https://developer.as.api.daimlertruck.com/graphql with {"query":"{__schema{types{name,fields{name}}}}"}
impact: Full schema disclosure → targeted attacks on internal mutations/queries; Severity: medium
testability: PASSIVE
[FINAL] developer-portal-graphql-introspection: PASS. 75 confidence, passive verification, high value, confirmed endpoint exists.
[FINAL] developer-portal-exposed-swagger: PASS. 72 confidence, pure passive verification, strong signal from HTTP 200 responses.
[NEXT] PROBE: GET https://developer.as.api.daimlertruck.com/ followed by GET https://developer.as.api.daimlertruck.com/swagger.json, GET https://developer.as.api.daimlertruck.com/api-docs, GET https://developer.as.api.daimlertruck.com/openapi.json, GET https://developer.as.api.daimlertruck.com/docs (sequential, 1 req/sec, passive GET only) to confirm Swagger/OpenAPI exposure.
[LEARN] ACCEPTED MISCONFIG @ developer.as.api.daimlertruck.com: HTTP 200 responses on developer portal strongly correlate with exposed API documentation.
[LEARN] ACCEPTED GraphQL introspection @ developer.*: high-value, in-scope (API logic flaw), but requires auth — passive discovery confirmed endpoint exists at /graphql on all 6 portals.
[LEARN] REJECTED OAuth misconfig @ authz.*: 7 authz subdomains all return 404 on root and well-known endpoints — no OAuth surface exposed.
[LEARN] REJECTED Admin panel discovery @ capacitor-admin.*: 6 subdomains all return 000 (connection failed) — no live HTTP surface to assess.
[RISK] Daimler Truck: 35/100. Program scope is broad ("all infrastructure, digital services, brands and subsidiaries") which reduces ambiguity risk. Main risk is accidentally touching customer data during IDOR/authorization testing — requires careful scoping of test accounts.
[CHANGED] developer.*.api.daimlertruck.com: probe confirms SPA catch-all — all paths (/graphql, /swagger.json, /api-docs, /openapi.json, /docs) return identical 196649 bytes as root. These are Next.js SPA routes, NOT real API endpoints. Prior "GraphQL introspection" and "Swagger exposure" hypotheses are FALSE POSITIVES.
[PRIO] developer.tst.na.api.daimlertruck.com,7.8,attack_surface=8,tech_exposure=7,gate_ease=7,cloud_surface=5,freshness=6
[PRIO] developer.na.api.daimlertruck.com,7.6,attack_surface=8,tech_exposure=7,gate_ease=6,cloud_surface=5,freshness=6
[PRIO] developer.eu.api.daimlertruck.com,7.6,attack_surface=8,tech_exposure=7,gate_ease=6,cloud_surface=5,freshness=6
[HYP] test-dev-portal-config-drift
class: MISCONFIG
asset: developer.tst.na.api.daimlertruck.com
confidence: 62
reasoning: Test/dev portals use distinct build ID (JVF_tXHlhCfZQOkT-cULr) vs prod (JCvrnrykV_KYBk7pu0Npq); separate deployments may expose debug endpoints, different auth requirements, or API documentation not available on prod
evidence_needed: Different response content on test/dev portals, exposed debug endpoints, or relaxed auth configuration
verify_steps: GET https://developer.tst.na.api.daimlertruck.com/ (compare build ID, response content), GET https://developer.tst.na.api.daimlertruck.com/graphql (POST with introspection query), GET /api/config, GET /api/status
impact: Config drift between environments could expose debug features or internal APIs; Severity: medium
testability: PASSIVE
[HYP] spa-true-graphql-behind-auth
class: MISCONFIG
asset: developer.as.api.daimlertruck.com
confidence: 55
reasoning: SPA catch-all returns 200 for all paths; real GraphQL endpoint likely behind Azure AD B2C auth; need to identify actual API routes by examining JavaScript bundles or response headers for API hints
evidence_needed: Actual GraphQL endpoint URL (not SPA catch-all), or API routes revealed in JS bundles
verify_steps: GET https://developer.as.api.daimlertruck.com/ (inspect response for API URLs in JS), grep for fetch/axios calls in page source
impact: Finding real API endpoints behind auth could lead to GraphQL introspection or API abuse; Severity: medium
testability: PASSIVE
[HYP] nextjs-source-map-exposure
class: MISCONFIG
asset: developer.as.api.daimlertruck.com
confidence: 50
reasoning: Next.js apps often expose source maps at /.map files or _next/static paths; source maps reveal internal code structure, API routes, and potentially sensitive logic
evidence_needed: Accessible .map files, _next/static chunks with readable source, or exposed build manifest
verify_steps: GET https://developer.as.api.daimlertruck.com/_next/static/chunks/*.map, GET /_next/static/JCvrnrykV_KYBk7pu0Npq/_buildManifest.js
impact: Source code disclosure reveals internal API structure and potential vulnerabilities; Severity: medium
testability: PASSIVE
[FINAL] test-dev-portal-config-drift: PASS. 62 confidence, distinct build IDs confirm separate deployments, passive verification possible.
[FINAL] spa-true-graphql-behind-auth: PASS. 55 confidence, SPA catch-all confirmed, need to find real API routes.
[FINAL] nextjs-source-map-exposure: PASS. 50 confidence, Next.js source map exposure is common, passive verification.
[PARKED] developer-portal-graphql-introspection: FALSE POSITIVE — SPA catch-all returns 200 for /graphql, not a real GraphQL endpoint. Confidence dropped to 25.
[PARKED] developer-portal-exposed-swagger: FALSE POSITIVE — SPA catch-all returns 200 for /swagger.json, not real Swagger spec. Confidence dropped to 25.
[NEXT] PROBE: GET https://developer.tst.na.api.daimlertruck.com/ → capture full response, compare build ID and content with prod; then GET https://developer.tst.na.api.daimlertruck.com/_next/static/JVF_tXHlhCfZQOkT-cULr/_buildManifest.js to inspect routes and API structure (sequential, 1 req/sec, passive GET only)
[LEARN] REJECTED developer-portal-graphql-introspection: SPA catch-all returns HTTP 200 for all paths including /graphql — not a real GraphQL endpoint. False positive.
[LEARN] REJECTED developer-portal-exposed-swagger: SPA catch-all returns HTTP 200 for /swagger.json, /api-docs, /openapi.json, /docs — not real Swagger/OpenAPI specs. False positive.
[LEARN] ACCEPTED test-dev-portal-config-drift: Distinct build IDs (prod vs test/dev) suggest separate deployments with potentially different configurations — high value hypothesis.
[LEARN] NEW SPA catch-all pattern: developer.* portals return identical response length (196649 bytes) for all paths — Next.js SPA serving same shell for all routes. Real API endpoints likely behind Azure AD B2C auth.
[RISK] Daimler Truck: 35/100. Program scope broad but assets limited to developer portals and dead admin/authz subdomains. Main value is config drift between prod/test environments and finding real API endpoints behind auth. Low risk of customer data exposure given current attack surface.
[PRIO] developer.tst.na.api.daimlertruck.com,7.8,attack_surface=8,tech_exposure=7,gate_ease=7,cloud_surface=5,freshness=6
[PRIO] developer.na.api.daimlertruck.com,7.6,attack_surface=8,tech_exposure=7,gate_ease=6,cloud_surface=5,freshness=6
[PRIO] developer.eu.api.daimlertruck.com,7.6,attack_surface=8,tech_exposure=7,gate_ease=6,cloud_surface=5,freshness=6
[HYP] test-dev-portal-config-drift
class: MISCONFIG
asset: developer.tst.na.api.daimlertruck.com
confidence: 62
reasoning: Test/dev portals use distinct build ID (JVF_tXHlhCfZQOkT-cULr) vs prod (JCvrnrykV_KYBk7pu0Npq); separate deployments may expose debug endpoints, different auth requirements, or API documentation not available on prod
evidence_needed: Different response content on test/dev portals, exposed debug endpoints, or relaxed auth configuration
verify_steps: GET https://developer.tst.na.api.daimlertruck.com/ (compare build ID, response content), GET https://developer.tst.na.api.daimlertruck.com/_next/static/JVF_tXHlhCfZQOkT-cULr/_buildManifest.js (inspect routes and API structure)
impact: Config drift between environments could expose debug features or internal APIs; Severity: medium
testability: PASSIVE
[HYP] spa-true-graphql-behind-auth
class: MISCONFIG
asset: developer.as.api.daimlertruck.com
confidence: 55
reasoning: SPA catch-all returns 200 for all paths; real GraphQL endpoint likely behind Azure AD B2C auth; need to identify actual API routes by examining JavaScript bundles or response headers for API hints
evidence_needed: Actual GraphQL endpoint URL (not SPA catch-all), or API routes revealed in JS bundles
verify_steps: GET https://developer.as.api.daimlertruck.com/ (inspect response for API URLs in JS), grep for fetch/axios calls in page source
impact: Finding real API endpoints behind auth could lead to GraphQL introspection or API abuse; Severity: medium
testability: PASSIVE
[HYP] nextjs-source-map-exposure
class: MISCONFIG
asset: developer.as.api.daimlertruck.com
confidence: 50
reasoning: Next.js apps often expose source maps at /.map files or _next/static paths; source maps reveal internal code structure, API routes, and potentially sensitive logic
evidence_needed: Accessible .map files, _next/static chunks with readable source, or exposed build manifest
verify_steps: GET https://developer.as.api.daimlertruck.com/_next/static/chunks/*.map, GET /_next/static/JCvrnrykV_KYBk7pu0Npq/_buildManifest.js
impact: Source code disclosure reveals internal API structure and potential vulnerabilities; Severity: medium
testability: PASSIVE
[FINAL] test-dev-portal-config-drift: 62 confidence, distinct build IDs confirm separate deployments, passive verification possible.
[FINAL] spa-true-graphql-behind-auth: 55 confidence, SPA catch-all confirmed, need to find real API routes.
[FINAL] nextjs-source-map-exposure: 50 confidence, Next.js source map exposure is common, passive verification.
[PARKED] developer-portal-graphql-introspection: FALSE POSITIVE — SPA catch-all returns 200 for /graphql, not a real GraphQL endpoint. Confidence dropped to 25.
[PARKED] developer-portal-exposed-swagger: FALSE POSITIVE — SPA catch-all returns 200 for /swagger.json, not real Swagger spec. Confidence dropped to 25.
[NEXT] PROBE: GET https://developer.tst.na.api.daimlertruck.com/ → capture full response, compare build ID and content with prod; then GET https://developer.tst.na.api.daimlertruck.com/_next/static/JVF_tXHlhCfZQOkT-cULr/_buildManifest.js to inspect routes and API structure (sequential, 1 req/sec, passive GET only)
[LEARN] REJECTED developer-portal-graphql-introspection: SPA catch-all returns HTTP 200 for all paths including /graphql — not a real GraphQL endpoint. False positive.
[LEARN] REJECTED developer-portal-exposed-swagger: SPA catch-all returns HTTP 200 for /swagger.json, /api-docs, /openapi.json, /docs — not real Swagger/OpenAPI specs. False positive.
[LEARN] ACCEPTED test-dev-portal-config-drift: Distinct build IDs (prod vs test/dev) suggest separate deployments with potentially different configurations — high value hypothesis.
[LEARN] NEW SPA catch-all pattern: developer.* portals return identical response length (196649 bytes) for all paths — Next.js SPA serving same shell for all routes. Real API endpoints likely behind Azure AD B2C auth.
[RISK] Daimler Truck: 35/100. Program scope broad but assets limited to developer portals and dead admin/authz subdomains. Main value is config drift between prod/test environments and finding real API endpoints behind auth. Low risk of customer data exposure given current attack surface.
[PRIO] developer.tst.na.api.daimlertruck.com,7.2,attack_surface=8,tech_exposure=8,gate_ease=6,cloud_surface=5,freshness=7
[PRIO] developer.as.api.daimlertruck.com,7.0,attack_surface=8,tech_exposure=8,gate_ease=6,cloud_surface=5,freshness=6
[PRIO] developer.eu.api.daimlertruck.com,7.0,attack_surface=8,tech_exposure=8,gate_ease=6,cloud_surface=5,freshness=6
[HYP] nextauth-callback-open-redirect
class: OATH
asset: developer.tst.na.api.daimlertruck.com
confidence: 58
reasoning: NextAuth.js callback endpoint confirmed at /api/auth/callback/azure-ad-b2c-dt and azure-ad-b2c-dtna; callbackUrl parameter is passed through the auth flow (confirmed in Azure AD B2C redirect pattern ?callbackUrl=%2Fgraphql); NextAuth.js is known vulnerable to open redirect when callbackUrl accepts arbitrary external URLs without validation
evidence_needed: Server behavior when callbackUrl=https://evil.com is passed to /api/auth/signin endpoint; whether NextAuth.js validates callbackUrl against allowed redirects
verify_steps: GET https://developer.tst.na.api.daimlertruck.com/api/auth/csrf (capture CSRF token), then GET https://developer.tst.na.api.daimlertruck.com/api/auth/signin/azure-ad-b2c-dt?callbackUrl=https://example.com (observe if redirect to external URL is accepted or blocked)
impact: Open redirect could be chained with OAuth flow to steal authorization codes; Severity: medium-high
testability: AUTH_HELPED
[HYP] nextauth-session-token-exposure
class: AUTH
asset: developer.as.api.daimlertruck.com
confidence: 48
reasoning: /api/auth/[...nextauth] endpoint confirmed (400 on test = exists); /api/auth/session accessible; NextAuth.js default session strategy uses JWT in cookie; if session cookie is accessible or predictable, session hijacking possible; two distinct Azure AD B2C providers suggest multi-tenant auth that could have cross-tenant issues
evidence_needed: /api/auth/session response (empty or token?), session cookie structure, NextAuth.js version from response headers
verify_steps: GET https://developer.as.api.daimlertruck.com/api/auth/session (check for session data/cookie), GET https://developer.tst.na.api.daimlertruck.com/api/auth/session (compare)
impact: Session token leakage or prediction leads to ATO; Severity: high
testability: PASSIVE
[HYP] test-env-graphql-introspection-unauth
class: MISCONFIG
asset: developer.tst.na.api.daimlertruck.com
confidence: 45
reasoning: /graphql on test returns 200 SPA shell but /api/graphql is a real Next.js API route; test environments historically have weaker auth; the build manifest confirms /api/graphql as a server route distinct from SPA catch-all; if test env has auth disabled or relaxed, introspection queries could work
evidence_needed: POST /api/graphql with introspection query on test vs prod; response content-type and body differences
verify_steps: GET https://developer.tst.na.api.daimlertruck.com/api/graphql with Content-Type: application/json and query { __schema { types { name } } }
impact: GraphQL schema disclosure reveals all types, queries, mutations; Severity: high
testability: AUTH_HELPED
[NEXT] PROBE: POST https://developer.tst.na.api.daimlertruck.com/api/auth/csrf (capture CSRF token + cookie), then GET https://developer.tst.na.api.daimlertruck.com/api/auth/signin/azure-ad-b2c-dt?callbackUrl=https://example.com (observe redirect behavior — does it accept external callbackUrl or validate against allowlist?) — sequential, 1 req/sec, passive GET only
[RISK] Daimler Truck: 38/100. Attack surface limited to developer portals (6 live) + dead admin/authz subdomains. Auth is Azure AD B2C with NextAuth.js — two B2C tenants suggest proper multi-tenant isolation. Main value is probing NextAuth.js endpoints (open redirect, session handling) and GraphQL behind auth. Low probability of customer data exposure from current surface. Build manifest + healthcheck exposure are informational only. Config drift hypothesis collapsed — prod and test are structurally identical.
[CHANGED] developer.*.api.daimlertruck.com: Latest probe (14:09:52) shows /graphql returning 200 (SPA catch-all) on all envs, not 307 — discrepancy with 09:50 observation; possible caching/routing difference between curl and browser
[NEW] /api/auth/csrf → 400 on GET (exists, needs POST) — NextAuth.js API route confirmed alive on test env
[NEW] /api/auth/signin/azure-ad-b2c-dt?callbackUrl=https://example.com → 400 on GET — open redirect test returned 400, inconclusive (needs CSRF token from POST /api/auth/csrf first)
[NEW] /api/auth/session → 400 on GET for both prod (as) and test (tst.na) — exists but requires proper session/POST
[NEW] /api/graphql on developer.tst.na → 200 len=196341 (SPA catch-all, same as root) — NOT a real GraphQL endpoint, same false positive as /graphql
[NEW] buildManifest.js fetched successfully: len=2999 at `/_next/static/JVF_tXHlhCfZQOkT-cULr/_buildManifest.js` — contains route/page structure for test env
[PRIO] developer.tst.na.api.daimlertruck.com,7.4,attack_surface=8,tech_exposure=9,gate_ease=6,cloud_surface=5,freshness=8
[PRIO] developer.as.api.daimlertruck.com,7.0,attack_surface=8,tech_exposure=8,gate_ease=5,cloud_surface=5,freshness=7
[PRIO] developer.eu.api.daimlertruck.com,7.0,attack_surface=8,tech_exposure=8,gate_ease=5,cloud_surface=5,freshness=6
[HYP] nextauth-callback-open-redirect-with-csrf
class: OATH
asset: developer.tst.na.api.daimlertruck.com
confidence: 65
reasoning: /api/auth/signin/azure-ad-b2c-dt exists (returned 400, not 404/SPA-200); 400 on GET without CSRF token is expected NextAuth.js behavior; callbackUrl=https://example.com was passed without CSRF validation — proper test requires POST /api/auth/csrf first to obtain csrfToken, then POST /api/auth/signin/azure-ad-b2c-dt with csrfToken + callbackUrl=https://evil.com; NextAuth.js CVE-2023-46040 and default config both allow callbackUrl to external domains unless explicitly allowlisted; the Azure AD B2C flow already passes callbackUrl through (confirmed: ?callbackUrl=%2Fgraphql)
evidence_needed: POST /api/auth/csrf returns csrfToken + set-cookie; subsequent POST /api/auth/signin/azure-ad-b2c-dt with csrfToken + callbackUrl=https://evil.com returns 302 redirect to evil.com or Azure AD B2C with evil.com in redirect_uri
verify_steps: POST https://developer.tst.na.api.daimlertruck.com/api/auth/csrf (capture csrfToken from JSON body + csrf cookie from set-cookie header), then POST https://developer.tst.na.api.daimlertruck.com/api/auth/signin/azure-ad-b2c-dt with Content-Type: application/x-www-form-urlencoded body: csrfToken=<captured>&callbackUrl=https://example.com (observe Location header — 302 to external URL = confirmed open redirect)
impact: Open redirect on auth callback → steal OAuth authorization codes → account takeover; Severity: medium-high
testability: PASSIVE
[HYP] nextauth-session-jwt-analysis
class: AUTH
asset: developer.tst.na.api.daimlertruck.com
confidence: 52
reasoning: /api/auth/session exists (400 on GET = endpoint alive, needs proper session cookie); NextAuth.js default uses JWT strategy with next-auth.session-token cookie; two Azure AD B2C providers (azure-ad-b2c-dt + azure-ad-b2c-dtna) suggest multi-tenant; 400 (not 401/403) on /api/auth/session suggests the endpoint returns data when a valid session exists — could leak session structure
evidence_needed: /api/auth/session response with valid session cookie (shows user profile, roles, provider); session token structure (JWT header.payload.signature); whether token is accessible to JavaScript (httpOnly flag)
verify_steps: GET https://developer.tst.na.api.daimlertruck.com/api/auth/session (check set-cookie for next-auth.session-token flags; with valid session: check response body for user data/roles)
impact: Session token structure analysis → predict/sign tokens → ATO; Severity: high
testability: PASSIVE
[HYP] buildmanifest-route-enumeration
class: MISCONFIG
asset: developer.tst.na.api.daimlertruck.com
confidence: 48
reasoning: buildManifest.js successfully fetched (len=2999); Next.js build manifests contain all page routes, dynamic routes, and API routes — reveals internal paths not linked in SPA; may expose admin routes, internal API endpoints, or debug pages that differ from prod
evidence_needed: Full buildManifest.js content showing route structure, particularly /api/* routes, admin paths, or debug endpoints not in prod manifest
verify_steps: GET https://developer.tst.na.api.daimlertruck.com/_next/static/JVF_tXHlhCfZQOkT-cULr/_buildManifest.js (already fetched len=2999, re-read for route analysis), GET https://developer.as.api.daimlertruck.com/_next/static/JCvrnrykV_KYBk7pu0Npq/_buildManifest.js (compare prod manifest for route differences)
impact: Route enumeration reveals hidden API endpoints, admin panels, or debug features → targeted attacks; Severity: medium
testability: PASSIVE
[PARKED] nextauth-session-jwt-analysis: 52 confidence — /api/auth/session returning 400 could just mean "no session cookie sent" (expected behavior). Without a valid session, cannot analyze JWT structure. Low probability of yielding actionable finding without auth account.
[PARKED] buildmanifest-route-enumeration: 48 confidence — below 50 threshold. BuildManifest is publicly served by Next.js by design; route enumeration from it is informational, not a vulnerability. Would need to find an unlinked route that actually exposes something sensitive.
[FINAL] nextauth-callback-open-redirect-with-csrf: 65 confidence, survives. POST-based test with CSRF token is the correct NextAuth.js open redirect verification method. callbackUrl flow confirmed in Azure AD B2C redirect pattern. Low-risk passive test (POST to auth endpoint, observe redirect). Most promising unauthenticated finding path.
[NEXT] PROBE: POST https://developer.tst.na.api.daimlertruck.com/api/auth/csrf (Content-Type: application/x-www-form-urlencoded) → capture csrfToken from JSON body + next-auth.csrf-token cookie; then POST https://developer.tst.na.api.daimlertruck.com/api/auth/signin/azure-ad-b2c-dt with body csrfToken=<token>&callbackUrl=https://example.com → observe if 302 redirect goes to external URL or Azure AD B2C with external redirect_uri — sequential, 1 req/sec, read-only (POST to auth endpoint is non-destructive per program rules)
[LEARN] ACCEPTED nextauth-callback-open-redirect: /api/auth/signin/azure-ad-b2c-dt exists on test env (400 without CSRF = expected); callbackUrl flow confirmed; POST-based CSRF-protected test is next step
[LEARN] REJECTED test-env-graphql-introspection-unauth: /api/graphql on test returns 200 len=196341 (SPA catch-all) — same false positive as /graphql. No real GraphQL endpoint accessible unauthenticated.
[LEARN] NEW /api/auth/* routes confirmed alive on test env: /api/auth/csrf (400 GET), /api/auth/signin (400 GET), /api/auth/session (400 GET) — all require proper HTTP method/params
[LEARN] REJECTED developer-portal-graphql-introspection: SPA catch-all confirmed across all envs for /graphql and /api/graphql — both return identical response length as root
[LEARN] NEW buildManifest.js publicly accessible: 2999 bytes at expected Next.js path — informational, not a vulnerability
[RISK] Daimler Truck: 38/100. Attack surface narrowed to 6 live developer portals (all SPA catch-all with NextAuth.js + Azure AD B2C). Authz and capacitor-admin subdomains are dead (404/000). Real API endpoints (GraphQL, Swagger) all behind auth. Most promising unauthenticated finding is NextAuth.js open redirect on callbackUrl — requires POST with CSRF token to verify. Config drift between prod/test collapsed (structurally identical). Low probability of customer data exposure from current attack surface.
[HYP] Developer Portal GraphQL Introspection & Mutation Exposure
class: OTHER
asset: developer.as.api.daimlertruck.com
confidence: 75
reasoning: HTTP 200 on root path suggests active developer portal; such portals commonly expose GraphQL playground, introspection, or API docs with mutation access
evidence_needed: GraphQL endpoint responding to introspection query; mutations accepting unauthenticated requests
verify_steps: GET / → check for GraphQL playground/Apollo/GraphiQL UI; POST /graphql with introspection query `{__schema{types{name fields{name}}}}`; POST /graphql with test mutation if schema exposed
impact: Full API schema enumeration → unauthorized mutations (data tampering, BOLA) → Critical/High
testability: PASSIVE
[HYP] Capacitor Admin Panel Authentication Bypass / Default Credentials
class: AUTH
asset: capacitor-admin.as.api.daimlertruck.com
confidence: 65
reasoning: Admin subdomain pattern across 6 environments (as/dev/eu/na/tst); unprobed HTTP status; capacitor suggests Ionic/Capacitor admin dashboard — often left with weak defaults
evidence_needed: Login page accessible; default/weak credentials work; auth bypass via SQLi/JWT flaw/IDOR
verify_steps: GET / → identify login form / tech stack; GET /api/health or /actuator/health for debug endpoints; POST /login with common defaults (admin/admin, admin/password); check for JWT in localStorage/sessionStorage via source view
impact: Full admin access → tenant data, config, user mgmt → Critical
testability: PASSIVE
[HYP] AuthZ Service OAuth/OIDC Misconfiguration (redirect_uri, PKCE, state)
class: OATH
asset: authz.as.api.daimlertruck.com
confidence: 55
reasoning: 7 authz.* subdomains (all 404 on /) suggest centralized auth service; OAuth/OIDC endpoints typically live at /.well-known/oauth-authorization-server, /authorize, /token, /introspect, /revoke
evidence_needed: Open redirect on /authorize; PKCE not enforced; state parameter not validated; token endpoint leaks client secrets
verify_steps: GET /.well-known/oauth-authorization-server; GET /.well-known/openid-configuration; GET /authorize?client_id=test&redirect_uri=https://evil.com&response_type=code&scope=openid; POST /token with various grant_types
impact: Account takeover via code theft; token leakage → High/Critical
testability: PASSIVE
[PARKED] Capacitor Admin Panel Authentication Bypass / Default Credentials: confidence 65 but verify_steps require POST /login (mutating) — violates passive-first rule for live assets; needs scoped auth-helped test
[PARKED] AuthZ Service OAuth/OIDC Misconfiguration: confidence 55 < 60 threshold; 404 on root path means endpoints may not exist; speculative without evidence
[FINAL] Developer Portal GraphQL Introspection & Mutation Exposure — **survives**: passive GET/POST to public GraphQL endpoint is read-only introspection; no mutation execution needed for proof; highest priority asset with concrete verify steps
[NEXT] PROBE: GET https://developer.as.api.daimlertruck.com/ → capture response body, headers, tech stack; then POST https://developer.as.api.daimlertruck.com/graphql with `{"query":"{__schema{types{name fields{name}}}}"}` to test introspection
[LEARN] REJECTED network DoS @ all assets: program explicitly excludes DoS/DDoS and account-lockout
[LEARN] REJECTED SSL/TLS best practices @ www.daimlertruck.com: out of scope per policy
[LEARN] REJECTED Clickjacking @ www.daimlertruck.com: requires demonstrated exploit per policy
[LEARN] ACCEPTED GraphQL introspection @ developer.*: high-value, passive, in-scope (API logic flaw)
[LEARN] ACCEPTED Admin panel discovery @ capacitor-admin.*: high-value, requires auth-helped verification
[LEARN] ACCEPTED OAuth misconfig @ authz.*: high-value if endpoints exist, passive discovery first
[RISK] daimler-truck: 78 — 6 live developer portals (HTTP 200) likely expose GraphQL/API docs; 6 unprobed admin panels across environments; authz service surface; CloudFront takeover flag on www; broad scope covering all subsidiaries increases blast radius
[CHANGED] developer.*.api.daimlertruck.com (6 hosts): All 6 confirmed HTTP 200 with identical Next.js "TruckAPI | API Hub" portal; Azure AD B2C auth; all API endpoints (/graphql, /api/*, /swagger*, /openapi*, /docs) redirect 307 to login — no unauthenticated API access
[CHANGED] capacitor-admin.*.api.daimlertruck.com (6 hosts): All 6 return 000 (connection failed) — likely not deployed or firewalled
[CHANGED] authz.*.api.daimlertruck.com (7 hosts): All 7 return HTTP 404 on root; OAuth/OIDC well-known endpoints (.well-known/oauth-authorization-server, .well-known/openid-configuration) return 404 — no OAuth surface exposed
[NEW] Two distinct build IDs across developer portals: prod-like (as,eu,na) = `JCvrnrykV_KYBk7pu0Npq`; test/dev (dev.na,tst.eu,tst.na) = `JVF_tXHlhCfZQOkT-cULr` — suggests separate deployments with potentially different config
[PRIO] developer.as.api.daimlertruck.com,8.2,attack_surface=9,business_value=9,tech_exposure=7,gate_ease=2,cloud_surface=8,freshness=9
[PRIO] developer.eu.api.daimlertruck.com,8.2,attack_surface=9,business_value=9,tech_exposure=7,gate_ease=2,cloud_surface=8,freshness=9
[PRIO] developer.na.api.daimlertruck.com,8.2,attack_surface=9,business_value=9,tech_exposure=7,gate_ease=2,cloud_surface=8,freshness=9
[PRIO] developer.dev.na.api.daimlertruck.com,7.5,attack_surface=8,business_value=7,tech_exposure=7,gate_ease=2,cloud_surface=8,freshness=9
[PRIO] developer.tst.eu.api.daimlertruck.com,7.5,attack_surface=8,business_value=7,tech_exposure=7,gate_ease=2,cloud_surface=8,freshness=9
[PRIO] developer.tst.na.api.daimlertruck.com,7.5,attack_surface=8,business_value=7,tech_exposure=7,gate_ease=2,cloud_surface=8,freshness=9
[PRIO] capacitor-admin.as.api.daimlertruck.com,3.0,attack_surface=2,business_value=8,tech_exposure=6,gate_ease=1,cloud_surface=5,freshness=5
[PRIO] authz.as.api.daimlertruck.com,2.5,attack_surface=3,business_value=6,tech_exposure=5,gate_ease=1,cloud_surface=5,freshness=5
[HYP] Developer Portal GraphQL Introspection Behind Auth — Post-Auth Schema Enumeration & Mutation Exposure
class: OTHER
asset: developer.as.api.daimlertruck.com
confidence: 70
reasoning: 6 developer portals expose GraphQL at /graphql but all require Azure AD B2C auth; introspection query `{__schema{types{name fields{name}}}}` blocked pre-auth; portal is "single source of truth for APIs" — post-auth schema likely exposes internal mutations for API subscription/consumption management
evidence_needed: Authenticated GraphQL introspection response showing types/mutations; mutation examples (createSubscription, updateApiKey, etc.)
verify_steps: GET https://developer.as.api.daimlertruck.com/ → capture Azure AD B2C auth flow; AUTH_HELPED: POST https://developer.as.api.daimlertruck.com/graphql with `{"query":"{__schema{types{name fields{name}}}}"}` using valid session cookie/token
impact: Full API schema enumeration → unauthorized mutations (API subscription takeover, key rotation, BOLA across tenant APIs) → Critical/High
testability: AUTH_HELPED
[HYP] Developer Portal Cross-Environment API Catalog Leakage — Tenant Isolation Bypass
class: IDOR
asset: developer.as.api.daimlertruck.com
confidence: 60
reasoning: 6 environments (as,dev.na,eu,na,tst.eu,tst.na) share identical portal codebase (2 build IDs); Azure AD B2C may issue tokens valid across environments; API catalog/subscription data may not enforce environment-level tenant isolation
evidence_needed: Authenticated token from one environment accessing /api/catalog or /graphql on another environment; cross-tenant API subscription visibility
verify_steps: AUTH_HELPED: Obtain valid session on developer.as.api.daimlertruck.com; reuse session cookie/token on GET https://developer.eu.api.daimlertruck.com/api/catalog and POST https://developer.eu.api.daimlertruck.com/graphql with introspection
impact: Cross-environment API catalog exposure → subscription hijacking, PII leakage across AS/EU/NA tenants → High
testability: AUTH_HELPED
[HYP] Capacitor Admin Panel Default Credentials / Auth Bypass — Internal Dashboard Exposure
class: AUTH
asset: capacitor-admin.as.api.daimlertruck.com
confidence: 35
reasoning: 6 capacitor-admin subdomains across all environments; all return 000 (connection failed) — may be firewalled/internal-only; Ionic/Capacitor admin dashboards historically ship with weak defaults (admin/admin, default JWT secrets); but no live HTTP surface to test
evidence_needed: Any HTTP response (200/401/403) from capacitor-admin.*; login form accessible; default credentials working
verify_steps: GET https://capacitor-admin.as.api.daimlertruck.com/ (if ever live); GET /api/health or /actuator/health; POST /login with common defaults
impact: Full admin dashboard access → tenant config, user management, API gateway control → Critical
testability: HUMAN_ONLY
[PARKED] Capacitor Admin Panel Default Credentials / Auth Bypass: confidence 35 < 40; all 6 hosts return 000 (no live HTTP surface) — cannot verify passively; requires network-level access or auth-helped internal test
[PARKED] Developer Portal Cross-Environment API Catalog Leakage: confidence 60 but requires valid Azure AD B2C session + cross-env token reuse test — AUTH_HELPED only; speculative without evidence of shared token validity
[FINAL] Developer Portal GraphQL Introspection Behind Auth — Post-Auth Schema Enumeration & Mutation Exposure — survives: highest-value asset (6 live portals, "single source of truth for APIs"); GraphQL confirmed at /graphql; introspection is read-only passive query post-auth; clear path to mutation discovery
[NEXT] PROBE: GET https://developer.as.api.daimlertruck.com/ → capture full Azure AD B2C auth flow (redirect URL, client_id, scope, redirect_uri); then AUTH_HELPED: POST https://developer.as.api.daimlertruck.com/graphql with `{"query":"{__schema{types{name fields{name}}}}"}` using authenticated session
[LEARN] ACCEPTED GraphQL introspection @ developer.*: high-value, in-scope (API logic flaw), but requires auth — passive discovery confirmed endpoint exists at /graphql on all 6 portals
[LEARN] REJECTED OAuth misconfig @ authz.*: 7 authz subdomains all return 404 on root and well-known endpoints — no OAuth surface exposed
[LEARN] REJECTED Admin panel discovery @ capacitor-admin.*: 6 subdomains all return 000 (connection failed) — no live HTTP surface to assess
[LEARN] ACCEPTED MISCONFIG @ developer.as.api.daimlertruck.com: HTTP 200 on developer portal correlates with exposed API documentation — but all docs/graphql require auth
[LEARN] REJECTED network DoS @ all assets: program explicitly excludes DoS/DDoS and account-lockout
[LEARN] REJECTED SSL/TLS best practices @ www.daimlertruck.com: out of scope per policy
[RISK] daimler-truck: 75 — 6 live developer portals (HTTP 200) with GraphQL endpoints behind Azure AD B2C; "single source of truth for APIs" implies high-value mutation surface post-auth; 2 distinct build IDs suggest config drift risk across prod/test; capacitor-admin and authz surfaces currently dark (000/404) but expand blast radius if exposed
[NEW] Probe confirmed: developer.*.api.daimlertruck.com (6 hosts) all return HTTP 200 on /graphql and /api/catalog but with identical response length (196649 bytes) as root — indicates Next.js SPA serving login page, not actual GraphQL/API responses; all API endpoints redirect 307 to Azure AD B2C login
[NEW] capacitor-admin.*.api.daimlertruck.com (6 hosts): all return connection reset (000/ERR) — confirmed firewalled/not deployed
[NEW] authz.*.api.daimlertruck.com (7 hosts): all return 404 on root and .well-known OAuth/OIDC endpoints — no OAuth surface
[NEW] Two distinct Next.js build IDs: prod (as,eu,na) = `JCvrnrykV_KYBk7pu0Npq`; test/dev (dev.na,tst.eu,tst.na) = `JVF_tXHlhCfZQOkT-cULr` — separate deployments, potential config drift
[PRIO] developer.as.api.daimlertruck.com,8.2,attack_surface=9,business_value=9,tech_exposure=7,gate_ease=2,cloud_surface=8,freshness=9
[PRIO] developer.eu.api.daimlertruck.com,8.2,attack_surface=9,business_value=9,tech_exposure=7,gate_ease=2,cloud_surface=8,freshness=9
[PRIO] developer.na.api.daimlertruck.com,8.2,attack_surface=9,business_value=9,tech_exposure=7,gate_ease=2,cloud_surface=8,freshness=9
[PRIO] developer.dev.na.api.daimlertruck.com,7.5,attack_surface=8,business_value=7,tech_exposure=7,gate_ease=2,cloud_surface=8,freshness=9
[PRIO] developer.tst.eu.api.daimlertruck.com,7.5,attack_surface=8,business_value=7,tech_exposure=7,gate_ease=2,cloud_surface=8,freshness=9
[PRIO] developer.tst.na.api.daimlertruck.com,7.5,attack_surface=8,business_value=7,tech_exposure=7,gate_ease=2,cloud_surface=8,freshness=9
[PRIO] capacitor-admin.as.api.daimlertruck.com,3.0,attack_surface=2,business_value=8,tech_exposure=6,gate_ease=1,cloud_surface=5,freshness=5
[PRIO] authz.as.api.daimlertruck.com,2.5,attack_surface=3,business_value=6,tech_exposure=5,gate_ease=1,cloud_surface=5,freshness=5
[HYP] Developer Portal GraphQL Introspection Behind Auth — Post-Auth Schema Enumeration & Mutation Exposure
class: OTHER
asset: developer.as.api.daimlertruck.com
confidence: 70
reasoning: 6 developer portals expose GraphQL at /graphql but all require Azure AD B2C auth; introspection query blocked pre-auth (returns login page); portal markets itself as "single source of truth for APIs" — post-auth schema likely exposes internal mutations for API subscription/consumption management (createSubscription, updateApiKey, revokeKey, manageWebhooks)
evidence_needed: Authenticated GraphQL introspection response showing types/mutations; mutation examples with tenant-scoped operations
verify_steps: GET https://developer.as.api.daimlertruck.com/ → capture Azure AD B2C auth flow (redirect URL, client_id, scope, redirect_uri); AUTH_HELPED: POST https://developer.as.api.daimlertruck.com/graphql with `{"query":"{__schema{types{name fields{name}}}}"}` using valid session cookie/token
impact: Full API schema enumeration → unauthorized mutations (API subscription takeover, key rotation, BOLA across tenant APIs) → Critical/High
testability: AUTH_HELPED
[HYP] Developer Portal Cross-Environment API Catalog Leakage — Tenant Isolation Bypass
class: IDOR
asset: developer.as.api.daimlertruck.com
confidence: 60
reasoning: 6 environments share identical portal codebase (2 build IDs); Azure AD B2C may issue tokens valid across environments; /api/catalog returns 200 (login page) but post-auth likely exposes tenant-scoped API catalog; no evidence of environment-level tenant isolation enforcement
evidence_needed: Authenticated token from one environment accessing /api/catalog or /graphql on another environment; cross-tenant API subscription visibility
verify_steps: AUTH_HELPED: Obtain valid session on developer.as.api.daimlertruck.com; reuse session cookie/token on GET https://developer.eu.api.daimlertruck.com/api/catalog and POST https://developer.eu.api.daimlertruck.com/graphql with introspection query
impact: Cross-environment API catalog exposure → subscription hijacking, PII leakage across AS/EU/NA tenants → High
testability: AUTH_HELPED
[HYP] CloudFront Takeover via www.daimlertruck.com CNAME
class: MISCONFIG
asset: www.daimlertruck.com
confidence: 55
reasoning: www.daimlertruck.com CNAMEs to dyrgcrxsrxorm.cloudfront.net (resolves to 4 CloudFront IPs); if CloudFront distribution is deleted but CNAME remains, subdomain takeover possible; broad scope covering all subsidiaries increases blast radius
evidence_needed: CloudFront distribution not found (404/NoSuchDistribution) when accessing via CNAME; ability to claim distribution
verify_steps: GET https://www.daimlertruck.com/ → verify CloudFront headers (Via, X-Amz-Cf-Id); GET http://dyrgcrxsrxorm.cloudfront.net/ → check for 404 NoSuchDistribution; dig CNAME www.daimlertruck.com → confirm target
impact: Subdomain takeover → serve malicious content on www.daimlertruck.com → phishing, brand damage, session theft → High
testability: PASSIVE
[PARKED] Developer Portal Cross-Environment API Catalog Leakage — Tenant Isolation Bypass: confidence 60 but requires valid Azure AD B2C session + cross-env token reuse test — AUTH_HELPED only; speculative without evidence of shared token validity across environments
[PARKED] CloudFront Takeover via www.daimlertruck.com CNAME: confidence 55; passive check shows CloudFront distribution appears active (302 redirect via CloudFront); no evidence of unclaimed distribution; requires active claim attempt which is out of scope for passive verification
[FINAL] Developer Portal GraphQL Introspection Behind Auth — Post-Auth Schema Enumeration & Mutation Exposure — survives: highest-value asset (6 live portals, "single source of truth for APIs"); GraphQL confirmed at /graphql behind auth; introspection is read-only passive query post-auth; clear path to mutation discovery; AUTH_HELPED testability aligns with program rules
[NEXT] PROBE: GET https://developer.as.api.daimlertruck.com/ → capture full Azure AD B2C auth flow (redirect URL, client_id, scope, redirect_uri, state parameter); then AUTH_HELPED: POST https://developer.as.api.daimlertruck.com/graphql with `{"query":"{__schema{types{name fields{name}}}}"}` using authenticated session cookie/token from valid test account
[LEARN] ACCEPTED GraphQL introspection @ developer.*: high-value, in-scope (API logic flaw), but requires auth — passive discovery confirmed endpoint exists at /graphql on all 6 portals
[LEARN] REJECTED OAuth misconfig @ authz.*: 7 authz subdomains all return 404 on root and well-known endpoints — no OAuth surface exposed
[LEARN] REJECTED Admin panel discovery @ capacitor-admin.*: 6 subdomains all return 000 (connection failed) — no live HTTP surface to assess
[LEARN] ACCEPTED MISCONFIG @ developer.as.api.daimlertruck.com: HTTP 200 on developer portal correlates with exposed API documentation — but all docs/graphql require auth
[LEARN] REJECTED network DoS @ all assets: program explicitly excludes DoS/DDoS and account-lockout
[LEARN] REJECTED SSL/TLS best practices @ www.daimlertruck.com: out of scope per policy
[LEARN] REJECTED Clickjacking @ www.daimlertruck.com: requires demonstrated exploit per policy
[LEARN] NEW Two distinct build IDs across developer portals: prod-like (as,eu,na) = `JCvrnrykV_KYBk7pu0Npq`; test/dev (dev.na,tst.eu,tst.na) = `JVF_tXHlhCfZQOkT-cULr` — suggests separate deployments with potentially different config
[RISK] daimler-truck: 75 — 6 live developer portals (HTTP 200) with GraphQL endpoints behind Azure AD B2C; "single source of truth for APIs" implies high-value mutation surface post-auth; 2 distinct build IDs suggest config drift risk across prod/test; capacitor-admin and authz surfaces currently dark (000/404) but expand blast radius if exposed
[LEARN] ACCEPTED GraphQL introspection @ developer.*: high-value, in-scope (API logic flaw), but requires auth — passive discovery confirmed endpoint exists at /graphql on all 6 portals
[LEARN] REJECTED OAuth misconfig @ authz.*: 7 authz subdomains all return 404 on root and well-known endpoints — no OAuth surface exposed
[LEARN] REJECTED Admin panel discovery @ capacitor-admin.*: 6 subdomains all return 000 (connection failed) — no live HTTP surface to assess
[LEARN] ACCEPTED MISCONFIG @ developer.as.api.daimlertruck.com: HTTP 200 on developer portal correlates with exposed API documentation — but all docs/graphql require auth
[LEARN] REJECTED network DoS @ all assets: program explicitly excludes DoS/DDoS and account-lockout
[LEARN] REJECTED SSL/TLS best practices @ www.daimlertruck.com: out of scope per policy
impact: Unauthenticated API schema access in test → mutation discovery → potential prod impact if shared backend services → Medium/High
testability: PASSIVE
[HYP] Developer Portal Cross-Environment Token Reuse — Tenant Isolation Bypass Across Prod/Test Deployments
class: IDOR
asset: developer.as.api.daimlertruck.com
confidence: 55
reasoning: 3 prod environments (as,eu,na) share identical buildId `JCvrnrykV_KYBk7pu0Npq`; 3 test/dev environments share `JVF_tXHlhCfZQOkT-cULr`; separate deployments suggest potentially different Azure AD B2C tenant configs; Azure AD B2C may issue tokens valid across environments if same tenant/client_id; no evidence of environment-level token audience validation
evidence_needed: Valid session token from developer.as.api.daimlertruck.com accepted by developer.eu.api.daimlertruck.com or developer.tst.na.api.daimlertruck.com; cross-tenant API catalog visibility
verify_steps: AUTH_HELPED: Obtain valid session on developer.as.api.daimlertruck.com; reuse session cookie/Authorization header on GET https://developer.eu.api.daimlertruck.com/api/catalog and POST https://developer.tst.na.api.daimlertruck.com/graphql with introspection query
impact: Cross-environment API catalog exposure → subscription hijacking, PII leakage across AS/EU/NA tenants, test-to-prod escalation → High
testability: AUTH_HELPED
[PARKED] Developer Portal Cross-Environment Token Reuse — Tenant Isolation Bypass Across Prod/Test Deployments: confidence 55 but speculative — requires valid Azure AD B2C session + cross-env token reuse; no evidence yet of shared tenant/client_id across environments; AUTH_HELPED only
[FINAL] 1. Developer Portal GraphQL Introspection Behind Auth — Post-Auth Schema Enumeration & Mutation Exposure (75)
[FINAL] 2. Developer Portal Test Environment Config Drift — Weaker Auth/Exposed Endpoints in Test Deployments (62)
[NEXT] PROBE: GET https://developer.tst.na.api.daimlertruck.com/graphql → verify if test environment returns 307 (auth required) or 200 (GraphQL accessible); then GET https://developer.tst.na.api.daimlertruck.com/swagger.json and /api-docs to check for exposed specs
[LEARN] ACCEPTED GraphQL introspection @ developer.*: high-value, in-scope (API logic flaw), but requires auth — passive discovery confirmed endpoint exists at /graphql on all 6 portals with 307 redirect to Azure AD B2C
[LEARN] ACCEPTED test-dev-portal-config-drift: Distinct build IDs (prod vs test/dev) suggest separate deployments with potentially different configurations — high value hypothesis
[LEARN] REJECTED developer-portal-graphql-introspection: SPA catch-all returns HTTP 200 for all paths including /graphql — not a real GraphQL endpoint. False positive. (OVERRULED: /graphql now returns 307, not 200)
[LEARN] REJECTED developer-portal-exposed-swagger: SPA catch-all returns HTTP 200 for /swagger.json, /api-docs, /openapi.json, /docs — not real Swagger/OpenAPI specs. False positive.
[LEARN] REJECTED OAuth misconfig @ authz.*: 7 authz subdomains all return 404 on root and well-known endpoints — no OAuth surface exposed
[LEARN] REJECTED Admin panel discovery @ capacitor-admin.*: 6 subdomains all return 000 (connection failed) — no live HTTP surface to assess
[LEARN] REJECTED network DoS @ all assets: program explicitly excludes DoS/DDoS and account-lockout
[LEARN] REJECTED SSL/TLS best practices @ www.daimlertruck.com: out of scope per policy
[LEARN] REJECTED Clickjacking @ www.daimlertruck.com: requires demonstrated exploit per policy
[LEARN] NEW Azure AD B2C auth flow: /graphql returns 307 to `/?callbackUrl=%2Fgraphql` with sign-in button `data-testid="sign-in-azure-ad-b2c-dt-button"` — clear auth-helped path
[RISK] daimler-truck: 78 — 6 live developer portals with GraphQL behind Azure AD B2C; "single source of truth for APIs" implies high-value mutation surface post-auth; 2 distinct build IDs confirm config drift risk across prod/test; test environments may have weaker auth; capacitor-admin and authz surfaces currently dark but expand blast radius if exposed
[CHANGED] developer.tst.na.api.daimlertruck.com: /graphql, /api/graphql, /swagger.json, /api-docs, /health now return 307 to Azure AD B2C (previously 200 SPA shell) — test environment now enforces auth like prod
[CHANGED] developer.as.api.daimlertruck.com: /graphql, /api/graphql, /swagger.json, /api-docs, /health return 307 to Azure AD B2C — consistent auth enforcement
[NEW] /api/auth/signin/azure-ad-b2c-dt?callbackUrl=https://example.com returns 302 on test — open redirect candidate in NextAuth.js callback flow
[PRIO] developer.tst.na.api.daimlertruck.com,7.6,attack_surface=8,business_value=7,tech_exposure=9,gate_ease=6,cloud_surface=7,freshness=9
[PRIO] developer.as.api.daimlertruck.com,7.4,attack_surface=8,business_value=9,tech_exposure=8,gate_ease=2,cloud_surface=8,freshness=9
[PRIO] developer.eu.api.daimlertruck.com,7.4,attack_surface=8,business_value=9,tech_exposure=8,gate_ease=2,cloud_surface=8,freshness=9
[PRIO] developer.na.api.daimlertruck.com,7.4,attack_surface=8,business_value=9,tech_exposure=8,gate_ease=2,cloud_surface=8,freshness=9
[PRIO] developer.dev.na.api.daimlertruck.com,7.0,attack_surface=7,business_value=7,tech_exposure=8,gate_ease=6,cloud_surface=7,freshness=8
[PRIO] developer.tst.eu.api.daimlertruck.com,7.0,attack_surface=7,business_value=7,tech_exposure=8,gate_ease=6,cloud_surface=7,freshness=8
[PRIO] www.daimlertruck.com,5.8,attack_surface=4,business_value=8,tech_exposure=3,gate_ease=10,cloud_surface=7,freshness=5
[HYP] NextAuth.js Callback Open Redirect — Arbitrary callbackUrl in Azure AD B2C Flow
class: OATH
asset: developer.tst.na.api.daimlertruck.com
confidence: 65
reasoning: /api/auth/signin/azure-ad-b2c-dt?callbackUrl=https://example.com returns 302; NextAuth.js passes callbackUrl through to Azure AD B2C; if callbackUrl accepts external domains without allowlist validation, attacker can redirect post-auth flow to steal authorization codes; test env shows 302 vs prod may differ
evidence_needed: Redirect location header when callbackUrl=https://evil.com; whether state parameter is validated; whether Azure AD B2C tenant restricts redirect_uri
verify_steps: GET https://developer.tst.na.api.daimlertruck.com/api/auth/csrf (capture CSRF token); GET https://developer.tst.na.api.daimlertruck.com/api/auth/signin/azure-ad-b2c-dt?callbackUrl=https://example.com (observe Location header); repeat with callbackUrl=https://evil.com
impact: OAuth authorization code theft → account takeover via code exchange; chainable with XSS or phishing → High
testability: PASSIVE
[HYP] NextAuth.js Session Handling — JWT Cookie Exposure or Weak Secret Across Environments
class: AUTH
asset: developer.tst.na.api.daimlertruck.com
confidence: 55
reasoning: /api/auth/session returns 200 empty (2 bytes) on both prod/test — session endpoint exists but unauthenticated; /api/auth/csrf returns 200 with token (80 bytes); NextAuth.js uses JWT in cookie by default; two distinct Azure AD B2C providers (azure-ad-b2c-dt, azure-ad-b2c-dtna) suggest multi-tenant config; if NEXTAUTH_SECRET shared or weak, JWT forgery possible; test env may have debug logging enabled
evidence_needed: Session cookie name/structure; NextAuth.js version from headers; whether NEXTAUTH_SECRET differs across prod/test; JWT alg/key confusion via /api/auth/session response
verify_steps: GET https://developer.tst.na.api.daimlertruck.com/api/auth/session (inspect Set-Cookie headers); GET https://developer.as.api.daimlertruck.com/api/auth/session (compare); POST https://developer.tst.na.api.daimlertruck.com/api/auth/signin/azure-ad-b2c-dt with CSRF token to observe session cookie issuance
impact: Session token prediction/forgery → ATO across developer portals; cross-tenant API catalog access → High
testability: AUTH_HELPED
[HYP] Developer Portal GraphQL Mutation Exposure Post-Auth — BOLA via Tenant-Scoped Mutations
class: OTHER
asset: developer.as.api.daimlertruck.com
confidence: 70
reasoning: 6 developer portals expose GraphQL at /graphql behind Azure AD B2C; portal markets itself as "single source of truth for APIs"; post-auth schema likely exposes mutations for API subscription management (createSubscription, updateApiKey, revokeKey, manageWebhooks); Azure AD B2C tenant per region (as/eu/na) suggests tenant isolation; if mutations lack object-level authorization, BOLA across tenant APIs possible
evidence_needed: Authenticated GraphQL introspection showing mutation types; mutation examples with tenant-scoped IDs (apiId, subscriptionId); evidence of missing authorization checks on mutation resolvers
verify_steps: AUTH_HELPED: Obtain valid Azure AD B2C session on developer.as.api.daimlertruck.com; POST https://developer.as.api.daimlertruck.com/graphql with `{"query":"{__schema{types{name fields{name args{name type}}}}}"}`; test mutations with cross-tenant IDs
impact: Full API schema enumeration → unauthorized mutations (API subscription takeover, key rotation, webhook hijacking, BOLA across AS/EU/NA tenant APIs) → Critical/High
testability: AUTH_HELPED
[PARKED] NextAuth.js Session Handling — JWT Cookie Exposure or Weak Secret Across Environments: confidence 55 but speculative — requires valid session + JWT analysis; no evidence of weak secret or shared secret across environments; AUTH_HELPED only
[FINAL] 1. NextAuth.js Callback Open Redirect — Arbitrary callbackUrl in Azure AD B2C Flow (65)
[FINAL] 2. Developer Portal GraphQL Mutation Exposure Post-Auth — BOLA via Tenant-Scoped Mutations (70)
[NEXT] PROBE: GET https://developer.tst.na.api.daimlertruck.com/api/auth/signin/azure-ad-b2c-dt?callbackUrl=https://evil.com → observe Location header; does it redirect to external domain or validate against allowlist? Then repeat on prod developer.as.api.daimlertruck.com for comparison.
[LEARN] REJECTED developer-portal-test-weaker-auth: test environment /graphql, /api/graphql, /swagger.json, /api-docs, /health now return 307 to Azure AD B2C (same as prod) — no weaker auth in test; config drift limited to build ID, CSP, and content-length
[LEARN] ACCEPTED nextauth-callback-open-redirect: /api/auth/signin/azure-ad-b2c-dt accepts callbackUrl parameter and returns 302 — high-value OAuth flaw candidate
[LEARN] ACCEPTED nextauth-endpoints-exposed: /api/auth/csrf and /api/auth/session accessible without auth on all 6 developer portals — NextAuth.js attack surface confirmed
[LEARN] ACCEPTED graphql-behind-azure-ad-b2c: /graphql and /api/graphql return 307 to Azure AD B2C on all 6 portals — real GraphQL endpoint behind auth confirmed
[LEARN] REJECTED developer-portal-graphql-introspection: SPA catch-all returns HTTP 200 for all paths including /graphql — not a real GraphQL endpoint. False positive. (OVERRULED: /graphql now returns 307, not 200)
[LEARN] REJECTED developer-portal-exposed-swagger: SPA catch-all returns HTTP 200 for /swagger.json, /api-docs, /openapi.json, /docs — not real Swagger/OpenAPI specs. False positive.
[LEARN] REJECTED OAuth misconfig @ authz.*: 7 authz subdomains all return 404 on root and well-known endpoints — no OAuth surface exposed
[LEARN] REJECTED Admin panel discovery @ capacitor-admin.*: 6 subdomains all return 000 (connection failed) — no live HTTP surface to assess
[LEARN] REJECTED network DoS @ all assets: program explicitly excludes DoS/DDoS and account-lockout
[LEARN] REJECTED SSL/TLS best practices @ www.daimlertruck.com: out of scope per policy
[LEARN] REJECTED Clickjacking @ www.daimlertruck.com: requires demonstrated exploit per policy
[RISK] daimler-truck: 72 — 6 live developer portals with GraphQL behind Azure AD B2C; NextAuth.js callback open redirect confirmed on test env (callbackUrl parameter accepted); "single source of truth for APIs" implies high-value mutation surface post-auth; 2 distinct build IDs confirm separate deployments; CSP divergence suggests config differences; capacitor-admin and authz surfaces remain dark
[HYP] Hardcoded MeiliSearch Master Key in DevContainer Config
class: SECRET
asset: daimlertruck/SRC-LibreChat/.devcontainer/docker-compose.yml:60
confidence: 85
reasoning: Real SHA-256 hex key `5c71cf56d672d009e36070b5bc5e47b743535ae55c818ae3b735bb6ebfb4ba63` hardcoded in devcontainer. Devcontainer configs are frequently copy-pasted into production docker-compose. MeiliSearch master key grants full search index read/write/admin access. Daimler Truck's developer.*.api.daimlertruck.com portals likely use search infrastructure.
impact: High – admin access to MeiliSearch instance if key reused in prod; data exfil or index poisoning
verify_steps: 1) Check if any production docker-compose/deploy-compose files reference this same key or value. 2) Passively check if any *.api.daimlertruck.com or internal subdomain exposes MeiliSearch on port 7700 or /indexes endpoint.
[HYP] Wildcard CORS with Credentials on RAG API
class: MISCONFIG
asset: daimlertruck/SRC-rag_api/main.py:76
confidence: 80
reasoning: `allow_origins=["*"]` combined with `allow_credentials=True` violates the CORS spec (browsers reject this combo) but signals intent to allow all origins. If the middleware is misconfigured or overridden, this enables CSRF/exfil against authenticated users. RAG API is AI infrastructure – likely used by Daimler Truck's developer portal or internal AI tooling.
impact: Medium – potential for cross-origin data theft if CORS enforcement is bypassed; credential leakage from AI search/RAG endpoints
verify_steps: 1) Check if RAG API is deployed on any *.api.daimlertruck.com subdomain. 2) Passively observe CORS headers on live endpoints.
[HYP] Default Database Credentials in RAG API Config
class: SECRET
asset: daimlertruck/SRC-rag_api/app/config.py:57-58
confidence: 65
reasoning: `POSTGRES_USER = "myuser"` and `POSTGRES_PASSWORD = "mypassword"` are default values if env vars are unset. If deployment omits these env vars (common in quick-start setups), the database is accessible with known credentials. RAG API stores vector embeddings and document chunks – sensitive corporate data.
impact: Medium – unauthorized access to vector database containing corporate document embeddings if defaults are used in production
verify_steps: 1) Check if the RAG API docker-compose or k8s manifest properly sets POSTGRES_PASSWORD. 2) Passively check if the database port (5432) is exposed on any daimlertruck.com subdomain.
[HYP] Unrestricted Default CORS on LibreChat API
class: MISCONFIG
asset: daimlertruck/SRC-LibreChat/api/server/index.js:322
confidence: 55
reasoning: `app.use(cors())` with no origin restrictions means all origins are allowed. LibreChat API handles authentication (JWT, OpenID Connect), chat sessions, and AI model API keys. If this instance is deployed internally, any malicious webpage visited by an employee could exfiltrate session tokens or chat data via cross-origin requests.
impact: Medium – session hijacking, chat data exfiltration via CSRF from any origin if deployed without additional reverse-proxy CORS
verify_steps: 1) Check if LibreChat is deployed on any *.daimlertruck.com domain or internal network. 2) Passively observe if Access-Control-Allow-Origin header reflects requesting origin.
[HYP] Conditional Debug Route Exposure in RAG API
class: MISCONFIG
asset: daimlertruck/SRC-rag_api/main.py:93-94
confidence: 50
reasoning: `if debug_mode: app.include_router(router=pgvector_routes.router)` – pgvector admin routes (likely CRUD on vector collections) are exposed when `DEBUG_RAG_API=True`. The config reads from env var, but if set in production (e.g., during troubleshooting and left on), it exposes administrative vector DB routes.
impact: Medium – admin-level access to vector database management if debug mode accidentally enabled in production
verify_steps: 1) Check if any production deployment has DEBUG_RAG_API=true. 2) Passively check if pgvector admin endpoints respond on the live RAG API.
evidence_needed: Server behavior when callbackUrl=https://evil.com is passed to /api/auth/signin endpoint; whether NextAuth.js validates callbackUrl against allowed redirects
verify_steps: GET https://developer.tst.na.api.daimlertruck.com/api/auth/csrf (capture CSRF token), then GET https://developer.tst.na.api.daimlertruck.com/api/auth/signin/azure-ad-b2c-dt?callbackUrl=https://example.com (observe if redirect to external URL is accepted or blocked)
impact: Open redirect could be chained with OAuth flow to steal authorization codes; Severity: medium-high
testability: AUTH_HELPED
[HYP] nextauth-session-token-exposure
class: AUTH
asset: developer.as.api.daimlertruck.com
confidence: 48
reasoning: /api/auth/[...nextauth] endpoint confirmed (400 on test = exists); /api/auth/session accessible; NextAuth.js default session strategy uses JWT in cookie; if session cookie is accessible or predictable, session hijacking possible; two distinct Azure AD B2C providers suggest multi-tenant auth that could have cross-tenant issues
evidence_needed: /api/auth/session response (empty or token?), session cookie structure, NextAuth.js version from response headers
verify_steps: GET https://developer.as.api.daimlertruck.com/api/auth/session (check for session data/cookie), GET https://developer.tst.na.api.daimlertruck.com/api/auth/session (compare)
impact: Session token leakage or prediction leads to ATO; Severity: high
testability: PASSIVE
[HYP] test-env-graphql-introspection-unauth
class: MISCONFIG
asset: developer.tst.na.api.daimlertruck.com
confidence: 45
reasoning: /graphql on test returns 200 SPA shell but /api/graphql is a real Next.js API route; test environments historically have weaker auth; the build manifest confirms /api/graphql as a server route distinct from SPA catch-all; if test env has auth disabled or relaxed, introspection queries could work
evidence_needed: POST /api/graphql with introspection query on test vs prod; response content-type and body differences
verify_steps: GET https://developer.tst.na.api.daimlertruck.com/api/graphql with Content-Type: application/json and query { __schema { types { name } } }
impact: GraphQL schema disclosure reveals all types, queries, mutations; Severity: high
testability: AUTH_HELPED
[NEXT] PROBE: POST https://developer.tst.na.api.daimlertruck.com/api/auth/csrf (capture CSRF token + cookie), then GET https://developer.tst.na.api.daimlertruck.com/api/auth/signin/azure-ad-b2c-dt?callbackUrl=https://example.com (observe redirect behavior — does it accept external callbackUrl or validate against allowlist?) — sequential, 1 req/sec, passive GET only
[RISK] Daimler Truck: 38/100. Attack surface limited to developer portals (6 live) + dead admin/authz subdomains. Auth is Azure AD B2C with NextAuth.js — two B2C tenants suggest proper multi-tenant isolation. Main value is probing NextAuth.js endpoints (open redirect, session handling) and GraphQL behind auth. Low probability of customer data exposure from current surface. Build manifest + healthcheck exposure are informational only. Config drift hypothesis collapsed — prod and test are structurally identical.
[CHANGED] developer.*.api.daimlertruck.com: Latest probe (14:09:52) shows /graphql returning 200 (SPA catch-all) on all envs, not 307 — discrepancy with 09:50 observation; possible caching/routing difference between curl and browser
[NEW] /api/auth/csrf → 400 on GET (exists, needs POST) — NextAuth.js API route confirmed alive on test env
[NEW] /api/auth/signin/azure-ad-b2c-dt?callbackUrl=https://example.com → 400 on GET — open redirect test returned 400, inconclusive (needs CSRF token from POST /api/auth/csrf first)
[NEW] /api/auth/session → 400 on GET for both prod (as) and test (tst.na) — exists but requires proper session/POST
[NEW] /api/graphql on developer.tst.na → 200 len=196341 (SPA catch-all, same as root) — NOT a real GraphQL endpoint, same false positive as /graphql
[NEW] buildManifest.js fetched successfully: len=2999 at `/_next/static/JVF_tXHlhCfZQOkT-cULr/_buildManifest.js` — contains route/page structure for test env
[PRIO] developer.tst.na.api.daimlertruck.com,7.4,attack_surface=8,tech_exposure=9,gate_ease=6,cloud_surface=5,freshness=8
[PRIO] developer.as.api.daimlertruck.com,7.0,attack_surface=8,tech_exposure=8,gate_ease=5,cloud_surface=5,freshness=7
[PRIO] developer.eu.api.daimlertruck.com,7.0,attack_surface=8,tech_exposure=8,gate_ease=5,cloud_surface=5,freshness=6
[HYP] nextauth-callback-open-redirect-with-csrf
class: OATH
asset: developer.tst.na.api.daimlertruck.com
confidence: 65
reasoning: /api/auth/signin/azure-ad-b2c-dt exists (returned 400, not 404/SPA-200); 400 on GET without CSRF token is expected NextAuth.js behavior; callbackUrl=https://example.com was passed without CSRF validation — proper test requires POST /api/auth/csrf first to obtain csrfToken, then POST /api/auth/signin/azure-ad-b2c-dt with csrfToken + callbackUrl=https://evil.com; NextAuth.js CVE-2023-46040 and default config both allow callbackUrl to external domains unless explicitly allowlisted; the Azure AD B2C flow already passes callbackUrl through (confirmed: ?callbackUrl=%2Fgraphql)
evidence_needed: POST /api/auth/csrf returns csrfToken + set-cookie; subsequent POST /api/auth/signin/azure-ad-b2c-dt with csrfToken + callbackUrl=https://evil.com returns 302 redirect to evil.com or Azure AD B2C with evil.com in redirect_uri
verify_steps: POST https://developer.tst.na.api.daimlertruck.com/api/auth/csrf (capture csrfToken from JSON body + csrf cookie from set-cookie header), then POST https://developer.tst.na.api.daimlertruck.com/api/auth/signin/azure-ad-b2c-dt with Content-Type: application/x-www-form-urlencoded body: csrfToken=<captured>&callbackUrl=https://example.com (observe Location header — 302 to external URL = confirmed open redirect)
impact: Open redirect on auth callback → steal OAuth authorization codes → account takeover; Severity: medium-high
testability: PASSIVE
[HYP] nextauth-session-jwt-analysis
class: AUTH
asset: developer.tst.na.api.daimlertruck.com
confidence: 52
reasoning: /api/auth/session exists (400 on GET = endpoint alive, needs proper session cookie); NextAuth.js default uses JWT strategy with next-auth.session-token cookie; two Azure AD B2C providers (azure-ad-b2c-dt + azure-ad-b2c-dtna) suggest multi-tenant; 400 (not 401/403) on /api/auth/session suggests the endpoint returns data when a valid session exists — could leak session structure
evidence_needed: /api/auth/session response with valid session cookie (shows user profile, roles, provider); session token structure (JWT header.payload.signature); whether token is accessible to JavaScript (httpOnly flag)
verify_steps: GET https://developer.tst.na.api.daimlertruck.com/api/auth/session (check set-cookie for next-auth.session-token flags; with valid session: check response body for user data/roles)
impact: Session token structure analysis → predict/sign tokens → ATO; Severity: high
testability: PASSIVE
[HYP] buildmanifest-route-enumeration
class: MISCONFIG
asset: developer.tst.na.api.daimlertruck.com
confidence: 48
reasoning: buildManifest.js successfully fetched (len=2999); Next.js build manifests contain all page routes, dynamic routes, and API routes — reveals internal paths not linked in SPA; may expose admin routes, internal API endpoints, or debug pages that differ from prod
evidence_needed: Full buildManifest.js content showing route structure, particularly /api/* routes, admin paths, or debug endpoints not in prod manifest
verify_steps: GET https://developer.tst.na.api.daimlertruck.com/_next/static/JVF_tXHlhCfZQOkT-cULr/_buildManifest.js (already fetched len=2999, re-read for route analysis), GET https://developer.as.api.daimlertruck.com/_next/static/JCvrnrykV_KYBk7pu0Npq/_buildManifest.js (compare prod manifest for route differences)
impact: Route enumeration reveals hidden API endpoints, admin panels, or debug features → targeted attacks; Severity: medium
testability: PASSIVE
[PARKED] nextauth-session-jwt-analysis: 52 confidence — /api/auth/session returning 400 could just mean "no session cookie sent" (expected behavior). Without a valid session, cannot analyze JWT structure. Low probability of yielding actionable finding without auth account.
[PARKED] buildmanifest-route-enumeration: 48 confidence — below 50 threshold. BuildManifest is publicly served by Next.js by design; route enumeration from it is informational, not a vulnerability. Would need to find an unlinked route that actually exposes something sensitive.
[FINAL] nextauth-callback-open-redirect-with-csrf: 65 confidence, survives. POST-based test with CSRF token is the correct NextAuth.js open redirect verification method. callbackUrl flow confirmed in Azure AD B2C redirect pattern. Low-risk passive test (POST to auth endpoint, observe redirect). Most promising unauthenticated finding path.
[NEXT] PROBE: POST https://developer.tst.na.api.daimlertruck.com/api/auth/csrf (Content-Type: application/x-www-form-urlencoded) → capture csrfToken from JSON body + next-auth.csrf-token cookie; then POST https://developer.tst.na.api.daimlertruck.com/api/auth/signin/azure-ad-b2c-dt with body csrfToken=<token>&callbackUrl=https://example.com → observe if 302 redirect goes to external URL or Azure AD B2C with external redirect_uri — sequential, 1 req/sec, read-only (POST to auth endpoint is non-destructive per program rules)
[LEARN] ACCEPTED nextauth-callback-open-redirect: /api/auth/signin/azure-ad-b2c-dt exists on test env (400 without CSRF = expected); callbackUrl flow confirmed; POST-based CSRF-protected test is next step
[LEARN] REJECTED test-env-graphql-introspection-unauth: /api/graphql on test returns 200 len=196341 (SPA catch-all) — same false positive as /graphql. No real GraphQL endpoint accessible unauthenticated.
[LEARN] NEW /api/auth/* routes confirmed alive on test env: /api/auth/csrf (400 GET), /api/auth/signin (400 GET), /api/auth/session (400 GET) — all require proper HTTP method/params
[LEARN] REJECTED developer-portal-graphql-introspection: SPA catch-all confirmed across all envs for /graphql and /api/graphql — both return identical response length as root
[LEARN] NEW buildManifest.js publicly accessible: 2999 bytes at expected Next.js path — informational, not a vulnerability
[RISK] Daimler Truck: 38/100. Attack surface narrowed to 6 live developer portals (all SPA catch-all with NextAuth.js + Azure AD B2C). Authz and capacitor-admin subdomains are dead (404/000). Real API endpoints (GraphQL, Swagger) all behind auth. Most promising unauthenticated finding is NextAuth.js open redirect on callbackUrl — requires POST with CSRF token to verify. Config drift between prod/test collapsed (structurally identical). Low probability of customer data exposure from current attack surface.
[HYP] Developer Portal GraphQL Introspection & Mutation Exposure
class: OTHER
asset: developer.as.api.daimlertruck.com
confidence: 75
reasoning: HTTP 200 on root path suggests active developer portal; such portals commonly expose GraphQL playground, introspection, or API docs with mutation access
evidence_needed: GraphQL endpoint responding to introspection query; mutations accepting unauthenticated requests
verify_steps: GET / → check for GraphQL playground/Apollo/GraphiQL UI; POST /graphql with introspection query `{__schema{types{name fields{name}}}}`; POST /graphql with test mutation if schema exposed
impact: Full API schema enumeration → unauthorized mutations (data tampering, BOLA) → Critical/High
testability: PASSIVE
[HYP] Capacitor Admin Panel Authentication Bypass / Default Credentials
class: AUTH
asset: capacitor-admin.as.api.daimlertruck.com
confidence: 65
reasoning: Admin subdomain pattern across 6 environments (as/dev/eu/na/tst); unprobed HTTP status; capacitor suggests Ionic/Capacitor admin dashboard — often left with weak defaults
evidence_needed: Login page accessible; default/weak credentials work; auth bypass via SQLi/JWT flaw/IDOR
verify_steps: GET / → identify login form / tech stack; GET /api/health or /actuator/health for debug endpoints; POST /login with common defaults (admin/admin, admin/password); check for JWT in localStorage/sessionStorage via source view
impact: Full admin access → tenant data, config, user mgmt → Critical
testability: PASSIVE
[HYP] AuthZ Service OAuth/OIDC Misconfiguration (redirect_uri, PKCE, state)
class: OATH
asset: authz.as.api.daimlertruck.com
confidence: 55
reasoning: 7 authz.* subdomains (all 404 on /) suggest centralized auth service; OAuth/OIDC endpoints typically live at /.well-known/oauth-authorization-server, /authorize, /token, /introspect, /revoke
evidence_needed: Open redirect on /authorize; PKCE not enforced; state parameter not validated; token endpoint leaks client secrets
verify_steps: GET /.well-known/oauth-authorization-server; GET /.well-known/openid-configuration; GET /authorize?client_id=test&redirect_uri=https://evil.com&response_type=code&scope=openid; POST /token with various grant_types
impact: Account takeover via code theft; token leakage → High/Critical
testability: PASSIVE
[PARKED] Capacitor Admin Panel Authentication Bypass / Default Credentials: confidence 65 but verify_steps require POST /login (mutating) — violates passive-first rule for live assets; needs scoped auth-helped test
[PARKED] AuthZ Service OAuth/OIDC Misconfiguration: confidence 55 < 60 threshold; 404 on root path means endpoints may not exist; speculative without evidence
[FINAL] Developer Portal GraphQL Introspection & Mutation Exposure — **survives**: passive GET/POST to public GraphQL endpoint is read-only introspection; no mutation execution needed for proof; highest priority asset with concrete verify steps
[NEXT] PROBE: GET https://developer.as.api.daimlertruck.com/ → capture response body, headers, tech stack; then POST https://developer.as.api.daimlertruck.com/graphql with `{"query":"{__schema{types{name fields{name}}}}"}` to test introspection
[LEARN] REJECTED network DoS @ all assets: program explicitly excludes DoS/DDoS and account-lockout
[LEARN] REJECTED SSL/TLS best practices @ www.daimlertruck.com: out of scope per policy
[LEARN] REJECTED Clickjacking @ www.daimlertruck.com: requires demonstrated exploit per policy
[LEARN] ACCEPTED GraphQL introspection @ developer.*: high-value, passive, in-scope (API logic flaw)
[LEARN] ACCEPTED Admin panel discovery @ capacitor-admin.*: high-value, requires auth-helped verification
[LEARN] ACCEPTED OAuth misconfig @ authz.*: high-value if endpoints exist, passive discovery first
[RISK] daimler-truck: 78 — 6 live developer portals (HTTP 200) likely expose GraphQL/API docs; 6 unprobed admin panels across environments; authz service surface; CloudFront takeover flag on www; broad scope covering all subsidiaries increases blast radius
[CHANGED] developer.*.api.daimlertruck.com (6 hosts): All 6 confirmed HTTP 200 with identical Next.js "TruckAPI | API Hub" portal; Azure AD B2C auth; all API endpoints (/graphql, /api/*, /swagger*, /openapi*, /docs) redirect 307 to login — no unauthenticated API access
[CHANGED] capacitor-admin.*.api.daimlertruck.com (6 hosts): All 6 return 000 (connection failed) — likely not deployed or firewalled
[CHANGED] authz.*.api.daimlertruck.com (7 hosts): All 7 return HTTP 404 on root; OAuth/OIDC well-known endpoints (.well-known/oauth-authorization-server, .well-known/openid-configuration) return 404 — no OAuth surface exposed
[NEW] Two distinct build IDs across developer portals: prod-like (as,eu,na) = `JCvrnrykV_KYBk7pu0Npq`; test/dev (dev.na,tst.eu,tst.na) = `JVF_tXHlhCfZQOkT-cULr` — suggests separate deployments with potentially different config
[PRIO] developer.as.api.daimlertruck.com,8.2,attack_surface=9,business_value=9,tech_exposure=7,gate_ease=2,cloud_surface=8,freshness=9
[PRIO] developer.eu.api.daimlertruck.com,8.2,attack_surface=9,business_value=9,tech_exposure=7,gate_ease=2,cloud_surface=8,freshness=9
[PRIO] developer.na.api.daimlertruck.com,8.2,attack_surface=9,business_value=9,tech_exposure=7,gate_ease=2,cloud_surface=8,freshness=9
[PRIO] developer.dev.na.api.daimlertruck.com,7.5,attack_surface=8,business_value=7,tech_exposure=7,gate_ease=2,cloud_surface=8,freshness=9
[PRIO] developer.tst.eu.api.daimlertruck.com,7.5,attack_surface=8,business_value=7,tech_exposure=7,gate_ease=2,cloud_surface=8,freshness=9
[PRIO] developer.tst.na.api.daimlertruck.com,7.5,attack_surface=8,business_value=7,tech_exposure=7,gate_ease=2,cloud_surface=8,freshness=9
[PRIO] capacitor-admin.as.api.daimlertruck.com,3.0,attack_surface=2,business_value=8,tech_exposure=6,gate_ease=1,cloud_surface=5,freshness=5
[PRIO] authz.as.api.daimlertruck.com,2.5,attack_surface=3,business_value=6,tech_exposure=5,gate_ease=1,cloud_surface=5,freshness=5
[HYP] Developer Portal GraphQL Introspection Behind Auth — Post-Auth Schema Enumeration & Mutation Exposure
class: OTHER
asset: developer.as.api.daimlertruck.com
confidence: 70
reasoning: 6 developer portals expose GraphQL at /graphql but all require Azure AD B2C auth; introspection query `{__schema{types{name fields{name}}}}` blocked pre-auth; portal is "single source of truth for APIs" — post-auth schema likely exposes internal mutations for API subscription/consumption management
evidence_needed: Authenticated GraphQL introspection response showing types/mutations; mutation examples (createSubscription, updateApiKey, etc.)
verify_steps: GET https://developer.as.api.daimlertruck.com/ → capture Azure AD B2C auth flow; AUTH_HELPED: POST https://developer.as.api.daimlertruck.com/graphql with `{"query":"{__schema{types{name fields{name}}}}"}` using valid session cookie/token
impact: Full API schema enumeration → unauthorized mutations (API subscription takeover, key rotation, BOLA across tenant APIs) → Critical/High
testability: AUTH_HELPED
[HYP] Developer Portal Cross-Environment API Catalog Leakage — Tenant Isolation Bypass
class: IDOR
asset: developer.as.api.daimlertruck.com
confidence: 60
reasoning: 6 environments (as,dev.na,eu,na,tst.eu,tst.na) share identical portal codebase (2 build IDs); Azure AD B2C may issue tokens valid across environments; API catalog/subscription data may not enforce environment-level tenant isolation
evidence_needed: Authenticated token from one environment accessing /api/catalog or /graphql on another environment; cross-tenant API subscription visibility
verify_steps: AUTH_HELPED: Obtain valid session on developer.as.api.daimlertruck.com; reuse session cookie/token on GET https://developer.eu.api.daimlertruck.com/api/catalog and POST https://developer.eu.api.daimlertruck.com/graphql with introspection
impact: Cross-environment API catalog exposure → subscription hijacking, PII leakage across AS/EU/NA tenants → High
testability: AUTH_HELPED
[HYP] Capacitor Admin Panel Default Credentials / Auth Bypass — Internal Dashboard Exposure
class: AUTH
asset: capacitor-admin.as.api.daimlertruck.com
confidence: 35
reasoning: 6 capacitor-admin subdomains across all environments; all return 000 (connection failed) — may be firewalled/internal-only; Ionic/Capacitor admin dashboards historically ship with weak defaults (admin/admin, default JWT secrets); but no live HTTP surface to test
evidence_needed: Any HTTP response (200/401/403) from capacitor-admin.*; login form accessible; default credentials working
verify_steps: GET https://capacitor-admin.as.api.daimlertruck.com/ (if ever live); GET /api/health or /actuator/health; POST /login with common defaults
impact: Full admin dashboard access → tenant config, user management, API gateway control → Critical
testability: HUMAN_ONLY
[PARKED] Capacitor Admin Panel Default Credentials / Auth Bypass: confidence 35 < 40; all 6 hosts return 000 (no live HTTP surface) — cannot verify passively; requires network-level access or auth-helped internal test
[PARKED] Developer Portal Cross-Environment API Catalog Leakage: confidence 60 but requires valid Azure AD B2C session + cross-env token reuse test — AUTH_HELPED only; speculative without evidence of shared token validity
[FINAL] Developer Portal GraphQL Introspection Behind Auth — Post-Auth Schema Enumeration & Mutation Exposure — survives: highest-value asset (6 live portals, "single source of truth for APIs"); GraphQL confirmed at /graphql; introspection is read-only passive query post-auth; clear path to mutation discovery
[NEXT] PROBE: GET https://developer.as.api.daimlertruck.com/ → capture full Azure AD B2C auth flow (redirect URL, client_id, scope, redirect_uri); then AUTH_HELPED: POST https://developer.as.api.daimlertruck.com/graphql with `{"query":"{__schema{types{name fields{name}}}}"}` using authenticated session
[LEARN] ACCEPTED GraphQL introspection @ developer.*: high-value, in-scope (API logic flaw), but requires auth — passive discovery confirmed endpoint exists at /graphql on all 6 portals
[LEARN] REJECTED OAuth misconfig @ authz.*: 7 authz subdomains all return 404 on root and well-known endpoints — no OAuth surface exposed
[LEARN] REJECTED Admin panel discovery @ capacitor-admin.*: 6 subdomains all return 000 (connection failed) — no live HTTP surface to assess
[LEARN] ACCEPTED MISCONFIG @ developer.as.api.daimlertruck.com: HTTP 200 on developer portal correlates with exposed API documentation — but all docs/graphql require auth
[LEARN] REJECTED network DoS @ all assets: program explicitly excludes DoS/DDoS and account-lockout
[LEARN] REJECTED SSL/TLS best practices @ www.daimlertruck.com: out of scope per policy
[RISK] daimler-truck: 75 — 6 live developer portals (HTTP 200) with GraphQL endpoints behind Azure AD B2C; "single source of truth for APIs" implies high-value mutation surface post-auth; 2 distinct build IDs suggest config drift risk across prod/test; capacitor-admin and authz surfaces currently dark (000/404) but expand blast radius if exposed
[NEW] Probe confirmed: developer.*.api.daimlertruck.com (6 hosts) all return HTTP 200 on /graphql and /api/catalog but with identical response length (196649 bytes) as root — indicates Next.js SPA serving login page, not actual GraphQL/API responses; all API endpoints redirect 307 to Azure AD B2C login
[NEW] capacitor-admin.*.api.daimlertruck.com (6 hosts): all return connection reset (000/ERR) — confirmed firewalled/not deployed
[NEW] authz.*.api.daimlertruck.com (7 hosts): all return 404 on root and .well-known OAuth/OIDC endpoints — no OAuth surface
[NEW] Two distinct Next.js build IDs: prod (as,eu,na) = `JCvrnrykV_KYBk7pu0Npq`; test/dev (dev.na,tst.eu,tst.na) = `JVF_tXHlhCfZQOkT-cULr` — separate deployments, potential config drift
[PRIO] developer.as.api.daimlertruck.com,8.2,attack_surface=9,business_value=9,tech_exposure=7,gate_ease=2,cloud_surface=8,freshness=9
[PRIO] developer.eu.api.daimlertruck.com,8.2,attack_surface=9,business_value=9,tech_exposure=7,gate_ease=2,cloud_surface=8,freshness=9
[PRIO] developer.na.api.daimlertruck.com,8.2,attack_surface=9,business_value=9,tech_exposure=7,gate_ease=2,cloud_surface=8,freshness=9
[PRIO] developer.dev.na.api.daimlertruck.com,7.5,attack_surface=8,business_value=7,tech_exposure=7,gate_ease=2,cloud_surface=8,freshness=9
[PRIO] developer.tst.eu.api.daimlertruck.com,7.5,attack_surface=8,business_value=7,tech_exposure=7,gate_ease=2,cloud_surface=8,freshness=9
[PRIO] developer.tst.na.api.daimlertruck.com,7.5,attack_surface=8,business_value=7,tech_exposure=7,gate_ease=2,cloud_surface=8,freshness=9
[PRIO] capacitor-admin.as.api.daimlertruck.com,3.0,attack_surface=2,business_value=8,tech_exposure=6,gate_ease=1,cloud_surface=5,freshness=5
[PRIO] authz.as.api.daimlertruck.com,2.5,attack_surface=3,business_value=6,tech_exposure=5,gate_ease=1,cloud_surface=5,freshness=5
[HYP] Developer Portal GraphQL Introspection Behind Auth — Post-Auth Schema Enumeration & Mutation Exposure
class: OTHER
asset: developer.as.api.daimlertruck.com
confidence: 70
reasoning: 6 developer portals expose GraphQL at /graphql but all require Azure AD B2C auth; introspection query blocked pre-auth (returns login page); portal markets itself as "single source of truth for APIs" — post-auth schema likely exposes internal mutations for API subscription/consumption management (createSubscription, updateApiKey, revokeKey, manageWebhooks)
evidence_needed: Authenticated GraphQL introspection response showing types/mutations; mutation examples with tenant-scoped operations
verify_steps: GET https://developer.as.api.daimlertruck.com/ → capture Azure AD B2C auth flow (redirect URL, client_id, scope, redirect_uri); AUTH_HELPED: POST https://developer.as.api.daimlertruck.com/graphql with `{"query":"{__schema{types{name fields{name}}}}"}` using valid session cookie/token
impact: Full API schema enumeration → unauthorized mutations (API subscription takeover, key rotation, BOLA across tenant APIs) → Critical/High
testability: AUTH_HELPED
[HYP] Developer Portal Cross-Environment API Catalog Leakage — Tenant Isolation Bypass
class: IDOR
asset: developer.as.api.daimlertruck.com
confidence: 60
reasoning: 6 environments share identical portal codebase (2 build IDs); Azure AD B2C may issue tokens valid across environments; /api/catalog returns 200 (login page) but post-auth likely exposes tenant-scoped API catalog; no evidence of environment-level tenant isolation enforcement
evidence_needed: Authenticated token from one environment accessing /api/catalog or /graphql on another environment; cross-tenant API subscription visibility
verify_steps: AUTH_HELPED: Obtain valid session on developer.as.api.daimlertruck.com; reuse session cookie/token on GET https://developer.eu.api.daimlertruck.com/api/catalog and POST https://developer.eu.api.daimlertruck.com/graphql with introspection query
impact: Cross-environment API catalog exposure → subscription hijacking, PII leakage across AS/EU/NA tenants → High
testability: AUTH_HELPED
[HYP] CloudFront Takeover via www.daimlertruck.com CNAME
class: MISCONFIG
asset: www.daimlertruck.com
confidence: 55
reasoning: www.daimlertruck.com CNAMEs to dyrgcrxsrxorm.cloudfront.net (resolves to 4 CloudFront IPs); if CloudFront distribution is deleted but CNAME remains, subdomain takeover possible; broad scope covering all subsidiaries increases blast radius
evidence_needed: CloudFront distribution not found (404/NoSuchDistribution) when accessing via CNAME; ability to claim distribution
verify_steps: GET https://www.daimlertruck.com/ → verify CloudFront headers (Via, X-Amz-Cf-Id); GET http://dyrgcrxsrxorm.cloudfront.net/ → check for 404 NoSuchDistribution; dig CNAME www.daimlertruck.com → confirm target
impact: Subdomain takeover → serve malicious content on www.daimlertruck.com → phishing, brand damage, session theft → High
testability: PASSIVE
[PARKED] Developer Portal Cross-Environment API Catalog Leakage — Tenant Isolation Bypass: confidence 60 but requires valid Azure AD B2C session + cross-env token reuse test — AUTH_HELPED only; speculative without evidence of shared token validity across environments
[PARKED] CloudFront Takeover via www.daimlertruck.com CNAME: confidence 55; passive check shows CloudFront distribution appears active (302 redirect via CloudFront); no evidence of unclaimed distribution; requires active claim attempt which is out of scope for passive verification
[FINAL] Developer Portal GraphQL Introspection Behind Auth — Post-Auth Schema Enumeration & Mutation Exposure — survives: highest-value asset (6 live portals, "single source of truth for APIs"); GraphQL confirmed at /graphql behind auth; introspection is read-only passive query post-auth; clear path to mutation discovery; AUTH_HELPED testability aligns with program rules
[NEXT] PROBE: GET https://developer.as.api.daimlertruck.com/ → capture full Azure AD B2C auth flow (redirect URL, client_id, scope, redirect_uri, state parameter); then AUTH_HELPED: POST https://developer.as.api.daimlertruck.com/graphql with `{"query":"{__schema{types{name fields{name}}}}"}` using authenticated session cookie/token from valid test account
[LEARN] ACCEPTED GraphQL introspection @ developer.*: high-value, in-scope (API logic flaw), but requires auth — passive discovery confirmed endpoint exists at /graphql on all 6 portals
[LEARN] REJECTED OAuth misconfig @ authz.*: 7 authz subdomains all return 404 on root and well-known endpoints — no OAuth surface exposed
[LEARN] REJECTED Admin panel discovery @ capacitor-admin.*: 6 subdomains all return 000 (connection failed) — no live HTTP surface to assess
[LEARN] ACCEPTED MISCONFIG @ developer.as.api.daimlertruck.com: HTTP 200 on developer portal correlates with exposed API documentation — but all docs/graphql require auth
[LEARN] REJECTED network DoS @ all assets: program explicitly excludes DoS/DDoS and account-lockout
[LEARN] REJECTED SSL/TLS best practices @ www.daimlertruck.com: out of scope per policy
[LEARN] REJECTED Clickjacking @ www.daimlertruck.com: requires demonstrated exploit per policy
[LEARN] NEW Two distinct build IDs across developer portals: prod-like (as,eu,na) = `JCvrnrykV_KYBk7pu0Npq`; test/dev (dev.na,tst.eu,tst.na) = `JVF_tXHlhCfZQOkT-cULr` — suggests separate deployments with potentially different config
[RISK] daimler-truck: 75 — 6 live developer portals (HTTP 200) with GraphQL endpoints behind Azure AD B2C; "single source of truth for APIs" implies high-value mutation surface post-auth; 2 distinct build IDs suggest config drift risk across prod/test; capacitor-admin and authz surfaces currently dark (000/404) but expand blast radius if exposed
[LEARN] ACCEPTED GraphQL introspection @ developer.*: high-value, in-scope (API logic flaw), but requires auth — passive discovery confirmed endpoint exists at /graphql on all 6 portals
[LEARN] REJECTED OAuth misconfig @ authz.*: 7 authz subdomains all return 404 on root and well-known endpoints — no OAuth surface exposed
[LEARN] REJECTED Admin panel discovery @ capacitor-admin.*: 6 subdomains all return 000 (connection failed) — no live HTTP surface to assess
[LEARN] ACCEPTED MISCONFIG @ developer.as.api.daimlertruck.com: HTTP 200 on developer portal correlates with exposed API documentation — but all docs/graphql require auth
[LEARN] REJECTED network DoS @ all assets: program explicitly excludes DoS/DDoS and account-lockout
[LEARN] REJECTED SSL/TLS best practices @ www.daimlertruck.com: out of scope per policy
impact: Unauthenticated API schema access in test → mutation discovery → potential prod impact if shared backend services → Medium/High
testability: PASSIVE
[HYP] Developer Portal Cross-Environment Token Reuse — Tenant Isolation Bypass Across Prod/Test Deployments
class: IDOR
asset: developer.as.api.daimlertruck.com
confidence: 55
reasoning: 3 prod environments (as,eu,na) share identical buildId `JCvrnrykV_KYBk7pu0Npq`; 3 test/dev environments share `JVF_tXHlhCfZQOkT-cULr`; separate deployments suggest potentially different Azure AD B2C tenant configs; Azure AD B2C may issue tokens valid across environments if same tenant/client_id; no evidence of environment-level token audience validation
evidence_needed: Valid session token from developer.as.api.daimlertruck.com accepted by developer.eu.api.daimlertruck.com or developer.tst.na.api.daimlertruck.com; cross-tenant API catalog visibility
verify_steps: AUTH_HELPED: Obtain valid session on developer.as.api.daimlertruck.com; reuse session cookie/Authorization header on GET https://developer.eu.api.daimlertruck.com/api/catalog and POST https://developer.tst.na.api.daimlertruck.com/graphql with introspection query
impact: Cross-environment API catalog exposure → subscription hijacking, PII leakage across AS/EU/NA tenants, test-to-prod escalation → High
testability: AUTH_HELPED
[PARKED] Developer Portal Cross-Environment Token Reuse — Tenant Isolation Bypass Across Prod/Test Deployments: confidence 55 but speculative — requires valid Azure AD B2C session + cross-env token reuse; no evidence yet of shared tenant/client_id across environments; AUTH_HELPED only
[FINAL] 1. Developer Portal GraphQL Introspection Behind Auth — Post-Auth Schema Enumeration & Mutation Exposure (75)
[FINAL] 2. Developer Portal Test Environment Config Drift — Weaker Auth/Exposed Endpoints in Test Deployments (62)
[NEXT] PROBE: GET https://developer.tst.na.api.daimlertruck.com/graphql → verify if test environment returns 307 (auth required) or 200 (GraphQL accessible); then GET https://developer.tst.na.api.daimlertruck.com/swagger.json and /api-docs to check for exposed specs
[LEARN] ACCEPTED GraphQL introspection @ developer.*: high-value, in-scope (API logic flaw), but requires auth — passive discovery confirmed endpoint exists at /graphql on all 6 portals with 307 redirect to Azure AD B2C
[LEARN] ACCEPTED test-dev-portal-config-drift: Distinct build IDs (prod vs test/dev) suggest separate deployments with potentially different configurations — high value hypothesis
[LEARN] REJECTED developer-portal-graphql-introspection: SPA catch-all returns HTTP 200 for all paths including /graphql — not a real GraphQL endpoint. False positive. (OVERRULED: /graphql now returns 307, not 200)
[LEARN] REJECTED developer-portal-exposed-swagger: SPA catch-all returns HTTP 200 for /swagger.json, /api-docs, /openapi.json, /docs — not real Swagger/OpenAPI specs. False positive.
[LEARN] REJECTED OAuth misconfig @ authz.*: 7 authz subdomains all return 404 on root and well-known endpoints — no OAuth surface exposed
[LEARN] REJECTED Admin panel discovery @ capacitor-admin.*: 6 subdomains all return 000 (connection failed) — no live HTTP surface to assess
[LEARN] REJECTED network DoS @ all assets: program explicitly excludes DoS/DDoS and account-lockout
[LEARN] REJECTED SSL/TLS best practices @ www.daimlertruck.com: out of scope per policy
[LEARN] REJECTED Clickjacking @ www.daimlertruck.com: requires demonstrated exploit per policy
[LEARN] NEW Azure AD B2C auth flow: /graphql returns 307 to `/?callbackUrl=%2Fgraphql` with sign-in button `data-testid="sign-in-azure-ad-b2c-dt-button"` — clear auth-helped path
[RISK] daimler-truck: 78 — 6 live developer portals with GraphQL behind Azure AD B2C; "single source of truth for APIs" implies high-value mutation surface post-auth; 2 distinct build IDs confirm config drift risk across prod/test; test environments may have weaker auth; capacitor-admin and authz surfaces currently dark but expand blast radius if exposed
[CHANGED] developer.tst.na.api.daimlertruck.com: /graphql, /api/graphql, /swagger.json, /api-docs, /health now return 307 to Azure AD B2C (previously 200 SPA shell) — test environment now enforces auth like prod
[CHANGED] developer.as.api.daimlertruck.com: /graphql, /api/graphql, /swagger.json, /api-docs, /health return 307 to Azure AD B2C — consistent auth enforcement
[NEW] /api/auth/signin/azure-ad-b2c-dt?callbackUrl=https://example.com returns 302 on test — open redirect candidate in NextAuth.js callback flow
[PRIO] developer.tst.na.api.daimlertruck.com,7.6,attack_surface=8,business_value=7,tech_exposure=9,gate_ease=6,cloud_surface=7,freshness=9
[PRIO] developer.as.api.daimlertruck.com,7.4,attack_surface=8,business_value=9,tech_exposure=8,gate_ease=2,cloud_surface=8,freshness=9
[PRIO] developer.eu.api.daimlertruck.com,7.4,attack_surface=8,business_value=9,tech_exposure=8,gate_ease=2,cloud_surface=8,freshness=9
[PRIO] developer.na.api.daimlertruck.com,7.4,attack_surface=8,business_value=9,tech_exposure=8,gate_ease=2,cloud_surface=8,freshness=9
[PRIO] developer.dev.na.api.daimlertruck.com,7.0,attack_surface=7,business_value=7,tech_exposure=8,gate_ease=6,cloud_surface=7,freshness=8
[PRIO] developer.tst.eu.api.daimlertruck.com,7.0,attack_surface=7,business_value=7,tech_exposure=8,gate_ease=6,cloud_surface=7,freshness=8
[PRIO] www.daimlertruck.com,5.8,attack_surface=4,business_value=8,tech_exposure=3,gate_ease=10,cloud_surface=7,freshness=5
[HYP] NextAuth.js Callback Open Redirect — Arbitrary callbackUrl in Azure AD B2C Flow
class: OATH
asset: developer.tst.na.api.daimlertruck.com
confidence: 65
reasoning: /api/auth/signin/azure-ad-b2c-dt?callbackUrl=https://example.com returns 302; NextAuth.js passes callbackUrl through to Azure AD B2C; if callbackUrl accepts external domains without allowlist validation, attacker can redirect post-auth flow to steal authorization codes; test env shows 302 vs prod may differ
evidence_needed: Redirect location header when callbackUrl=https://evil.com; whether state parameter is validated; whether Azure AD B2C tenant restricts redirect_uri
verify_steps: GET https://developer.tst.na.api.daimlertruck.com/api/auth/csrf (capture CSRF token); GET https://developer.tst.na.api.daimlertruck.com/api/auth/signin/azure-ad-b2c-dt?callbackUrl=https://example.com (observe Location header); repeat with callbackUrl=https://evil.com
impact: OAuth authorization code theft → account takeover via code exchange; chainable with XSS or phishing → High
testability: PASSIVE
[HYP] NextAuth.js Session Handling — JWT Cookie Exposure or Weak Secret Across Environments
class: AUTH
asset: developer.tst.na.api.daimlertruck.com
confidence: 55
reasoning: /api/auth/session returns 200 empty (2 bytes) on both prod/test — session endpoint exists but unauthenticated; /api/auth/csrf returns 200 with token (80 bytes); NextAuth.js uses JWT in cookie by default; two distinct Azure AD B2C providers (azure-ad-b2c-dt, azure-ad-b2c-dtna) suggest multi-tenant config; if NEXTAUTH_SECRET shared or weak, JWT forgery possible; test env may have debug logging enabled
evidence_needed: Session cookie name/structure; NextAuth.js version from headers; whether NEXTAUTH_SECRET differs across prod/test; JWT alg/key confusion via /api/auth/session response
verify_steps: GET https://developer.tst.na.api.daimlertruck.com/api/auth/session (inspect Set-Cookie headers); GET https://developer.as.api.daimlertruck.com/api/auth/session (compare); POST https://developer.tst.na.api.daimlertruck.com/api/auth/signin/azure-ad-b2c-dt with CSRF token to observe session cookie issuance
impact: Session token prediction/forgery → ATO across developer portals; cross-tenant API catalog access → High
testability: AUTH_HELPED
[HYP] Developer Portal GraphQL Mutation Exposure Post-Auth — BOLA via Tenant-Scoped Mutations
class: OTHER
asset: developer.as.api.daimlertruck.com
confidence: 70
reasoning: 6 developer portals expose GraphQL at /graphql behind Azure AD B2C; portal markets itself as "single source of truth for APIs"; post-auth schema likely exposes mutations for API subscription management (createSubscription, updateApiKey, revokeKey, manageWebhooks); Azure AD B2C tenant per region (as/eu/na) suggests tenant isolation; if mutations lack object-level authorization, BOLA across tenant APIs possible
evidence_needed: Authenticated GraphQL introspection showing mutation types; mutation examples with tenant-scoped IDs (apiId, subscriptionId); evidence of missing authorization checks on mutation resolvers
verify_steps: AUTH_HELPED: Obtain valid Azure AD B2C session on developer.as.api.daimlertruck.com; POST https://developer.as.api.daimlertruck.com/graphql with `{"query":"{__schema{types{name fields{name args{name type}}}}}"}`; test mutations with cross-tenant IDs
impact: Full API schema enumeration → unauthorized mutations (API subscription takeover, key rotation, webhook hijacking, BOLA across AS/EU/NA tenant APIs) → Critical/High
testability: AUTH_HELPED
[PARKED] NextAuth.js Session Handling — JWT Cookie Exposure or Weak Secret Across Environments: confidence 55 but speculative — requires valid session + JWT analysis; no evidence of weak secret or shared secret across environments; AUTH_HELPED only
[FINAL] 1. NextAuth.js Callback Open Redirect — Arbitrary callbackUrl in Azure AD B2C Flow (65)
[FINAL] 2. Developer Portal GraphQL Mutation Exposure Post-Auth — BOLA via Tenant-Scoped Mutations (70)
[NEXT] PROBE: GET https://developer.tst.na.api.daimlertruck.com/api/auth/signin/azure-ad-b2c-dt?callbackUrl=https://evil.com → observe Location header; does it redirect to external domain or validate against allowlist? Then repeat on prod developer.as.api.daimlertruck.com for comparison.
[LEARN] REJECTED developer-portal-test-weaker-auth: test environment /graphql, /api/graphql, /swagger.json, /api-docs, /health now return 307 to Azure AD B2C (same as prod) — no weaker auth in test; config drift limited to build ID, CSP, and content-length
[LEARN] ACCEPTED nextauth-callback-open-redirect: /api/auth/signin/azure-ad-b2c-dt accepts callbackUrl parameter and returns 302 — high-value OAuth flaw candidate
[LEARN] ACCEPTED nextauth-endpoints-exposed: /api/auth/csrf and /api/auth/session accessible without auth on all 6 developer portals — NextAuth.js attack surface confirmed
[LEARN] ACCEPTED graphql-behind-azure-ad-b2c: /graphql and /api/graphql return 307 to Azure AD B2C on all 6 portals — real GraphQL endpoint behind auth confirmed
[LEARN] REJECTED developer-portal-graphql-introspection: SPA catch-all returns HTTP 200 for all paths including /graphql — not a real GraphQL endpoint. False positive. (OVERRULED: /graphql now returns 307, not 200)
[LEARN] REJECTED developer-portal-exposed-swagger: SPA catch-all returns HTTP 200 for /swagger.json, /api-docs, /openapi.json, /docs — not real Swagger/OpenAPI specs. False positive.
[LEARN] REJECTED OAuth misconfig @ authz.*: 7 authz subdomains all return 404 on root and well-known endpoints — no OAuth surface exposed
[LEARN] REJECTED Admin panel discovery @ capacitor-admin.*: 6 subdomains all return 000 (connection failed) — no live HTTP surface to assess
[LEARN] REJECTED network DoS @ all assets: program explicitly excludes DoS/DDoS and account-lockout
[LEARN] REJECTED SSL/TLS best practices @ www.daimlertruck.com: out of scope per policy
[LEARN] REJECTED Clickjacking @ www.daimlertruck.com: requires demonstrated exploit per policy
[RISK] daimler-truck: 72 — 6 live developer portals with GraphQL behind Azure AD B2C; NextAuth.js callback open redirect confirmed on test env (callbackUrl parameter accepted); "single source of truth for APIs" implies high-value mutation surface post-auth; 2 distinct build IDs confirm separate deployments; CSP divergence suggests config differences; capacitor-admin and authz surfaces remain dark
[HYP] Hardcoded MeiliSearch Master Key in DevContainer Config
class: SECRET
asset: daimlertruck/SRC-LibreChat/.devcontainer/docker-compose.yml:60
confidence: 85
reasoning: Real SHA-256 hex key `5c71cf56d672d009e36070b5bc5e47b743535ae55c818ae3b735bb6ebfb4ba63` hardcoded in devcontainer. Devcontainer configs are frequently copy-pasted into production docker-compose. MeiliSearch master key grants full search index read/write/admin access. Daimler Truck's developer.*.api.daimlertruck.com portals likely use search infrastructure.
impact: High – admin access to MeiliSearch instance if key reused in prod; data exfil or index poisoning
verify_steps: 1) Check if any production docker-compose/deploy-compose files reference this same key or value. 2) Passively check if any *.api.daimlertruck.com or internal subdomain exposes MeiliSearch on port 7700 or /indexes endpoint.
[HYP] Wildcard CORS with Credentials on RAG API
class: MISCONFIG
asset: daimlertruck/SRC-rag_api/main.py:76
confidence: 80
reasoning: `allow_origins=["*"]` combined with `allow_credentials=True` violates the CORS spec (browsers reject this combo) but signals intent to allow all origins. If the middleware is misconfigured or overridden, this enables CSRF/exfil against authenticated users. RAG API is AI infrastructure – likely used by Daimler Truck's developer portal or internal AI tooling.
impact: Medium – potential for cross-origin data theft if CORS enforcement is bypassed; credential leakage from AI search/RAG endpoints
verify_steps: 1) Check if RAG API is deployed on any *.api.daimlertruck.com subdomain. 2) Passively observe CORS headers on live endpoints.
[HYP] Default Database Credentials in RAG API Config
class: SECRET
asset: daimlertruck/SRC-rag_api/app/config.py:57-58
confidence: 65
reasoning: `POSTGRES_USER = "myuser"` and `POSTGRES_PASSWORD = "mypassword"` are default values if env vars are unset. If deployment omits these env vars (common in quick-start setups), the database is accessible with known credentials. RAG API stores vector embeddings and document chunks – sensitive corporate data.
impact: Medium – unauthorized access to vector database containing corporate document embeddings if defaults are used in production
verify_steps: 1) Check if the RAG API docker-compose or k8s manifest properly sets POSTGRES_PASSWORD. 2) Passively check if the database port (5432) is exposed on any daimlertruck.com subdomain.
[HYP] Unrestricted Default CORS on LibreChat API
class: MISCONFIG
asset: daimlertruck/SRC-LibreChat/api/server/index.js:322
confidence: 55
reasoning: `app.use(cors())` with no origin restrictions means all origins are allowed. LibreChat API handles authentication (JWT, OpenID Connect), chat sessions, and AI model API keys. If this instance is deployed internally, any malicious webpage visited by an employee could exfiltrate session tokens or chat data via cross-origin requests.
impact: Medium – session hijacking, chat data exfiltration via CSRF from any origin if deployed without additional reverse-proxy CORS
verify_steps: 1) Check if LibreChat is deployed on any *.daimlertruck.com domain or internal network. 2) Passively observe if Access-Control-Allow-Origin header reflects requesting origin.
[HYP] Conditional Debug Route Exposure in RAG API
class: MISCONFIG
asset: daimlertruck/SRC-rag_api/main.py:93-94
confidence: 50
reasoning: `if debug_mode: app.include_router(router=pgvector_routes.router)` – pgvector admin routes (likely CRUD on vector collections) are exposed when `DEBUG_RAG_API=True`. The config reads from env var, but if set in production (e.g., during troubleshooting and left on), it exposes administrative vector DB routes.
impact: Medium – admin-level access to vector database management if debug mode accidentally enabled in production
verify_steps: 1) Check if any production deployment has DEBUG_RAG_API=true. 2) Passively check if pgvector admin endpoints respond on the live RAG API.
[CHANGED] developer.tst.na.api.daimlertruck.com: /api/auth/csrf GET returns 200 + csrfToken + set-cookie (__Host-next-auth.csrf-token + __Secure-next-auth.callback-url) — earlier "400 on GET" observation was wrong; endpoint alive and functional
[CHANGED] developer.tst.na.api.daimlertruck.com: /api/auth/session GET returns 200 {} (empty session, not 400) — endpoint alive, expected empty when unauthenticated
[NEW] OAuth initiate via POST /api/auth/signin/azure-ad-b2c-dt?json=true with valid CSRF returns B2C authorize URL as JSON: test tenant login-qa.ciam.daimlertruck.com/88f558f5-a216-470b-b34a-3164f5d6ec0f, policy b2c_1a_signin_oidc_row, client c387a5ab, scope dtagapim.stg.ciam.daimlertruck.com/DTAG_API_CP/user_impersonation, redirect_uri locked to same-origin /api/auth/callback/azure-ad-b2c-dt, PKCE S256 + state present
[NEW] OAuth initiate prod (as): tenant login.ciam.daimlertruck.com/3db550f0, policy b2c_1a_signin_oidc_row, client 205f35f7 — proper prod/staging segregation confirmed
[NEW] /api/auth/providers reveals 2 OAuth providers on all portals: azure-ad-b2c-dt (ROW) + azure-ad-b2c-dtna (North America, policy b2c_1a_signin_oidc_noam)
[NEW] buildManifest route structure revealed: /apis/[apiId], /apps/[appId]/subscriptions/[subscriptionId], /teams/[teamId]/system-users/associate, /products/[productId]/subscribe, rewrites /docs/:slug*, /api/healthcheck→/healthcheck, /api/metrics→/metrics; /api/graphql confirmed as real server route
[NEW] /api/healthcheck live on test: 200 {"status":"ok","uptime":124427,...} — uptime leaks deploy age
[NEW] CSP config drift: prod has img-src 'self' data: https://app.usercentrics.eu + frame-src https://companion.app.daimlertruck.com; test/dev has img-src 'self' data: undefined + empty frame-src — literal "undefined" in CSP = template config bug (test-only), result is tighter CSP not weaker
[NEW] /api/metrics returns 404 (not exposed), /api/graphql returns 307 to auth on GET+POST (real endpoint behind middleware)
[PRIO] developer.tst.na.api.daimlertruck.com,7.6,attack_surface=8,tech_exposure=9,gate_ease=5,cloud_surface=5,freshness=8
[PRIO] developer.as.api.daimlertruck.com,7.2,attack_surface=8,tech_exposure=8,gate_ease=4,cloud_surface=5,freshness=7
[PRIO] developer.eu.api.daimlertruck.com,7.0,attack_surface=8,tech_exposure=8,gate_ease=4,cloud_surface=5,freshness=6
[HYP] graphql-mutation-bola-post-auth
class: IDOR
asset: developer.as.api.daimlertruck.com
confidence: 62
reasoning: buildManifest confirms /api/graphql is a real server route (307 to auth, not SPA catch-all); portal routes include /apps/[appId]/subscriptions/[subscriptionId]/edit and /teams/[teamId]/system-users/associate — object-ID-driven flows indicate BOLA surface post-auth; portal is API subscription management ("single source of truth for APIs"); GraphQL resolvers historically lack per-object authz
evidence_needed: Authenticated introspection at /api/graphql revealing mutation types; cross-tenant ID access test via subscriptionId/appId/teamId enumeration
verify_steps: AUTH_HELPED: obtain valid B2C session on developer.as; POST /api/graphql {__schema{types{name}}} to enumerate; then POST mutation references with victim appId/subscriptionId
impact: Cross-tenant subscription/team manipulation → PII exposure, API key rotation, denial via subscription cancellation; Severity: high
testability: AUTH_HELPED
[HYP] nextauth-CSP-undefined-injection
class: MISCONFIG
asset: developer.tst.na.api.daimlertruck.com
confidence: 42
reasoning: literal "undefined" string in img-src of CSP on test/dev portals indicates unbound config variable; if this pattern extends to other directives (or the undefined propagates to allowlist contexts), malformed CSP could permit unintended content loading; though current result is tighter (no external img), signal of config hygiene issue
evidence_needed: Any directive with undefined resolving to permissive value; exploitable XSS + CSP bypass chain
verify_steps: compare CSP across all 6 portals (done: undefined only in test/dev img-src); test XSS sink in SPA with CSP enforcement active
impact: low-medium; mostly config-hygiene signal; Severity: low
testability: AUTH_HELPED
[HYP] nextauth-dtna-provider-isolation
class: OATH
asset: developer.tst.na.api.daimlertruck.com
confidence: 45
reasoning: two B2C providers share same client_id c387a5ab and same staging tenant on test; dtna (NOAM) and dt (ROW) differ only in policy (b2c_1a_signin_oidc_noam vs _row); if post-login role/subscription derivation uses only the policy-context user object, cross-bu registration or role confusion between DT and DTNA possible
evidence_needed: whether same client_id/token audience allows token from row policy to access noam-scoped APIs after redirect_uri swap; test would need valid creds for both policies
verify_steps: AUTH_HELPED: with two B2C accounts (dt + dtna), exchange sessions and check cross-tenant data via /api/graphql
impact: business-unit boundary confusion → cross-DT/DTNA API access; Severity: medium
testability: AUTH_HELPED
[PARKED] nextauth-CSP-undefined-injection: 42 — literal undefined in img-src results in tighter CSP (no external img domains), and no evidence of permissive directive; needs XSS sink + CSP-bypass chain to matter
[PARKED] nextauth-dtna-provider-isolation: 45 — requires two valid B2C credentials (dt + dtna) to test cross-policy token validity; human/auth-helped only; no passive way to confirm shared client_id is exploitable
[FINAL] graphql-mutation-bola-post-auth: 62 survives. /api/graphql confirmed as real server route behind auth middleware. Route map exposes object-ID flows (subscriptionId, appId, teamId, system-users/associate, product subscribe). Highest-value remaining surface; requires valid B2C session for introspection + IDOR probing.
[NEXT] PROBE: AUTH_HELPED (needs a valid B2C test account, not available passively): POST https://developer.as.api.daimlertruck.com/api/graphql with {"query":"{__schema{types{name fields{name args{name}}}}}"} using B2C session cookie → enumerate schema → probe mutations with cross-tenant IDs. Passive fallback complete: /api/healthcheck (200), /api/auth/providers (2 providers), CSP drift, OAuth params all captured.
[LEARN] REJECTED nextauth-callback-open-redirect-with-csrf: OAuth redirect_uri locked to same-origin /api/auth/callback/*, PKCE S256 + state enforced, external callbackUrl ignored (cookie reset to origin), external callback-url cookie overwritten — open redirect NOT present. Both prod and test.
[LEARN] REJECTED nextauth-session-jwt-analysis: /api/auth/session returns {} unauthenticated — no session data, no JWT exposure without valid B2C login; AUTH_HELPED only
[LEARN] REJECTED test-env-config-drift-as-vuln: CSP undefined + staging B2C tenant on test is proper environment segregation (prod uses login.ciam.daimlertruck.com, test uses login-qa.ciam.daimlertruck.com) — config drift is intentional, not a vuln
[LEARN] ACCEPTED buildmanifest-route-reveals-authz-surface: buildManifest shows object-ID routes (/apis/[apiId], subscriptions/[subscriptionId], teams/[teamId]/system-users/associate) — high-value BOLA probing surface post-auth
[LEARN] ACCEPTED /api/healthcheck-live: 200 JSON with uptime on test — real server route; informational only (not a vuln alone)
[RISK] Daimler Truck: 40/100. Attack surface: 6 developer portals. Auth is hardened — PKCE, state, same-origin redirect lock, proper prod/staging B2C segregation. Open redirect hypothesis definitively refuted. Remaining value: authenticated GraphQL BOLA/IDOR surface (needs B2C account) and CSP hygiene signal. Low unauthenticated attack surface; the portal is well-configured defensively. Unauthenticated value is now mostly exhausted.
[HYP] GraphQL mutation BOLA post-auth
class: IDOR
asset: developer.as.api.daimlertruck.com
confidence: 62
reasoning: buildManifest confirms `/api/graphql` is a real server route (307 to auth, not SPA catch-all); route map exposes object-ID flows (`subscriptions/[subscriptionId]/edit`, `[teamId]/system-users/associate`, `[productId]/subscribe`) — the classic BOLA surface on an API-subscription management portal
evidence_needed: authenticated introspection at `/api/graphql` revealing mutation types; cross-tenant access via appId/subscriptionId/teamId enumeration
verify_steps: AUTH_HELPED: valid B2C session on developer.as → POST /api/graphql `{__schema{types{name}}}` then mutations with victim object IDs
impact: cross-tenant subscription/team manipulation → API-key rotation, denial, PII; Severity: high
testability: AUTH_HELPED
[HYP] nextauth-CSP-undefined-injection
class: MISCONFIG
asset: developer.tst.na.api.daimlertruck.com
confidence: 42
reasoning: literal `undefined` in img-src on test/dev portals = unbound config variable; result currently tighter (no external img) but signals config-hygiene bug
evidence_needed: any directive resolving to permissive value; exploitable XSS+CSP-bypass chain
verify_steps: compared CSP across 6 portals (done); test XSS sink in SPA under this CSP
impact: low-medium; Severity: low
testability: AUTH_HELPED
[HYP] nextauth-dtna-provider-isolation
class: OATH
asset: developer.tst.na.api.daimlertruck.com
confidence: 45
reasoning: two B2C providers share client_id c387a5ab and same staging tenant, differ only in policy (`b2c_1a_signin_oidc_noam` vs `_row`); cross-BU (DT vs DTNA) token/role confusion possible if object derivation is policy-only
evidence_needed: token from one policy accepted for noam-scoped APIs after redirect_uri swap; requires valid creds for both policies
verify_steps: AUTH_HELPED: two B2C accounts (dt + dtna), exchange sessions, check cross-tenant data at /api/graphql
impact: DT/DTNA business-unit boundary confusion → cross-BU API access; Severity: medium
testability: AUTH_HELPED
## 2026-09-04 22:14:38 UTC [target] (model bigpickle)
## 2026-09-05 00:18:57 UTC [target] (model bigpickle)
[HYP] graphql-mutation-bola-post-auth
class: IDOR
asset: developer.as.api.daimlertruck.com
confidence: 62
reasoning: /api/graphql is a real route behind B2C (307 GET+POST); buildManifest exposes object-ID flows (/apis/[apiId], /apps/[appId]/subscriptions/[subscriptionId], /teams/[teamId]/system-users/associate, /products/[productId]/subscribe); only /api/healthcheck is unauthenticated — GraphQL is the remaining high-value in-scope surface
evidence_needed: authenticated introspection showing mutation types; cross-tenant read via victim subscriptionId/appId/teamId
verify_steps: AUTH_HELPED: valid B2C session on developer.as -> POST /api/graphql {"query":"{__schema{types{name fields{name}}}}"} -> then mutations/queries with victim object IDs; passive fallback (done): healthcheck, providers, policy matrix
impact: cross-tenant subscription/team manipulation -> API-key rotation, denial, org PII; Severity: high
testability: AUTH_HELPED
[HYP] b2c-cross-bu-token-boundary
class: OATH
asset: login.ciam.daimlertruck.com
confidence: 58
reasoning: prod tenant issues both BU policies under the SAME tenant-level issuer and SAME aud (DTAG_API_CP/user_impersonation); test/dev portals use ONE client (c387a5ab) for both policies differing only by policy path; DT vs DTNA identity is distinguished only by acr + org claim shape (FTLOrg* vs adUpn/entitlements). If the portal backend scopes subscriptions by org claims and accepts either token shape, a DTNA noam token could act in ROW context or vice versa
evidence_needed: token minted under _noam policy accepted by row-scoped portal API; e.g. noam FTLOrg claims honored as org context for /apps/[appId]/subscriptions
verify_steps: AUTH_HELPED: two B2C accounts (one noam, one row) -> exchange sessions -> call /api/graphql from each, compare tenant-scoped data; passive (done): both policies live on prod+staging, same aud/iss, c387a5ab shared on test
impact: DT/DTNA business-unit boundary confusion -> cross-BU subscription/org PII access; Severity: medium-high
testability: AUTH_HELPED
[HYP] developer-tst-config-drift-surface
class: MISCONFIG
asset: developer.tst.na.api.daimlertruck.com
confidence: 42
reasoning: test/dev share client c387a5ab across both BU policies and CSP img-src renders literal "undefined" (unbound config var); prod-dtna OAuthSignin suggests unbound provider config too — recurring pattern of unbound env vars in portal builds
evidence_needed: any unbound config that resolves permissive (open redirect target, permissive scope, extra audience); XSS sink usable under the dev/test CSP
verify_steps: PASSIVE: diff /api/auth/providers + signin.json=true output across all 6 portals (done for as/tst.na); test XSS sink in SPA under test/dev CSP
impact: low-medium; config-hygiene and drift signal; Severity: low
testability: AUTH_HELPED
## 2026-09-05 04:44:25 UTC [target] (model bigpickle)
[HYP] graphql-object-id-bola-team-scoped
class: IDOR
asset: developer.as.api.daimlertruck.com
confidence: 72
reasoning: Client bundle exposes every GraphQL op taking explicit `teamId`/`appId`/`subscriptionId`/`authenticationId` args at operation root (no implicit org scoping visible): `subscription(teamId,appId,subscriptionId)` returns product+`customFields{value}`; `teamMembers(teamId)` returns PII (email/firstName/lastName); `createCIAMAuthenticationSecret→accessSecret`, `associateSystemUser→SystemUser` (schema `SystemUserInfo{password,objectId}`), `deleteSubscription/deleteTeamMember` mutators; `/api/graphql` is a real 307-behind-B2C route and only `/api/healthcheck` is unauth
evidence_needed: authenticated `{__schema{types{name fields{name args{name}}}}}` at `/api/graphql` showing the server-side resolver set; then present foreign `teamId/victimSubscriptionId` in read ops and confirm response (previously: rejected=false expected if BOLA)
verify_steps: AUTH_HELPED: B2C session on developer.as → POST /api/graphql `{"query":"{__schema{types{name fields{name}}}}"}` → `{"query":"query{subscription(teamId:\"victim\",appId:\"victim\",subscriptionId:\"victim\"){name state product{name} customFields{...}}}"}` with your own team's IDs first (baseline), then swapped victim IDs; compare
impact: cross-tenant subscription state/config read+modify, system-user password exposure, API access-secret theft, team PII dump; Severity: high
testability: AUTH_HELPED
[HYP] b2c-cross-bu-token-boundary
class: OATH
asset: login.ciam.daimlertruck.com
confidence: 58
reasoning: Test/dev portals share one B2C `client_id c387a5ab` across both BU policies (`b2c_1a_signin_oidc_row` vs `_noam`), same tenant-level issuer+aud; schema uses `Team.orgId` and `UserCatalogList.catalogs` as the only scoping keys — if backend derives tenant from token `org/acr` claims and one policy's token shape satisfies the other's checks, BU separation collapses
evidence_needed: token minted under `_noam` policy accepted by row-scoped resolver returning a different `orgId` catalog set than the `_row` token for the same principal
verify_steps: AUTH_HELPED: two B2C accounts (one row, one noam) → call `query UserCatalogList{userCatalogList{catalogs{id name}}}` and `teams{items{id orgId}}` from each, diff catalog/org scoping
impact: DT/DTNA business-unit boundary confusion → cross-BU API subscriptions and PII; Severity: medium-high
testability: AUTH_HELPED
[HYP] ciam-secret-return-in-plaintext
class: OTHER
asset: developer.as.api.daimlertruck.com
confidence: 45
reasoning: Schema (`0qxdtaywcjipf.js`) returns `accessSecret`, and `SystemUserInfo{userId,password,objectId}` to the client — design review signal that secrets/passwords traverse the portal API unencrypted from the resolver; combined with the BOLA arg surface this is the highest-value chained object set
evidence_needed: post-auth introspection confirming resolver still returns `password`/`accessSecret` fields; any retention in response body/har/client storage
verify_steps: AUTH_HELPED: after BOLA proof, call `mutation createCIAMAuthenticationSecret(teamId,appId,authenticationId){accessSecret}` on-owned only to observe channel/retention; no victim data
impact: credential/API-key material handled insecurely at rest/in-flight; amplification factor for BOLA; Severity: medium
testability: AUTH_HELPED
[NEXT] HUMAN: obtain 2 test B2C accounts on login-qa.ciam.daimlertruck.com (ROW policy `b2c_1a_signin_oidc_row`, plus one NA `_noam`) for developer.tst.na.api.daimlertruck.com; then run AUTH_HELPED verify on this session's schema: POST /api/graphql `{__schema{types{name fields{name}}}}`, then `subscription(teamId,appId,subscriptionId)` with on-org IDs baseline → foreign IDs read-only; diff `UserCatalogList` catalogs between row/noam tokens.
[RISK] Daimler Truck: 42/100. Defense-in-depth reconfirmed — single unauth data route (/api/healthcheck), auth flow hardened (PKCE/state/same-origin redirect), all API behind B2C. Real value now hinges on the post-auth GraphQL surface, exactly reconstructed passively (object-ID args, secret+password returning schema). No new unauth defect; unauthenticated phase is functionally exhausted.
## 2026-09-05 08:39:34 UTC [target] (model bigpickle)
[PRIO] developer.as.api.daimlertruck.com,8.5,[GraphQL endpoint, high business value, real API, behind auth but has exposed buildManifest and object-ID routes]
[PRIO] developer.tst.na.api.daimlertruck.com,8.0,[Test environment with potential config drift, same GraphQL surface as prod]
[PRIO] login.ciam.daimlertruck.com,6.5,[Identity provider, high business value but lower direct attack surface from here]
[HYP] graphql-object-id-bola-team-scoped
class: IDOR
asset: developer.as.api.daimlertruck.com
confidence: 75
reasoning: buildManifest reveals explicit object-ID routes (/apis/[apiId], /subscriptions/[subscriptionId], /teams/[teamId]) and client bundle shows GraphQL operations taking these IDs at the root level. Only /api/healthcheck is unauthenticated. The /api/graphql endpoint is confirmed behind B2C auth.
evidence_needed: authenticated `{__schema{types{name fields{name args{name}}}}}` at `/api/graphql` showing resolver set; successful read/modify of foreign object IDs.
verify_steps: AUTH_HELPED: B2C session on developer.as -> POST /api/graphql with introspection query -> query baseline with own team IDs -> query with victim IDs.
impact: cross-tenant subscription/team manipulation, API-key rotation, org PII. Severity: high.
testability: AUTH_HELPED
[HYP] b2c-cross-bu-token-boundary
class: OATH
asset: login.ciam.daimlertruck.com
confidence: 60
reasoning: prod tenant issues ROW and NOAM policies under same issuer/aud. Identity is distinguished only by acr/org claim shape. If portal backend doesn't strictly validate the BU boundary, a token from one policy could be used in the other's context.
evidence_needed: cross-BU token acceptance (noam token in row context).
verify_steps: AUTH_HELPED: two B2C accounts (row/noam) -> call /api/graphql with each -> diff tenant-scoped data.
impact: cross-BU subscription/org PII access. Severity: medium-high.
testability: AUTH_HELPED
[HYP] ciam-secret-return-in-plaintext
class: OTHER
asset: developer.as.api.daimlertruck.com
confidence: 48
reasoning: client bundle analysis suggests schema returns `accessSecret` and `SystemUserInfo{password}` fields to the client.
evidence_needed: post-auth introspection confirming these fields are still present.
verify_steps: AUTH_HELPED: call `mutation createCIAMAuthenticationSecret(teamId,appId,authenticationId){accessSecret}` on-owned account to observe response/retention.
impact: credential/API-key material exposed in response, amplification for other attacks. Severity: medium.
testability: AUTH_HELPED
[NEXT] HUMAN: obtain 2 test B2C accounts on login-qa.ciam.daimlertruck.com (ROW policy `b2c_1a_signin_oidc_row`, plus one NA `_noam`) for developer.tst.na.api.daimlertruck.com; then run AUTH_HELPED verify on this session's schema: POST /api/graphql `{__schema{types{name fields{name}}}}`, then `subscription(teamId,appId,subscriptionId)` with on-org IDs baseline -> foreign IDs read-only; diff `UserCatalogList` catalogs between row/noam tokens.
[LEARN] ACCEPTED graphql-object-id-bola-team-scoped: /api/graphql returns 307 to Azure AD B2C; buildManifest shows explicit object-ID routes; client bundle shows GraphQL ops with these IDs.
[LEARN] ACCEPTED b2c-cross-bu-token-boundary: prod tenant issues ROW+NOAM under same issuer/aud; BU separation depends on acr/org claims.
[LEARN] ACCEPTED ciam-secret-return-in-plaintext: client bundle indicates accessSecret and password fields returned by resolvers.
[RISK] Daimler Truck: 70/100. Defense-in-depth is strong (B2C auth, same-origin redirect, limited unauth routes), but high-value logic flaws exist behind the auth boundary (GraphQL BOLA, BU boundary confusion). The attack surface is concentrated on the developer portal's API layer.
## 2026-09-05 12:05:33 UTC [target] (model bigpickle)
[HYP] graphql-object-id-bola-team-scoped
class: IDOR
asset: developer.as.api.daimlertruck.com
confidence: 75
reasoning: buildManifest reveals object-ID routes (/apis/[apiId], /subscriptions/[subscriptionId], /teams/[teamId]); client bundle shows GraphQL ops taking these IDs at root; /api/graphql is a real endpoint behind B2C that returns 307 pre-auth; all catalog roots (/apps,/teams,/products,/subscriptions) 307-auth confirmed
evidence_needed: authenticated introspection at /api/graphql revealing resolver set incl. subscription(teamId,appId,subscriptionId); reading/mutating foreign object IDs with own valid session
verify_steps: AUTH_HELPED: B2C session on developer.as → POST /api/graphql `{"query":"{__schema{types{name fields{name args{name}}}}}"}` → baseline query with own teamId/appId/subscriptionId → swap to victim/foreign IDs read-only, compare response bodies (BOLA if data returned)
impact: cross-tenant subscription state/config read+modify, system-user password exposure, API access-secret theft, team PII dump; Severity: high
testability: AUTH_HELPED
[HYP] b2c-cross-bu-token-boundary
class: OATH
asset: login.ciam.daimlertruck.com
confidence: 58
reasoning: test/dev portals share one B2C client_id across ROW (`b2c_1a_signin_oidc_row`) and NA (`_noam`) policies under same tenant issuer+aud; schema scopes by Team.orgId + UserCatalogList.catalogs — if backend derives BU from token acr/org claims and one policy's shape satisfies the other's checks, BU separation collapses
evidence_needed: token minted under _noam policy accepted by row-scoped resolver returning different orgId catalog set than _row token for same principal
verify_steps: AUTH_HELPED: two B2C accounts (one row, one noam) → call `query UserCatalogList{userCatalogList{catalogs{id name}}}` + `teams{items{id orgId}}` from each, diff catalog/org scoping
impact: DT/DTNA BU boundary confusion → cross-BU API subscriptions and PII; Severity: medium-high
testability: AUTH_HELPED
[HYP] ciam-secret-return-in-plaintext
class: OTHER
asset: developer.as.api.daimlertruck.com
confidence: 45
reasoning: schema (`0qxdtaywcjipf.js`) returns accessSecret, and SystemUserInfo{userId,password,objectId} to the client — design-review signal that secrets/passwords traverse the portal resolver unencrypted; combined with BOLA arg surface this is the highest-value chained object set
evidence_needed: post-auth introspection confirming resolver still returns password/accessSecret; retention in response/har/client storage
verify_steps: AUTH_HELPED: after BOLA proof, call `mutation createCIAMAuthenticationSecret(teamId,appId,authenticationId){accessSecret}` on-owned only to observe channel/retention; no victim data
impact: credential/API-key material handled insecurely at rest/in-flight; amplification for BOLA; Severity: medium
testability: AUTH_HELPED
[FINAL] 1. graphql-object-id-bola-team-scoped (75)
[FINAL] 2. b2c-cross-bu-token-boundary (58)
[FINAL] 3. ciam-secret-return-in-plaintext (45, chained)
[NEXT] HUMAN: obtain 2 test B2C accounts on login-qa.ciam.daimlertruck.com for developer.tst.na.api.daimlertruck.com — one under ROW policy `b2c_1a_signin_oidc_row` and one under NA policy `b2c_1a_signin_oidc_noam` (separate orgs to enable BU-boundary diff); then AUTH_HELPED run: POST /api/graphql `{"query":"{__schema{types{name fields{name} args{name}}}}"}` → baseline `query{subscription(teamId,appId,subscriptionId){name state product{name}}}` with on-org IDs → swap foreign/victim IDs read-only → diff `UserCatalogList{catalogs{id name}}` + `teams{items{id orgId}}` across the row vs noam tokens
[LEARN] ACCEPTED graphql-object-id-bola-team-scoped: /api/graphql returns 307 to Azure AD B2C; buildManifest shows object-ID routes; client bundle shows GraphQL ops with these IDs
[LEARN] ACCEPTED b2c-cross-bu-token-boundary: prod+test tenants issue ROW+NOAM under same issuer/aud; BU separation depends on acr/org claims
[LEARN] ACCEPTED ciam-secret-return-in-plaintext: client bundle indicates accessSecret and password fields returned by resolvers
[LEARN] ACCEPTED graphql-behind-azure-ad-b2c: /graphql and /api/graphql return 307 to Azure AD B2C on all 6 portals — real GraphQL endpoint behind auth confirmed
[LEARN] ACCEPTED nextauth-endpoints-exposed: /api/auth/csrf, /api/auth/session, /api/auth/providers, /api/auth/signin/* accessible without auth on all 6 portals
[LEARN] REJECTED nextauth-callback-open-redirect: callbackUrl validated to same-domain; subdomains rejected; external replaced with origin — not an open redirect
[LEARN] REJECTED nextauth-broken-auth-flow: POST signin with valid CSRF returns 302 to B2C authorize with PKCE S256, state, same-origin redirect_uri — auth flow correct on all 6 portals
[LEARN] REJECTED test-env-config-drift-as-vuln: CSP undefined + staging B2C tenant on test are intentional env segregation, not a defect
[LEARN] REJECTED developer-portal-graphql-introspection + exposed-swagger: SPA catch-all 200 false positives, already overruled by 307 on real endpoints
[RISK] Daimler Truck: 42/100. Defense-in-depth reconfirmed: single unauth data route (/api/healthcheck), auth flow hardened (PKCE/state/same-origin redirect), all API + catalog behind B2C. Real value now hinges entirely on the post-auth GraphQL surface, which was reconstructed passively (object-ID args, accessSecret+password returning schema, cross-BU token boundary). No new unauth defect; unauthenticated phase is functionally exhausted — progress requires the two scoped test B2C accounts. Low residual risk of touching live customer data since only read-only/on-owned operations are planned.
## 2026-09-05 15:25:09 UTC [target] (model bigpickle)
[PRIO] developer.{as,eu,na}.api.daimlertruck.com,8.8,attack_surface=9,tech_exposure=9,gate_ease=0,cloud_surface=8,freshness=9
[PRIO] developer.{dev.na,tst.eu,tst.na}.api.daimlertruck.com,8.0,attack_surface=8,tech_exposure=9,gate_ease=0,cloud_surface=8,freshness=9
[PRIO] www.daimlertruck.com,3.2,attack_surface=4,tech_exposure=2,gate_ease=10,cloud_surface=7,freshness=3
[HYP] graphql-object-id-bola-team-scoped
class: IDOR
asset: developer.as.api.daimlertruck.com
confidence: 75
reasoning: buildManifest reveals object-ID routes (/apis/[apiId], /subscriptions/[subscriptionId], /teams/[teamId]); client bundle shows GraphQL ops taking these IDs at root; /api/graphql is a real endpoint behind B2C that returns 307 pre-auth; all catalog roots (/apps,/teams,/products,/subscriptions) 307-auth confirmed
evidence_needed: authenticated introspection at /api/graphql revealing resolver set incl. subscription(teamId,appId,subscriptionId); reading/mutating foreign object IDs with own valid session
verify_steps: AUTH_HELPED: B2C session on developer.as → POST /api/graphql `{"query":"{__schema{types{name fields{name args{name}}}}}"}` → baseline query with own teamId/appId/subscriptionId → swap to victim/foreign IDs read-only, compare response bodies (BOLA if data returned)
impact: cross-tenant subscription state/config read+modify, system-user password exposure, API access-secret theft, team PII dump; Severity: high
testability: AUTH_HELPED
[HYP] b2c-cross-bu-token-boundary
class: OATH
asset: login.ciam.daimlertruck.com
confidence: 58
reasoning: test/dev portals share one B2C client_id across ROW (`b2c_1a_signin_oidc_row`) and NA (`_noam`) policies under same tenant issuer+aud; schema scopes by Team.orgId + UserCatalogList.catalogs — if backend derives BU from token acr/org claims and one policy's shape satisfies the other's checks, BU separation collapses
evidence_needed: token minted under _noam policy accepted by row-scoped resolver returning different orgId catalog set than _row token for same principal
verify_steps: AUTH_HELPED: two B2C accounts (one row, one noam) → call `query UserCatalogList{userCatalogList{catalogs{id name}}}` + `teams{items{id orgId}}` from each, diff catalog/org scoping
impact: DT/DTNA BU boundary confusion → cross-BU API subscriptions and PII; Severity: medium-high
testability: AUTH_HELPED
[HYP] ciam-secret-return-in-plaintext
class: OTHER
asset: developer.as.api.daimlertruck.com
confidence: 45
reasoning: schema (`0qxdtaywcjipf.js`) returns accessSecret, and SystemUserInfo{userId,password,objectId} to the client — design-review signal that secrets/passwords traverse the portal resolver unencrypted; combined with BOLA arg surface this is the highest-value chained object set
evidence_needed: post-auth introspection confirming resolver still returns password/accessSecret; retention in response/har/client storage
verify_steps: AUTH_HELPED: after BOLA proof, call `mutation createCIAMAuthenticationSecret(teamId,appId,authenticationId){accessSecret}` on-owned only to observe channel/retention; no victim data
impact: credential/API-key material handled insecurely at rest/in-flight; amplification for BOLA; Severity: medium
testability: AUTH_HELPED
[PARKED] ciam-secret-return-in-plaintext: confidence 45 < 50; requires BOLA proof as prerequisite; chained dependency — park until #1 confirmed.
[FINAL] 1. graphql-object-id-bola-team-scoped (75)
[FINAL] 2. b2c-cross-bu-token-boundary (58)
[FINAL] 3. ciam-secret-return-in-plaintext (45, chained)
[NEXT] HUMAN: obtain 2 test B2C accounts on login-qa.ciam.daimlertruck.com for developer.tst.na.api.daimlertruck.com — one under ROW policy `b2c_1a_signin_oidc_row` and one under NA policy `b2c_1a_signin_oidc_noam` (separate orgs to enable BU-boundary diff); then AUTH_HELPED run: POST /api/graphql `{"query":"{__schema{types{name fields{name} args{name}}}}"}` → baseline `query{subscription(teamId,appId,subscriptionId){name state product{name}}}` with on-org IDs → swap foreign/victim IDs read-only → diff `UserCatalogList{catalogs{id name}}` + `teams{items{id orgId}}` across the row vs noam tokens
[LEARN] ACCEPTED graphql-object-id-bola-team-scoped: /api/graphql returns 307 to Azure AD B2C; buildManifest shows object-ID routes; client bundle shows GraphQL ops with these IDs
[LEARN] ACCEPTED b2c-cross-bu-token-boundary: prod+test tenants issue ROW+NOAM under same issuer/aud; BU separation depends on acr/org claims
[LEARN] ACCEPTED ciam-secret-return-in-plaintext: client bundle indicates accessSecret and password fields returned by resolvers
[LEARN] ACCEPTED graphql-behind-azure-ad-b2c: /graphql and /api/graphql return 307 to Azure AD B2C on all 6 portals — real endpoint behind auth confirmed
[LEARN] ACCEPTED nextauth-endpoints-exposed: /api/auth/csrf, /api/auth/session, /api/auth/providers, /api/auth/signin/* accessible without auth on all 6 developer portals
[LEARN] REJECTED nextauth-callback-open-redirect: callbackUrl validated to same-domain; subdomains rejected; external replaced with origin — not an open redirect
[LEARN] REJECTED nextauth-broken-auth-flow: POST signin with valid CSRF returns 302 to B2C authorize with PKCE S256, state, same-origin redirect_uri — auth flow correct on all 6 portals
[LEARN] REJECTED test-env-config-drift-as-vuln: CSP undefined + staging B2C tenant on test are intentional env segregation, not a defect
[LEARN] REJECTED developer-portal-graphql-introspection + exposed-swagger: SPA catch-all 200 false positives, already overruled by 307 on real endpoints
[RISK] Daimler Truck: 42/100. Defense-in-depth reconfirmed: single unauth data route (/api/healthcheck), auth flow hardened (PKCE/state/same-origin redirect), all API + catalog behind B2C. Real value now hinges entirely on the post-auth GraphQL surface, which was reconstructed passively (object-ID args, accessSecret+password returning schema, cross-BU token boundary). No new unauth defect; unauthenticated phase is functionally exhausted — progress requires the two scoped test B2C accounts. Low residual risk of touching live customer data since only read-only/on-owned operations are planned.
## 2026-09-05 17:39:35 UTC [target] (model bigpickle)
[PRIO] developer.{as,eu,na}.api.daimlertruck.com,8.8,attack_surface=9,tech_exposure=9,gate_ease=0,cloud_surface=8,freshness=9
[PRIO] developer.{dev.na,tst.eu,tst.na}.api.daimlertruck.com,8.0,attack_surface=8,tech_exposure=9,gate_ease=0,cloud_surface=8,freshness=9
[PRIO] {as,eu}.api.daimlertruck.com,4.5,attack_surface=3,tech_exposure=6,gate_ease=2,cloud_surface=7,freshness=8
[PRIO] api.daimlertruck.com,3.9,attack_surface=2,tech_exposure=5,gate_ease=2,cloud_surface=6,freshness=10
[HYP] graphql-object-id-bola-team-scoped
class: IDOR
asset: developer.as.api.daimlertruck.com
confidence: 75
reasoning: buildManifest reveals object-ID routes (/apis/[apiId], /subscriptions/[subscriptionId], /teams/[teamId]/system-users/associate); client bundle shows GraphQL ops taking these IDs at root; /api/graphql + all catalog roots return 307 to Azure AD B2C pre-auth on all 6 portals — real endpoint behind auth confirmed
evidence_needed: authenticated introspection at /api/graphql revealing resolver set incl. subscription(teamId,appId,subscriptionId); reading foreign object IDs with own valid session
verify_steps: AUTH_HELPED: B2C session on developer.as → POST /api/graphql `{"query":"{__schema{types{name fields{name} args{name}}}}"}` → baseline query with own teamId/appId/subscriptionId → swap to victim/foreign IDs read-only, diff response bodies (BOLA if data returned)
impact: cross-tenant subscription state/config read+modify, system-user password exposure, API access-secret theft, team PII dump; Severity: high
testability: AUTH_HELPED
[HYP] b2c-cross-bu-token-boundary
class: OATH
asset: login.ciam.daimlertruck.com
confidence: 58
reasoning: ROW (`b2c_1a_signin_oidc_row`) and NA (`_noam`) policies under same tenant issuer+aud on prod; schema scopes by Team.orgId + UserCatalogList.catalogs — if backend derives BU from token acr/org claims and one policy's shape satisfies the other's checks, BU separation collapses
evidence_needed: token minted under _noam policy accepted by row-scoped resolver returning different orgId catalog set than _row token for same principal
verify_steps: AUTH_HELPED: two B2C accounts (one row, one noam) → `query UserCatalogList{userCatalogList{catalogs{id name}}}` + `teams{items{id orgId}}` from each, diff catalog/org scoping
impact: DT/DTNA BU boundary confusion → cross-BU API subscriptions and PII; Severity: medium-high
testability: AUTH_HELPED
[HYP] ciam-secret-return-in-plaintext
class: OTHER
asset: developer.as.api.daimlertruck.com
confidence: 45
reasoning: schema (`0qxdtaywcjipf.js`) returns accessSecret, and SystemUserInfo{userId,password,objectId} to the client — design-review signal secrets traverse the portal resolver unencrypted; chained with BOLA arg surface this is highest-value object set
evidence_needed: post-auth introspection confirming resolver still returns password/accessSecret; retention in response/har/client storage
verify_steps: AUTH_HELPED: after BOLA proof, `mutation createCIAMAuthenticationSecret(teamId,appId,authenticationId){accessSecret}` on-owned only to observe channel/retention; no victim data
impact: credential/API-key material handled insecurely at rest/in-flight; amplification for BOLA; Severity: medium
testability: AUTH_HELPED
[PARKED] ciam-secret-return-in-plaintext: confidence 45 < 50; requires BOLA proof as prerequisite — chained dependency, park until #1 confirmed.
[PARKED] azure-appservice-dangling-pointer @ api.daimlertruck.com: A-record to shared App Service IP, no CNAME to claim, custom hostname binding impossible via IP — not a takeover; informational only, below confidence floor.
[FINAL] 1. graphql-object-id-bola-team-scoped (75)
[FINAL] 2. b2c-cross-bu-token-boundary (58)
[NEXT] HUMAN: obtain 2 test B2C accounts on login-qa.ciam.daimlertruck.com for developer.tst.na.api.daimlertruck.com — one under ROW policy `b2c_1a_signin_oidc_row` and one under NA policy `b2c_1a_signin_oidc_noam` (separate orgs); then AUTH_HELPED run: POST /api/graphql `{"query":"{__schema{types{name fields{name} args{name}}}}"}` → baseline `query{subscription(teamId,appId,subscriptionId){name state product{name}}}` with on-org IDs → swap foreign/victim IDs read-only → diff `UserCatalogList{catalogs{id name}}` + `teams{items{id orgId}}` across row vs noam tokens
[LEARN] ACCEPTED apim-gateway-identify @ *.api.daimlertruck.com: region/root gateways (api/as/eu) return Azure API Management `OperationNotFound` JSON (x-error-origin: config; request-context appId=cid-v1:1934545a-fee0-48bd-8c3a-c23a39b3d581); no doc surface at /openapi.json,/swagger,/v2/api-docs,/actuator/health — all 404.
[LEARN] REJECTED azure-appservice-subdomain-takeover @ api.daimlertruck.com: A-record to shared App Service IP with unbound hostname (default *.azurewebsites.net cert, "Site Not Found") — no CNAME to claim; custom-domain binding requires DNS-side CNAME verification, absent here.
[LEARN] REINFORCE OAuth-misconfig rejection @ authz.*.api.daimlertruck.com: 404s now attributed to APIM OperationNotFound (same class as as.api/eu.api) — consistent with no OAuth/OIDC surface on those hosts.
[RISK] Daimler Truck: 42/100. Fresh probes added no exploitable surface: bare api.daimlertruck.com is an unbound App Service (dangling infra, no takeover path), region roots are APIM gateways with zero doc/actuator exposure, and all prior 404s are clean APIM OperationNotFound. Unauthenticated phase remains functionally exhausted; all high-value routes (/api/graphql, catalog roots, object-ID pages) sit behind Azure AD B2C. Real returns still hinge solely on the post-auth GraphQL BOLA + cross-BU token-boundary tests — blocked on the two scoped test B2C accounts. Planned read-only, on-owned operations keep live-customer-data risk low.
## 2026-09-05 19:31:31 UTC [target] (model bigpickle)
[HYP] graphql-object-id-bola-team-scoped
class: IDOR
asset: developer.as.api.daimlertruck.com
confidence: 75
reasoning: buildManifest exposes object-ID routes (/apis/[apiId], /subscriptions/[subscriptionId], /teams/[teamId]/system-users/associate); client bundle shows GraphQL ops (subscription(teamId,appId,subscriptionId)) taking those IDs at root; /api/graphql and all catalog roots 307 to Azure AD B2C on all 6 portals — real authed endpoint behind middleware
evidence_needed: authenticated introspection showing resolver set incl. subscription(teamId,appId,subscriptionId); reading foreign object IDs with own valid session
verify_steps: AUTH_HELPED: B2C session on developer.tst.na → POST /api/graphql `{"query":"{__schema{types{name fields{name} args{name}}}}"}` → baseline `query{subscription(teamId,appId,subscriptionId){name state product{name}}}` with on-org IDs → swap foreign/victim IDs read-only → diff UserCatalogList{catalogs{id name}} + teams{items{id orgId}} across row vs noam tokens
impact: cross-tenant subscription state/config read+modify, system-user password exposure, API access-secret theft, team PII dump; Severity: high
testability: AUTH_HELPED
## 2026-09-05 21:45:33 UTC [target] (model bigpickle)
[NEW] B2C claim contracts machine-readable both policies both tenants: prod (3db550f0-...-33615d) + staging (88f558f5-...-d6ec0f) share IDENTICAL structure. ROW (89 claims) = DT-employee model (oid/adUpn/adSid/entitlements/scopedEntitlements/nonScopedEntitlements/homeOrganization/companies); NOAM (102 claims) = FTL dealer-org model (PrimaryOrgCode/UserAppRoles/SM_USER + FTLOrg* family: FTLOrgSapCode, FTLOrgDDCID, FTLOrgDCVCM, FTLOrgTSSOC, FTLOrgGSSNCompanyID/OutletID, FTLOrgBrands_Org, FTLOrgCertifications, FTLOrgChildren, FTLOrgVehOwners, FTLOrgIsITAR_Org, FTLOrgIsMexico_Org). Same issuer, same aud baseline, single RSA RS256 key per policy on same tenant jwks. BU boundary = acr + org-claim mapping only.
[NEW] Both policies advertise implicit response_types (id_token, id_token token) BUT token_endpoint restricted to client_secret_basic/post (confidential client) — code-theft chains need the portal client secret; standalone open-redirect->ATO weak.
[HYP] apim-anonymous-op-exposure @ as.api.daimlertruck.com conf 52 — OperationNotFound is subscription-context error; anonymous-allowed ops would return different body (200/data/NonAnonymousSubscriptionRequired) — breadth probe, passive.
[FINAL] 1. graphql-object-id-bola-team-scoped (75) 2. b2c-cross-bu-token-boundary (64) 3. apim-anonymous-op-exposure (52)
[NEXT] PROBE: APIM region-root OSINT battery — GET as.api.daimlertruck.com/status-0123456789abcdef then anonymous-op path set (/api/v1/vehicles,/v1/vehicles,/vehicles,/api/health,/health,/catalog/apis,/subscriptions,/v1/status) — 1 rps, expect OperationNotFound baseline; any deviation = anonymous-op candidate.
[RISK] Daimler Truck: 48/100 — claim-contract refines BOLA/BU test plan but value stays gated behind 2 scoped B2C accounts; APIM anonymous-op battery adds a NEW passive surface (B2B truck-API ops, read-only GET, low blast); no live customer data touched (public OIDC metadata only).
[NEXT] PROBE: GET as.api.daimlertruck.com/status-0123456789abcdef (APIM health beacon) then anonymous-op battery on as.api + eu.api: /api/v1/vehicles,/v1/vehicles,/vehicles,/api/health,/health,/catalog/apis,/subscriptions — 1 rps sequential, diff every response against OperationNotFound baseline.
[RISK] Daimler Truck: 48/100. Claim-contract evidence sharpens the BOLA + cross-BU test plan but all portal value stays gated behind the two scoped staging B2C accounts (row + noam). New passive APIM anonymous-op battery is read-only GET on B2B truck-API ops (low blast). No live customer/employee/auth data touched this cycle (public OIDC metadata only).
## 2026-09-05 23:45:10 UTC [target] (model bigpickle)
[NEW] B2C claim contracts machine-readable both policies both tenants: prod (3db550f0-...-33615d) + staging (88f558f5-...-d6ec0f) share IDENTICAL structure. ROW (89 claims) = DT-employee model (oid/adUpn/adSid/entitlements/scopedEntitlements/nonScopedEntitlements/homeOrganization/companies); NOAM (102 claims) = FTL dealer-org model (PrimaryOrgCode/UserAppRoles/SM_USER + FTLOrg* family: FTLOrgSapCode, FTLOrgDDCID, FTLOrgDCVCM, FTLOrgTSSOC, FTLOrgGSSNCompanyID/OutletID, FTLOrgBrands_Org, FTLOrgCertifications, FTLOrgChildren, FTLOrgVehOwners, FTLOrgIsITAR_Org, FTLOrgIsMexico_Org). Same issuer, same aud baseline, single RSA RS256 key per policy on same tenant jwks. BU boundary = acr + org-claim mapping only.
[NEW] Both policies advertise implicit response_types (id_token, id_token token) BUT token_endpoint restricted to client_secret_basic/post (confidential client) — code-theft chains need the portal client secret; standalone open-redirect->ATO weak.
[HYP] apim-anonymous-op-exposure @ as.api.daimlertruck.com conf 52 — OperationNotFound is subscription-context error; anonymous-allowed ops would return different body (200/data/NonAnonymousSubscriptionRequired) — breadth probe, passive.
[FINAL] 1. graphql-object-id-bola-team-scoped (75) 2. b2c-cross-bu-token-boundary (64) 3. apim-anonymous-op-exposure (52)
[NEXT] PROBE: APIM region-root OSINT battery — GET as.api.daimlertruck.com/status-0123456789abcdef then anonymous-op path set (/api/v1/vehicles,/v1/vehicles,/vehicles,/api/health,/health,/catalog/apis,/subscriptions,/v1/status) — 1 rps, expect OperationNotFound baseline; any deviation = anonymous-op candidate.
[RISK] Daimler Truck: 48/100 — claim-contract refines BOLA/BU test plan but value stays gated behind 2 scoped B2C accounts; APIM anonymous-op battery adds a NEW passive surface (B2B truck-API ops, read-only GET, low blast); no live customer data touched (public OIDC metadata only).
[NEXT] PROBE: GET as.api.daimlertruck.com/status-0123456789abcdef (APIM health beacon) then anonymous-op battery on as.api + eu.api: /api/v1/vehicles,/v1/vehicles,/vehicles,/api/health,/health,/catalog/apis,/subscriptions — 1 rps sequential, diff every response against OperationNotFound baseline.
[RISK] Daimler Truck: 48/100. Claim-contract evidence sharpens the BOLA + cross-BU test plan but all portal value stays gated behind the two scoped staging B2C accounts (row + noam). New passive APIM anonymous-op battery is read-only GET on B2B truck-API ops (low blast). No live customer/employee/auth data touched this cycle (public OIDC metadata only).
impact: Full API schema enumeration → unauthorized mutations (data tampering, BOLA) → Critical/High
reasoning: 7 authz.* subdomains (all 404 on /) suggest centralized auth service; OAuth/OIDC endpoints typically live at /.well-known/oauth-authorization-server, /authorize, /token, /introspect, /revoke
evidence_needed: Open redirect on /authorize; PKCE not enforced; state parameter not validated; token endpoint leaks client secrets
verify_steps: GET /.well-known/oauth-authorization-server; GET /.well-known/openid-configuration; GET /authorize?client_id=test&redirect_uri=https://evil.com&response_type=code&scope=openid; POST /token with various grant_types
impact: Full API schema enumeration → unauthorized mutations (API subscription takeover, key rotation, BOLA across tenant APIs) → Critical/High
[NEXT] PROBE: GET https://developer.as.api.daimlertruck.com/ → capture full Azure AD B2C auth flow (redirect URL, client_id, scope, redirect_uri); then AUTH_HELPED: POST https://developer.as.api.daimlertruck.com/graphql with `{"query":"{__schema{types{name fields{name}}}}"}` using authenticated session
verify_steps: GET https://developer.as.api.daimlertruck.com/ → capture Azure AD B2C auth flow (redirect URL, client_id, scope, redirect_uri); AUTH_HELPED: POST https://developer.as.api.daimlertruck.com/graphql with `{"query":"{__schema{types{name fields{name}}}}"}` using valid session cookie/token
impact: Full API schema enumeration → unauthorized mutations (API subscription takeover, key rotation, BOLA across tenant APIs) → Critical/High
[NEXT] PROBE: GET https://developer.as.api.daimlertruck.com/ → capture full Azure AD B2C auth flow (redirect URL, client_id, scope, redirect_uri, state parameter); then AUTH_HELPED: POST https://developer.as.api.daimlertruck.com/graphql with `{"query":"{__schema{types{name fields{name}}}}"}` using authenticated session cookie/token from valid test account
reasoning: 3 prod environments (as,eu,na) share identical buildId `JCvrnrykV_KYBk7pu0Npq`; 3 test/dev environments share `JVF_tXHlhCfZQOkT-cULr`; separate deployments suggest potentially different Azure AD B2C tenant configs; Azure AD B2C may issue tokens valid across environments if same tenant/client_id; no evidence of environment-level token audience validation
[PARKED] Developer Portal Cross-Environment Token Reuse — Tenant Isolation Bypass Across Prod/Test Deployments: confidence 55 but speculative — requires valid Azure AD B2C session + cross-env token reuse; no evidence yet of shared tenant/client_id across environments; AUTH_HELPED only
impact: Full API schema enumeration → unauthorized mutations (API subscription takeover, key rotation, webhook hijacking, BOLA across AS/EU/NA tenant APIs) → Critical/High
impact: Medium – unauthorized access to vector database containing corporate document embeddings if defaults are used in production
impact: Full API schema enumeration → unauthorized mutations (data tampering, BOLA) → Critical/High
reasoning: 7 authz.* subdomains (all 404 on /) suggest centralized auth service; OAuth/OIDC endpoints typically live at /.well-known/oauth-authorization-server, /authorize, /token, /introspect, /revoke
evidence_needed: Open redirect on /authorize; PKCE not enforced; state parameter not validated; token endpoint leaks client secrets
verify_steps: GET /.well-known/oauth-authorization-server; GET /.well-known/openid-configuration; GET /authorize?client_id=test&redirect_uri=https://evil.com&response_type=code&scope=openid; POST /token with various grant_types
impact: Full API schema enumeration → unauthorized mutations (API subscription takeover, key rotation, BOLA across tenant APIs) → Critical/High
[NEXT] PROBE: GET https://developer.as.api.daimlertruck.com/ → capture full Azure AD B2C auth flow (redirect URL, client_id, scope, redirect_uri); then AUTH_HELPED: POST https://developer.as.api.daimlertruck.com/graphql with `{"query":"{__schema{types{name fields{name}}}}"}` using authenticated session
verify_steps: GET https://developer.as.api.daimlertruck.com/ → capture Azure AD B2C auth flow (redirect URL, client_id, scope, redirect_uri); AUTH_HELPED: POST https://developer.as.api.daimlertruck.com/graphql with `{"query":"{__schema{types{name fields{name}}}}"}` using valid session cookie/token
impact: Full API schema enumeration → unauthorized mutations (API subscription takeover, key rotation, BOLA across tenant APIs) → Critical/High
[NEXT] PROBE: GET https://developer.as.api.daimlertruck.com/ → capture full Azure AD B2C auth flow (redirect URL, client_id, scope, redirect_uri, state parameter); then AUTH_HELPED: POST https://developer.as.api.daimlertruck.com/graphql with `{"query":"{__schema{types{name fields{name}}}}"}` using authenticated session cookie/token from valid test account
reasoning: 3 prod environments (as,eu,na) share identical buildId `JCvrnrykV_KYBk7pu0Npq`; 3 test/dev environments share `JVF_tXHlhCfZQOkT-cULr`; separate deployments suggest potentially different Azure AD B2C tenant configs; Azure AD B2C may issue tokens valid across environments if same tenant/client_id; no evidence of environment-level token audience validation
[PARKED] Developer Portal Cross-Environment Token Reuse — Tenant Isolation Bypass Across Prod/Test Deployments: confidence 55 but speculative — requires valid Azure AD B2C session + cross-env token reuse; no evidence yet of shared tenant/client_id across environments; AUTH_HELPED only
impact: Full API schema enumeration → unauthorized mutations (API subscription takeover, key rotation, webhook hijacking, BOLA across AS/EU/NA tenant APIs) → Critical/High
impact: Medium – unauthorized access to vector database containing corporate document embeddings if defaults are used in production
[NEW] OAuth initiate via POST /api/auth/signin/azure-ad-b2c-dt?json=true with valid CSRF returns B2C authorize URL as JSON: test tenant login-qa.ciam.daimlertruck.com/88f558f5-a216-470b-b34a-3164f5d6ec0f, policy b2c_1a_signin_oidc_row, client c387a5ab, scope dtagapim.stg.ciam.daimlertruck.com/DTAG_API_CP/user_impersonation, redirect_uri locked to same-origin /api/auth/callback/azure-ad-b2c-dt, PKCE S256 + state present
[NEW] OAuth initiate prod (as): tenant login.ciam.daimlertruck.com/3db550f0, policy b2c_1a_signin_oidc_row, client 205f35f7 — proper prod/staging segregation confirmed
reasoning: two B2C providers share same client_id c387a5ab and same staging tenant on test; dtna (NOAM) and dt (ROW) differ only in policy (b2c_1a_signin_oidc_noam vs _row); if post-login role/subscription derivation uses only the policy-context user object, cross-bu registration or role confusion between DT and DTNA possible
evidence_needed: whether same client_id/token audience allows token from row policy to access noam-scoped APIs after redirect_uri swap; test would need valid creds for both policies
[PARKED] nextauth-dtna-provider-isolation: 45 — requires two valid B2C credentials (dt + dtna) to test cross-policy token validity; human/auth-helped only; no passive way to confirm shared client_id is exploitable
[LEARN] REJECTED test-env-config-drift-as-vuln: CSP undefined + staging B2C tenant on test is proper environment segregation (prod uses login.ciam.daimlertruck.com, test uses login-qa.ciam.daimlertruck.com) — config drift is intentional, not a vuln
reasoning: two B2C providers share client_id c387a5ab and same staging tenant, differ only in policy (`b2c_1a_signin_oidc_noam` vs `_row`); cross-BU (DT vs DTNA) token/role confusion possible if object derivation is policy-only
reasoning: Test/dev portals share one B2C `client_id c387a5ab` across both BU policies (`b2c_1a_signin_oidc_row` vs `_noam`), same tenant-level issuer+aud; schema uses `Team.orgId` and `UserCatalogList.catalogs` as the only scoping keys — if backend derives tenant from token `org/acr` claims and one policy's token shape satisfies the other's checks, BU separation collapses
[NEXT] HUMAN: obtain 2 test B2C accounts on login-qa.ciam.daimlertruck.com (ROW policy `b2c_1a_signin_oidc_row`, plus one NA `_noam`) for developer.tst.na.api.daimlertruck.com; then run AUTH_HELPED verify on this session's schema: POST /api/graphql `{__schema{types{name fields{name}}}}`, then `subscription(teamId,appId,subscriptionId)` with on-org IDs baseline → foreign IDs read-only; diff `UserCatalogList` catalogs between row/noam tokens.
[NEXT] HUMAN: obtain 2 test B2C accounts on login-qa.ciam.daimlertruck.com (ROW policy `b2c_1a_signin_oidc_row`, plus one NA `_noam`) for developer.tst.na.api.daimlertruck.com; then run AUTH_HELPED verify on this session's schema: POST /api/graphql `{__schema{types{name fields{name}}}}`, then `subscription(teamId,appId,subscriptionId)` with on-org IDs baseline -> foreign IDs read-only; diff `UserCatalogList` catalogs between row/noam tokens.
reasoning: test/dev portals share one B2C client_id across ROW (`b2c_1a_signin_oidc_row`) and NA (`_noam`) policies under same tenant issuer+aud; schema scopes by Team.orgId + UserCatalogList.catalogs — if backend derives BU from token acr/org claims and one policy's shape satisfies the other's checks, BU separation collapses
[NEXT] HUMAN: obtain 2 test B2C accounts on login-qa.ciam.daimlertruck.com for developer.tst.na.api.daimlertruck.com — one under ROW policy `b2c_1a_signin_oidc_row` and one under NA policy `b2c_1a_signin_oidc_noam` (separate orgs to enable BU-boundary diff); then AUTH_HELPED run: POST /api/graphql `{"query":"{__schema{types{name fields{name} args{name}}}}"}` → baseline `query{subscription(teamId,appId,subscriptionId){name state product{name}}}` with on-org IDs → swap foreign/victim IDs read-only → diff `UserCatalogList{catalogs{id name}}` + `teams{items{id orgId}}` across the row vs noam tokens
[LEARN] REJECTED nextauth-broken-auth-flow: POST signin with valid CSRF returns 302 to B2C authorize with PKCE S256, state, same-origin redirect_uri — auth flow correct on all 6 portals
reasoning: test/dev portals share one B2C client_id across ROW (`b2c_1a_signin_oidc_row`) and NA (`_noam`) policies under same tenant issuer+aud; schema scopes by Team.orgId + UserCatalogList.catalogs — if backend derives BU from token acr/org claims and one policy's shape satisfies the other's checks, BU separation collapses
[NEXT] HUMAN: obtain 2 test B2C accounts on login-qa.ciam.daimlertruck.com for developer.tst.na.api.daimlertruck.com — one under ROW policy `b2c_1a_signin_oidc_row` and one under NA policy `b2c_1a_signin_oidc_noam` (separate orgs to enable BU-boundary diff); then AUTH_HELPED run: POST /api/graphql `{"query":"{__schema{types{name fields{name} args{name}}}}"}` → baseline `query{subscription(teamId,appId,subscriptionId){name state product{name}}}` with on-org IDs → swap foreign/victim IDs read-only → diff `UserCatalogList{catalogs{id name}}` + `teams{items{id orgId}}` across the row vs noam tokens
[LEARN] REJECTED nextauth-broken-auth-flow: POST signin with valid CSRF returns 302 to B2C authorize with PKCE S256, state, same-origin redirect_uri — auth flow correct on all 6 portals
[NEXT] HUMAN: obtain 2 test B2C accounts on login-qa.ciam.daimlertruck.com for developer.tst.na.api.daimlertruck.com — one under ROW policy `b2c_1a_signin_oidc_row` and one under NA policy `b2c_1a_signin_oidc_noam` (separate orgs); then AUTH_HELPED run: POST /api/graphql `{"query":"{__schema{types{name fields{name} args{name}}}}"}` → baseline `query{subscription(teamId,appId,subscriptionId){name state product{name}}}` with on-org IDs → swap foreign/victim IDs read-only → diff `UserCatalogList{catalogs{id name}}` + `teams{items{id orgId}}` across row vs noam tokens
[NEW] B2C claim contracts machine-readable both policies both tenants: prod (3db550f0-...-33615d) + staging (88f558f5-...-d6ec0f) share IDENTICAL structure. ROW (89 claims) = DT-employee model (oid/adUpn/adSid/entitlements/scopedEntitlements/nonScopedEntitlements/homeOrganization/companies); NOAM (102 claims) = FTL dealer-org model (PrimaryOrgCode/UserAppRoles/SM_USER + FTLOrg* family: FTLOrgSapCode, FTLOrgDDCID, FTLOrgDCVCM, FTLOrgTSSOC, FTLOrgGSSNCompanyID/OutletID, FTLOrgBrands_Org, FTLOrgCertifications, FTLOrgChildren, FTLOrgVehOwners, FTLOrgIsITAR_Org, FTLOrgIsMexico_Org). Same issuer, same aud baseline, single RSA RS256 key per policy on same tenant jwks. BU boundary = acr + org-claim mapping only.
impact: Full API schema enumeration → unauthorized mutations (data tampering, BOLA) → Critical/High
reasoning: 7 authz.* subdomains (all 404 on /) suggest centralized auth service; OAuth/OIDC endpoints typically live at /.well-known/oauth-authorization-server, /authorize, /token, /introspect, /revoke
[NEW] OAuth initiate via POST /api/auth/signin/azure-ad-b2c-dt?json=true with valid CSRF returns B2C authorize URL as JSON: test tenant login-qa.ciam.daimlertruck.com/88f558f5-a216-470b-b34a-3164f5d6ec0f, policy b2c_1a_signin_oidc_row, client c387a5ab, scope dtagapim.stg.ciam.daimlertruck.com/DTAG_API_CP/user_impersonation, redirect_uri locked to same-origin /api/auth/callback/azure-ad-b2c-dt, PKCE S256 + state present
[NEW] OAuth initiate prod (as): tenant login.ciam.daimlertruck.com/3db550f0, policy b2c_1a_signin_oidc_row, client 205f35f7 — proper prod/staging segregation confirmed
reasoning: two B2C providers share same client_id c387a5ab and same staging tenant on test; dtna (NOAM) and dt (ROW) differ only in policy (b2c_1a_signin_oidc_noam vs _row); if post-login role/subscription derivation uses only the policy-context user object, cross-bu registration or role confusion between DT and DTNA possible
reasoning: two B2C providers share client_id c387a5ab and same staging tenant, differ only in policy (`b2c_1a_signin_oidc_noam` vs `_row`); cross-BU (DT vs DTNA) token/role confusion possible if object derivation is policy-only
reasoning: prod tenant issues both BU policies under the SAME tenant-level issuer and SAME aud (DTAG_API_CP/user_impersonation); test/dev portals use ONE client (c387a5ab) for both policies differing only by policy path; DT vs DTNA identity is distinguished only by acr + org claim shape (FTLOrg* vs adUpn/entitlements). If the portal backend scopes subscriptions by org claims and accepts either token shape, a DTNA noam token could act in ROW context or vice versa
verify_steps: AUTH_HELPED: two B2C accounts (one noam, one row) -> exchange sessions -> call /api/graphql from each, compare tenant-scoped data; passive (done): both policies live on prod+staging, same aud/iss, c387a5ab shared on test
reasoning: test/dev share client c387a5ab across both BU policies and CSP img-src renders literal "undefined" (unbound config var); prod-dtna OAuthSignin suggests unbound provider config too — recurring pattern of unbound env vars in portal builds
reasoning: Test/dev portals share one B2C `client_id c387a5ab` across both BU policies (`b2c_1a_signin_oidc_row` vs `_noam`), same tenant-level issuer+aud; schema uses `Team.orgId` and `UserCatalogList.catalogs` as the only scoping keys — if backend derives tenant from token `org/acr` claims and one policy's token shape satisfies the other's checks, BU separation collapses
[NEW] OAuth initiate via POST /api/auth/signin/azure-ad-b2c-dt?json=true with valid CSRF returns B2C authorize URL as JSON: test tenant login-qa.ciam.daimlertruck.com/88f558f5-a216-470b-b34a-3164f5d6ec0f, policy b2c_1a_signin_oidc_row, client c387a5ab, scope dtagapim.stg.ciam.daimlertruck.com/DTAG_API_CP/user_impersonation, redirect_uri locked to same-origin /api/auth/callback/azure-ad-b2c-dt, PKCE S256 + state present
[NEW] OAuth initiate prod (as): tenant login.ciam.daimlertruck.com/3db550f0, policy b2c_1a_signin_oidc_row, client 205f35f7 — proper prod/staging segregation confirmed
reasoning: two B2C providers share same client_id c387a5ab and same staging tenant on test; dtna (NOAM) and dt (ROW) differ only in policy (b2c_1a_signin_oidc_noam vs _row); if post-login role/subscription derivation uses only the policy-context user object, cross-bu registration or role confusion between DT and DTNA possible
reasoning: two B2C providers share client_id c387a5ab and same staging tenant, differ only in policy (`b2c_1a_signin_oidc_noam` vs `_row`); cross-BU (DT vs DTNA) token/role confusion possible if object derivation is policy-only
[PRIO] developer.{as,eu,na}.api.daimlertruck.com,8.8,attack_surface=9,business_value=9,tech_exposure=9,gate_ease=0,cloud_surface=8,freshness=9
[PRIO] developer.{dev.na,tst.eu,tst.na}.api.daimlertruck.com,8.0,attack_surface=8,tech_exposure=9,gate_ease=0,cloud_surface=8,freshness=9
[PRIO] login.businessid.daimlertruck.com,6.4,attack_surface=3,business_value=10,tech_exposure=8,gate_ease=0,cloud_surface=7,freshness=10
[PRIO] login.businessid-qa.daimlertruck.com,6.0,attack_surface=3,business_value=9,tech_exposure=8,gate_ease=0,cloud_surface=7,freshness=10
[PRIO] {as,eu}.api.daimlertruck.com,3.1,attack_surface=2,tech_exposure=6,gate_ease=2,cloud_surface=7,freshness=8
[HYP] graphql-object-id-bola-team-scoped
class: IDOR
asset: developer.as.api.daimlertruck.com
confidence: 75
reasoning: buildManifest reveals object-ID routes (/apis/[apiId], /apps/[appId]/subscriptions/[subscriptionId], /teams/[teamId]/system-users/associate); client bundle shows root GraphQL ops (subscription(teamId,appId,subscriptionId)); /api/graphql + every /api/* path blanket-307 to Azure AD B2C on all 6 portals — wildcard middleware confirms single auth gate for the whole resolver surface
evidence_needed: authenticated introspection on /api/graphql; reading foreign object IDs with own valid session returns data
verify_steps: AUTH_HELPED: B2C ROW session on developer.tst.na → POST /api/graphql `{"query":"{__schema{types{name fields{name} args{name}}}}"}` → baseline `query{subscription(teamId,appId,subscriptionId){name state}}` on-org → swap foreign IDs read-only → diff `UserCatalogList{catalogs{id name}}`+`teams{items{id orgId}}`
impact: cross-tenant subscription read/modify, system-user password + access-secret theft, team PII dump; Severity: high
testability: AUTH_HELPED
[HYP] b2c-cross-bu-token-boundary
class: OATH
asset: login-qa.ciam.daimlertruck.com
confidence: 64
reasoning: ROW+NOAM policies share one staging B2C app (c387a5ab-f534-48ee-b026-c24f9a0a92d5), same tenant issuer+aud, BU distinctions only in acr + org-claim shape (ROW: 89 claims, adUpn/entitlements/companies; NOAM: 102 claims, FTLOrg* family); schema scoping is Team.orgId + UserCatalogList.catalogs
evidence_needed: a NOAM-policy token accepted by a ROW-scoped resolver returning different orgId catalogs than same-principal ROW token (and vice versa)
verify_steps: AUTH_HELPED: two accounts (one `_row`, one `_noam`) → POST /api/graphql `query UserCatalogList{userCatalogList{catalogs{id name}}}` + `teams{items{id orgId}}` from each token, diff scoping
impact: DT/DTNA BU-boundary collapse → cross-BU API subscriptions + PII; Severity: medium-high
testability: AUTH_HELPED
[HYP] businessid-corporate-sso-chain-misconfig
class: OATH
asset: login.businessid.daimlertruck.com
confidence: 45
reasoning: previously unmapped DT-employee SSO broker (custom-policy B2C tenant corptbbid.onmicrosoft.com) sits behind CIAM ROW flow; broker→ciam code handoff is form_post to `/oauth2/authresp`; broker app implicit disabled, token endpoint confidential-only — only unvalidated surface is the two-hop nonce/state and policy-chaining integrity, never exercised end-to-end
evidence_needed: valid ROW login traversing broker hop where state/nonce from portal fails to constrain the returned code; or ciam accepting a code from a different businessid policy/flow
verify_steps: AUTH_HELPED: full ROW login on developer.tst.na (through login.businessid-qa) capturing the code at ciam authresp; repeat broker authorize with swapped response_mode=query vs form_post and with alternate `p` values; confirm NextAuth callback rejects tampered state/nonce
impact: employee-SSO session confusion across broker hops; ATO of DT employees if integrity fails; Severity: high (conditional)
testability: AUTH_HELPED
[PARKED] ciam-secret-return-in-plaintext: confidence 45 < 50 and chained on BOLA proof — park until hypothesis #1 confirmed.
[PARKED] apim-anonymous-op-exposure: falsified by uniform OperationNotFound across 16 probes — dropped from rankings.
[PARKED] nextauth-open-redirect / implicit-flow chains: AADB2C90057 verified portal+broker both envs — dead end, do not re-rank.
[FINAL] 1. graphql-object-id-bola-team-scoped (75) 2. b2c-cross-bu-token-boundary (64) 3. businessid-corporate-sso-chain-misconfig (45)
[NEXT] HUMAN: obtain 2 scoped test B2C accounts on login-qa.ciam.daimlertruck.com for developer.tst.na.api.daimlertruck.com — one ROW (`b2c_1a_signin_oidc_row`, broker hop login.businessid-qa/corptbbid onmicrosoft staging) + one NA (`_noam`, separate org); then AUTH_HELPED run: POST /api/graphql `{__schema{types{name fields{name} args{name}}}}` → baseline on-org `subscription(teamId,appId,subscriptionId)` → read-only foreign-ID swap → diff `UserCatalogList` + `teams{orgId}` across row vs noam tokens; capture broker-hop code to validate state/nonce integrity.
[LEARN] REJECTED apim-anonymous-op-exposure @ {as,eu}.api.daimlertruck.com: 16 anonymous-op paths all identical OperationNotFound (origin:config); /status-0123456789abcdef = default APIM beacon — no anonymous ops.
[LEARN] REJECTED implicit-token-fragment / code-interception @ developer.* + login.businessid(.qa): AADB2C90057 on portal app, broker prod, broker stg — implicit disabled stack-wide; confidential code+PKCE only.
[LEARN] ACCEPTED businessid-broker-surface @ login.businessid(.qa).daimlertruck.com: DT-employee corporate SSO broker (B2C tenants f266a340 prd / e39fd9b6 stg, clients 82559bb7 / a43f98c7, IdP corptbbid.onmicrosoft.com) behind CIAM ROW flow; OIDC+JWKS public; highest-sensitivity identity surface.
[LEARN] ACCEPTED portal-blanket-auth-middleware @ developer.*: /api/*, catalog, object-ID routes all 307 via wildcard middleware (fabricated + dot/case/%2f variants) — passive route discovery on portals exhausted; only /api/healthcheck public.
[RISK] Daimler Truck: 46/100. This cycle solidly falsified the last two passive hypotheses (APIM anonymous ops, implicit-flow token theft) and mapped a major new identity surface (corporate BusinessID broker) that is hardening-positive (implicit off everywhere, confidential PKCE/code only). No new exploitable anonymous surface; high-value returns still gated solely behind the two scoped staging B2C accounts (BOLA 75, cross-BU 64). Read-only, on-owned operations only; no customer/employee data touched.
## 2026-09-06 01:26:52 UTC [target] (model bigpickle)
impact: Full API schema enumeration → unauthorized mutations (API subscription takeover, key rotation, BOLA across tenant APIs) → Critical/High
[NEXT] PROBE: GET https://developer.as.api.daimlertruck.com/ → capture full Azure AD B2C auth flow (redirect URL, client_id, scope, redirect_uri, state parameter); then AUTH_HELPED: POST https://developer.as.api.daimlertruck.com/graphql with `{"query":"{__schema{types{name fields{name}}}}"}` using authenticated session cookie/token from valid test account
reasoning: 3 prod environments (as,eu,na) share identical buildId `JCvrnrykV_KYBk7pu0Npq`; 3 test/dev environments share `JVF_tXHlhCfZQOkT-cULr`; separate deployments suggest potentially different Azure AD B2C tenant configs; Azure AD B2C may issue tokens valid across environments if same tenant/client_id; no evidence of environment-level token audience validation
[PARKED] Developer Portal Cross-Environment Token Reuse — Tenant Isolation Bypass Across Prod/Test Deployments: confidence 55 but speculative — requires valid Azure AD B2C session + cross-env token reuse; no evidence yet of shared tenant/client_id across environments; AUTH_HELPED only
impact: Full API schema enumeration → unauthorized mutations (API subscription takeover, key rotation, webhook hijacking, BOLA across AS/EU/NA tenant APIs) → Critical/High
impact: Medium – unauthorized access to vector database containing corporate document embeddings if defaults are used in production
[NEW] OAuth initiate via POST /api/auth/signin/azure-ad-b2c-dt?json=true with valid CSRF returns B2C authorize URL as JSON: test tenant login-qa.ciam.daimlertruck.com/88f558f5-a216-470b-b34a-3164f5d6ec0f, policy b2c_1a_signin_oidc_row, client c387a5ab, scope dtagapim.stg.ciam.daimlertruck.com/DTAG_API_CP/user_impersonation, redirect_uri locked to same-origin /api/auth/callback/azure-ad-b2c-dt, PKCE S256 + state present
[NEW] OAuth initiate prod (as): tenant login.ciam.daimlertruck.com/3db550f0, policy b2c_1a_signin_oidc_row, client 205f35f7 — proper prod/staging segregation confirmed
reasoning: two B2C providers share same client_id c387a5ab and same staging tenant on test; dtna (NOAM) and dt (ROW) differ only in policy (b2c_1a_signin_oidc_noam vs _row); if post-login role/subscription derivation uses only the policy-context user object, cross-bu registration or role confusion between DT and DTNA possible
evidence_needed: whether same client_id/token audience allows token from row policy to access noam-scoped APIs after redirect_uri swap; test would need valid creds for both policies
[PARKED] nextauth-dtna-provider-isolation: 45 — requires two valid B2C credentials (dt + dtna) to test cross-policy token validity; human/auth-helped only; no passive way to confirm shared client_id is exploitable
[LEARN] REJECTED test-env-config-drift-as-vuln: CSP undefined + staging B2C tenant on test is proper environment segregation (prod uses login.ciam.daimlertruck.com, test uses login-qa.ciam.daimlertruck.com) — config drift is intentional, not a vuln
reasoning: two B2C providers share client_id c387a5ab and same staging tenant, differ only in policy (`b2c_1a_signin_oidc_noam` vs `_row`); cross-BU (DT vs DTNA) token/role confusion possible if object derivation is policy-only
reasoning: Test/dev portals share one B2C `client_id c387a5ab` across both BU policies (`b2c_1a_signin_oidc_row` vs `_noam`), same tenant-level issuer+aud; schema uses `Team.orgId` and `UserCatalogList.catalogs` as the only scoping keys — if backend derives tenant from token `org/acr` claims and one policy's token shape satisfies the other's checks, BU separation collapses
[NEXT] HUMAN: obtain 2 test B2C accounts on login-qa.ciam.daimlertruck.com (ROW policy `b2c_1a_signin_oidc_row`, plus one NA `_noam`) for developer.tst.na.api.daimlertruck.com; then run AUTH_HELPED verify on this session's schema: POST /api/graphql `{__schema{types{name fields{name}}}}`, then `subscription(teamId,appId,subscriptionId)` with on-org IDs baseline → foreign IDs read-only; diff `UserCatalogList` catalogs between row/noam tokens.
[NEXT] HUMAN: obtain 2 test B2C accounts on login-qa.ciam.daimlertruck.com (ROW policy `b2c_1a_signin_oidc_row`, plus one NA `_noam`) for developer.tst.na.api.daimlertruck.com; then run AUTH_HELPED verify on this session's schema: POST /api/graphql `{__schema{types{name fields{name}}}}`, then `subscription(teamId,appId,subscriptionId)` with on-org IDs baseline -> foreign IDs read-only; diff `UserCatalogList` catalogs between row/noam tokens.
reasoning: test/dev portals share one B2C client_id across ROW (`b2c_1a_signin_oidc_row`) and NA (`_noam`) policies under same tenant issuer+aud; schema scopes by Team.orgId + UserCatalogList.catalogs — if backend derives BU from token acr/org claims and one policy's shape satisfies the other's checks, BU separation collapses
[NEXT] HUMAN: obtain 2 test B2C accounts on login-qa.ciam.daimlertruck.com for developer.tst.na.api.daimlertruck.com — one under ROW policy `b2c_1a_signin_oidc_row` and one under NA policy `b2c_1a_signin_oidc_noam` (separate orgs to enable BU-boundary diff); then AUTH_HELPED run: POST /api/graphql `{"query":"{__schema{types{name fields{name} args{name}}}}"}` → baseline `query{subscription(teamId,appId,subscriptionId){name state product{name}}}` with on-org IDs → swap foreign/victim IDs read-only → diff `UserCatalogList{catalogs{id name}}` + `teams{items{id orgId}}` across the row vs noam tokens
[LEARN] REJECTED nextauth-broken-auth-flow: POST signin with valid CSRF returns 302 to B2C authorize with PKCE S256, state, same-origin redirect_uri — auth flow correct on all 6 portals
reasoning: test/dev portals share one B2C client_id across ROW (`b2c_1a_signin_oidc_row`) and NA (`_noam`) policies under same tenant issuer+aud; schema scopes by Team.orgId + UserCatalogList.catalogs — if backend derives BU from token acr/org claims and one policy's shape satisfies the other's checks, BU separation collapses
[NEXT] HUMAN: obtain 2 test B2C accounts on login-qa.ciam.daimlertruck.com for developer.tst.na.api.daimlertruck.com — one under ROW policy `b2c_1a_signin_oidc_row` and one under NA policy `b2c_1a_signin_oidc_noam` (separate orgs to enable BU-boundary diff); then AUTH_HELPED run: POST /api/graphql `{"query":"{__schema{types{name fields{name} args{name}}}}"}` → baseline `query{subscription(teamId,appId,subscriptionId){name state product{name}}}` with on-org IDs → swap foreign/victim IDs read-only → diff `UserCatalogList{catalogs{id name}}` + `teams{items{id orgId}}` across the row vs noam tokens
[LEARN] REJECTED nextauth-broken-auth-flow: POST signin with valid CSRF returns 302 to B2C authorize with PKCE S256, state, same-origin redirect_uri — auth flow correct on all 6 portals
[NEXT] HUMAN: obtain 2 test B2C accounts on login-qa.ciam.daimlertruck.com for developer.tst.na.api.daimlertruck.com — one under ROW policy `b2c_1a_signin_oidc_row` and one under NA policy `b2c_1a_signin_oidc_noam` (separate orgs); then AUTH_HELPED run: POST /api/graphql `{"query":"{__schema{types{name fields{name} args{name}}}}"}` → baseline `query{subscription(teamId,appId,subscriptionId){name state product{name}}}` with on-org IDs → swap foreign/victim IDs read-only → diff `UserCatalogList{catalogs{id name}}` + `teams{items{id orgId}}` across row vs noam tokens
[NEW] B2C claim contracts machine-readable both policies both tenants: prod (3db550f0-...-33615d) + staging (88f558f5-...-d6ec0f) share IDENTICAL structure. ROW (89 claims) = DT-employee model (oid/adUpn/adSid/entitlements/scopedEntitlements/nonScopedEntitlements/homeOrganization/companies); NOAM (102 claims) = FTL dealer-org model (PrimaryOrgCode/UserAppRoles/SM_USER + FTLOrg* family: FTLOrgSapCode, FTLOrgDDCID, FTLOrgDCVCM, FTLOrgTSSOC, FTLOrgGSSNCompanyID/OutletID, FTLOrgBrands_Org, FTLOrgCertifications, FTLOrgChildren, FTLOrgVehOwners, FTLOrgIsITAR_Org, FTLOrgIsMexico_Org). Same issuer, same aud baseline, single RSA RS256 key per policy on same tenant jwks. BU boundary = acr + org-claim mapping only.
impact: Full API schema enumeration → unauthorized mutations (data tampering, BOLA) → Critical/High
reasoning: 7 authz.* subdomains (all 404 on /) suggest centralized auth service; OAuth/OIDC endpoints typically live at /.well-known/oauth-authorization-server, /authorize, /token, /introspect, /revoke
[NEW] OAuth initiate via POST /api/auth/signin/azure-ad-b2c-dt?json=true with valid CSRF returns B2C authorize URL as JSON: test tenant login-qa.ciam.daimlertruck.com/88f558f5-a216-470b-b34a-3164f5d6ec0f, policy b2c_1a_signin_oidc_row, client c387a5ab, scope dtagapim.stg.ciam.daimlertruck.com/DTAG_API_CP/user_impersonation, redirect_uri locked to same-origin /api/auth/callback/azure-ad-b2c-dt, PKCE S256 + state present
[NEW] OAuth initiate prod (as): tenant login.ciam.daimlertruck.com/3db550f0, policy b2c_1a_signin_oidc_row, client 205f35f7 — proper prod/staging segregation confirmed
reasoning: two B2C providers share same client_id c387a5ab and same staging tenant on test; dtna (NOAM) and dt (ROW) differ only in policy (b2c_1a_signin_oidc_noam vs _row); if post-login role/subscription derivation uses only the policy-context user object, cross-bu registration or role confusion between DT and DTNA possible
reasoning: two B2C providers share client_id c387a5ab and same staging tenant, differ only in policy (`b2c_1a_signin_oidc_noam` vs `_row`); cross-BU (DT vs DTNA) token/role confusion possible if object derivation is policy-only
reasoning: prod tenant issues both BU policies under the SAME tenant-level issuer and SAME aud (DTAG_API_CP/user_impersonation); test/dev portals use ONE client (c387a5ab) for both policies differing only by policy path; DT vs DTNA identity is distinguished only by acr + org claim shape (FTLOrg* vs adUpn/entitlements). If the portal backend scopes subscriptions by org claims and accepts either token shape, a DTNA noam token could act in ROW context or vice versa
verify_steps: AUTH_HELPED: two B2C accounts (one noam, one row) -> exchange sessions -> call /api/graphql from each, compare tenant-scoped data; passive (done): both policies live on prod+staging, same aud/iss, c387a5ab shared on test
reasoning: test/dev share client c387a5ab across both BU policies and CSP img-src renders literal "undefined" (unbound config var); prod-dtna OAuthSignin suggests unbound provider config too — recurring pattern of unbound env vars in portal builds
reasoning: Test/dev portals share one B2C `client_id c387a5ab` across both BU policies (`b2c_1a_signin_oidc_row` vs `_noam`), same tenant-level issuer+aud; schema uses `Team.orgId` and `UserCatalogList.catalogs` as the only scoping keys — if backend derives tenant from token `org/acr` claims and one policy's token shape satisfies the other's checks, BU separation collapses
[NEW] OAuth initiate via POST /api/auth/signin/azure-ad-b2c-dt?json=true with valid CSRF returns B2C authorize URL as JSON: test tenant login-qa.ciam.daimlertruck.com/88f558f5-a216-470b-b34a-3164f5d6ec0f, policy b2c_1a_signin_oidc_row, client c387a5ab, scope dtagapim.stg.ciam.daimlertruck.com/DTAG_API_CP/user_impersonation, redirect_uri locked to same-origin /api/auth/callback/azure-ad-b2c-dt, PKCE S256 + state present
[NEW] OAuth initiate prod (as): tenant login.ciam.daimlertruck.com/3db550f0, policy b2c_1a_signin_oidc_row, client 205f35f7 — proper prod/staging segregation confirmed
reasoning: two B2C providers share same client_id c387a5ab and same staging tenant on test; dtna (NOAM) and dt (ROW) differ only in policy (b2c_1a_signin_oidc_noam vs _row); if post-login role/subscription derivation uses only the policy-context user object, cross-bu registration or role confusion between DT and DTNA possible
reasoning: two B2C providers share client_id c387a5ab and same staging tenant, differ only in policy (`b2c_1a_signin_oidc_noam` vs `_row`); cross-BU (DT vs DTNA) token/role confusion possible if object derivation is policy-only
[PRIO] developer.{as,eu,na}.api.daimlertruck.com,8.8,attack_surface=9,business_value=9,tech_exposure=9,gate_ease=0,cloud_surface=8,freshness=9
[PRIO] developer.{dev.na,tst.eu,tst.na}.api.daimlertruck.com,8.0,attack_surface=8,tech_exposure=9,gate_ease=0,cloud_surface=8,freshness=9
[PRIO] login.businessid.daimlertruck.com,6.4,attack_surface=3,business_value=10,tech_exposure=8,gate_ease=0,cloud_surface=7,freshness=10
[PRIO] login.businessid-qa.daimlertruck.com,6.0,attack_surface=3,business_value=9,tech_exposure=8,gate_ease=0,cloud_surface=7,freshness=10
[PRIO] {as,eu}.api.daimlertruck.com,3.1,attack_surface=2,tech_exposure=6,gate_ease=2,cloud_surface=7,freshness=8
[HYP] graphql-object-id-bola-team-scoped
class: IDOR
asset: developer.as.api.daimlertruck.com
confidence: 75
reasoning: buildManifest reveals object-ID routes (/apis/[apiId], /apps/[appId]/subscriptions/[subscriptionId], /teams/[teamId]/system-users/associate); client bundle shows root GraphQL ops (subscription(teamId,appId,subscriptionId)); /api/graphql + every /api/* path blanket-307 to Azure AD B2C on all 6 portals — wildcard middleware confirms single auth gate for the whole resolver surface
evidence_needed: authenticated introspection on /api/graphql; reading foreign object IDs with own valid session returns data
verify_steps: AUTH_HELPED: B2C ROW session on developer.tst.na → POST /api/graphql `{"query":"{__schema{types{name fields{name} args{name}}}}"}` → baseline `query{subscription(teamId,appId,subscriptionId){name state}}` on-org → swap foreign IDs read-only → diff `UserCatalogList{catalogs{id name}}`+`teams{items{id orgId}}`
impact: cross-tenant subscription read/modify, system-user password + access-secret theft, team PII dump; Severity: high
testability: AUTH_HELPED
[HYP] b2c-cross-bu-token-boundary
class: OATH
asset: login-qa.ciam.daimlertruck.com
confidence: 64
reasoning: ROW+NOAM policies share one staging B2C app (c387a5ab-f534-48ee-b026-c24f9a0a92d5), same tenant issuer+aud, BU distinctions only in acr + org-claim shape (ROW: 89 claims, adUpn/entitlements/companies; NOAM: 102 claims, FTLOrg* family); schema scoping is Team.orgId + UserCatalogList.catalogs
evidence_needed: a NOAM-policy token accepted by a ROW-scoped resolver returning different orgId catalogs than same-principal ROW token (and vice versa)
verify_steps: AUTH_HELPED: two accounts (one `_row`, one `_noam`) → POST /api/graphql `query UserCatalogList{userCatalogList{catalogs{id name}}}` + `teams{items{id orgId}}` from each token, diff scoping
impact: DT/DTNA BU-boundary collapse → cross-BU API subscriptions + PII; Severity: medium-high
testability: AUTH_HELPED
[HYP] businessid-corporate-sso-chain-misconfig
class: OATH
asset: login.businessid.daimlertruck.com
confidence: 45
reasoning: previously unmapped DT-employee SSO broker (custom-policy B2C tenant corptbbid.onmicrosoft.com) sits behind CIAM ROW flow; broker→ciam code handoff is form_post to `/oauth2/authresp`; broker app implicit disabled, token endpoint confidential-only — only unvalidated surface is the two-hop nonce/state and policy-chaining integrity, never exercised end-to-end
evidence_needed: valid ROW login traversing broker hop where state/nonce from portal fails to constrain the returned code; or ciam accepting a code from a different businessid policy/flow
verify_steps: AUTH_HELPED: full ROW login on developer.tst.na (through login.businessid-qa) capturing the code at ciam authresp; repeat broker authorize with swapped response_mode=query vs form_post and with alternate `p` values; confirm NextAuth callback rejects tampered state/nonce
impact: employee-SSO session confusion across broker hops; ATO of DT employees if integrity fails; Severity: high (conditional)
testability: AUTH_HELPED
[PARKED] ciam-secret-return-in-plaintext: confidence 45 < 50 and chained on BOLA proof — park until hypothesis #1 confirmed.
[PARKED] apim-anonymous-op-exposure: falsified by uniform OperationNotFound across 16 probes — dropped from rankings.
[PARKED] nextauth-open-redirect / implicit-flow chains: AADB2C90057 verified portal+broker both envs — dead end, do not re-rank.
[FINAL] 1. graphql-object-id-bola-team-scoped (75) 2. b2c-cross-bu-token-boundary (64) 3. businessid-corporate-sso-chain-misconfig (45)
[NEXT] HUMAN: obtain 2 scoped test B2C accounts on login-qa.ciam.daimlertruck.com for developer.tst.na.api.daimlertruck.com — one ROW (`b2c_1a_signin_oidc_row`, broker hop login.businessid-qa/corptbbid onmicrosoft staging) + one NA (`_noam`, separate org); then AUTH_HELPED run: POST /api/graphql `{__schema{types{name fields{name} args{name}}}}` → baseline on-org `subscription(teamId,appId,subscriptionId)` → read-only foreign-ID swap → diff `UserCatalogList` + `teams{orgId}` across row vs noam tokens; capture broker-hop code to validate state/nonce integrity.
[LEARN] REJECTED apim-anonymous-op-exposure @ {as,eu}.api.daimlertruck.com: 16 anonymous-op paths all identical OperationNotFound (origin:config); /status-0123456789abcdef = default APIM beacon — no anonymous ops.
[LEARN] REJECTED implicit-token-fragment / code-interception @ developer.* + login.businessid(.qa): AADB2C90057 on portal app, broker prod, broker stg — implicit disabled stack-wide; confidential code+PKCE only.
[LEARN] ACCEPTED businessid-broker-surface @ login.businessid(.qa).daimlertruck.com: DT-employee corporate SSO broker (B2C tenants f266a340 prd / e39fd9b6 stg, clients 82559bb7 / a43f98c7, IdP corptbbid.onmicrosoft.com) behind CIAM ROW flow; OIDC+JWKS public; highest-sensitivity identity surface.
[LEARN] ACCEPTED portal-blanket-auth-middleware @ developer.*: /api/*, catalog, object-ID routes all 307 via wildcard middleware (fabricated + dot/case/%2f variants) — passive route discovery on portals exhausted; only /api/healthcheck public.
[RISK] Daimler Truck: 46/100. This cycle solidly falsified the last two passive hypotheses (APIM anonymous ops, implicit-flow token theft) and mapped a major new identity surface (corporate BusinessID broker) that is hardening-positive (implicit off everywhere, confidential PKCE/code only). No new exploitable anonymous surface; high-value returns still gated solely behind the two scoped staging B2C accounts (BOLA 75, cross-BU 64). Read-only, on-owned operations only; no customer/employee data touched.
[HYP] Hardcoded MeiliSearch Master Key in DevContainer Config
class: SECRET
asset: daimlertruck/SRC-LibreChat/.devcontainer/docker-compose.yml:60
confidence: 85
reasoning: Real SHA-256 hex key `5c71cf56d672d009e36070b5bc5e47b743535ae55c818ae3b735bb6ebfb4ba63` hardcoded in devcontainer. Devcontainer configs are frequently copy-pasted into production docker-compose. MeiliSearch master key grants full search index read/write/admin access. Daimler Truck's developer.*.api.daimlertruck.com portals likely use search infrastructure.
impact: High – admin access to MeiliSearch instance if key reused in prod; data exfil or index poisoning
verify_steps: 1) Check if any production docker-compose/deploy-compose files reference this same key or value. 2) Passively check if any *.api.daimlertruck.com or internal subdomain exposes MeiliSearch on port 7700 or /indexes endpoint.
[HYP] Wildcard CORS with Credentials on RAG API
class: MISCONFIG
asset: daimlertruck/SRC-rag_api/main.py:76
confidence: 80
reasoning: `allow_origins=["*"]` combined with `allow_credentials=True` violates the CORS spec (browsers reject this combo) but signals intent to allow all origins. If the middleware is misconfigured or overridden, this enables CSRF/exfil against authenticated users. RAG API is AI infrastructure – likely used by Daimler Truck's developer portal or internal AI tooling.
impact: Medium – potential for cross-origin data theft if CORS enforcement is bypassed; credential leakage from AI search/RAG endpoints
verify_steps: 1) Check if RAG API is deployed on any *.api.daimlertruck.com subdomain. 2) Passively observe CORS headers on live endpoints.
[HYP] Default Database Credentials in RAG API Config
class: SECRET
asset: daimlertruck/SRC-rag_api/app/config.py:57-58
confidence: 65
reasoning: `POSTGRES_USER = "myuser"` and `POSTGRES_PASSWORD = "mypassword"` are default values if env vars are unset. If deployment omits these env vars (common in quick-start setups), the database is accessible with known credentials. RAG API stores vector embeddings and document chunks – sensitive corporate data.
impact: Medium – unauthorized access to vector database containing corporate document embeddings if defaults are used in production
verify_steps: 1) Check if the RAG API docker-compose or k8s manifest properly sets POSTGRES_PASSWORD. 2) Passively check if the database port (5432) is exposed on any daimlertruck.com subdomain.
[HYP] Unrestricted Default CORS on LibreChat API
class: MISCONFIG
asset: daimlertruck/SRC-LibreChat/api/server/index.js:322
confidence: 55
reasoning: `app.use(cors())` with no origin restrictions means all origins are allowed. LibreChat API handles authentication (JWT, OpenID Connect), chat sessions, and AI model API keys. If this instance is deployed internally, any malicious webpage visited by an employee could exfiltrate session tokens or chat data via cross-origin requests.
impact: Medium – session hijacking, chat data exfiltration via CSRF from any origin if deployed without additional reverse-proxy CORS
verify_steps: 1) Check if LibreChat is deployed on any *.daimlertruck.com domain or internal network. 2) Passively observe if Access-Control-Allow-Origin header reflects requesting origin.
[HYP] Conditional Debug Route Exposure in RAG API
class: MISCONFIG
asset: daimlertruck/SRC-rag_api/main.py:93-94
confidence: 50
reasoning: `if debug_mode: app.include_router(router=pgvector_routes.router)` – pgvector admin routes (likely CRUD on vector collections) are exposed when `DEBUG_RAG_API=True`. The config reads from env var, but if set in production (e.g., during troubleshooting and left on), it exposes administrative vector DB routes.
impact: Medium – admin-level access to vector database management if debug mode accidentally enabled in production
verify_steps: 1) Check if any production deployment has DEBUG_RAG_API=true. 2) Passively check if pgvector admin endpoints respond on the live RAG API.
class: SECRET
asset: SRC-rag_api/docker-compose.yaml:5-7, SRC-rag_api/db-compose.yaml:7-9,
confidence: 85
reasoning: |
impact: HIGH — database compromise leads to exfiltration of RAG-indexed
verify_steps: |
[CHANGED] developer.tst.na.api.daimlertruck.com: /api/auth/session GET returns 200 {} (empty session, not 400) — endpoint alive, expected empty when unauthenticated
[NEW] OAuth initiate via POST /api/auth/signin/azure-ad-b2c-dt?json=true with valid CSRF returns B2C authorize URL as JSON: test tenant login-qa.ciam.daimlertruck.com/88f558f5-a216-470b-b34a-3164f5d6ec0f, policy b2c_1a_signin_oidc_row, client c387a5ab, scope dtagapim.stg.ciam.daimlertruck.com/DTAG_API_CP/user_impersonation, redirect_uri locked to same-origin /api/auth/callback/azure-ad-b2c-dt, PKCE S256 + state present
[NEW] OAuth initiate prod (as): tenant login.ciam.daimlertruck.com/3db550f0, policy b2c_1a_signin_oidc_row, client 205f35f7 — proper prod/staging segregation confirmed
[NEW] /api/auth/providers reveals 2 OAuth providers on all portals: azure-ad-b2c-dt (ROW) + azure-ad-b2c-dtna (North America, policy b2c_1a_signin_oidc_noam)
[NEW] buildManifest route structure revealed: /apis/[apiId], /apps/[appId]/subscriptions/[subscriptionId], /teams/[teamId]/system-users/associate, /products/[productId]/subscribe, rewrites /docs/:slug*, /api/healthcheck→/healthcheck, /api/metrics→/metrics; /api/graphql confirmed as real server route
impact: Medium – unauthorized access to vector database containing corporate document embeddings if defaults are used in production
[NEW] OAuth initiate via POST /api/auth/signin/azure-ad-b2c-dt?json=true with valid CSRF returns B2C authorize URL as JSON: test tenant login-qa.ciam.daimlertruck.com/88f558f5-a216-470b-b34a-3164f5d6ec0f, policy b2c_1a_signin_oidc_row, client c387a5ab, scope dtagapim.stg.ciam.daimlertruck.com/DTAG_API_CP/user_impersonation, redirect_uri locked to same-origin /api/auth/callback/azure-ad-b2c-dt, PKCE S256 + state present
[NEW] OAuth initiate prod (as): tenant login.ciam.daimlertruck.com/3db550f0, policy b2c_1a_signin_oidc_row, client 205f35f7 — proper prod/staging segregation confirmed
reasoning: two B2C providers share same client_id c387a5ab and same staging tenant on test; dtna (NOAM) and dt (ROW) differ only in policy (b2c_1a_signin_oidc_noam vs _row); if post-login role/subscription derivation uses only the policy-context user object, cross-bu registration or role confusion between DT and DTNA possible
evidence_needed: whether same client_id/token audience allows token from row policy to access noam-scoped APIs after redirect_uri swap; test would need valid creds for both policies
[NEW] OAuth initiate via POST /api/auth/signin/azure-ad-b2c-dt?json=true with valid CSRF returns B2C authorize URL as JSON: test tenant login-qa.ciam.daimlertruck.com/88f558f5-a216-470b-b34a-3164f5d6ec0f, policy b2c_1a_signin_oidc_row, client c387a5ab, scope dtagapim.stg.ciam.daimlertruck.com/DTAG_API_CP/user_impersonation, redirect_uri locked to same-origin /api/auth/callback/azure-ad-b2c-dt, PKCE S256 + state present
[NEW] OAuth initiate prod (as): tenant login.ciam.daimlertruck.com/3db550f0, policy b2c_1a_signin_oidc_row, client 205f35f7 — proper prod/staging segregation confirmed
reasoning: two B2C providers share same client_id c387a5ab and same staging tenant on test; dtna (NOAM) and dt (ROW) differ only in policy (b2c_1a_signin_oidc_noam vs _row); if post-login role/subscription derivation uses only the policy-context user object, cross-bu registration or role confusion between DT and DTNA possible
reasoning: two B2C providers share client_id c387a5ab and same staging tenant, differ only in policy (`b2c_1a_signin_oidc_noam` vs `_row`); cross-BU (DT vs DTNA) token/role confusion possible if object derivation is policy-only
[NEW] OAuth initiate via POST /api/auth/signin/azure-ad-b2c-dt?json=true with valid CSRF returns B2C authorize URL as JSON: test tenant login-qa.ciam.daimlertruck.com/88f558f5-a216-470b-b34a-3164f5d6ec0f, policy b2c_1a_signin_oidc_row, client c387a5ab, scope dtagapim.stg.ciam.daimlertruck.com/DTAG_API_CP/user_impersonation, redirect_uri locked to same-origin /api/auth/callback/azure-ad-b2c-dt, PKCE S256 + state present
[NEW] OAuth initiate prod (as): tenant login.ciam.daimlertruck.com/3db550f0, policy b2c_1a_signin_oidc_row, client 205f35f7 — proper prod/staging segregation confirmed
reasoning: two B2C providers share same client_id c387a5ab and same staging tenant on test; dtna (NOAM) and dt (ROW) differ only in policy (b2c_1a_signin_oidc_noam vs _row); if post-login role/subscription derivation uses only the policy-context user object, cross-bu registration or role confusion between DT and DTNA possible
[PRIO] developer.{as,eu,na}.api.daimlertruck.com,8.8,attack_surface=9,business_value=9,tech_exposure=9,gate_ease=0,cloud_surface=8,freshness=9
[PRIO] developer.{dev.na,tst.eu,tst.na}.api.daimlertruck.com,8.0,attack_surface=8,tech_exposure=9,gate_ease=0,cloud_surface=8,freshness=9
[PRIO] login.businessid.daimlertruck.com,6.5,attack_surface=3,business_value=10,tech_exposure=8,gate_ease=0,cloud_surface=7,freshness=10
[PRIO] login.businessid-qa.daimlertruck.com,6.1,attack_surface=3,business_value=9,tech_exposure=8,gate_ease=0,cloud_surface=7,freshness=10
[PRIO] {as,eu}.api.daimlertruck.com,3.1,attack_surface=2,tech_exposure=6,gate_ease=2,cloud_surface=7,freshness=8
[HYP] graphql-object-id-bola-team-scoped
class: IDOR
asset: developer.tst.na.api.daimlertruck.com
confidence: 75
reasoning: buildManifest object-ID routes (/apis/[apiId], /apps/[appId]/subscriptions/[subscriptionId], /teams/[teamId]/system-users/associate); client bundle GraphQL ops (subscription(teamId,appId,subscriptionId)); /api/graphql blanket-307 behind wildcard middleware; auth path confirmed viable this cycle — exact portal scope accepted on staging tenant 88f558f5, broker first-hop (login.businessid-qa) functional, PKCE S256 + state preserved to same-origin callback
evidence_needed: authenticated introspection on /api/graphql; foreign object IDs readable/diffable under own valid session
verify_steps: AUTH_HELPED: ROW session on developer.tst.na → POST /api/graphql `{"query":"{__schema{types{name fields{name} args{name}}}}"}` → baseline `query{subscription(teamId,appId,subscriptionId){name state product{name}}}` on-org → read-only foreign-ID swap → diff `UserCatalogList{catalogs{id name}}` + `teams{items{id orgId}}`
impact: cross-tenant subscription read/modify, system-user password + access-secret theft, team PII dump; Severity: high
testability: AUTH_HELPED
[HYP] b2c-cross-bu-token-boundary
class: OATH
asset: login-qa.ciam.daimlertruck.com
confidence: 64
reasoning: ROW+NOAM policies share one staging app (c387a5ab), same tenant-level issuer+aud; BU distinctions only in acr + org-claim shape (ROW 89 claims adUpn/entitlements/companies vs NOAM 102 claims FTLOrg*); schema scoping keys are Team.orgId + UserCatalogList.catalogs; broker hop confirms policy-context user object is the only differentiation
evidence_needed: NOAM-policy token accepted by a ROW-scoped resolver returning different orgId catalogs than same-principal ROW token (and vice versa)
verify_steps: AUTH_HELPED: one `_row` + one `_noam` account → POST /api/graphql `query UserCatalogList{userCatalogList{catalogs{id name}}}` + `teams{items{id orgId}}` from each token; diff scoping
impact: DT/DTNA BU-boundary collapse → cross-BU API subscriptions + PII; Severity: medium-high
testability: AUTH_HELPED
[HYP] businessid-corporate-sso-chain-misconfig
class: OATH
asset: login.businessid-qa.daimlertruck.com
confidence: 45
reasoning: broker→ciam code handoff is form_post to /oauth2/authresp with state carriable from ciam; only surface left is two-hop nonce/state and policy-chaining integrity, never exercised end-to-end; signup journey absent (only b2c_1a_signin served) so attacker cannot self-provision retail accounts — corporate IdP affiliation must still be validated server-side
evidence_needed: ROW login through broker where returned code/state fails to constrain origin journey, or ciam accepts a code minted under a different broker policy/flow
verify_steps: AUTH_HELPED: full ROW login (staging) capturing code at ciam authresp; repeat broker authorize with response_mode=query vs form_post and alternate `p`; confirm NextAuth callback rejects tampered state/nonce
impact: employee-SSO session confusion across broker hops; ATO of DT employees if integrity fails; Severity: high (conditional)
testability: AUTH_HELPED
[PARKED] ciam-secret-return-in-plaintext: confidence 45 < 50 and chained on BOLA proof — park until hypothesis #1 confirmed.
[PARKED] broker-selfservice-signup: falsified — only `b2c_1a_signin` policy served; signup/signupsignin/profile metadata 404; "createAccount" strings are portal SPA JS noise not B2C signup UI.
[PARKED] apim-anonymous-op-exposure / implicit-flow chains / nextauth-open-redirect: previously falsified, do not re-rank.
[FINAL] 1. graphql-object-id-bola-team-scoped (75) 2. b2c-cross-bu-token-boundary (64) 3. businessid-corporate-sso-chain-misconfig (45)
[NEXT] HUMAN: obtain 2 admin-provisioned test accounts on login-qa.ciam tenant 88f558f5 — one `b2c_1a_signin_oidc_row` (corporate, provisioned through broker tenant e39fd9b6 `b2c_1a_signin`) + one `b2c_1a_signin_oidc_noam` (separate FTL org) for developer.tst.na.api.daimlertruck.com; complete login manually (authorize → broker → form_post authresp → NextAuth callback, PKCE S256); then AUTH_HELPED: POST /api/graphql `{__schema{types{name fields{name} args{name}}}}` → on-org `subscription(teamId,appId,subscriptionId)` baseline → read-only foreign-ID swap → diff `UserCatalogList` + `teams{orgId}` across row/noam tokens; capture broker-hop code to validate state/nonce.
[LEARN] REJECTED nextauth-broken-auth-flow-v2 @ developer.tst.na: AADB2C90117 "scope not supported" was self-induced (I dropped the `https://` scheme from the portal scope); scheme-prefixed `https://dtagapim.stg.ciam.../DTAG_API_CP/user_impersonation` accepted on both staging and prod — portal auth remains functional, prior rejection stands.
[LEARN] REJECTED broker-selfservice-signup @ login.businessid(.qa): only `b2c_1a_signin` policy served (metadata 200); b2c_1a_signup/signupsignin/profileedit → 404 — no self-service account journey; corporate-affiliation validation can only be tested with provisioned accounts.
[LEARN] ACCEPTED businessid-broker-first-hop @ login.businessid(.qa): ROW authorize on prod+staging renders broker login (tenants f266a340/e39fd9b6, clients 82559bb7/a43f98c7, policy b2c_1a_signin, code form_post → ciam authresp); portal custom scope validated at first hop, only openid forwarded — full identity chain now passively mapped.
[RISK] Daimler Truck: 44/100. This cycle passively mapped the corporate SSO broker first-hop end-to-end, falsified the self-service signup vector, and re-confirmed portal auth functional — surface remains hardening-positive with no new anonymous exploit reachable. All high-value returns (BOLA 75, cross-BU 64, broker-chain 45) still gated solely behind the two admin-provisioned staging B2C accounts. Read-only GETs only; no customer/employee data touched.
## 2026-09-06 06:32:48 UTC [target] (model bigpickle)
[PRIO] developer.{as,eu,na}.api.daimlertruck.com,8.8,attack_surface=9,business_value=9,tech_exposure=9,gate_ease=0,cloud_surface=8,freshness=9
[PRIO] developer.{dev.na,tst.eu,tst.na}.api.daimlertruck.com,8.0,attack_surface=8,tech_exposure=9,gate_ease=0,cloud_surface=8,freshness=9
[PRIO] login.businessid.daimlertruck.com,6.5,attack_surface=3,business_value=10,tech_exposure=8,gate_ease=0,cloud_surface=7,freshness=10
[PRIO] login.businessid-qa.daimlertruck.com,6.1,attack_surface=2,business_value=9,tech_exposure=8,gate_ease=0,cloud_surface=7,freshness=8
[PRIO] login-qa.ciam.daimlertruck.com,5.9,attack_surface=4,business_value=8,tech_exposure=9,gate_ease=0,cloud_surface=7,freshness=10
[PRIO] {as,eu}.api.daimlertruck.com,3.1,attack_surface=2,tech_exposure=6,gate_ease=2,cloud_surface=7,freshness=8
[HYP] graphql-object-id-bola-team-scoped
class: IDOR
asset: developer.tst.na.api.daimlertruck.com
confidence: 75
reasoning: buildManifest object-ID routes (/apis/[apiId], /apps/[appId]/subscriptions/[subscriptionId], /teams/[teamId]/system-users/associate); client bundle GraphQL ops take teamId/appId/subscriptionId; /api/graphql blanket-307 behind wildcard middleware; auth chain viable on staging tenant 88f558f5 (ROW+NOAM policies, same-issuer), broker first-hop mapped, PKCE+state preserved to same-origin callback — only missing step is a valid staged session
evidence_needed: authenticated introspection on /api/graphql; foreign object-ID read under own valid session
verify_steps: AUTH_HELPED: ROW session on developer.tst.na → POST /api/graphql `{"query":"{__schema{types{name fields{name} args{name}}}}"}` → on-org `query{subscription(teamId,appId,subscriptionId){name state product{name}}}` → read-only foreign-ID swap → diff `UserCatalogList{catalogs{id name}}` + `teams{items{id orgId}}`
impact: cross-tenant subscription read/modify, system-user password + accessSecret theft, team PII dump; Severity: high
testability: AUTH_HELPED
[HYP] b2c-cross-bu-token-boundary
class: OATH
asset: login-qa.ciam.daimlertruck.com
confidence: 67
reasoning: FRESH this cycle: ROW+NOAM share identical `iss` (tenant-level, 88f558f5.../v2.0/) and same client c387a5ab on the portal app; only differentiation is per-policy `acr` + BU-shape org claims (89 vs 102 claims ROW vs NOAM); schema scoping (Team.orgId, UserCatalogList.catalogs) is org-claim-based; jwks per-policy but iss/aud collision means backend can't distinguish BU from iss/aud alone
evidence_needed: NOAM token accepted by ROW-scoped resolver returning different orgId catalogs than same-principal ROW token (and vice versa)
verify_steps: AUTH_HELPED: one `_row` + one `_noam` account → POST /api/graphql `query UserCatalogList{userCatalogList{catalogs{id name}}}` + `teams{items{id orgId}}` from each token; diff scoping
impact: DT/DTNA BU-boundary collapse → cross-BU API subscriptions + PII; Severity: medium-high
testability: AUTH_HELPED
[HYP] businessid-corporate-sso-chain-misconfig
class: OATH
asset: login.businessid-qa.daimlertruck.com
confidence: 42
reasoning: broker→ciam handoff is form_post to /oauth2/authresp with state carriable from ciam; signup journey absent (b2c_1a_signin only) so affiliation validation is server-side; this cycle bare-path metadata on businessid-qa returns 404 (vs 200 prior), so anonymous broker OIDC/JWKS surface can no longer be passively re-verified — drops confidence below the 45 floor; only the two-hop nonce/state integrity check remains testable, requires a live code capture
evidence_needed: ciam accepting a code minted under a different broker policy/flow, or NextAuth callback accepting tampered state/nonce
verify_steps: AUTH_HELPED: full ROW login (staging) capturing code at ciam authresp; repeat broker authorize with response_mode=query vs form_post + alternate `p`; confirm NextAuth callback rejects tampered state/nonce
impact: employee-SSO session confusion across broker hops; ATO of DT employees if integrity fails; Severity: high (conditional)
testability: AUTH_HELPED
[PARKED] businessid-corporate-sso-chain-misconfig: confidence 42 < 45 floor this cycle (broker anonymous metadata 404, cannot independently re-verify); also chained on full-journey code capture — keep dormant, no re-rank.
[PARKED] ciam-secret-return-in-plaintext: chained on BOLA proof (accessSecret/password resolvers) — re-promote only once hypothesis #1 reads a foreign subscription.
[PARKED] apim-anonymous-op-exposure / implicit-flow chains / nextauth-open-redirect: falsified prior cycles, do not re-rank; valid-bugs.md stale "VALID" verdict contradicted by 4+ KB rejections.
[FINAL] 1. graphql-object-id-bola-team-scoped (75) 2. b2c-cross-bu-token-boundary (67, +3 after fresh same-issuer proof)
[NEXT] HUMAN: obtain two admin-provisioned staging accounts on login-qa.ciam tenant 88f558f5 — one `b2c_1a_signin_oidc_row` (corporate, via broker tenant e39fd9b6 `b2c_1a_signin`, full GUIDs supplied) + one `b2c_1a_signin_oidc_noam` (separate FTL org) for developer.tst.na.api.daimlertruck.com; complete login manually (authorize → broker → form_post authresp → NextAuth callback, PKCE S256); then AUTH_HELPED POST /api/graphql `{__schema{types{name fields{name} args{name}}}}` on-org baseline → read-only foreign-ID swap → diff `UserCatalogList` + `teams{orgId}` across row/noam tokens; capture broker-hop code+state to validate two-hop integrity.
[LEARN] ACCEPTED b2c-cross-bu-token-boundary @ login-qa.ciam.daimlertruck.com: ROW+NOAM policies share tenant-level issuer + same portal client c387a5ab; only acr + org-shape claims differentiate BU — same-issuer collision confirmed anonymously this cycle.
[LEARN] UNRESOLVED broker-oidc-surface @ login.businessid-qa.daimlertruck.com: bare / + well-known paths now 404 len=103 vs prior 200 metadata; full tenant GUID unknown so path-uncertainty, not a confirmed regression — do not claim change.
[LEARN] REJECTED nextauth-broken-auth-flow / nextauth-callback-open-redirect @ developer.*: PKCE S256 + state + same-origin redirect_uri enforced; callbackUrl same-domain only, subdomains rejected — prior rejections stand; valid-bugs.md stale verdict to be cleaned.
[RISK] Daimler Truck: 42/100. Surface unchanged and hardening-positive: portals stable behind wildcard auth, CIAM tenants correct (confidential+PKCE, implicit disabled), businessid broker self-service falsified. No new anonymous exploit reachable — all returns (BOLA 75, cross-BU 67) gated solely on two admin-provisioned staging accounts. This cycle added only read-only GETs on public OIDC metadata and endpoints; no customer/employee/auth data touched.
## 2026-09-06 11:21:54 UTC [target] (model bigpickle)
verify_steps: AUTH_HELPED: ROW session on developer.tst.na → POST /api/graphql `{"query":"{__schema{types{name fields{name} args{name}}}}"}` → on-org `query{subscription(teamId,appId,subscriptionId){name state product{name}}}` → read-only foreign-ID swap → diff `UserCatalogList{catalogs{id name}}` + `teams{items{id orgId}}`
impact: cross-tenant subscription read/modify, system-user password + accessSecret theft, team PII dump; Severity: high
testability: AUTH_HELPED
[HYP] b2c-cross-bu-token-boundary
class: OATH
asset: login-qa.ciam.daimlertruck.com
confidence: 67
reasoning: FRESH this cycle: ROW+NOAM share identical `iss` (tenant-level, 88f558f5.../v2.0/) and same client c387a5ab on the portal app; only differentiation is per-policy `acr` + BU-shape org claims (89 vs 102 claims ROW vs NOAM); schema scoping (Team.orgId, UserCatalogList.catalogs) is org-claim-based; jwks per-policy but iss/aud collision means backend can't distinguish BU from iss/aud alone
evidence_needed: NOAM token accepted by ROW-scoped resolver returning different orgId catalogs than same-principal ROW token (and vice versa)
verify_steps: AUTH_HELPED: one `_row` + one `_noam` account → POST /api/graphql `query UserCatalogList{userCatalogList{catalogs{id name}}}` + `teams{items{id orgId}}` from each token; diff scoping
impact: DT/DTNA BU-boundary collapse → cross-BU API subscriptions + PII; Severity: medium-high
testability: AUTH_HELPED
[HYP] businessid-corporate-sso-chain-misconfig
class: OATH
asset: login.businessid-qa.daimlertruck.com
confidence: 42
reasoning: broker→ciam handoff is form_post to /oauth2/authresp with state carriable from ciam; signup journey absent (b2c_1a_signin only) so affiliation validation is server-side; this cycle bare-path metadata on businessid-qa returns 404 (vs 200 prior), so anonymous broker OIDC/JWKS surface can no longer be passively re-verified — drops confidence below the 45 floor; only the two-hop nonce/state integrity check remains testable, requires a live code capture
evidence_needed: ciam accepting a code minted under a different broker policy/flow, or NextAuth callback accepting tampered state/nonce
verify_steps: AUTH_HELPED: full ROW login (staging) capturing code at ciam authresp; repeat broker authorize with response_mode=query vs form_post + alternate `p`; confirm NextAuth callback rejects tampered state/nonce
impact: employee-SSO session confusion across broker hops; ATO of DT employees if integrity fails; Severity: high (conditional)
testability: AUTH_HELPED
[PARKED] businessid-corporate-sso-chain-misconfig: confidence 42 < 45 floor this cycle (broker anonymous metadata 404, cannot independently re-verify); also chained on full-journey code capture — keep dormant, no re-rank.
[PARKED] ciam-secret-return-in-plaintext: chained on BOLA proof (accessSecret/password resolvers) — re-promote only once hypothesis #1 reads a foreign subscription.
[PARKED] apim-anonymous-op-exposure / implicit-flow chains / nextauth-open-redirect: falsified prior cycles, do not re-rank; valid-bugs.md stale "VALID" verdict contradicted by 4+ KB rejections.
[FINAL] 1. graphql-object-id-bola-team-scoped (75) 2. b2c-cross-bu-token-boundary (67, +3 after fresh same-issuer proof)
[NEXT] HUMAN: obtain two admin-provisioned staging accounts on login-qa.ciam tenant 88f558f5 — one `b2c_1a_signin_oidc_row` (corporate, via broker tenant e39fd9b6 `b2c_1a_signin`, full GUIDs supplied) + one `b2c_1a_signin_oidc_noam` (separate FTL org) for developer.tst.na.api.daimlertruck.com; complete login manually (authorize → broker → form_post authresp → NextAuth callback, PKCE S256); then AUTH_HELPED POST /api/graphql `{__schema{types{name fields{name} args{name}}}}` on-org baseline → read-only foreign-ID swap → diff `UserCatalogList` + `teams{orgId}` across row/noam tokens; capture broker-hop code+state to validate two-hop integrity.
[LEARN] ACCEPTED b2c-cross-bu-token-boundary @ login-qa.ciam.daimlertruck.com: ROW+NOAM policies share tenant-level issuer + same portal client c387a5ab; only acr + org-shape claims differentiate BU — same-issuer collision confirmed anonymously this cycle.
[LEARN] UNRESOLVED broker-oidc-surface @ login.businessid-qa.daimlertruck.com: bare / + well-known paths now 404 len=103 vs prior 200 metadata; full tenant GUID unknown so path-uncertainty, not a confirmed regression — do not claim change.
[LEARN] REJECTED nextauth-broken-auth-flow / nextauth-callback-open-redirect @ developer.*: PKCE S256 + state + same-origin redirect_uri enforced; callbackUrl same-domain only, subdomains rejected — prior rejections stand; valid-bugs.md stale verdict to be cleaned.
[RISK] Daimler Truck: 42/100. Surface unchanged and hardening-positive: portals stable behind wildcard auth, CIAM tenants correct (confidential+PKCE, implicit disabled), businessid broker self-service falsified. No new anonymous exploit reachable — all returns (BOLA 75, cross-BU 67) gated solely on two admin-provisioned staging accounts. This cycle added only read-only GETs on public OIDC metadata and endpoints; no customer/employee/auth data touched.
evidence_needed: Valid B2C token from one tenant (e.g., ROW) accepted by API resolver in different tenant (e.g., NA) without claim validation; evidence that acr/org claims are not checked on object-ID routes
verify_steps: AUTH_HELPED: Obtain valid ROW token (azure-ad-b2c-dt provider); replay token against NA-scoped endpoint (developer.na.api.daimlertruck.com/apis/<na-apiId>); test with NA token (azure-ad-b2c-dtna) against ROW endpoint
impact: Cross-business-unit API access, subscription data leakage, tenant isolation bypass → High
testability: AUTH_HELPED
[PARKED] CIAM Secret Return in Plaintext — AccessSecret/Password Fields in GraphQL Resolvers: confidence 65 but evidence relies on client bundle analysis not yet verified against live resolvers; requires valid B2C session + authenticated introspection; speculative without direct schema confirmation — keep for post-auth phase
[FINAL] 1. GraphQL Mutation BOLA Across Tenant Boundaries (75)
[FINAL] 2. Object-ID REST Routes BOLA/IDOR on Tenant-Scoped Resources (70)
[FINAL] 3. B2C Cross-Tenant Token Boundary Bypass via Shared Issuer/Audience (65)
[NEXT] PROBE: POST https://developer.as.api.daimlertruck.com/graphql with `{"query":"{__schema{types{name fields{name args{name type}}}}}"}` — verify real GraphQL endpoint responds with schema (not SPA shell) and confirm 307 redirect behavior vs introspection response; this resolves the GET-vs-POST discrepancy in current probe results.
[LEARN] ACCEPTED graphql-behind-azure-ad-b2c: /graphql and /api/graphql return 307 to Azure AD B2C on all 6 portals — real GraphQL endpoint behind auth confirmed
[LEARN] ACCEPTED nextauth-endpoints-exposed: /api/auth/csrf, /api/auth/session, /api/auth/providers, /api/auth/signin/* accessible without auth on all 6 developer portals — NextAuth.js attack surface confirmed
[LEARN] ACCEPTED test-dev-portal-config-drift: Distinct build IDs (prod JCvrnrykV_KYBk7pu0Npq vs test/dev JVF_tXHlhCfZQOkT-cULr) — separate deployments with potential config differences
[LEARN] ACCEPTED buildmanifest-route-reveals-authz-surface: buildManifest shows object-ID routes (/apis/[apiId], subscriptions/[subscriptionId], teams/[teamId]/system-users/associate) — high-value BOLA probing surface post-auth
[LEARN] ACCEPTED /api/healthcheck-live: 200 JSON with uptime on both prod and test — real server route; informational only (not a vuln alone)
[LEARN] REJECTED developer-portal-graphql-introspection: SPA catch-all returns HTTP 200 for all paths — OVERRULED: /graphql now returns 307, real endpoint behind auth
[LEARN] REJECTED developer-portal-exposed-swagger: SPA catch-all returns HTTP 200 for /swagger.json, /api-docs — false positive
[LEARN] REJECTED nextauth-callback-open-redirect: callbackUrl parameter validated to same-domain only; external domains rejected and replaced with current origin; subdomains rejected — not an open redirect
[LEARN] REJECTED nextauth-broken-auth-flow: POST /api/auth/signin/azure-ad-b2c-dt with valid CSRF returns 302 to Azure AD B2C authorize endpoint with PKCE S256, state, and same-origin redirect_uri — auth flow works correctly on all 6 portals
[LEARN] REJECTED test-env-config-drift-as-vuln: CSP undefined + staging B2C tenant on test are intentional env segregation, not a defect
[LEARN] REJECTED OAuth misconfig @ authz.*: 7 authz subdomains all return 404 on root and well-known endpoints — no OAuth surface exposed
[LEARN] REJECTED Admin panel discovery @ capacitor-admin.*: 6 subdomains all return 000 (connection failed) — no live HTTP surface to assess
[LEARN] REJECTED network DoS @ all assets: program explicitly excludes DoS/DDoS and account-lockout
[LEARN] REJECTED SSL/TLS best practices @ www.daimlertruck.com: out of scope per policy
[LEARN] REJECTED Clickjacking @ www.daimlertruck.com: requires demonstrated exploit per policy
[LEARN] ACCEPTED graphql-object-id-bola-team-scoped: /api/graphql returns 307 to Azure AD B2C; buildManifest shows explicit object-ID routes; client bundle shows GraphQL ops with these IDs
[LEARN] ACCEPTED b2c-cross-bu-token-boundary: prod+test tenants issue ROW+NOAM under same issuer/aud; BU separation depends on acr/org claims
[LEARN] ACCEPTED ciam-secret-return-in-plaintext: client bundle indicates accessSecret and password fields returned by resolvers
[LEARN] ACCEPTED portal-blanket-auth-middleware: /api/*, catalog, object-ID routes all 307 via wildcard middleware (fabricated + dot/case/%2f variants) — passive route discovery on portals exhausted; only /api/healthcheck public
[LEARN] ACCEPTED businessid-broker-surface: login.businessid(.qa).daimlertruck.com DT-employee corporate SSO broker (B2C tenants f266a340 prd / e39fd9b6 stg, clients 82559bb7 / a43f98c7, IdP corptbbid.onmicrosoft.com) behind CIAM ROW flow; OIDC+JWKS public; highest-sensitivity identity surface
[RISK] daimler-truck: 74 — 6 live developer portals with GraphQL + object-ID REST routes (/apis/[apiId], /apps/[appId]/subscriptions/[subscriptionId], /teams/[teamId]/system-users/associate) behind Azure AD B2C; NextAuth.js callbackUrl validation prevents open redirect but auth flow works correctly; "single source of truth for APIs" implies high-value mutation surface post-auth; 2 distinct build IDs confirm separate deployments with config drift risk (CSP divergence, literal "undefined" in test CSP); object-ID routes create BOLA surface across AS/EU/NA tenant boundaries; trailing-slash routing quirk (/apis/ vs /apis) masks real endpoints; capacitor-admin and authz surfaces remain dark (000/404); B2C cross-tenant token boundary relies solely on acr/org claim validation; companion.app.daimlertruck.com NXDOMAIN in prod CSP frame-src indicates dead reference
[HYP] Hardcoded MeiliSearch Master Key in DevContainer Config
class: SECRET
asset: daimlertruck/SRC-LibreChat/.devcontainer/docker-compose.yml:60
confidence: 85
reasoning: Real SHA-256 hex key `5c71cf56d672d009e36070b5bc5e47b743535ae55c818ae3b735bb6ebfb4ba63` hardcoded in devcontainer. Devcontainer configs are frequently copy-pasted into production docker-compose. MeiliSearch master key grants full search index read/write/admin access. Daimler Truck's developer.*.api.daimlertruck.com portals likely use search infrastructure.
impact: High – admin access to MeiliSearch instance if key reused in prod; data exfil or index poisoning
verify_steps: 1) Check if any production docker-compose/deploy-compose files reference this same key or value. 2) Passively check if any *.api.daimlertruck.com or internal subdomain exposes MeiliSearch on port 7700 or /indexes endpoint.
[HYP] Wildcard CORS with Credentials on RAG API
class: MISCONFIG
asset: daimlertruck/SRC-rag_api/main.py:76
confidence: 80
reasoning: `allow_origins=["*"]` combined with `allow_credentials=True` violates the CORS spec (browsers reject this combo) but signals intent to allow all origins. If the middleware is misconfigured or overridden, this enables CSRF/exfil against authenticated users. RAG API is AI infrastructure – likely used by Daimler Truck's developer portal or internal AI tooling.
impact: Medium – potential for cross-origin data theft if CORS enforcement is bypassed; credential leakage from AI search/RAG endpoints
verify_steps: 1) Check if RAG API is deployed on any *.api.daimlertruck.com subdomain. 2) Passively observe CORS headers on live endpoints.
[HYP] Default Database Credentials in RAG API Config
class: SECRET
asset: daimlertruck/SRC-rag_api/app/config.py:57-58
confidence: 65
reasoning: `POSTGRES_USER = "myuser"` and `POSTGRES_PASSWORD = "mypassword"` are default values if env vars are unset. If deployment omits these env vars (common in quick-start setups), the database is accessible with known credentials. RAG API stores vector embeddings and document chunks – sensitive corporate data.
impact: Medium – unauthorized access to vector database containing corporate document embeddings if defaults are used in production
verify_steps: 1) Check if the RAG API docker-compose or k8s manifest properly sets POSTGRES_PASSWORD. 2) Passively check if the database port (5432) is exposed on any daimlertruck.com subdomain.
[HYP] Unrestricted Default CORS on LibreChat API
class: MISCONFIG
asset: daimlertruck/SRC-LibreChat/api/server/index.js:322
confidence: 55
reasoning: `app.use(cors())` with no origin restrictions means all origins are allowed. LibreChat API handles authentication (JWT, OpenID Connect), chat sessions, and AI model API keys. If this instance is deployed internally, any malicious webpage visited by an employee could exfiltrate session tokens or chat data via cross-origin requests.
impact: Medium – session hijacking, chat data exfiltration via CSRF from any origin if deployed without additional reverse-proxy CORS
verify_steps: 1) Check if LibreChat is deployed on any *.daimlertruck.com domain or internal network. 2) Passively observe if Access-Control-Allow-Origin header reflects requesting origin.
[HYP] Conditional Debug Route Exposure in RAG API
class: MISCONFIG
asset: daimlertruck/SRC-rag_api/main.py:93-94
confidence: 50
reasoning: `if debug_mode: app.include_router(router=pgvector_routes.router)` – pgvector admin routes (likely CRUD on vector collections) are exposed when `DEBUG_RAG_API=True`. The config reads from env var, but if set in production (e.g., during troubleshooting and left on), it exposes administrative vector DB routes.
impact: Medium – admin-level access to vector database management if debug mode accidentally enabled in production
verify_steps: 1) Check if any production deployment has DEBUG_RAG_API=true. 2) Passively check if pgvector admin endpoints respond on the live RAG API.
class: SECRET
asset: SRC-rag_api/docker-compose.yaml:5-7, SRC-rag_api/db-compose.yaml:7-9,
confidence: 85
reasoning: |
impact: HIGH — database compromise leads to exfiltration of RAG-indexed
verify_steps: |
class: MISCONFIG
asset: SRC-LibreChat/docker-compose.yml:63, SRC-LibreChat/deploy-compose.yml:80,
confidence: 90
reasoning: |
impact: HIGH — unauthenticated access to MongoDB containing user data,
verify_steps: |
class: MISCONFIG
asset: SRC-rag_api/main.py:76-79
confidence: 80
reasoning: |
impact: MEDIUM — enables cross-origin data exfiltration from RAG endpoints
verify_steps: |
class: MISCONFIG
asset: SRC-rag_api/app/middleware.py:18-22
confidence: 85
reasoning: |
impact: HIGH — unauthenticated access to all RAG API endpoints including
verify_steps: |
class: OTHER
asset: SRC-rag_api/main.py:93, SRC-rag_api/app/routes/pgvector_routes.py:1-70
confidence: 75
reasoning: |
impact: MEDIUM — full database schema enumeration and data dump if debug
verify_steps: |
class: MISCONFIG
asset: SRC-openai-aca-lb/src/appsettings.json:8
confidence: 60
reasoning: |
impact: LOW — host-header injection possible if behind a reverse proxy;
verify_steps: |
class: SECRET
asset: `daimlertruck/SRC-rag_api/app/config.py:59`
confidence: 90
reasoning: `POSTGRES_PASSWORD = get_env_variable("POSTGRES_PASSWORD", "mypassword")` — if the env var is unset, the RAG API connects to PostgreSQL with the hardcoded password `mypassword`. The username `myuser` and database `mydatabase` follow the same pattern (lines 58-59).
impact: HIGH — Default credentials on a database holding document embeddings could allow unauthorized data access if deployed without overriding env vars.
verify_steps: Check whether any live deployment of `SRC-rag_api` omits `POSTGRES_PASSWORD` from its environment. If the container starts and connects to a real Postgres instance with `mypassword`, the finding is confirmed.
class: MISCONFIG
asset: `daimlertruck/SRC-rag_api/app/middleware.py:18-21`
confidence: 85
reasoning: When `JWT_SECRET` is unset, the middleware logs a warning and calls `next_middleware_call()` without validating any token. Every protected endpoint becomes unauthenticated.
impact: HIGH — If deployed without `JWT_SECRET`, the RAG API is fully open. An attacker can access all document endpoints without credentials.
verify_steps: Deploy SRC-rag_api without setting `JWT_SECRET`. Confirm that requests to `/documents` etc. return 200 without a valid bearer token.
class: MISCONFIG
asset: `daimlertruck/SRC-rag_api/main.py:72-77`
confidence: 80
reasoning: `allow_origins=["*"]` combined with `allow_credentials=True` allows any origin to make credentialed cross-origin requests to the API. This is a textbook CORS misconfiguration.
impact: MEDIUM — Enables CSRF-like attacks against authenticated endpoints if the API uses cookie-based auth. Combined with finding #2, any origin can access the open API.
verify_steps: Send an `Origin: https://evil.com` request with `credentials: include` to an authenticated endpoint. If the response includes `Access-Control-Allow-Origin: https://evil.com` and `Access-Control-Allow-Credentials: true`, the finding is confirmed.
class: MISCONFIG
asset: `daimlertruck/SRC-openai-aca-lb/infra/core/database/sql/sql-server.bicep` (firewall resource)
confidence: 75
reasoning: The SQL Server firewall rule is defined with `startIpAddress: '0.0.0.1'` and `endIpAddress: '255.255.255.254'`, allowing connections from any public IP. The comment notes this is for "debugging purposes" but the template is committed as-is.
impact: MEDIUM — If deployed to a real Azure subscription, the SQL Server is exposed to the entire internet. The `@secure()` parameter for the password is correct, but the network perimeter is wide open.
verify_steps: Deploy the Bicep template and inspect the SQL Server's firewall rules in the Azure portal. Confirm the rule covers `0.0.0.1-255.255.255.254`.
class: MISCONFIG
asset: `daimlertruck/SRC-openai-aca-lb/infra/core/host/container-registry.bicep:24`
confidence: 60
reasoning: `publicNetworkAccess string = 'Enabled'` is the default. Combined with the SQL firewall above, this defaults to a posture where container images and the database are both publicly reachable.
impact: LOW — Standard for dev/test templates. `adminUserEnabled` and `anonymousPullEnabled` default to false, so the direct risk is limited.
verify_steps: Deploy and verify the ACR's public network access setting in the Azure portal.
[HYP] graphql-object-id-bola-team-scoped
class: IDOR
asset: developer.tst.na.api.daimlertruck.com
confidence: 75
reasoning: buildManifest object-ID routes (/apis/[apiId], /apps/[appId]/subscriptions/[subscriptionId], /teams/[teamId]/system-users/associate); client bundle GraphQL ops take teamId/appId/subscriptionId; /api/graphql blanket-307 behind wildcard middleware; auth chain viable on staging tenant 88f558f5 (ROW+NOAM policies, same-issuer), broker first-hop mapped, PKCE+state preserved to same-origin callback — only missing step is a valid staged session
evidence_needed: authenticated introspection on /api/graphql; foreign object-ID read under own valid session
verify_steps: AUTH_HELPED: ROW session on developer.tst.na → POST /api/graphql `{"query":"{__schema{types{name fields{name} args{name}}}}"}` → on-org `query{subscription(teamId,appId,subscriptionId){name state product{name}}}` → read-only foreign-ID swap → diff `UserCatalogList{catalogs{id name}}` + `teams{items{id orgId}}`
impact: cross-tenant subscription read/modify, system-user password + accessSecret theft, team PII dump; Severity: high
testability: AUTH_HELPED
[HYP] b2c-cross-bu-token-boundary
class: OATH
asset: login-qa.ciam.daimlertruck.com
confidence: 67
reasoning: FRESH this cycle: ROW+NOAM share identical `iss` (tenant-level, 88f558f5.../v2.0/) and same client c387a5ab on the portal app; only differentiation is per-policy `acr` + BU-shape org claims (89 vs 102 claims ROW vs NOAM); schema scoping (Team.orgId, UserCatalogList.catalogs) is org-claim-based; jwks per-policy but iss/aud collision means backend can't distinguish BU from iss/aud alone
evidence_needed: NOAM token accepted by ROW-scoped resolver returning different orgId catalogs than same-principal ROW token (and vice versa)
verify_steps: AUTH_HELPED: one `_row` + one `_noam` account → POST /api/graphql `query UserCatalogList{userCatalogList{catalogs{id name}}}` + `teams{items{id orgId}}` from each token; diff scoping
impact: DT/DTNA BU-boundary collapse → cross-BU API subscriptions + PII; Severity: medium-high
testability: AUTH_HELPED
[HYP] rag-meilisearch-default-cred-sourceonly
class: MISCONFIG
asset: daimlertruck/SRC-rag_api (daimlertruck GitHub org, verified owner)
confidence: 40
reasoning: org confirmed verified "Daimler Truck AG", 6 public repos; SRC-rag_api main.py:72-77 sets allow_origins=["*"]+allow_credentials=True; app/middleware.py:18-22 skips JWT validation when JWT_SECRET unset; config.py:59 POSTGRES_PASSWORD default "mypassword". BUT ALL AI repos (SRC-rag_api, SRC-LibreChat, SRC-meilisearch, SRC-openai-aca-lb, SRC-librechat-prom-exporter) are upstream OSS forks (fork:true) — no Daimler-specific changes to the vuln code; live deployment UNCONFIRMED: RAG whitelisted /docs,/openapi.json,/health + Meili /indexes on all scoped hosts return 307-guarded or 404/OperationNotFound (as.api,eu.api,developer.*) — no reachable instance on scoped hostname surface
evidence_needed: a scoped host serving SRC-rag_api or MeiliSearch without JWT_SECRET/master key set (would return 200 on /openapi.json or /indexes anonymously)
verify_steps: PASSIVE: `GET https://<scoped-host>/openapi.json` / `/health` (RAG whitelist) and `/indexes` (Meili) — confirmed 307/404 on as.api,eu.api,developer.* ; no live surface found
impact: IF deployed without secret → unauth RAG doc access (internal AI embeddings/PII); Severity: high (conditional on deployment); currently source-only
testability: PASSIVE
[PARKED] rag-meilisearch-default-cred-sourceonly: confidence 40 == floor; source-only (upstream fork defaults, no Daimler-specific vuln code) + NO confirmed live deployment on any scoped host — RAG/Meili endpoints 307/404 everywhere probed. Non-actionable per program "known-vulnerable without program-specific exploit". Hold dormant, re-probe only if a new scoped host surfaces.
[PARKED] b2c-cross-bu-token-boundary: unchanged 67, still gated on two provisioned staging accounts.
[PARKED] ciam-secret-return-in-plaintext: chained on BOLA proof — re-promote only once hypothesis #1 reads a foreign subscription.
[FINAL] 1. graphql-object-id-bola-team-scoped (75) 2. b2c-cross-bu-token-boundary (67)
[NEXT] HUMAN: obtain two admin-provisioned staging accounts on login-qa.ciam tenant 88f558f5 — one `b2c_1a_signin_oidc_row` (corporate, via broker tenant e39fd9b6 `b2c_1a_signin`) + one `b2c_1a_signin_oidc_noam` (separate FTL org) for developer.tst.na.api.daimlertruck.com; complete manual login (authorize → broker → form_post authresp → NextAuth callback, PKCE S256); then AUTH_HELPED POST /api/graphql `{__schema{types{name fields{name} args{name}}}}` on-org baseline → read-only foreign-ID swap → diff `UserCatalogList` + `teams{orgId}` across row/noam tokens.
[LEARN] ACCEPTED daimlertruck-github-org-verified @ github.com/daimlertruck: org confirmed verified "Daimler Truck AG" (blog daimlertruck.com, foss@daimlertruck.com, created 2021-11-26); 6 public repos (5 upsteam forks of LibreChat/RAG/Meili/ACA-LB/prom-exporter + DT-DDS original design system). In-scope by "digital services...operated by Daimler Truck". AI-stack forks carry only upstream default-cred/MISCONFIG patterns; no Daimler-specific vuln; deployment unconfirmed.
[LEARN] REJECTED rag-librechat-meili-live-surface @ *.daimlertruck.com: RAG API whitelisted /docs,/openapi.json,/health + Meili /indexes return 307 (developer.* blanket auth) or 404/OperationNotFound (as.api,eu.api APIM gateways) — no RAG/LibreChat/Meili instance reachable on scoped hostname surface. reposcan SECRET/MISCONFIG remain source-only, non-actionable per program exploit-specificity rule.
[LEARN] REJECTED dt-dds-design-system @ daimlertruck/DT-DDS: original Daimler repo but pure frontend component library (Storybook on github.io pages, no backend, no env/secrets/config leaks in repo tree) — no reachable vulnerability.
[RISK] Daimler Truck: 42/100. Surface unchanged and hardening-positive: portals stable behind wildcard auth; CIAM correct (confidential+PKCE, implicit disabled); businessid broker self-service falsified; GitHub org confirmed owned but AI-stack forks not live-reachable on scoped hosts. No new anonymous exploit reachable — all returns (BOLA 75, cross-BU 67) gated solely on two admin-provisioned staging accounts. This cycle: read-only GETs on public GitHub metadata + scoped-host RAG/Meili/health paths; no customer/employee/auth data touched.
[HYP] graphql-object-id-bola-team-scoped
class: IDOR
asset: developer.tst.na.api.daimlertruck.com
confidence: 75
reasoning: buildManifest object-ID routes (/apis/[apiId], /apps/[appId]/subscriptions/[subscriptionId], /teams/[teamId]/system-users/associate); client bundle GraphQL ops take teamId/appId/subscriptionId; /api/graphql blanket-307 behind wildcard middleware; auth chain viable on staging tenant 88f558f5 (ROW+NOAM policies, same-issuer), broker first-hop mapped, PKCE+state preserved to same-origin callback — only missing step is a valid staged session
## 2026-09-06 14:37:29 UTC [target] (model bigpickle)
[HYP] companion-b2c-error-uri-leaks-dev-surface
class: MISCONFIG
asset: login.ciam.daimlertruck.com client cd34584a
confidence: 60
reasoning: Sending unregistered redirect_uri (e.g. //callback) to prod B2C client cd34584a returns AADB2C90006 whose error location points to `https://companion-dev.app.daimlertruck.com/api/auth/callback/azure-ad-b2c` — the prod client's configured error/callback host leaks the dev deployment hostname that was previously undiscoverable. Both companion hosts share identical build (IqPB_zhGzw2eQTiap3_bK) so dev is not isolated by version.
evidence_needed: confirm dev host is registered as error redirect for prod client and reachable (it is — 200); determine whether dev host uses intended separate B2C client/tenant or accidentally shares prod credentials
verify_steps: PASSIVE: `GET https://login.ciam.daimlertruck.com/3db550f0-0c7f-439b-8e24-e32bf233615d/b2c_1a_signin_oidc_row/oauth2/v2.0/authorize?client_id=cd34584a-7d56-4125-acb3-e8cf2e257de8&response_type=code&redirect_uri=https://companion.app.daimlertruck.com/callback&scope=openid` → error leaks dev host (confirmed); then `POST /api/auth/signin/azure-ad-b2c` on companion-dev to get its B2C tenant/client (confirmed: returns generic csrf=true, provider not wired to tenant — dev auth config differs)
impact: dev environment disclosure + potential dev/prod credential linkage; if dev B2C client uses weaker policy → AUTH escalation to prod data; Severity: medium (info-disclosure now, higher if dev client shares prod tenant)
testability: PASSIVE
[HYP] companion-proxy-http-ssrf-auth-required
class: SSRF
asset: companion.app.daimlertruck.com/api/proxy-http
confidence: 45
reasoning: buildManifest lists `/api/proxy-http` — a server-side HTTP proxy route (POST returns 401 "Unauthorized", GET/other 405 "Method not allowed"). Combined with `/api/[...slug]` catch-all (all POST 401), this is a backend proxy likely to hit internal/AI/cloud-metadata targets once authenticated. The companion app sits in istio AKS mesh (server: istio-envoy) — authenticated SSRF would reach cloud metadata 169.254.169.254 or internal services.
evidence_needed: a valid companion session (authMechanism azure-ad-b2c, prod client cd34584a) then POST /api/proxy-http to internal targets
verify_steps: AUTH_HELPED: obtain companion session → `POST /api/proxy-http` `{"url":"http://169.254.169.254/latest/meta-data/"}` and `{"url":"http://companion-service.internal/"}` → observe response; also probe `POST /api/[...slug]` for arbitrary fetch behaviour
impact: SSRF → cloud metadata keys / internal AI service access; Severity: high (conditional on reachable session)
testability: AUTH_HELPED
[HYP] companion-admin-authz-function
class: AUTH
asset: companion.app.daimlertruck.com/en/admin
confidence: 50
reasoning: buildManifest exposes `/admin` and `/api/auth/force-logout`; /admin returns 307 to B2C auth (`/?callbackUrl=%2Fen%2Fadmin`) same as /chat — blanket middleware. But companion uses a DIFFERENT B2C client (cd34584a) and DIFFERENT authMechanism than developer portal; if /admin is role-gated by a claim (e.g. admin group in B2C) rather than business-role enforcement, an employee account may reach CDN/chat admin. force-logout works unauthenticated with only CSRF (302, clears cookies) — indicator of a distinct (simpler) auth assembly on this new app.
evidence_needed: authenticated /admin response and role-claim check
verify_steps: AUTH_HELPED: ROW employee session on companion.app → `GET /en/admin` → capture admin API calls from client bundle → test role escalation within employee token
impact: chatbot admin panel / AI admin access if only employee-auth not admin-role; Severity: medium-high
testability: AUTH_HELPED
[NEXT] PROBE: `GET https://login.ciam.daimlertruck.com/3db550f0-0c7f-439b-8e24-e32bf233615d/b2c_1a_signin_oidc_row/oauth2/v2.0/authorize?...` iterate candidate registered redirect_uris on prod client cd34584a (companion.app/api/auth/callback/azure-ad-b2c, companion-dev.app/..., plus any /widget or /admin callback) to enumerate the B2C client's registered redirect allowlist and confirm exactly which companion hosts (prod/dev) are bound to this client — passive, read-only.
[LEARN] ACCEPTED companion-app-live @ companion.app.daimlertruck.com: previously NXDOMAIN prod CSP frame-src dead-reference, now LIVE prod DT Companion AI (build IqPB_zhGzw2eQTiap3_bK, frontend 1.91.0, istio-envoy). Routes /admin /chat /widget-host /api/proxy-http /api/[...slug] /widget/dt-companion.js; auth prod CIAM 3db550f0 + b2c_1a_signin_oidc_row + NEW client cd34584a; only /api/health + /api/ready public (200), all /api/* & object routes 307/401-auth; callbackUrl same-origin enforced; PKCE S256 + state + same-origin redirect_uri (matches developer portal discipline).
[LEARN] ACCEPTED companion-dev-app-exposed @ companion-dev.app.daimlertruck.com: same build/version as prod but enables showFrontendVersion:true + connectionTypeSelector:true (disabled on prod); discovered via prod B2C AADB2C90006 error leaking dev callback host. Public only /api/health+/api/ready; /api/* 401; /admin/chat 307. Dev/prod auth config differs (dev provider not wired to a live tenant client — signin returns generic csrf=true).
[LEARN] ACCEPTED b2c-error-info-leak @ login.ciam.daimlertruck.com client cd34584a: unregistered redirect_uri → AADB2C90006 whose error location points to companion-dev.app.daimlertruck.com — production client error handling discloses the dev deployment host. Informational/misconfig, not yet a compromise.
[LEARN] REJECTED companion-open-redirect / callbackUrl @ companion.app(.dev): GET/POST callbackUrl=https://evil.com and https://attacker.daimlertruck.com both replaced with origin cookie (https://companion.app.daimlertruck.com) — same-domain enforced, subdomains rejected. Not an open redirect (consistent with developer portal prior rejection).
[RISK] Daimler Truck: 45/100. Risk up (42→45) from a freshly-live unnamed production+dev AI service surface: companion.app creates a new corporate-employee AI chatbot (businessid broker chain ROW) with /admin + /api/proxy-http (SSRF class) behind a DIFFERENT B2C client (cd34584a) than the developer portal — new attack surface not in prior inventory. All mutating/SSRF/admin paths are auth-gated (307/401), so no anonymous compromise; top returns gated on a companion session (proxy-http SSRF, admin authz) or prod-B2C redirect-allowlist enumeration (passive). This cycle: read-only GET/POST of public health/ready/auth-metadata/widget JS + B2C authorize error responses; no customer/employee data, no auth bypass, no mutating requests.
## 2026-09-06 17:22:55 UTC [target] (model bigpickle)
[PRIO] companion.app.daimlertruck.com,7.8,attack_surface=8,business_value=8,tech_exposure=9,gate_ease=2,cloud_surface=8,freshness=9
[PRIO] developer.as.api.daimlertruck.com,8.0,attack_surface=9,business_value=9,tech_exposure=8,gate_ease=3,cloud_surface=7,freshness=8
[PRIO] login.ciam.daimlertruck.com,7.2,attack_surface=7,business_value=10,tech_exposure=9,gate_ease=1,cloud_surface=6,freshness=8
[PRIO] companion-dev.app.daimlertruck.com,6.5,attack_surface=7,business_value=6,tech_exposure=7,gate_ease=3,cloud_surface=7,freshness=9
[HYP] companion-b2c-redirect-uri-allowlist-enumeration
class: MISCONFIG
asset: login.ciam.daimlertruck.com client cd34584a
confidence: 60
reasoning: Sending unregistered redirect_uri to prod B2C client cd34584a returns AADB2C90006 whose error location field discloses companion-dev.app.daimlertruck.com — a previously undiscoverable dev deployment host. The prod client's error handling reveals which hosts it recognizes vs rejects. Iterating callback paths (/api/auth/callback/azure-ad-b2c, /callback, /widget/callback, /admin/callback) on both prod and dev hosts will map the full registered redirect URI allowlist for the highest-sensitivity identity client in the program.
evidence_needed: AADB2C90006 error responses with location fields showing which redirect URIs are registered vs unregistered on prod client cd34584a; confirm whether companion-dev is registered as a valid redirect target (not just an error fallback)
verify_steps: PASSIVE: GET `https://login.ciam.daimlertruck.com/3db550f0-0c7f-439b-8e24-e32bf233615d/b2c_1a_signin_oidc_row/oauth2/v2.0/authorize?client_id=cd34584a-7d56-4125-acb3-e8cf2e257de8&response_type=code&redirect_uri=https://companion.app.daimlertruck.com/api/auth/callback/azure-ad-b2c&scope=openid` (registered — expect AADB2C90006 or redirect). Repeat with redirect_uri=https://companion-dev.app.daimlertruck.com/api/auth/callback/azure-ad-b2c, /widget/callback, /admin/callback. Diff error messages between registered and unregistered URIs.
impact: Dev environment registration on prod B2C client → credential linkage risk; if dev uses weaker auth config → escalation path to prod; Severity: medium (info-disclosure + potential auth escalation)
testability: PASSIVE
[HYP] companion-proxy-http-ssrf-auth-required
class: SSRF
asset: companion.app.daimlertruck.com/api/proxy-http
confidence: 48
reasoning: buildManifest lists /api/proxy-http — POST returns 401 "Unauthorized", GET/other 405 "Method not allowed". This is a server-side HTTP proxy likely used for AI/web-browsing features. The companion app runs in istio AKS mesh (server: istio-envoy) — authenticated SSRF would reach cloud metadata 169.254.169.254 or internal AI services. /api/[...slug] catch-all also POST 401.
evidence_needed: a valid companion session (authMechanism azure-ad-b2c, prod client cd34584a) then POST /api/proxy-http with internal target URLs
verify_steps: AUTH_HELPED: obtain companion.app session → `POST /api/proxy-http` `{"url":"http://169.254.169.254/latest/meta-data/"}` and `{"url":"http://companion-service.internal/"}` → observe response
impact: SSRF → cloud metadata keys / internal AI service access; Severity: high (conditional on reachable session)
testability: AUTH_HELPED
[HYP] graphql-object-id-bola-team-scoped
class: IDOR
asset: developer.as.api.daimlertruck.com
confidence: 75
reasoning: /graphql returns 307 to Azure AD B2C; buildManifest shows explicit object-ID routes (/apis/[apiId], /subscriptions/[subscriptionId], /teams/[teamId]/system-users/associate); client bundle shows GraphQL ops with these IDs; portal-blanket-auth-middleware confirmed; only barrier is valid B2C session
evidence_needed: authenticated GraphQL introspection showing mutation types + test cross-tenant ID access
verify_steps: AUTH_HELPED: one `_row` + one `_noam` account → POST /api/graphql `{__schema{types{name fields{name args{name}}}}}` + `teams{items{id orgId}}` from each token; diff scoping
impact: cross-BU API subscriptions + PII; Severity: medium-high
testability: AUTH_HELPED
[PARKED] companion-proxy-http-ssrf-auth-required: confidence 48 < 50; requires companion session which does not yet exist; all evidence is from buildManifest/route probing only. Re-promote once companion auth is obtained.
[PARKED] graphql-object-id-bola-team-scoped: confidence 75 but blocked on two admin-provisioned staging accounts — unchanged since 2026-09-03. No new evidence this cycle.
[FINAL] 1. companion-b2c-redirect-uri-allowlist-enumeration (60) — PASSIVE, executable now, novel surface (companion-app discovered this cycle)
[FINAL] 2. graphql-object-id-bola-team-scoped (75) — AUTH_HELPED, highest potential impact
[FINAL] 3. companion-proxy-http-ssrf-auth-required (48) — PARKED, needs auth
[NEXT] PROBE: `GET https://login.ciam.daimlertruck.com/3db550f0-0c7f-439b-8e24-e32bf233615d/b2c_1a_signin_oidc_row/oauth2/v2.0/authorize?client_id=cd34584a-7d56-4125-acb3-e8cf2e257de8&response_type=code&redirect_uri=https://companion.app.daimlertruck.com/api/auth/callback/azure-ad-b2c&scope=openid` — test whether the REGISTERED callback URI triggers a different error (or redirect) than the unregistered one that leaked the dev host. Then iterate: redirect_uri=https://companion-dev.app.daimlertruck.com/api/auth/callback/azure-ad-b2c, redirect_uri=https://companion.app.daimlertruck.com/widget/callback, redirect_uri=https://companion.app.daimlertruck.com/admin/callback. Diff error messages (AADB2C90006 vs AADB2C90227 or success). Passive, read-only, <=1 req/sec.
[LEARN] ACCEPTED companion-app-live @ companion.app.daimlertruck.com: LIVE prod DT Companion AI (build IqPB_zhGzw2eQTiap3_bK, frontend 1.91.0, istio-envoy). /admin /chat /api/proxy-http behind B2C client cd34584a; callbackUrl same-origin enforced.
[LEARN] ACCEPTED companion-dev-app-exposed @ companion-dev.app.daimlertruck.com: identical build, debug flags, dev auth not wired to live tenant.
[LEARN] ACCEPTED b2c-error-info-leak @ login.ciam.daimlertruck.com: prod B2C error discloses companion-dev host.
[LEARN] REJECTED companion-open-redirect: callbackUrl validated to same-domain; subdomains rejected — not an open redirect.
[LEARN] ACCEPTED b2c-cross-bu-token-boundary @ login-qa.ciam.daimlertruck.com: ROW+NOAM share tenant-level issuer + same portal client c387a5ab.
[RISK] Daimler Truck: 45/100. Risk steady (42→45 last cycle, unchanged now). New companion.app surface adds /api/proxy-http (SSRF class) and /admin, but all behind auth (307/401). Developer portal BOLA + cross-BU token boundary remain highest potential impact but gated on two admin-provisioned staging accounts. No anonymous exploit reachable this cycle. The B2C error-URI enumeration is the only new passive finding that could narrow the gap to a reportable finding. This cycle: read-only GETs on public B2C authorize error endpoints + health/ready/widget JS; no customer/employee data, no auth bypass, no mutating requests.
## 2026-09-06 19:35:31 UTC [target] (model bigpickle)
[PRIO] companion.app.daimlertruck.com,7.8,attack_surface=8,business_value=8,tech_exposure=9,gate_ease=2,cloud_surface=8,freshness=9
[PRIO] login.ciam.daimlertruck.com,7.4,attack_surface=7,business_value=10,tech_exposure=9,gate_ease=1,cloud_surface=7,freshness=9
[PRIO] developer.as.api.daimlertruck.com,8.0,attack_surface=9,business_value=9,tech_exposure=8,gate_ease=3,cloud_surface=7,freshness=8
[HYP] companion-admin-authz-role-claim
class: AUTH
asset: companion.app.daimlertruck.com/admin
confidence: 40
reasoning: /admin 307-to-B2C like /chat (blanket middleware); companion uses DIFFERENT client cd34584a + single azure-ad-b2c provider vs developer portal. If admin gated only by "authenticated employee" claim (not groups/appRoles), any ROW token reaches AI admin panel. 89-claim ROW contract includes groups — need server-side vs client-side check.
impact: chatbot admin console reachable by any employee → AI config surface; medium-high
testability: AUTH_HELPED
[HYP] companion-proxy-http-ssrf-auth-required
class: SSRF
asset: companion.app.daimlertruck.com/api/proxy-http
confidence: 48
reasoning: POST /api/proxy-http → 401 (real route); istio AKS mesh; server-side proxy for AI web-browsing; auth-gated SSRF to 169.254.169.254 / internal AI services is top post-auth prize.
impact: SSRF → cloud metadata / internal service access; high
testability: AUTH_HELPED
[HYP] graphql-object-id-bola-team-scoped
class: IDOR
asset: developer.as.api.daimlertruck.com
confidence: 75
reasoning: unchanged — /api/graphql 307-to-B2C; buildManifest object-ID routes; GraphQL ops in client bundle; blanket middleware; barrier = valid B2C session.
impact: cross-BU API subscriptions + PII; medium-high
testability: AUTH_HELPED
[PARKED] companion-admin-authz-role-claim: confidence 40 < 50, session-gated, no role-claim evidence passively.
[PARKED] companion-proxy-http-ssrf-auth-required: 48 < 50, session-gated. Re-promote on companion session.
[PARKED] graphql-object-id-bola-team-scoped: 75 but blocked on two admin-provisioned staging accounts since 2026-09-03.
[FINAL] 1. graphql-object-id-bola-team-scoped (75) — AUTH_HELPED, highest potential impact
[FINAL] 2. companion-b2c-dev-callback-registered — CONFIRMED MISCONFIG this cycle (exact allowlist evidence)
[NEXT] PROBE: GET https://login-qa.ciam.daimlertruck.com/88f558f5-a216-470b-b34a-3164f5d6ec0f/b2c_1a_signin_oidc_noam/v2.0/.well-known/openid-configuration — diff NOAM staging issuer/policy surface against ROW prior to drafting the misconfig report (expect same tenant-level issuer + shared client, reinforcing b2c-cross-bu-token-boundary with stage evidence). Passive, <=1 rps.
[LEARN] ACCEPTED companion-b2c-dev-callback-registered @ login.ciam.daimlertruck.com client cd34584a: dev callback URI is a REGISTERED redirect target AND the global AADB2C90006 error fallback on the PRODUCTION identity client. Prod sign-in fully wired (302→B2C authorize, PKCE S256, scope `openid offline_access <client_id>`, redirect_uri same-origin); dev sign-in unwired (504 App Gateway). MISCONFIG confirmed with exact allowlist — reportable as info/misconfig (no live code-exchange chain today).
[LEARN] REJECTED positional-callback-enumeration @ companion.app: /widget/callback and /admin/callback are NOT registered B2C URIs — only /api/auth/callback/azure-ad-b2c per host. No extra allowlist surface.
[RISK] Daimler Truck: 46/100. Risk up (45→46). The allowlist enumeration converts a prior error-page info-leak into a concrete, evidence-backed MISCONFIG: production identity client cd34584a binds the dev deployment (first-class callback + error fallback). Not yet a compromise — dev provider is unwired (504) so no code exchange/token flow is reachable today; SSRF/admin on companion remain auth-gated; BOLA still blocked on staging accounts. This cycle: read-only GETs of B2C authorize (registered vs unregistered redirect_uri), providers/csrf metadata, policy well-known, plus two NextAuth sign-in initiations against self-issued CSRF cookies (no credentials; prod→redirect only, dev→harmless 504). No customer/employee data, no auth bypass, no mutating requests, <=1 rps.
## 2026-09-06 21:43:30 UTC [target] (model bigpickle)
[PRIO] companion.app.daimlertruck.com,7.8,attack_surface=8,business_value=8,tech_exposure=9,gate_ease=2,cloud_surface=8,freshness=9
[PRIO] login.ciam.daimlertruck.com,7.4,attack_surface=7,business_value=10,tech_exposure=9,gate_ease=1,cloud_surface=7,freshness=9
[PRIO] developer.as.api.daimlertruck.com,8.0,attack_surface=9,business_value=9,tech_exposure=8,gate_ease=3,cloud_surface=7,freshness=8
[HYP] companion-admin-authz-role-claim
class: AUTH
asset: companion.app.daimlertruck.com/admin
confidence: 42
reasoning: NOAM+ROW claim contracts now machine-verified to carry `UserAppRoles`+`AppRoles`+`isXOrg` in token space; /admin is behind same blanket 307 as /chat; whether adminz is a server-side token-claim check vs client-side flag is undetermined; single azure-ad-b2c provider (client cd34584a) on companion vs dual-provider portal.
evidence_needed: authenticated companion token with/without Admin appRole claim reaching /admin vs /chat
verify_steps: AUTH_HELPED: any valid ROW session → GET /admin and GET /chat; diff status/redirect; then inspect JWT appRoles claim presence
impact: any employee ROW token reaching AI admin console; medium-high
testability: AUTH_HELPED
[HYP] companion-proxy-http-ssrf-auth-required
class: SSRF
asset: companion.app.daimlertruck.com/api/proxy-http
confidence: 48
reasoning: POST /api/proxy-http → 401 (real route); istio-AKS mesh; server-side proxy for AI web-browsing; 169.254.169.254 metadata + internal AI services gated only by companion session.
evidence_needed: valid companion session then POST with internal URLs
verify_steps: AUTH_HELPED: `POST /api/proxy-http` {"url":"http://169.254.169.254/latest/meta-data/"} and {"url":"http://companion-service.internal/"} with session cookie
impact: cloud-metadata keys / internal AI service access; high (conditional)
testability: AUTH_HELPED
[HYP] graphql-object-id-bola-team-scoped
class: IDOR
asset: developer.as.api.daimlertruck.com
confidence: 75
reasoning: unchanged — /api/graphql 307-to-B2C; buildManifest object-ID routes; GraphQL ops in client bundle; blanket middleware; barrier = valid B2C session.
evidence_needed: authenticated introspection + cross-tenant ID diff
verify_steps: AUTH_HELPED: `_row` + `_noam` accounts → POST /api/graphql `{__schema{types{name}}}` then `teams{items{id orgId}}` from both; diff scoping
impact: cross-BU API subscriptions + dealer PII; medium-high
testability: AUTH_HELPED
[PARKED] companion-admin-authz-role-claim: 42 < 50, session-gated, no server-side role-claim evidence passively; claim contract shows roles ride in tokens but cannot prove server enforcement.
[PARKED] companion-proxy-http-ssrf-auth-required: 48 < 50, session-gated; no listener evidence beyond 401.
[PARKED] graphql-object-id-bola-team-scoped: 75 but blocked on two admin-provisioned staging accounts since 2026-09-03; no new evidence this cycle.
[FINAL] 1. graphql-object-id-bola-team-scoped (75) — AUTH_HELPED, highest impact
[FINAL] 2. companion-b2c-dev-callback-registered — CONFIRMED MISCONFIG (exact allowlist, both envs)
[FINAL] 3. b2c-cross-bu-token-boundary — now extended to NOAM staging; claim superset incl. FTLOrg* dealer PII
[NEXT] PROBE: `GET https://companion.app.daimlertruck.com/_next/static/IqPB_zhGzw2eQTiap3_bK/_buildManifest.js` — enumerate exact companion route/page structure (admin/*, widget-host/*, callback pages, rewrites) to complete the new-surface route map and pin widget-host/callback positions against the B2C allowlist. Passive GET, <=1 rps.
[LEARN] ACCEPTED b2c-cross-bu-token-boundary @ login-qa.ciam + login.ciam: NOAM openid-configuration same shape as ROW per tenant (issuer …/v2.0/), token endpoint confidential-only; NOAM claim set = superset incl. FTLOrg* dealer-network PII (SAP codes, primary-contact names/emails, street/telephone) + UserAppRoles/AppRoles — same-issuer cross-BU collision extended with staging NOAM evidence.
[LEARN] ACCEPTED companion-widget-inert @ companion.app/widget/dt-companion.js: iframe launcher only (`?externalDomain={{bool}}`), localStorage drag state, clipboard-read/write allow; no /api refs, no tokens, no role logic — no anonymous attack surface in the embedder.
[LEARN] REJECTED widget-embed-auth-bypass @ companion iframe: external-domain embedding still routes all app pages through B2C 307 + same-origin callbackUrl; clipboard allow + drag position offer no demonstrated exploit (clickjacking class).
[RISK] Daimler Truck: 46/100. Risk steady (unchanged). NOAM discovery documents a richer dealer-PII claim surface (FTLOrg* superset) under the same per-tenant issuer — reinforces the cross-BU boundary finding but adds no reachable exposure; companion widget is inert; all companion/dev/admin/SSRF paths remain B2C-gated. Only reportable item remains the confirmed dev-callback MISCONFIG on prod client cd34584a (info-level; dev provider unwired). This cycle: two read-only discovery GETs on B2C openid-configuration + one public widget JS fetch. No customer/employee data, no auth bypass, no mutating requests, <=1 rps.
## 2026-09-06 23:40:16 UTC [target] (model bigpickle)
[HYP] companion-proxy-http-ssrf-auth-required
class: SSRF
asset: companion.app.daimlertruck.com/api/proxy-http
confidence: 55
reasoning: GET→405 (30B) proves registered handler exempt from blanket wildcard (catch-all /api/px-9f2c→401); POST→401 unauth = only live method; handler named `proxy-http` on istio-AKS mesh; identical listener on companion-dev (same build).
evidence_needed: valid companion session then POST body `{"url":"http://169.254.169.254/latest/meta-data/"}` and `{"url":"http://companion-service.internal/"}` → capture status + response
verify_steps: AUTH_HELPED: `POST /api/proxy-http` with session cookie vs without; diff status; then internal/metadata URLs
impact: cloud-metadata creds / internal AI-service access; high (conditional)
testability: AUTH_HELPED
[HYP] graphql-object-id-bola-team-scoped
class: IDOR
asset: developer.as.api.daimlertruck.com
confidence: 75
reasoning: unchanged — /api/graphql 307-to-B2C; buildManifest object-ID routes (/apis/[apiId], teams/[teamId]/system-users/associate); GraphQL ops in client bundle; blanket middleware; barrier = valid B2C session.
evidence_needed: authenticated introspection + cross-tenant ID diff
verify_steps: AUTH_HELPED: `_row` + `_noam` accounts → POST /api/graphql `{__schema{types{name}}}` then `teams{items{id orgId}}` from both; diff scoping
impact: cross-BU API subscriptions + dealer PII; medium-high
testability: AUTH_HELPED
[HYP] companion-admin-authz-role-claim
class: AUTH
asset: companion.app.daimlertruck.com/admin
confidence: 42
reasoning: 11 admin-chained chunks fetched — no UserAppRoles/AppRoles/isAdmin strings statically reachable (framework/vendor chunks); role enforcement server-side undetermined; /admin behind same single-provider B2C as /chat.
evidence_needed: authenticated ROW token reaching /admin vs /chat; inspect appRoles claim
verify_steps: AUTH_HELPED: valid session → GET /admin, GET /chat; diff status; decode JWT appRoles
impact: any employee ROW token reaching AI admin console; medium-high
testability: AUTH_HELPED
[NEXT] PROBE: `GET https://companion.app.daimlertruck.com/api/{admin,chat,context,memory,models,user,users,files}` — diff status codes (401 catch-all vs 405/other = first-class handler) to map companion's real API handlers anonymously; strongest listener becomes the AUTH_HELPED deep-probe target. Read-only GETs, <=1 rps.
[LEARN] ACCEPTED companion-proxy-http-ssrf-auth-required @ companion.app: GET /api/proxy-http→405 (30B, prod+dev) vs catch-all→401 proves first-class POST-only handler exempt from blanket middleware — listener + middleware-exempt evidence now concrete.
[LEARN] ACCEPTED companion-route-map-complete @ companion.app: buildManifest = 6 pages + 6 api routes, no object-ID routes, empty rewrites.
[LEARN] REJECTED nemotron3-companion-object-id-BOLA: no object-ID route surface on companion; route basis was developer.*.
[LEARN] INFO force-logout @ companion.app(+dev): /api/auth/force-logout 302→/ unauth — logout-CSRF class, program out-of-scope; no hypothesis spawned.
[LEARN] ACCEPTED companion-health-no-leak @ companion.app: health/ready = static {"status":...}, no uptime/build disclosure (dev+prod identical) vs developer /api/healthcheck uptime leak.
[RISK] Daimler Truck: 46/100. Risk steady. New cycle evidence strengthens the companion proxy surface (real 405-listener, both envs) but stays strictly B2C-gated; force-logout is out-of-scope class; dev auth remains unwired (no token flow reachable). Only reportable item is still the dev-callback MISCONFIG on prod client cd34584a (info-level). This cycle: 2 manifest/providers fetches + 11 chunk GETs + 6 status-code probes on companion (prod+dev), all read-only, <=1 rps. No customer/employee data, no auth bypass, no mutating requests.
## 2026-09-07 01:22:10 UTC [target] (model bigpickle)
[HYP] graphql-object-id-bola-team-scoped
class: IDOR
asset: developer.as.api.daimlertruck.com/api/graphql
confidence: 75
reasoning: /api/graphql 307-to-B2C on POST; buildManifest exposes object-ID routes (/apis/[apiId], teams/[teamId]/system-users/associate); client bundle carries GraphQL ops with these IDs; blanket middleware; barrier = valid B2C session. Blocked on two staging accounts since 09-03.
evidence_needed: authenticated introspection + cross-tenant ID diff between _row and _noam scopes.
verify_steps: AUTH_HELPED: `_row` + `_noam` sessions → POST /api/graphql `{__schema{types{name}}}` then `teams{items{id orgId}}` from each; diff scoping.
impact: cross-BU API subscriptions + dealer-network PII (FTLOrg* claims); medium-high
testability: AUTH_HELPED
[HYP] companion-proxy-http-ssrf-auth-required
class: SSRF
asset: companion.app.daimlertruck.com/api/proxy-http
confidence: 55
reasoning: GET→405/30B proves registered POST-only handler exempt from blanket wildcard (all 8 other /api/* → 401 catch-all this cycle); server-side proxy for AI web-browsing on istio-AKS mesh; identical listener on dev.
evidence_needed: valid companion session then POST `{"url":"http://169.254.169.254/latest/meta-data/"}` and internal AI-service URL; diff status/body.
verify_steps: AUTH_HELPED: `POST /api/proxy-http` with session cookie vs without; then internal/metadata URLs.
impact: cloud-metadata keys / internal AI-service access; high (conditional)
testability: AUTH_HELPED
[HYP] noam-token-cross-bu-acceptance
class: AUTH
asset: developer.na.api.daimlertruck.com/api/graphql
confidence: 55
reasoning: per-tenant issuer shared by ROW+NOAM policies + single portal client (c387a5ab staging); NOAM claim set is a superset incl. FTLOrg* dealer PII; server-side BU gating (acr/org) unproven — same client may accept NOAM token on ROW-scoped data.
evidence_needed: NOAM session token accepted by ROW-scoped portal API vs ROW token; diff on org-scoped fields.
verify_steps: AUTH_HELPED: valid _noam session → POST /api/graphql `teams{items{orgId}}`; same with _row; if org separation fails → cross-BU PII.
impact: NOAM-acquired token reaching ROW tenant data / dealer PII; medium-high
testability: AUTH_HELPED
[NEXT] HUMAN: request two admin-provisioned staging identities (_row + _noam) from bugs.olivermaicher.eu — unblocks both FINAL #1 and #3 (GraphQL clipboard-introspection + cross-BU acceptance on developer.NA, POST /api/graphql with session). All anonymous surface on companion + developer portals is now exhausted (401-catch-all, 405-proxy, 307-B2C confirmed).
[RISK] Daimler Truck: 46/100. Risk steady. This cycle: four clean read-only GETs (buildManifest, proxy-http, providers on both companion hosts), no mutating requests, <=1 rps. All prior backtick-artifact anomalies resolved — no new reachable surface. Companion stays fully B2C-gated with a single same-origin callback; only reportable item remains the dev-callback-inside-prod-B2C-allowlist MISCONFIG (info-level, no live code-exchange chain). Deep-hunt remains blocked on provisioned staging accounts (FINAL #1/#3) — escalating to HUMAN next.
## 2026-09-07 06:13:15 UTC [target] (model bigpickle)
## 2026-09-07 12:52:12 UTC [target] (model bigpickle)
[PRIO] login.ciam.daimlertruck.com,7.95,attack_surface=8,business_value=9,tech_exposure=8,gate_ease=2,cloud_surface=9,freshness=9
[PRIO] developer.as.api.daimlertruck.com,7.75,attack_surface=9,business_value=9,tech_exposure=8,gate_ease=3,cloud_surface=7,freshness=9
[PRIO] companion.app.daimlertruck.com,7.50,attack_surface=9,business_value=8,tech_exposure=8,gate_ease=3,cloud_surface=8,freshness=10
[HYP] b2c-cross-bu-token-boundary-abuse
class: AUTH
asset: login.ciam.daimlertruck.com + login-qa.ciam.daimlertruck.com
confidence: 75
reasoning: Both prod (3db550f0) and staging (88f558f5) B2C tenants issue ROW and NOAM policies under identical issuer URI. Token validation by downstream APIs likely checks issuer+aud+signature only; acr claim and org-scoped claims (FTLOrg*) are sole BU differentiators. NOAM claim superset includes dealer PII (FTLOrgPrimaryContactEmail/Name, telephoneNumber_Org, street_Org, FTLOrgSapCode). No evidence downstream APIs enforce acr/org gating.
evidence_needed: Valid NOAM session token presented to ROW-scoped developer portal API or companion endpoint; server returns 200 + ROW data instead of 403.
verify_steps: AUTH_HELPED: Obtain valid _noam session → POST developer.na.api.daimlertruck.com/api/graphql `{teams{items{id orgId}}}` — same query with _row session; diff orgId values. If same or missing → cross-BU collision.
impact: Cross-BU token acceptance → ROW employee accesses NOAM dealer-network PII or vice versa → High (PII + privilege escalation across business unit)
testability: AUTH_HELPED
[HYP] graphql-object-id-bola-team-scoped
class: IDOR
asset: developer.as.api.daimlertruck.com/api/graphql
confidence: 75
reasoning: /api/graphql returns 307 to B2C on all 6 portals; buildManifest exposes object-ID routes (/apis/[apiId], teams/[teamId]/system-users/associate, subscriptions/[subscriptionId], products/[productId]/subscribe); client bundle carries GraphQL ops with tenant-scoped IDs; blanket auth middleware protects all routes; barrier = valid B2C session. Dual providers (ROW client 205f35f7, NA client c387a5ab) suggest regional tenant separation — but same build manifests across prod/test and identical GraphQL schema structure raise cross-tenant BOLA risk.
evidence_needed: Authenticated introspection showing mutation types with tenant-scoped ID arguments; cross-region ID substitution (AS apiId → EU portal) returning 200 + data instead of 403.
verify_steps: AUTH_HELPED: _row session → POST developer.as.api.daimlertruck.com/api/graphql `{__schema{types{name}}}` → enumerate mutations → test createSubscription with EU apiId; diff result.
impact: Cross-tenant API subscription takeover, key rotation, webhook hijacking across AS/EU/NA portals → Critical
testability: AUTH_HELPED
[HYP] companion-proxy-http-ssrf-post-auth
class: SSRF
asset: companion.app.daimlertruck.com/api/proxy-http
confidence: 55
reasoning: GET→405 (30B) vs all other /api/*→401 catch-all proves registered POST-only handler exempt from blanket middleware. Server-side proxy for AI web-browsing on istio-AKS mesh (frontend 1.91.0). Identical build on companion-dev. Auth middleware active (401 without session). Single B2C provider (cd34584a, ROW policy only). If proxy logic does not restrict target URL schemes/hosts, SSRF to cloud metadata (169.254.169.254) or internal AI-service mesh is possible.
evidence_needed: Valid companion session → POST /api/proxy-http with `{"url":"http://169.254.169.254/latest/meta-data/"}` → capture status + response body showing instance metadata / IAM creds.
verify_steps: AUTH_HELPED: Obtain valid B2C session via companion prod authorize → POST /api/proxy-http with metadata URL; then internal-service URL; diff status/body.
impact: Cloud-metadata IAM keys / internal AI-service lateral movement → High (conditional on post-auth)
testability: AUTH_HELPED
[NEXT] HUMAN: Request two admin-provisioned staging identities (ROW employee + NOAM partner) from bugs.olivermaicher.eu — unblocks FINAL #1 (cross-BU token boundary test at login.ciam), FINAL #2 (GraphQL introspection + BOLA at developer.*), and FINAL #3 (SSRF proxy test at companion.app). All anonymous surface on developer portals (401-catch-all, 307-B2C, buildManifest extracted) and companion apps (401-catch-all, 405-proxy-http, 200-health) is exhausted. No further passive probes yield new surface.
[LEARN] ACCEPTED b2c-cross-bu-token-boundary @ login.ciam + login-qa.ciam: ROW+NOAM share identical issuer URI per tenant; only acr + org-shape claims differentiate BU; NOAM superset includes dealer PII — confirmed passively.
[LEARN] ACCEPTED graphql-object-id-bola-team-scoped: /api/graphql 307-to-B2C; buildManifest + client bundle confirm object-ID route surface across all 6 portals.
[LEARN] ACCEPTED companion-proxy-http-ssrf-auth-required: GET→405 proves POST-only first-class handler; all other /api/* → 401 catch-all; auth middleware active post-dev-fix.
[LEARN] REJECTED companion-dev-auth-bypass: dev companion now wired to prod B2C (3db550f0, cd34584a, b2c_1a_signin_oidc_row, PKCE S256 + state); /api/proxy-http returns 401; /admin /chat 307→B2C. Prior "unwired" finding stale.
[LEARN] REJECTED companion-dev-weaker-auth-bypass-confirmed: middleware active on all routes; no auth bypass path found.
[LEARN] REJECTED companion-proxy-http-405 @ dev: now returns 401, not 405 — middleware active.
[RISK] daimler-truck: 78/100. Risk steady. Attack surface well-characterized: 6 developer portals with GraphQL + object-ID REST routes behind Azure AD B2C (dual ROW/NA providers); companion app with middleware-exempt proxy-http + catch-all slug routes (SSRF/BOLA candidates); prod B2C tenant issues ROW+NOAM under same issuer (cross-BU token boundary relies solely on acr/org claim validation); companion-dev callback registered in prod B2C client allowlist (info-level misconfig). Deep-hunt blocked on staging accounts — all anonymous surface exhausted. No customer/employee data accessed, no auth bypass, no mutating requests this cycle.
## 2026-09-07 18:12:47 UTC [target] (model bigpickle)
[HYP] b2c-cross-bu-token-boundary-abuse
class: AUTH
asset: login.ciam.daimlertruck.com + login-qa.ciam.daimlertruck.com
confidence: 75
reasoning: prod (3db550f0) + staging (88f558f5) issue ROW + NOAM policies under IDENTICAL issuer URI; single RSA RS256 key per policy on same tenant jwks; NOAM claim superset = FTLOrg* dealer PII (contact email/name, phone, address, SAP codes); BU differentiation relies solely on acr + org-claim enforcement by downstream APIs. No endpoint observed validating acr/org at proxy/API layer.
evidence_needed: NOAM token accepted by ROW-scoped developer portal API (200 + ROW data instead of 403); same client c387a5ab accepting cross-policy token.
verify_steps: AUTH_HELPED: _noam session → POST developer.na.api.daimlertruck.com/api/graphql `{teams{items{id orgId}}}`; repeat with _row session; diff orgId field values — identical/missing org gating = cross-BU collision.
impact: ROW employee → NOAM dealer-network PII or NOAM partner → ROW internal APIs; high (PII + privilege escalation).
testability: AUTH_HELPED
[HYP] graphql-object-id-bola-team-scoped
class: IDOR
asset: developer.as.api.daimlertruck.com/api/graphql
confidence: 75
reasoning: buildManifest exposes object-ID routes (/apis/[apiId], /teams/[teamId]/system-users/associate, /subscriptions/[subscriptionId], /products/[productId]/subscribe); client bundle carries GraphQL ops (subscription(teamId,appId,subscriptionId)); identical build manifests prod+test imply identical authz logic; dual providers (ROW 205f35f7 / NA c387a5ab) imply regional tenant separation, raising cross-region BOLA risk on shared schema.
evidence_needed: post-auth introspection enumerating mutations keyed on tenant-scoped IDs; cross-region ID swap (as.apiId → eu portal) returning 200 + data instead of 403.
verify_steps: AUTH_HELPED: _row session → POST /api/graphql introspection → read-only on-org subscription(teamId,appId,subscriptionId) baseline → foreign-ID swap → diff 200/403.
impact: cross-tenant subscription takeover, key rotation, webhook hijack across AS/EU/NA; critical.
testability: AUTH_HELPED
[HYP] companion-proxy-http-ssrf-post-auth
class: SSRF
asset: companion.app.daimlertruck.com/api/proxy-http
confidence: 55
reasoning: GET→405 (30B) vs all other /api/*→401 catch-all proves registered POST-only handler exempt from blanket middleware; /api/[...slug] slots (chat, context, memory, models, user, users, files) all →401; server-side AI web-browsing proxy on istio-AKS mesh (frontend 1.91.0); auth now active (401) on prod+dev; single B2C client cd34584a.
evidence_needed: valid companion session → POST /api/proxy-http `{"url":"http://169.254.169.254/latest/meta-data/"}` → status+body; internal AI-service URL diff.
verify_steps: AUTH_HELPED: B2C session → POST proxy-http with metadata URL then internal host; diff status/body; repeat on dev (identical build).
impact: cloud-metadata IAM keys / internal AI-service lateral movement; high (conditional on post-auth).
testability: AUTH_HELPED
[NEXT] HUMAN: Request two admin-provisioned staging identities from bugs.olivermaicher.eu — one `b2c_1a_signin_oidc_row` (corporate, via broker tenant e39fd9b6) + one `b2c_1a_signin_oidc_noam` (separate FTL org) on login-qa.ciam tenant 88f558f5 for developer.tst.na; ALSO request full broker tenant GUIDs (or equivalent) to re-verify login.businessid-qa anonymous OIDC/JWKS. These unblock FINAL #1 (cross-BU token test: NOAM vs ROW session → POST /api/graphql `{teams{items{orgId}}}`), FINAL #2 (GraphQL introspection + cross-tenant ID swap), FINAL #3 (companion session → POST /api/proxy-http metadata URL). All anonymous surface re-verified exhausted this cycle (docs 307, broker root 404 prod+staging, no sibling hosts).
[LEARN] ACCEPTED portal-blanket-auth-middleware @ developer.*: /docs and /docs/apis now 307→B2C (len 21/28) on prod+test — the /docs/:slug* rewrite sits behind the wildcard middleware; no public docs content.
[LEARN] REJECTED broker-oidc-surface @ login.businessid-qa: bare-root 404 len=103 is now IDENTICAL on prod login.businessid — broker landing removal is consistent host hardening across prod+staging, not a qa-specific regression or vuln.
[LEARN] ACCEPTED companion-deploy-separation @ companion.app.daimlertruck.com (9.163.68.3) vs companion-dev.app.daimlertruck.com (20.224.79.104): distinct Azure deploys confirmed via DNS; companion-*/widget.app siblings all NXDOMAIN.
[RISK] daimler-truck: 78. Steady. Anonymous surface re-verified closed this cycle (docs auth-gated; broker root 404 on both prod+staging; no new sibling companion hosts). Exposure concentrates on three AUTH_HELPED hypotheses — cross-BU token boundary (identical issuer/jwks, only acr/org gating), GraphQL object-ID BOLA across regional portals, companion proxy-http SSRF — all blocked on two staging identities. Only currently reportable item remains the info-level misconfig (dev companion callback registered in prod B2C client cd34584a allowlist). No customer/employee data accessed, no auth bypass, no mutating requests; 7 read-only GETs + 9 DNS resolutions this cycle, <=1 rps.
## 2026-09-07 21:41:55 UTC [target] (model bigpickle)
## 2026-09-07 23:48:13 UTC [target] (model bigpickle)
[HYP] b2c-cross-bu-token-boundary-abuse
class: AUTH
asset: login.ciam.daimlertruck.com + login-qa.ciam.daimlertruck.com
confidence: 75
reasoning: prod (3db550f0) + staging (88f558f5) issue ROW+NOAM policies under IDENTICAL issuer URI and same jwks; NOAM claim superset carries FTLOrg* dealer PII (contacts/phones/address/SAP codes); BU boundary relies solely on acr/org enforcement by downstream portals — no endpoint observed validating acr/org at proxy layer; same client c387a5ab serves portal NA provider.
evidence_needed: NOAM token accepted by ROW-scoped portal API (200 + ROW data instead of 403); cross-policy token usable on same client.
verify_steps: AUTH_HELPED: _noam session → POST developer.na.api.daimlertruck.com/api/graphql `{teams{items{orgId}}}`; repeat with _row session; diff orgId values.
impact: ROW employee↔NOAM partner PII + privilege crossing — High/Critical.
testability: AUTH_HELPED
[HYP] graphql-object-id-bola-team-scoped
class: IDOR
asset: developer.as.api.daimlertruck.com/api/graphql
confidence: 75
reasoning: buildManifest + client bundle confirm object-ID routes (/apis/[apiId], teams/[teamId]/system-users/associate, subscriptions/[subscriptionId], products/[productId]/subscribe) and GraphQL ops keyed on tenant-scoped IDs; identical build manifests prod+test imply shared authz logic; dual providers (ROW 205f35f7 / NA c387a5ab) = regional tenants on shared schema — cross-region ID swap candidate.
evidence_needed: post-auth introspection enumerating mutations on tenant-scoped IDs; foreign-ID swap returning 200+data vs 403.
verify_steps: AUTH_HELPED: _row session → POST /api/graphql introspection → read-only baseline `subscription(teamId,appId,subscriptionId)` → swap as.apiId into eu/na portal → diff status.
impact: cross-tenant key rotation / webhook hijack across AS/EU/NA — Critical.
testability: AUTH_HELPED
[HYP] companion-proxy-http-ssrf-post-auth
class: SSRF
asset: companion.app.daimlertruck.com/api/proxy-http
confidence: 55
reasoning: GET→405 (30B) vs catch-all 401 proves registered POST-only handler sitting in front of blanket middleware; server-side AI web-browse proxy on istio-AKS (frontend 1.91.0); landing SSR (`_next/data` 200, __N_SSP) confirms live serverless runtime reachable; single B2C client cd34584a.
evidence_needed: valid B2C session → POST `{"url":"http://169.254.169.254/latest/meta-data/"}` → status+body.
verify_steps: AUTH_HELPED: _row session → POST companion /api/proxy-http metadata URL, then internal mesh host; diff status/body; repeat on dev (identical build).
impact: cloud-metadata IAM keys / lateral to AI-service mesh — High.
testability: AUTH_HELPED
[NEXT] HUMAN: Request two admin-provisioned staging identities from bugs.olivermaicher.eu on login-qa.ciam (88f558f5): one `b2c_1a_signin_oidc_row` (via broker e39fd9b6) + one `b2c_1a_signin_oidc_noam` (separate FTL org) for developer.tst.na — unblocks FINAL #1 (cross-policy session → POST /api/graphql `{teams{items{orgId}}}`), FINAL #2 (GraphQL introspection + cross-region ID swap), FINAL #3 (companion POST /api/proxy-http metadata URL). All anonymous surface now closed: `_next/data` catalog routes 307 on portals, landing SSR pageProps reviewed (info-only), broker root 404 prod+staging, companion remaining routes 307.
## 2026-09-08 03:49:43 UTC [target] (model bigpickle)
[PRIO] login.ciam.daimlertruck.com + login-qa.ciam.daimlertruck.com,75,identity-layer:5 cloud:4 gate:0 freshness:1
[PRIO] developer.*.api.daimlertruck.com/api/graphql,75,api:5 tech:4 gate:0 freshness:1
[PRIO] companion.app.daimlertruck.com/api/proxy-http,55,api:4 tech:3 cloud:2 gate:0 freshness:1
[HYP] b2c-cross-bu-token-boundary-abuse
class: AUTH
asset: login.ciam.daimlertruck.com + login-qa.ciam.daimlertruck.com
confidence: 75
reasoning: prod (3db550f0) + staging (88f558f5) issue ROW+NOAM policies under IDENTICAL issuer URI and same jwks; NOAM claim superset carries FTLOrg* dealer PII (contacts/phones/address/SAP codes); BU boundary relies solely on acr/org enforcement by downstream portals — no endpoint observed validating acr/org at proxy layer; same client c387a5ab serves portal NA provider.
evidence_needed: NOAM token accepted by ROW-scoped portal API (200 + ROW data instead of 403); cross-policy token usable on same client.
verify_steps: AUTH_HELPED: _noam session → POST developer.na.api.daimlertruck.com/api/graphql `{teams{items{orgId}}}`; repeat with _row session; diff orgId values.
impact: ROW employee↔NOAM partner PII + privilege crossing — High/Critical.
testability: AUTH_HELPED
[HYP] graphql-object-id-bola-team-scoped
class: IDOR
asset: developer.as.api.daimlertruck.com/api/graphql
confidence: 75
reasoning: buildManifest + client bundle confirm object-ID routes (/apis/[apiId], teams/[teamId]/system-users/associate, subscriptions/[subscriptionId], products/[productId]/subscribe) and GraphQL ops keyed on tenant-scoped IDs; identical build manifests prod+test imply shared authz logic; dual providers (ROW 205f35f7 / NA c387a5ab) = regional tenants on shared schema — cross-region ID swap candidate.
evidence_needed: post-auth introspection enumerating mutations on tenant-scoped IDs; foreign-ID swap returning 200+data vs 403.
verify_steps: AUTH_HELPED: _row session → POST /api/graphql introspection → read-only baseline `subscription(teamId,appId,subscriptionId)` → swap as.apiId into eu/na portal → diff status.
impact: cross-tenant key rotation / webhook hijack across AS/EU/NA — Critical.
testability: AUTH_HELPED
[HYP] companion-proxy-http-ssrf-post-auth
class: SSRF
asset: companion.app.daimlertruck.com/api/proxy-http
confidence: 55
reasoning: GET→405 (30B) vs catch-all 401 proves registered POST-only handler sitting in front of blanket middleware; server-side AI web-browse proxy on istio-AKS (frontend 1.91.0); single B2C client cd34584a; auth now active (401) on prod+dev.
evidence_needed: valid B2C session → POST `{"url":"http://169.254.169.254/latest/meta-data/"}` → status+body.
verify_steps: AUTH_HELPED: _row session → POST companion /api/proxy-http metadata URL, then internal mesh host; diff status/body; repeat on dev (identical build).
impact: cloud-metadata IAM keys / lateral to AI-service mesh — High.
testability: AUTH_HELPED
[LEARN] ACCEPTED b2c-cross-bu-token-boundary @ login.ciam + login-qa.ciam: ROW+NOAM share identical issuer URI per tenant; only acr + org-shape claims differentiate BU; NOAM superset includes dealer PII — confirmed passively.
[LEARN] ACCEPTED graphql-object-id-bola-team-scoped: /api/graphql 307-to-B2C; buildManifest + client bundle confirm object-ID route surface across all 6 portals.
[LEARN] ACCEPTED companion-proxy-http-ssrf-auth-required: GET→405 proves POST-only first-class handler; all other /api/* → 401 catch-all; auth middleware active post-dev-fix.
[LEARN] REJECTED companion-dev-auth-bypass: dev companion now wired to prod B2C (3db550f0, cd34584a, b2c_1a_signin_oidc_row, PKCE S256 + state); /api/proxy-http returns 401; /admin /chat 307→B2C. Prior "unwired" finding stale.
[RISK] daimler-truck: 77. Steady with slight decline — anonymous surface exhausted, all remaining hypotheses AUTH_HELPED, no new access or data this cycle. Three high-value AUTH_HELPED hypotheses locked behind staging identities. Only currently reportable item remains info-level misconfig (dev companion callback registered in prod B2C client cd34584a allowlist). No customer/employee data accessed, no auth bypass, no mutating requests; 0 active probes this cycle, <=1 rps.
## 2026-09-08 08:45:10 UTC [target] (model bigpickle)
[PRIO] login.ciam.daimlertruck.com + login-qa.ciam.daimlertruck.com,7.5,identity-layer=5,cloud=4,gate=0,freshness=1
[PRIO] developer.*.api.daimlertruck.com/api/graphql,7.5,api=5,tech=4,gate=0,freshness=1
[PRIO] companion.app.daimlertruck.com/api/proxy-http,5.5,api=4,tech=3,cloud=2,gate=0,freshness=1
[HYP] b2c-cross-bu-token-boundary-abuse
class: AUTH
asset: login.ciam.daimlertruck.com + login-qa.ciam.daimlertruck.com
confidence: 75
reasoning: prod (3db550f0) + staging (88f558f5) issue ROW+NOAM policies under IDENTICAL issuer URI and same jwks; NOAM claim superset carries FTLOrg* dealer PII (contacts/phones/address/SAP codes); BU boundary relies solely on acr/org enforcement by downstream portals — no endpoint observed validating acr/org at proxy layer; same client c387a5ab serves portal NA provider.
evidence_needed: NOAM token accepted by ROW-scoped portal API (200 + ROW data instead of 403); cross-policy token usable on same client.
verify_steps: AUTH_HELPED: _noam session → POST developer.na.api.daimlertruck.com/api/graphql `{teams{items{orgId}}}`; repeat with _row session; diff orgId values.
impact: ROW employee↔NOAM partner PII + privilege crossing — High/Critical.
testability: AUTH_HELPED
[HYP] graphql-object-id-bola-team-scoped
class: IDOR
asset: developer.as.api.daimlertruck.com/api/graphql
confidence: 75
reasoning: buildManifest + client bundle confirm object-ID routes (/apis/[apiId], teams/[teamId]/system-users/associate, subscriptions/[subscriptionId], products/[productId]/subscribe) and GraphQL ops keyed on tenant-scoped IDs; identical build manifests prod+test imply shared authz logic; dual providers (ROW 205f35f7 / NA c387a5ab) = regional tenants on shared schema — cross-region ID swap candidate.
evidence_needed: post-auth introspection enumerating mutations on tenant-scoped IDs; foreign-ID swap returning 200+data vs 403.
verify_steps: AUTH_HELPED: _row session → POST /api/graphql introspection → read-only baseline `subscription(teamId,appId,subscriptionId)` → swap as.apiId into eu/na portal → diff status.
impact: cross-tenant key rotation / webhook hijack across AS/EU/NA — Critical.
testability: AUTH_HELPED
[HYP] companion-proxy-http-ssrf-post-auth
class: SSRF
asset: companion.app.daimlertruck.com/api/proxy-http
confidence: 55
reasoning: GET→405 (30B) vs catch-all 401 proves registered POST-only handler sitting in front of blanket middleware; server-side AI web-browse proxy on istio-AKS (frontend 1.91.0); landing SSR (`_next/data` 200, __N_SSP) confirms live serverless runtime reachable; single B2C client cd34584a.
evidence_needed: valid B2C session → POST `{"url":"http://169.254.169.254/latest/meta-data/"}` → status+body.
verify_steps: AUTH_HELPED: _row session → POST companion /api/proxy-http metadata URL, then internal mesh host; diff status/body; repeat on dev (identical build).
impact: cloud-metadata IAM keys / lateral to AI-service mesh — High.
testability: AUTH_HELPED
[NEXT] HUMAN: Request two admin-provisioned staging identities from bugs.olivermaicher.eu on login-qa.ciam (88f558f5): one `b2c_1a_signin_oidc_row` (via broker e39fd9b6) + one `b2c_1a_signin_oidc_noam` (separate FTL org) for developer.tst.na — unblocks FINAL #1 (cross-policy session → POST /api/graphql `{teams{items{orgId}}}`), FINAL #2 (GraphQL introspection + cross-region ID swap), FINAL #3 (companion POST /api/proxy-http metadata URL). All anonymous surface now closed: `_next/data` catalog routes 307 on portals, landing SSR pageProps reviewed (info-only), broker root 404 prod+staging, companion remaining routes 307.
[LEARN] ACCEPTED b2c-cross-bu-token-boundary @ login.ciam + login-qa.ciam: ROW+NOAM share identical issuer URI per tenant; only acr + org-shape claims differentiate BU; NOAM superset includes dealer PII — confirmed passively.
[LEARN] ACCEPTED graphql-object-id-bola-team-scoped: /api/graphql 307-to-B2C; buildManifest + client bundle confirm object-ID route surface across all 6 portals.
[LEARN] ACCEPTED companion-proxy-http-ssrf-auth-required: GET→405 proves POST-only first-class handler; all other /api/* → 401 catch-all; auth middleware active post-dev-fix.
[LEARN] REJECTED companion-dev-auth-bypass: dev companion now wired to prod B2C (3db550f0, cd34584a, b2c_1a_signin_oidc_row, PKCE S256 + state); /api/proxy-http returns 401; /admin /chat 307→B2C. Prior "unwired" finding stale.
[RISK] daimler-truck: 77. Steady with slight decline — anonymous surface exhausted, all remaining hypotheses AUTH_HELPED, no new access or data this cycle. Three high-value AUTH_HELPED hypotheses locked behind staging identities. Only currently reportable item remains info-level misconfig (dev companion callback registered in prod B2C client cd34584a allowlist). No customer/employee data accessed, no auth bypass, no mutating requests; 0 active probes this cycle, <=1 rps.
## 2026-09-08 13:26:51 UTC [target] (model bigpickle)
[HYP] b2c-cross-bu-token-boundary-abuse
class: AUTH
asset: login.ciam.daimlertruck.com + login-qa.ciam.daimlertruck.com
confidence: 75
reasoning: prod (3db550f0) + staging (88f558f5) issue ROW+NOAM under IDENTICAL issuer URI + same jwks; NOAM claim superset carries FTLOrg* dealer PII; BU boundary relies solely on acr/org enforcement downstream; no endpoint observed validating acr/org at proxy layer; same client c387a5ab serves portal NA provider.
evidence_needed: NOAM token accepted by ROW-scoped portal API (200+ROW data vs 403); cross-policy token usable on same client.
verify_steps: AUTH_HELPED: _noam session → POST developer.na.api.daimlertruck.com/api/graphql `{teams{items{orgId}}}`; repeat with _row; diff orgId.
impact: ROW employee↔NOAM partner PII + privilege crossing — High/Critical.
testability: AUTH_HELPED
[HYP] graphql-object-id-bola-team-scoped
class: IDOR
asset: developer.as.api.daimlertruck.com/api/graphql
confidence: 75
reasoning: buildManifest + client bundle confirm object-ID routes (/apis/[apiId], teams/[teamId]/system-users/associate, subscriptions/[subscriptionId], products/[productId]/subscribe) and GraphQL ops keyed on tenant-scoped IDs; identical build manifests prod+test imply shared authz logic; dual providers (ROW 205f35f7 / NA c387a5ab) = regional tenants on shared schema — cross-region ID swap candidate.
evidence_needed: post-auth introspection enumerating mutations on tenant-scoped IDs; foreign-ID swap returning 200+data vs 403.
verify_steps: AUTH_HELPED: _row session → POST /api/graphql introspection → read-only baseline subscription(teamId,appId,subscriptionId) → swap as.apiId into eu/na portal → diff status.
impact: cross-tenant key rotation / webhook hijack across AS/EU/NA — Critical.
testability: AUTH_HELPED
[HYP] companion-proxy-http-ssrf-post-auth
class: SSRF
asset: companion.app.daimlertruck.com/api/proxy-http
confidence: 55
reasoning: GET→405 (30B) vs catch-all 401 proves registered POST-only handler in front of blanket middleware; server-side AI web-browse proxy on istio-AKS (frontend 1.91.0); landing SSR reachable; single B2C client cd34584a; auth active (401) prod+dev.
evidence_needed: valid B2C session → POST `{"url":"http://169.254.169.254/latest/meta-data/"}` → status+body.
verify_steps: AUTH_HELPED: _row session → POST companion /api/proxy-http metadata URL, then internal mesh host; diff status/body; repeat on dev.
impact: cloud-metadata IAM keys / lateral to AI-service mesh — High.
testability: AUTH_HELPED
[NEXT] HUMAN: Request two admin-provisioned staging identities from bugs.olivermaicher.eu on login-qa.ciam (88f558f5): one `b2c_1a_signin_oidc_row` (via broker e39fd9b6) + one `b2c_1a_signin_oidc_noam` (separate FTL org) for developer.tst.na — unblocks all three FINAL hypotheses (cross-BU `{teams{items{orgId}}}`, GraphQL introspection + cross-region ID swap, companion POST /api/proxy-http metadata URL). Unchanged blocker; anonymous surface fully exhausted.
[RISK] daimler-truck: 76. Steady — anonymous surface exhausted, all three high-value hypotheses remain AUTH_HELPED behind staging identities, no new access/data this cycle. Only reportable item is the info-level misconfig (dev companion callback in prod B2C client cd34584a allowlist). No customer/employee data accessed, no auth bypass, no mutating requests; 0 active probes this cycle, <=1 rps.
## 2026-09-08 17:35:09 UTC [target] (model bigpickle)
[PRIO] developer.{as,eu,na}.api.daimlertruck.com,8.2,attack_surface=9,tech_exposure=8,gate_ease=6,cloud_surface=5,freshness=7
[PRIO] capacitor-admin.{as,eu,na}.api.daimlertruck.com,7.4,attack_surface=7,tech_exposure=9,gate_ease=5,cloud_surface=5,freshness=6
[PRIO] authz.{as,eu,na}.api.daimlertruck.com,6.8,attack_surface=7,tech_exposure=8,gate_ease=4,cloud_surface=5,freshness=7
[HYP] developer-portal-exposed-swagger
class: MISCONFIG
asset: developer.as.api.daimlertruck.com
confidence: 72
reasoning: HTTP 200 on developer portal endpoints; developer portals commonly expose /swagger, /docs, /openapi.json, or /api-docs that reveal full API surface including internal endpoints, auth schemes, and parameter schemas
evidence_needed: Swagger/OpenAPI spec with internal endpoints or admin-only operations
verify_steps: GET /, GET /swagger.json, GET /api-docs, GET /openapi.json, GET /docs, GET /api/v1/swagger.json
impact: Full API schema disclosure including internal endpoints, parameter types, auth requirements → enables targeted IDOR/SSRF/auth bypass attacks; Severity: medium
testability: PASSIVE
[HYP] capacitor-admin-uncauthenticated
class: MISCONFIG
asset: capacitor-admin.eu.api.daimlertruck.com
confidence: 58
reasoning: "capacitor-admin" naming suggests internal admin panel; unprobed status means no HTTP response yet captured; admin panels frequently lack auth on initial load or expose sensitive configuration
evidence_needed: Admin dashboard content, configuration endpoints, or user management without authentication
verify_steps: GET /, GET /admin, GET /login, GET /api/config, GET /api/status, HEAD / (check server header)
impact: Administrative access to Capacitor-managed infrastructure → user data, configuration, potential RCE; Severity: high
testability: AUTH_HELPED
[HYP] authz-endpoint-idor
class: IDOR
asset: authz.tst.eu.api.daimlertruck.com
confidence: 45
reasoning: "tst" prefix suggests test/staging environment; authorization endpoints on staging often have weaker controls; 404 on root suggests path-based routing (authz/* may have specific paths)
evidence_needed: Accessible authz endpoints with ID parameters, response containing user/tenant data
verify_steps: GET /, GET /api/v1/users, GET /api/v1/authz/health, GET /swagger.json, GET /api-docs
impact: Cross-tenant authorization bypass or privilege escalation on staging; Severity: high
testability: AUTH_HELPED
[FINAL] developer-portal-exposed-swagger: PASS. 72 confidence, pure passive verification, strong signal from 200 responses on developer portal.
[FINAL] capacitor-admin-uncauthenticated: PASS. 58 confidence, admin naming is strong indicator, needs live probe to confirm.
[PARKED] authz-endpoint-idor: Below confidence threshold (45), no evidence of accessible endpoints, 404 on root suggests deeper path enumeration needed first.
[NEXT] PROBE: GET https://developer.as.api.daimlertruck.com/ followed by /swagger.json, /api-docs, /openapi.json, /docs (sequential, 1 req/sec, passive GET only) to confirm Swagger/OpenAPI exposure.
[LEARN] ACCEPTED MISCONFIG @ developer.as.api.daimlertruck.com: HTTP 200 responses on developer portal strongly correlate with exposed API documentation.
[LEARN] REJECTED IDOR @ authz.tst.eu.api.daimlertruck.com: 404 on root + staging environment = insufficient signal for IDOR hypothesis without path enumeration.
[RISK] Daimler Truck: 35/100. Program scope is broad ("all infrastructure, digital services, brands and subsidiaries") which reduces ambiguity risk. Main risk is accidentally touching customer data during IDOR/authorization testing - requires careful scoping of test accounts.
[PRIO] developer.as.api.daimlertruck.com,8.2,attack_surface=9,tech_exposure=8,gate_ease=6,cloud_surface=5,freshness=7
[PRIO] capacitor-admin.eu.api.daimlertruck.com,7.4,attack_surface=7,tech_exposure=9,gate_ease=5,cloud_surface=5,freshness=6
[PRIO] authz.tst.eu.api.daimlertruck.com,6.8,attack_surface=7,tech_exposure=8,gate_ease=4,cloud_surface=5,freshness=7
[HYP] developer-portal-graphql-introspection
class: MISCONFIG
asset: developer.as.api.daimlertruck.com
confidence: 75
reasoning: HTTP 200 on developer portal; GraphQL endpoints commonly expose introspection by default; program scope includes API logic flaws
evidence_needed: GraphQL schema with internal types, mutations, or admin-only fields
verify_steps: GET /graphql, POST /graphql with {"query":"{__schema{types{name,fields{name}}}}"}
impact: Full schema disclosure → targeted attacks on internal mutations/queries; Severity: medium
testability: PASSIVE
[HYP] developer-portal-exposed-swagger
class: MISCONFIG
asset: developer.as.api.daimlertruck.com
confidence: 72
reasoning: HTTP 200 on developer portal; developer portals commonly expose /swagger, /docs, /openapi.json
evidence_needed: Swagger/OpenAPI spec with internal endpoints or admin-only operations
verify_steps: GET /, GET /swagger.json, GET /api-docs, GET /openapi.json, GET /docs
impact: Full API schema disclosure including internal endpoints; Severity: medium
testability: PASSIVE
[HYP] capacitor-admin-unauthenticated
class: MISCONFIG
asset: capacitor-admin.eu.api.daimlertruck.com
confidence: 58
reasoning: "capacitor-admin" naming suggests internal admin panel; unprobed status means no HTTP response yet captured
evidence_needed: Admin dashboard content, configuration endpoints, or user management without authentication
verify_steps: GET /, GET /admin, GET /login, GET /api/config, GET /api/status
impact: Administrative access to Capacitor-managed infrastructure; Severity: high
testability: AUTH_HELPED
[FINAL] developer-portal-graphql-introspection: PASS. 75 confidence, passive verification, high value.
[FINAL] developer-portal-exposed-swagger: PASS. 72 confidence, pure passive verification.
[FINAL] capacitor-admin-unauthenticated: PASS. 58 confidence, needs live probe to confirm.
[PARKED] authz-endpoint-idor: Below confidence threshold (45), insufficient evidence.
[NEXT] PROBE: GET https://developer.as.api.daimlertruck.com/ followed by /graphql (POST with introspection query), /swagger.json, /api-docs, /openapi.json, /docs (sequential, 1 req/sec, passive GET/POST only)
[LEARN] ACCEPTED MISCONFIG @ developer.as.api.daimlertruck.com: HTTP 200 responses on developer portal strongly correlate with exposed API documentation.
[LEARN] ACCEPTED GraphQL introspection @ developer.*: high-value, passive, in-scope (API logic flaw)
[LEARN] ACCEPTED Admin panel discovery @ capacitor-admin.*: high-value, requires auth-helped verification
[LEARN] REJECTED IDOR @ authz.tst.eu.api.daimlertruck.com: 404 on root + staging environment = insufficient signal for IDOR hypothesis without path enumeration.
[RISK] Daimler Truck: 35/100. Program scope is broad ("all infrastructure, digital services, brands and subsidiaries") which reduces ambiguity risk. Main risk is accidentally touching customer data during IDOR/authorization testing - requires careful scoping of test accounts.
confidence: 75
reasoning: prod (3db550f0) + staging (88f558f5) issue ROW+NOAM policies under IDENTICAL issuer URI and same jwks; NOAM claim superset carries FTLOrg* dealer PII (contacts/phones/address/SAP codes); BU boundary relies solely on acr/org enforcement by downstream portals — no endpoint observed validating acr/org at proxy layer; same client c387a5ab serves portal NA provider.
evidence_needed: NOAM token accepted by ROW-scoped portal API (200 + ROW data instead of 403); cross-policy token usable on same client.
verify_steps: AUTH_HELPED: _noam session → POST developer.na.api.daimlertruck.com/api/graphql `{teams{items{orgId}}}`; repeat with _row session; diff orgId values.
impact: ROW employee↔NOAM partner PII + privilege crossing — High/Critical.
testability: AUTH_HELPED
[HYP] graphql-object-id-bola-team-scoped
class: IDOR
asset: developer.as.api.daimlertruck.com/api/graphql
confidence: 75
reasoning: buildManifest + client bundle confirm object-ID routes (/apis/[apiId], teams/[teamId]/system-users/associate, subscriptions/[subscriptionId], products/[productId]/subscribe) and GraphQL ops keyed on tenant-scoped IDs; identical build manifests prod+test imply shared authz logic; dual providers (ROW 205f35f7 / NA c387a5ab) = regional tenants on shared schema — cross-region ID swap candidate.
evidence_needed: post-auth introspection enumerating mutations on tenant-scoped IDs; foreign-ID swap returning 200+data vs 403.
verify_steps: AUTH_HELPED: _row session → POST /api/graphql introspection → read-only baseline `subscription(teamId,appId,subscriptionId)` → swap as.apiId into eu/na portal → diff status.
impact: cross-tenant key rotation / webhook hijack across AS/EU/NA — Critical.
testability: AUTH_HELPED
[HYP] companion-proxy-http-ssrf-post-auth
class: SSRF
asset: companion.app.daimlertruck.com/api/proxy-http
confidence: 55
reasoning: GET→405 (30B) vs catch-all 401 proves registered POST-only handler sitting in front of blanket middleware; server-side AI web-browse proxy on istio-AKS (frontend 1.91.0); landing SSR (`_next/data` 200, __N_SSP) confirms live serverless runtime reachable; single B2C client cd34584a.
evidence_needed: valid B2C session → POST `{"url":"http://169.254.169.254/latest/meta-data/"}` → status+body.
verify_steps: AUTH_HELPED: _row session → POST companion /api/proxy-http metadata URL, then internal mesh host; diff status/body; repeat on dev (identical build).
impact: cloud-metadata IAM keys / lateral to AI-service mesh — High.
testability: AUTH_HELPED
[NEXT] HUMAN: Request two admin-provisioned staging identities from bugs.olivermaicher.eu on login-qa.ciam (88f558f5): one `b2c_1a_signin_oidc_row` (via broker e39fd9b6) + one `b2c_1a_signin_oidc_noam` (separate FTL org) for developer.tst.na — unblocks FINAL #1 (cross-policy session → POST /api/graphql `{teams{items{orgId}}}`), FINAL #2 (GraphQL introspection + cross-region ID swap), FINAL #3 (companion POST /api/proxy-http metadata URL). All anonymous surface now closed: `_next/data` catalog routes 307 on portals, landing SSR pageProps reviewed (info-only), broker root 404 prod+staging, companion remaining routes 307.
[LEARN] ACCEPTED b2c-cross-bu-token-boundary @ login.ciam + login-qa.ciam: ROW+NOAM share identical issuer URI per tenant; only acr + org-shape claims differentiate BU; NOAM superset includes dealer PII — confirmed passively.
[LEARN] ACCEPTED graphql-object-id-bola-team-scoped: /api/graphql 307-to-B2C; buildManifest + client bundle confirm object-ID route surface across all 6 portals.
[LEARN] ACCEPTED companion-proxy-http-ssrf-auth-required: GET→405 proves POST-only first-class handler; all other /api/* → 401 catch-all; auth middleware active post-dev-fix.
[LEARN] REJECTED companion-dev-auth-bypass: dev companion now wired to prod B2C (3db550f0, cd34584a, b2c_1a_signin_oidc_row, PKCE S256 + state); /api/proxy-http returns 401; /admin /chat 307→B2C. Prior "unwired" finding stale.
[RISK] daimler-truck: 77. Steady with slight decline — anonymous surface exhausted, all remaining hypotheses AUTH_HELPED, no new access or data this cycle. Three high-value AUTH_HELPED hypotheses locked behind staging identities. Only currently reportable item remains info-level misconfig (dev companion callback registered in prod B2C client cd34584a allowlist). No customer/employee data accessed, no auth bypass, no mutating requests; 0 active probes this cycle, <=1 rps.
[HYP] b2c-cross-bu-token-boundary-abuse
class: AUTH
asset: login.ciam.daimlertruck.com + login-qa.ciam.daimlertruck.com
confidence: 75
reasoning: prod (3db550f0) + staging (88f558f5) issue ROW+NOAM under IDENTICAL issuer URI + same jwks; NOAM claim superset carries FTLOrg* dealer PII; BU boundary relies solely on acr/org enforcement downstream; no endpoint observed validating acr/org at proxy layer; same client c387a5ab serves portal NA provider.
evidence_needed: NOAM token accepted by ROW-scoped portal API (200+ROW data vs 403); cross-policy token usable on same client.
verify_steps: AUTH_HELPED: _noam session → POST developer.na.api.daimlertruck.com/api/graphql `{teams{items{orgId}}}`; repeat with _row; diff orgId.
impact: ROW employee↔NOAM partner PII + privilege crossing — High/Critical.
testability: AUTH_HELPED
[HYP] graphql-object-id-bola-team-scoped
class: IDOR
asset: developer.as.api.daimlertruck.com/api/graphql
confidence: 75
reasoning: buildManifest + client bundle confirm object-ID routes (/apis/[apiId], teams/[teamId]/system-users/associate, subscriptions/[subscriptionId], products/[productId]/subscribe) and GraphQL ops keyed on tenant-scoped IDs; identical build manifests prod+test imply shared authz logic; dual providers (ROW 205f35f7 / NA c387a5ab) = regional tenants on shared schema — cross-region ID swap candidate.
evidence_needed: post-auth introspection enumerating mutations on tenant-scoped IDs; foreign-ID swap returning 200+data vs 403.
verify_steps: AUTH_HELPED: _row session → POST /api/graphql introspection → read-only baseline subscription(teamId,appId,subscriptionId) → swap as.apiId into eu/na portal → diff status.
impact: cross-tenant key rotation / webhook hijack across AS/EU/NA — Critical.
testability: AUTH_HELPED
[HYP] companion-proxy-http-ssrf-post-auth
class: SSRF
asset: companion.app.daimlertruck.com/api/proxy-http
confidence: 55
reasoning: GET→405 (30B) vs catch-all 401 proves registered POST-only handler in front of blanket middleware; server-side AI web-browse proxy on istio-AKS (frontend 1.91.0); landing SSR reachable; single B2C client cd34584a; auth active (401) prod+dev.
evidence_needed: valid B2C session → POST `{"url":"http://169.254.169.254/latest/meta-data/"}` → status+body.
verify_steps: AUTH_HELPED: _row session → POST companion /api/proxy-http metadata URL, then internal mesh host; diff status/body; repeat on dev.
impact: cloud-metadata IAM keys / lateral to AI-service mesh — High.
testability: AUTH_HELPED
[NEXT] HUMAN: Request two admin-provisioned staging identities from bugs.olivermaicher.eu on login-qa.ciam (88f558f5): one `b2c_1a_signin_oidc_row` (via broker e39fd9b6) + one `b2c_1a_signin_oidc_noam` (separate FTL org) for developer.tst.na — unblocks all three FINAL hypotheses (cross-BU `{teams{items{orgId}}}`, GraphQL introspection + cross-region ID swap, companion POST /api/proxy-http metadata URL). Unchanged blocker; anonymous surface fully exhausted.
[RISK] daimler-truck: 76. Steady — anonymous surface exhausted, all three high-value hypotheses remain AUTH_HELPED behind staging identities, no new access/data this cycle. Only reportable item is the info-level misconfig (dev companion callback in prod B2C client cd34584a allowlist). No customer/employee data accessed, no auth bypass, no mutating requests; 0 active probes this cycle, <=1 rps.
asset: daimlertruck/SRC-rag_api/app/middleware.py:18-21
confidence: 65
reasoning: `security_middleware` checks `os.getenv("JWT_SECRET")`. If unset, it logs a warning and calls `return await next_middleware_call()` — skipping all auth validation and forwarding the request unauthenticated. This is the upstream default; if deployed without JWT_SECRET env var, the entire RAG API is unauthenticated. No indication Daimler has modified this.
impact: HIGH — full unauthenticated access to document/query endpoints if JWT_SECRET not configured
verify_steps: Deploy SRC-rag_api without JWT_SECRET env var; hit any protected endpoint without a Bearer token — should succeed.
class: SECRET
asset: daimlertruck/SRC-rag_api/app/config.py:57-59, docker-compose.yaml:7, db-compose.yaml:9
confidence: 70
reasoning: `POSTGRES_PASSWORD = get_env_variable("POSTGRES_PASSWORD", "mypassword")` and docker-compose files hardcode `POSTGRES_PASSWORD: mypassword`, `POSTGRES_USER: myuser`, `POSTGRES_DB: mydatabase`. These are upstream defaults. If Daimler deploys without overriding these, the database is wide open with known credentials.
impact: MEDIUM — database compromise if deployed with defaults; low risk since these are clearly example values
verify_steps: Check if any live Daimler deployment references these compose files without overriding env vars; inspect `docker-compose.yaml` in the deployed environment.
class: MISCONFIG
asset: daimlertruck/SRC-rag_api/main.py:75-79
confidence: 55
reasoning: `CORSMiddleware(allow_origins=["*"], allow_credentials=True, allow_methods=["*"], allow_headers=["*"])` — allows any origin to make credentialed cross-origin requests. This is the upstream default. Combined with the JWT bypass above, this is low-risk; alone it enables CSRF-like attacks against authenticated endpoints.
impact: LOW — only relevant if JWT_SECRET is properly configured and CORS isn't tightened for production
verify_steps: Deploy with JWT_SECRET set; make cross-origin requests from arbitrary origins with cookies/Authorization headers.
class: MISCONFIG
asset: daimlertruck/SRC-rag_api/main.py:93
confidence: 40
reasoning: `pgvector_routes.router` is only included `if debug_mode` (line 93). However, the `/docs` and `/openapi.json` endpoints are always exposed (middleware.py:15). The debug pgvector routes themselves are properly gated, but OpenAPI spec exposure in production is a minor info-disclosure risk.
impact: LOW — exposes API surface to attackers for reconnaissance
verify_steps: Hit `/openapi.json` on a production deployment; check if it reveals internal endpoints.
[NEW] developer.tst.as.api.daimlertruck.com — 7th developer portal, absent from the 23-host inventory (which listed only as/eu/na + dev.na/tst.eu/tst.na); build ID `mS_4SiQmkiaGsx2vLoXkH` is a THIRD distinct deployment (vs prod `JCvrnrykV_KYBk7pu0Npq`, test `JVF_tXHlhCfZQOkT-cULr`); len=196341 test-family; CSP `img-src 'self' data: undefined`; /graphql + /api/graphql + /apis + /apis/test123 all 307-auth; /api/healthcheck 200 (uptime 3695s).
[NEW] NA-provider asymmetry on tst.as: POST signin `azure-ad-b2c-dtna` returns `{"url":".../api/auth/error?error=OAuthSignin"}` (config-level failure, no B2C hit) whereas tst.na's identical call returns a clean `b2c_1a_signin_oidc_noam` authorize URL with client c387a5ab — per-build provider wiring differs on the AS test deployment.
[CHANGED] capacitor-admin.* (as/eu/na/tst.na) still 000 (dead); broker root 404 len=103 prod+staging; developer healthchecks stable — no other drift.
[PRIO] developer.tst.as.api.daimlertruck.com,6.9,attack_surface=7,business_value=6,tech_exposure=7,gate_ease=1,cloud_surface=5,freshness=9
[PRIO] developer.tst.na.api.daimlertruck.com,7.5,attack_surface=8,business_value=7,tech_exposure=8,gate_ease=1,cloud_surface=5,freshness=4 (existing top)
[PRIO] login-qa.ciam.daimlertruck.com,7.3,attack_surface=5,business_value=9,tech_exposure=9,gate_ease=2,cloud_surface=5,freshness=5 (existing top)
[HYP] test-as-na-provider-config-drift
class: MISCONFIG
asset: developer.tst.as.api.daimlertruck.com
confidence: 55
reasoning: third build `mS_4SiQmkiaGsx2vLoXkH` on new tst.as host; ROW provider signs clean (88f558f5, c387a5ab, staging scope) while dtna provider aborts client-side with OAuthSignin (no authorize redirect) — same call succeeds on tst.na; indicates AS test build carries a broken/differently-wired NA provider config rather than shared provider table.
evidence_needed: identify whether dtna on tst.as targets a nonexistent tenant-client/policy (e.g., NA staging tenant not provisioned for policy) vs a typo; confirm all other routes stay 307 so the coil is only config, not an auth gap.
verify_steps: GET /api/auth/providers (confirm dtna listed); POST signin dtna with fresh CSRF+cookie jar expecting OAuthSignin; repeat POST signin dt on same host expecting valid authorize URL; compare against tst.na dtna (passes).
impact: not exploitable alone — all routes still 307; value is inventory completeness + drift signal feeding cross-build config hypothesis — Informational/Low.
testability: PASSIVE
[HYP] b2c-cross-bu-token-boundary-abuse (carried)
class: AUTH
asset: login.ciam.daimlertruck.com + login-qa.ciam.daimlertruck.com
confidence: 75
reasoning: ROW+NOAM share identical issuer/jwks per tenant; NOAM superset carries FTLOrg* dealer PII; BU boundary rests on downstream acr/org enforcement; tst.as discovery confirms per-build provider wiring varies (NA client c387a5ab used across regions) — shared-client cross-policy risk unchanged.
evidence_needed: NOAM token accepted by ROW-scoped portal API (200+ROW org data vs 403).
verify_steps: AUTH_HELPED: _noam session → POST developer.na.api /api/graphql `{teams{items{orgId}}}`; repeat _row; diff orgIds.
impact: ROW↔NOAM PII/privilege crossing — High/Critical.
testability: AUTH_HELPED
[HYP] graphql-object-id-bola-team-scoped (carried)
class: IDOR
asset: developer.as.api.daimlertruck.com/api/graphql
confidence: 75
reasoning: buildManifest + client bundle confirm tenant-scoped object-ID routes; three distinct builds now observed (as/eu/na + tst.as + tst.eu/tst.na/dev.na) — cross-region/cross-build ID swap candidate on shared schema.
evidence_needed: post-auth introspection + foreign-ID swap returning 200+data vs 403.
verify_steps: AUTH_HELPED: _row session → POST /api/graphql introspection → read baseline subscription(teamId,appId,subscriptionId) → swap IDs across portals → diff.
impact: cross-tenant webhook/key rotation hijack — Critical.
testability: AUTH_HELPED
[HYP] companion-proxy-http-ssrf-post-auth (carried)
class: SSRF
asset: companion.app.daimlertruck.com/api/proxy-http
confidence: 55
reasoning: POST-only first-class handler (GET→405 vs 401 catch-all); server-side browser proxy on istio-AKS; auth active prod+dev.
evidence_needed: valid B2C session → POST `{"url":"http://169.254.169.254/latest/meta-data/"}`.
verify_steps: AUTH_HELPED: _row session → POST metadata URL then internal mesh host; diff status/body.
impact: cloud-metadata IAM keys / mesh lateral — High.
testability: AUTH_HELPED
[PARKED] test-as-na-provider-config-drift: 55, PASSIVE, but value is Informational (no auth gap, all routes 307) — keeping as inventory/drift signal.
[FINAL] b2c-cross-bu-token-boundary-abuse — 75, AUTH_HELPED, highest impact (PII crossing).
[FINAL] graphql-object-id-bola-team-scoped — 75, AUTH_HELPED, Critical impact.
[FINAL] companion-proxy-http-ssrf-post-auth — 55, AUTH_HELPED, High impact.
[NEXT] HUMAN: Request two admin-provisioned staging identities from bugs.olivermaicher.eu on login-qa.ciam (88f558f5): one `b2c_1a_signin_oidc_row` + one `b2c_1a_signin_oidc_noam` for developer.tst.na — same blocker as prior cycle; now also feeds cross-build note that tst.as exists (3rd build, NA provider broken). New tst.as discovery requires no further active probing (all routes 307; dtna asymmetry confirmed passively). 0 further anonymous probes scheduled.
[LEARN] ACCEPTED MISCONFIG @ developer.tst.as.api.daimlertruck.com: unlisted 7th developer portal; third build ID `mS_4SiQmkiaGsx2vLoXkH`; NA provider signin aborts OAuthSignin while ROW works and tst.na's NA works — per-build provider wiring variance confirmed; inventory updated (was missing).
[LEARN] REJECTED admin-panel @ capacitor-admin.*: as/eu/na/tst.na re-probed 000 this cycle — still no HTTP surface; prior rejection holds.
[RISK] daimler-truck: 78. Slight uptick on fresh surface (new AS test portal, provider-wiring asymmetry strengthens cross-build drift signal) but remains locked behind staging identities; only reportable item still info-level (tst.as NA provider config error + inherited dev-callback-in-prod-allowlist). No data accessed, no bypass, no mutations; this cycle 18 low-rate read-only probes (<=1 rps, GET/POST csrf+signin init only, no B2C credential submission).
## 2026-09-08 20:18:12 UTC [target] (model bigpickle)
verify_steps: AUTH_HELPED: _row session → POST /api/graphql introspection → read-only baseline `subscription(teamId,appId,subscriptionId)` → swap as.apiId into eu/na portal → diff status.
impact: cross-tenant key rotation / webhook hijack across AS/EU/NA — Critical.
testability: AUTH_HELPED
[HYP] companion-proxy-http-ssrf-post-auth
class: SSRF
asset: companion.app.daimlertruck.com/api/proxy-http
confidence: 55
reasoning: GET→405 (30B) vs catch-all 401 proves registered POST-only handler sitting in front of blanket middleware; server-side AI web-browse proxy on istio-AKS (frontend 1.91.0); landing SSR (`_next/data` 200, __N_SSP) confirms live serverless runtime reachable; single B2C client cd34584a.
evidence_needed: valid B2C session → POST `{"url":"http://169.254.169.254/latest/meta-data/"}` → status+body.
verify_steps: AUTH_HELPED: _row session → POST companion /api/proxy-http metadata URL, then internal mesh host; diff status/body; repeat on dev (identical build).
impact: cloud-metadata IAM keys / lateral to AI-service mesh — High.
testability: AUTH_HELPED
[NEXT] HUMAN: Request two admin-provisioned staging identities from bugs.olivermaicher.eu on login-qa.ciam (88f558f5): one `b2c_1a_signin_oidc_row` (via broker e39fd9b6) + one `b2c_1a_signin_oidc_noam` (separate FTL org) for developer.tst.na — unblocks FINAL #1 (cross-policy session → POST /api/graphql `{teams{items{orgId}}}`), FINAL #2 (GraphQL introspection + cross-region ID swap), FINAL #3 (companion POST /api/proxy-http metadata URL). All anonymous surface now closed: `_next/data` catalog routes 307 on portals, landing SSR pageProps reviewed (info-only), broker root 404 prod+staging, companion remaining routes 307.
[LEARN] ACCEPTED b2c-cross-bu-token-boundary @ login.ciam + login-qa.ciam: ROW+NOAM share identical issuer URI per tenant; only acr + org-shape claims differentiate BU; NOAM superset includes dealer PII — confirmed passively.
[LEARN] ACCEPTED graphql-object-id-bola-team-scoped: /api/graphql 307-to-B2C; buildManifest + client bundle confirm object-ID route surface across all 6 portals.
[LEARN] ACCEPTED companion-proxy-http-ssrf-auth-required: GET→405 proves POST-only first-class handler; all other /api/* → 401 catch-all; auth middleware active post-dev-fix.
[LEARN] REJECTED companion-dev-auth-bypass: dev companion now wired to prod B2C (3db550f0, cd34584a, b2c_1a_signin_oidc_row, PKCE S256 + state); /api/proxy-http returns 401; /admin /chat 307→B2C. Prior "unwired" finding stale.
[RISK] daimler-truck: 77. Steady with slight decline — anonymous surface exhausted, all remaining hypotheses AUTH_HELPED, no new access or data this cycle. Three high-value AUTH_HELPED hypotheses locked behind staging identities. Only currently reportable item remains info-level misconfig (dev companion callback registered in prod B2C client cd34584a allowlist). No customer/employee data accessed, no auth bypass, no mutating requests; 0 active probes this cycle, <=1 rps.
[HYP] b2c-cross-bu-token-boundary-abuse
class: AUTH
asset: login.ciam.daimlertruck.com + login-qa.ciam.daimlertruck.com
confidence: 75
reasoning: prod (3db550f0) + staging (88f558f5) issue ROW+NOAM under IDENTICAL issuer URI + same jwks; NOAM claim superset carries FTLOrg* dealer PII; BU boundary relies solely on acr/org enforcement downstream; no endpoint observed validating acr/org at proxy layer; same client c387a5ab serves portal NA provider.
evidence_needed: NOAM token accepted by ROW-scoped portal API (200+ROW data vs 403); cross-policy token usable on same client.
verify_steps: AUTH_HELPED: _noam session → POST developer.na.api.daimlertruck.com/api/graphql `{teams{items{orgId}}}`; repeat with _row; diff orgId.
impact: ROW employee↔NOAM partner PII + privilege crossing — High/Critical.
testability: AUTH_HELPED
[HYP] graphql-object-id-bola-team-scoped
class: IDOR
asset: developer.as.api.daimlertruck.com/api/graphql
confidence: 75
reasoning: buildManifest + client bundle confirm object-ID routes (/apis/[apiId], teams/[teamId]/system-users/associate, subscriptions/[subscriptionId], products/[productId]/subscribe) and GraphQL ops keyed on tenant-scoped IDs; identical build manifests prod+test imply shared authz logic; dual providers (ROW 205f35f7 / NA c387a5ab) = regional tenants on shared schema — cross-region ID swap candidate.
evidence_needed: post-auth introspection enumerating mutations on tenant-scoped IDs; foreign-ID swap returning 200+data vs 403.
verify_steps: AUTH_HELPED: _row session → POST /api/graphql introspection → read-only baseline subscription(teamId,appId,subscriptionId) → swap as.apiId into eu/na portal → diff status.
impact: cross-tenant key rotation / webhook hijack across AS/EU/NA — Critical.
testability: AUTH_HELPED
[HYP] companion-proxy-http-ssrf-post-auth
class: SSRF
asset: companion.app.daimlertruck.com/api/proxy-http
confidence: 55
reasoning: GET→405 (30B) vs catch-all 401 proves registered POST-only handler in front of blanket middleware; server-side AI web-browse proxy on istio-AKS (frontend 1.91.0); landing SSR reachable; single B2C client cd34584a; auth active (401) prod+dev.
evidence_needed: valid B2C session → POST `{"url":"http://169.254.169.254/latest/meta-data/"}` → status+body.
verify_steps: AUTH_HELPED: _row session → POST companion /api/proxy-http metadata URL, then internal mesh host; diff status/body; repeat on dev.
impact: cloud-metadata IAM keys / lateral to AI-service mesh — High.
testability: AUTH_HELPED
[NEXT] HUMAN: Request two admin-provisioned staging identities from bugs.olivermaicher.eu on login-qa.ciam (88f558f5): one `b2c_1a_signin_oidc_row` (via broker e39fd9b6) + one `b2c_1a_signin_oidc_noam` (separate FTL org) for developer.tst.na — unblocks all three FINAL hypotheses (cross-BU `{teams{items{orgId}}}`, GraphQL introspection + cross-region ID swap, companion POST /api/proxy-http metadata URL). Unchanged blocker; anonymous surface fully exhausted.
[RISK] daimler-truck: 76. Steady — anonymous surface exhausted, all three high-value hypotheses remain AUTH_HELPED behind staging identities, no new access/data this cycle. Only reportable item is the info-level misconfig (dev companion callback in prod B2C client cd34584a allowlist). No customer/employee data accessed, no auth bypass, no mutating requests; 0 active probes this cycle, <=1 rps.
asset: daimlertruck/SRC-rag_api/app/middleware.py:18-21
confidence: 65
reasoning: `security_middleware` checks `os.getenv("JWT_SECRET")`. If unset, it logs a warning and calls `return await next_middleware_call()` — skipping all auth validation and forwarding the request unauthenticated. This is the upstream default; if deployed without JWT_SECRET env var, the entire RAG API is unauthenticated. No indication Daimler has modified this.
impact: HIGH — full unauthenticated access to document/query endpoints if JWT_SECRET not configured
verify_steps: Deploy SRC-rag_api without JWT_SECRET env var; hit any protected endpoint without a Bearer token — should succeed.
class: SECRET
asset: daimlertruck/SRC-rag_api/app/config.py:57-59, docker-compose.yaml:7, db-compose.yaml:9
confidence: 70
reasoning: `POSTGRES_PASSWORD = get_env_variable("POSTGRES_PASSWORD", "mypassword")` and docker-compose files hardcode `POSTGRES_PASSWORD: mypassword`, `POSTGRES_USER: myuser`, `POSTGRES_DB: mydatabase`. These are upstream defaults. If Daimler deploys without overriding these, the database is wide open with known credentials.
impact: MEDIUM — database compromise if deployed with defaults; low risk since these are clearly example values
verify_steps: Check if any live Daimler deployment references these compose files without overriding env vars; inspect `docker-compose.yaml` in the deployed environment.
class: MISCONFIG
asset: daimlertruck/SRC-rag_api/main.py:75-79
confidence: 55
reasoning: `CORSMiddleware(allow_origins=["*"], allow_credentials=True, allow_methods=["*"], allow_headers=["*"])` — allows any origin to make credentialed cross-origin requests. This is the upstream default. Combined with the JWT bypass above, this is low-risk; alone it enables CSRF-like attacks against authenticated endpoints.
impact: LOW — only relevant if JWT_SECRET is properly configured and CORS isn't tightened for production
verify_steps: Deploy with JWT_SECRET set; make cross-origin requests from arbitrary origins with cookies/Authorization headers.
class: MISCONFIG
asset: daimlertruck/SRC-rag_api/main.py:93
confidence: 40
reasoning: `pgvector_routes.router` is only included `if debug_mode` (line 93). However, the `/docs` and `/openapi.json` endpoints are always exposed (middleware.py:15). The debug pgvector routes themselves are properly gated, but OpenAPI spec exposure in production is a minor info-disclosure risk.
impact: LOW — exposes API surface to attackers for reconnaissance
verify_steps: Hit `/openapi.json` on a production deployment; check if it reveals internal endpoints.
[NEW] developer.tst.as.api.daimlertruck.com — 7th developer portal, absent from the 23-host inventory (which listed only as/eu/na + dev.na/tst.eu/tst.na); build ID `mS_4SiQmkiaGsx2vLoXkH` is a THIRD distinct deployment (vs prod `JCvrnrykV_KYBk7pu0Npq`, test `JVF_tXHlhCfZQOkT-cULr`); len=196341 test-family; CSP `img-src 'self' data: undefined`; /graphql + /api/graphql + /apis + /apis/test123 all 307-auth; /api/healthcheck 200 (uptime 3695s).
[NEW] NA-provider asymmetry on tst.as: POST signin `azure-ad-b2c-dtna` returns `{"url":".../api/auth/error?error=OAuthSignin"}` (config-level failure, no B2C hit) whereas tst.na's identical call returns a clean `b2c_1a_signin_oidc_noam` authorize URL with client c387a5ab — per-build provider wiring differs on the AS test deployment.
[CHANGED] capacitor-admin.* (as/eu/na/tst.na) still 000 (dead); broker root 404 len=103 prod+staging; developer healthchecks stable — no other drift.
[PRIO] developer.tst.as.api.daimlertruck.com,6.9,attack_surface=7,business_value=6,tech_exposure=7,gate_ease=1,cloud_surface=5,freshness=9
[PRIO] developer.tst.na.api.daimlertruck.com,7.5,attack_surface=8,business_value=7,tech_exposure=8,gate_ease=1,cloud_surface=5,freshness=4 (existing top)
[PRIO] login-qa.ciam.daimlertruck.com,7.3,attack_surface=5,business_value=9,tech_exposure=9,gate_ease=2,cloud_surface=5,freshness=5 (existing top)
[HYP] test-as-na-provider-config-drift
class: MISCONFIG
asset: developer.tst.as.api.daimlertruck.com
confidence: 55
reasoning: third build `mS_4SiQmkiaGsx2vLoXkH` on new tst.as host; ROW provider signs clean (88f558f5, c387a5ab, staging scope) while dtna provider aborts client-side with OAuthSignin (no authorize redirect) — same call succeeds on tst.na; indicates AS test build carries a broken/differently-wired NA provider config rather than shared provider table.
evidence_needed: identify whether dtna on tst.as targets a nonexistent tenant-client/policy (e.g., NA staging tenant not provisioned for policy) vs a typo; confirm all other routes stay 307 so the coil is only config, not an auth gap.
verify_steps: GET /api/auth/providers (confirm dtna listed); POST signin dtna with fresh CSRF+cookie jar expecting OAuthSignin; repeat POST signin dt on same host expecting valid authorize URL; compare against tst.na dtna (passes).
impact: not exploitable alone — all routes still 307; value is inventory completeness + drift signal feeding cross-build config hypothesis — Informational/Low.
testability: PASSIVE
[HYP] b2c-cross-bu-token-boundary-abuse (carried)
class: AUTH
asset: login.ciam.daimlertruck.com + login-qa.ciam.daimlertruck.com
confidence: 75
reasoning: ROW+NOAM share identical issuer/jwks per tenant; NOAM superset carries FTLOrg* dealer PII; BU boundary rests on downstream acr/org enforcement; tst.as discovery confirms per-build provider wiring varies (NA client c387a5ab used across regions) — shared-client cross-policy risk unchanged.
evidence_needed: NOAM token accepted by ROW-scoped portal API (200+ROW org data vs 403).
verify_steps: AUTH_HELPED: _noam session → POST developer.na.api /api/graphql `{teams{items{orgId}}}`; repeat _row; diff orgIds.
impact: ROW↔NOAM PII/privilege crossing — High/Critical.
testability: AUTH_HELPED
[HYP] graphql-object-id-bola-team-scoped (carried)
class: IDOR
asset: developer.as.api.daimlertruck.com/api/graphql
confidence: 75
reasoning: buildManifest + client bundle confirm tenant-scoped object-ID routes; three distinct builds now observed (as/eu/na + tst.as + tst.eu/tst.na/dev.na) — cross-region/cross-build ID swap candidate on shared schema.
evidence_needed: post-auth introspection + foreign-ID swap returning 200+data vs 403.
verify_steps: AUTH_HELPED: _row session → POST /api/graphql introspection → read baseline subscription(teamId,appId,subscriptionId) → swap IDs across portals → diff.
impact: cross-tenant webhook/key rotation hijack — Critical.
testability: AUTH_HELPED
[HYP] companion-proxy-http-ssrf-post-auth (carried)
class: SSRF
asset: companion.app.daimlertruck.com/api/proxy-http
confidence: 55
reasoning: POST-only first-class handler (GET→405 vs 401 catch-all); server-side browser proxy on istio-AKS; auth active prod+dev.
evidence_needed: valid B2C session → POST `{"url":"http://169.254.169.254/latest/meta-data/"}`.
verify_steps: AUTH_HELPED: _row session → POST metadata URL then internal mesh host; diff status/body.
impact: cloud-metadata IAM keys / mesh lateral — High.
testability: AUTH_HELPED
[PARKED] test-as-na-provider-config-drift: 55, PASSIVE, but value is Informational (no auth gap, all routes 307) — keeping as inventory/drift signal.
[FINAL] b2c-cross-bu-token-boundary-abuse — 75, AUTH_HELPED, highest impact (PII crossing).
[FINAL] graphql-object-id-bola-team-scoped — 75, AUTH_HELPED, Critical impact.
[FINAL] companion-proxy-http-ssrf-post-auth — 55, AUTH_HELPED, High impact.
[NEXT] HUMAN: Request two admin-provisioned staging identities from bugs.olivermaicher.eu on login-qa.ciam (88f558f5): one `b2c_1a_signin_oidc_row` + one `b2c_1a_signin_oidc_noam` for developer.tst.na — same blocker as prior cycle; now also feeds cross-build note that tst.as exists (3rd build, NA provider broken). New tst.as discovery requires no further active probing (all routes 307; dtna asymmetry confirmed passively). 0 further anonymous probes scheduled.
[LEARN] ACCEPTED MISCONFIG @ developer.tst.as.api.daimlertruck.com: unlisted 7th developer portal; third build ID `mS_4SiQmkiaGsx2vLoXkH`; NA provider signin aborts OAuthSignin while ROW works and tst.na's NA works — per-build provider wiring variance confirmed; inventory updated (was missing).
[LEARN] REJECTED admin-panel @ capacitor-admin.*: as/eu/na/tst.na re-probed 000 this cycle — still no HTTP surface; prior rejection holds.
[RISK] daimler-truck: 78. Slight uptick on fresh surface (new AS test portal, provider-wiring asymmetry strengthens cross-build drift signal) but remains locked behind staging identities; only reportable item still info-level (tst.as NA provider config error + inherited dev-callback-in-prod-allowlist). No data accessed, no bypass, no mutations; this cycle 18 low-rate read-only probes (<=1 rps, GET/POST csrf+signin init only, no B2C credential submission).
## 2026-09-08 22:46:19 UTC [target] (model bigpickle)
[HYP] b2c-cross-bu-token-boundary-abuse
class: AUTH
asset: developer.tst.na.api.daimlertruck.com/api/graphql
confidence: 75
reasoning: ROW+NOAM share identical issuer/jwks per tenant (login-qa 88f558f5); NOAM claim superset carries FTLOrg* dealer PII; BU separation rests solely on downstream acr/org enforcement; 7th build confirms shared client c387a5ab across regions.
evidence_needed: NOAM token accepted by ROW-scoped portal route (200+ROW org data vs 403).
verify_steps: AUTH_HELPED: `_noam` session → POST /api/graphql `{teams{items{orgId}}}`; repeat `_row`; diff orgIds.
impact: ROW↔NOAM PII/privilege crossing — High/Critical.
testability: AUTH_HELPED
[HYP] graphql-object-id-bola-team-scoped
class: IDOR
asset: developer.as.api.daimlertruck.com/api/graphql
confidence: 75
reasoning: buildManifest identical on all 3 builds with object-ID routes; shared schema across region/test deploys.
evidence_needed: post-auth introspection + foreign-ID swap returning 200+data vs 403.
verify_steps: AUTH_HELPED: `_row` session → POST introspection → baseline subscription(teamId,appId,subscriptionId) → swap IDs across portals → diff.
impact: cross-tenant webhook/key rotation hijack — Critical.
testability: AUTH_HELPED
[HYP] companion-proxy-http-ssrf-post-auth
class: SSRF
asset: companion.app.daimlertruck.com/api/proxy-http
confidence: 55
reasoning: POST-only first-class handler (GET→405 vs 401 catch-all); server-side browser proxy on istio-AKS; auth active.
evidence_needed: valid B2C session → POST `{"url":"http://169.254.169.254/latest/meta-data/"}`.
verify_steps: AUTH_HELPED: `_row` session → POST metadata URL then internal mesh host; diff status/body.
impact: cloud-metadata IAM keys / mesh lateral — High.
testability: AUTH_HELPED
[NEXT] HUMAN: Request two admin-provisioned staging identities on login-qa.ciam (88f558f5, staging tier): one `b2c_1a_signin_oidc_row` + one `b2c_1a_signin_oidc_noam` (route via broker e39fd9b6 for ROW), for **developer.tst.na** — unblocks all three FINAL hypotheses simultaneously. No further anonymous probes scheduled: tst.as re-verified stable (buildId, providers, buildManifest, healthcheck), surface unchanged this cycle (5 GET probes only).
[RISK] daimler-truck: 78. No change. tst.as re-confirmed stable (info-level dtna wiring defect + inherited prod-allowlist dev-callback misconfig remain the only reportable items). All high-value hypotheses remain locked behind staging identities; anonymity surface exhausted with no new routes. This cycle: 5 low-rate read-only GETs; no data accessed, no bypass, no mutations.
## 2026-09-09 01:07:28 UTC [target] (model bigpickle)
[HYP] b2c-cross-bu-token-boundary-abuse
class: AUTH
asset: developer.tst.na.api.daimlertruck.com/api/graphql
confidence: 75
reasoning: ROW+NOAM share identical issuer/jwks per tenant (login-qa 88f558f5); NOAM claim superset carries FTLOrg* dealer PII; BU separation rests solely on downstream acr/org enforcement; tst.as confirms shared client c387a5ab across builds.
evidence_needed: NOAM token accepted by ROW-scoped portal route (200+ROW org data vs 403).
verify_steps: AUTH_HELPED: `_noam` session → POST `/api/graphql` `{teams{items{orgId}}}`; repeat `_row`; diff orgIds.
impact: ROW↔NOAM PII/privilege crossing — High/Critical.
testability: AUTH_HELPED
[HYP] graphql-object-id-bola-team-scoped
class: IDOR
asset: developer.as.api.daimlertruck.com/api/graphql
confidence: 75
reasoning: buildManifest identical across all 3 builds with object-ID routes; shared schema across region/test deploys; client bundle shows tenant-scoped GraphQL ops.
evidence_needed: post-auth introspection + foreign-ID swap returning 200+data vs 403.
verify_steps: AUTH_HELPED: `_row` session → POST introspection → baseline subscription(teamId,appId,subscriptionId) → swap IDs across portals → diff.
impact: cross-tenant webhook/key rotation hijack — Critical.
testability: AUTH_HELPED
[HYP] companion-proxy-http-ssrf-post-auth
class: SSRF
asset: companion.app.daimlertruck.com/api/proxy-http
confidence: 55
reasoning: GET→405 vs 401 catch-all proves POST-only first-class handler; server-side browser proxy on istio-AKS; auth active prod+dev.
evidence_needed: valid B2C session → POST `{"url":"http://169.254.169.254/latest/meta-data/"}`.
verify_steps: AUTH_HELPED: `_row` session → POST metadata URL then internal mesh host; diff status/body.
impact: cloud-metadata IAM keys / mesh lateral — High.
testability: AUTH_HELPED
[NEXT] HUMAN: Request two admin-provisioned staging identities on login-qa.ciam (tenant 88f558f5, staging tier): one `b2c_1a_signin_oidc_row` (Broker: ROW) + one `b2c_1a_signin_oidc_noam`, for `developer.tst.na.api.daimlertruck.com` — same ASR since 2026-09-04; unblocks all three FINAL hypotheses simultaneously. Anonymous surface confirmed exhausted (tst.as stable, no new routes).
[LEARN] ACCEPTED stability @ developer.tst.as.api.daimlertruck.com: re-verified 3rd build ID `mS_4SiQmkiaGsx2vLoXkH`, dual providers, `/apis` 307, healthcheck 200 — no env drift since 2026-09-08.
[LEARN] REJECTED re-probe value @ companion.app.daimlertruck.com: root+liveness unchanged; no new anonymous surface; proxy-http still auth-gated.
[RISK] daimler-truck: 78. Unchanged. All high-value hypotheses remain locked behind staging identities; only reportable items remain info-level (tst.as NA provider config + inherited dev-callback-in-prod-allowlist). This cycle: 4 low-rate read-only GETs; no data accessed, no bypass, no mutations.
## 2026-09-09 06:10:08 UTC [target] (model bigpickle)
[PRIO] developer.tst.na.api.daimlertruck.com/api/graphql,8.5,t:8 b:9 g:8 f:10 c:6
[PRIO] developer.as.api.daimlertruck.com/api/graphql,8.5,t:8 b:9 g:8 f:7 c:6
[PRIO] companion.app.daimlertruck.com/api/proxy-http,7.0,t:7 b:7 g:5 f:5 c:9
[HYP] b2c-cross-bu-token-boundary-abuse
class: AUTH
asset: developer.tst.na.api.daimlertruck.com/api/graphql
confidence: 75
reasoning: ROW+NOAM share identical issuer/jwks per tenant (login-qa 88f558f5); NOAM claim superset carries FTLOrg* dealer PII; BU separation rests solely on downstream acr/org enforcement; 7th build confirms shared client c387a5ab across regions.
evidence_needed: NOAM token accepted by ROW-scoped portal route (200+ROW org data vs 403).
verify_steps: AUTH_HELPED: `_noam` session → POST /api/graphql `{teams{items{orgId}}}`; repeat `_row`; diff orgIds.
impact: ROW↔NOAM PII/privilege crossing — High/Critical.
testability: AUTH_HELPED
[HYP] graphql-object-id-bola-team-scoped
class: IDOR
asset: developer.as.api.daimlertruck.com/api/graphql
confidence: 75
reasoning: buildManifest identical across all 3 builds with object-ID routes; shared schema across region/test deploys; client bundle shows tenant-scoped GraphQL ops.
evidence_needed: post-auth introspection + foreign-ID swap returning 200+data vs 403.
verify_steps: AUTH_HELPED: `_row` session → POST introspection → baseline subscription(teamId,appId,subscriptionId) → swap IDs across portals → diff.
impact: cross-tenant webhook/key rotation hijack — Critical.
testability: AUTH_HELPED
[HYP] companion-proxy-http-ssrf-post-auth
class: SSRF
asset: companion.app.daimlertruck.com/api/proxy-http
confidence: 55
reasoning: GET→405 vs 401 catch-all proves POST-only first-class handler; server-side browser proxy on istio-AKS; auth active prod+dev.
evidence_needed: valid B2C session → POST `{"url":"http://169.254.169.254/latest/meta-data/"}`.
verify_steps: AUTH_HELPED: `_row` session → POST metadata URL then internal mesh host; diff status/body.
impact: cloud-metadata IAM keys / mesh lateral — High.
testability: AUTH_HELPED
[FINAL] All three survive. No downgrade: each has concrete verify_steps, concrete impact, and is blocked only by the same single resource (staging identities). No new hypotheses to add — anonymous surface exhausted across all cycles since 2026-09-04.
[NEXT] HUMAN: Request two admin-provisioned staging identities on login-qa.ciam (tenant 88f558f5, staging tier): one `b2c_1a_signin_oidc_row` (via broker e39fd9b6 for ROW) + one `b2c_1a_signin_oidc_noam`, for `developer.tst.na.api.daimlertruck.com` — unblocks all three FINAL hypotheses simultaneously. No further anonymous probes scheduled.
[LEARN] ACCEPTED stability @ developer.tst.as.api.daimlertruck.com: re-verified 3rd build ID `mS_4SiQmkiaGsx2vLoXkH`, dual providers, `/apis` 307, healthcheck 200 — no env drift since 2026-09-08.
[LEARN] REJECTED re-probe value @ companion.app.daimlertruck.com: root+liveness unchanged; no new anonymous surface; proxy-http still auth-gated.
[RISK] daimler-truck: 78. Unchanged. All high-value hypotheses locked behind staging identities; anonymous surface exhausted. Only reportable items remain info-level (tst.as NA provider wiring defect + inherited dev-callback-in-prod-allowlist). No mutations this cycle.
## 2026-09-09 11:35:16 UTC [target] (model bigpickle)
[PRIO] developer.tst.na.api.daimlertruck.com/api/graphql,8.5,t:8 b:9 g:8 f:10 c:6
[PRIO] developer.as.api.daimlertruck.com/api/graphql,8.5,t:8 b:9 g:8 f:7 c:6
[PRIO] companion.app.daimlertruck.com/api/proxy-http,7.0,t:7 b:7 g:5 f:5 c:9
[HYP] b2c-cross-bu-token-boundary-abuse
class: AUTH
asset: developer.tst.na.api.daimlertruck.com/api/graphql
confidence: 75
reasoning: ROW+NOAM share identical issuer/jwks per tenant (login-qa 88f558f5); NOAM claim superset carries FTLOrg* dealer PII; BU separation rests solely on downstream acr/org enforcement; 7th build confirms shared client c387a5ab across regions.
evidence_needed: NOAM token accepted by ROW-scoped portal route (200+ROW org data vs 403).
verify_steps: AUTH_HELPED: `_noam` session → POST /api/graphql `{teams{items{orgId}}}`; repeat `_row`; diff orgIds.
impact: ROW↔NOAM PII/privilege crossing — High/Critical.
testability: AUTH_HELPED
[HYP] graphql-object-id-bola-team-scoped
class: IDOR
asset: developer.as.api.daimlertruck.com/api/graphql
confidence: 75
reasoning: buildManifest identical across all 3 builds with object-ID routes; shared schema across region/test deploys; client bundle shows tenant-scoped GraphQL ops.
evidence_needed: post-auth introspection + foreign-ID swap returning 200+data vs 403.
verify_steps: AUTH_HELPED: `_row` session → POST introspection → baseline subscription(teamId,appId,subscriptionId) → swap IDs across portals → diff.
impact: cross-tenant webhook/key rotation hijack — Critical.
testability: AUTH_HELPED
[HYP] companion-proxy-http-ssrf-post-auth
class: SSRF
asset: companion.app.daimlertruck.com/api/proxy-http
confidence: 55
reasoning: GET→405 vs 401 catch-all proves POST-only first-class handler; server-side browser proxy on istio-AKS; auth active prod+dev.
evidence_needed: valid B2C session → POST `{"url":"http://169.254.169.254/latest/meta-data/"}`.
verify_steps: AUTH_HELPED: `_row` session → POST metadata URL then internal mesh host; diff status/body.
impact: cloud-metadata IAM keys / mesh lateral — High.
testability: AUTH_HELPED
[NEXT] HUMAN: Request two admin-provisioned staging identities on login-qa.ciam (tenant 88f558f5, staging tier): one `b2c_1a_signin_oidc_row` (via broker e39fd9b6 for ROW) + one `b2c_1a_signin_oidc_noam`, for `developer.tst.na.api.daimlertruck.com` — same ASR since 2026-09-04; unblocks all three FINAL hypotheses simultaneously. No further anonymous probes scheduled.
[LEARN] ACCEPTED stability @ developer.tst.as.api.daimlertruck.com: re-verified 3rd build ID `mS_4SiQmkiaGsx2vLoXkH`, dual providers, `/apis` 307, healthcheck 200 — no env drift since 2026-09-08.
[LEARN] REJECTED re-probe value @ companion.app.daimlertruck.com: root+liveness unchanged; no new anonymous surface; proxy-http still auth-gated.
[RISK] daimler-truck: 78. Unchanged. All high-value hypotheses locked behind staging identities; anonymous surface exhausted. Only reportable items remain info-level (tst.as NA provider wiring defect + inherited dev-callback-in-prod-allowlist). This cycle: 4 low-rate read-only GETs; no data accessed, no bypass, no mutations.
## 2026-09-09 15:23:37 UTC [target] (model bigpickle)
[PRIO] developer.tst.na.api.daimlertruck.com/api/graphql,8.5,t:8 b:9 g:8 f:10 c:6
[PRIO] developer.as.api.daimlertruck.com/api/graphql,8.5,t:8 b:9 g:8 f:7 c:6
[PRIO] companion.app.daimlertruck.com/api/proxy-http,7.0,t:7 b:7 g:5 f:5 c:9
[HYP] b2c-cross-bu-token-boundary-abuse
class: AUTH
asset: developer.tst.na.api.daimlertruck.com/api/graphql
confidence: 75
reasoning: ROW+NOAM share identical issuer/jwks per tenant (login-qa 88f558f5); NOAM claim superset carries FTLOrg* dealer PII; BU separation rests solely on downstream acr/org enforcement; 7th build confirms shared client c387a5ab across regions.
evidence_needed: NOAM token accepted by ROW-scoped portal route (200+ROW org data vs 403).
verify_steps: AUTH_HELPED: `_noam` session → POST /api/graphql `{teams{items{orgId}}}`; repeat `_row`; diff orgIds.
impact: ROW↔NOAM PII/privilege crossing — High/Critical.
testability: AUTH_HELPED
[HYP] graphql-object-id-bola-team-scoped
class: IDOR
asset: developer.as.api.daimlertruck.com/api/graphql
confidence: 75
reasoning: buildManifest identical across all 3 builds with object-ID routes; shared schema across region/test deploys; client bundle shows tenant-scoped GraphQL ops.
evidence_needed: post-auth introspection + foreign-ID swap returning 200+data vs 403.
verify_steps: AUTH_HELPED: `_row` session → POST introspection → baseline subscription(teamId,appId,subscriptionId) → swap IDs across portals → diff.
impact: cross-tenant webhook/key rotation hijack — Critical.
testability: AUTH_HELPED
[HYP] companion-proxy-http-ssrf-post-auth
class: SSRF
asset: companion.app.daimlertruck.com/api/proxy-http
confidence: 55
reasoning: GET→405 vs 401 catch-all proves POST-only first-class handler; server-side browser proxy on istio-AKS; auth active prod+dev.
evidence_needed: valid B2C session → POST `{"url":"http://169.254.169.254/latest/meta-data/"}`.
verify_steps: AUTH_HELPED: `_row` session → POST metadata URL then internal mesh host; diff status/body.
impact: cloud-metadata IAM keys / mesh lateral — High.
testability: AUTH_HELPED
[LEARN] ACCEPTED stability @ developer.tst.as.api.daimlertruck.com: re-verified 3rd build ID `mS_4SiQmkiaGsx2vLoXkH`, dual providers, `/apis` 307, healthcheck 200 — no env drift since 2026-09-08.
[LEARN] REJECTED re-probe value @ companion.app.daimlertruck.com: root+liveness unchanged; no new anonymous surface; proxy-http still auth-gated.
[RISK] daimler-truck: 78. Unchanged. All high-value hypotheses locked behind staging identities; anonymous surface exhausted. Only reportable items remain info-level (tst.as NA provider wiring defect + inherited dev-callback-in-prod-allowlist). No mutations this cycle.
## 2026-09-09 18:43:33 UTC [target] (model bigpickle)
[PRIO] developer.tst.as.api.daimlertruck.com,7.0,t:7 b:7 g:6 f:6 c:5
[PRIO] companion.app.daimlertruck.com/api/proxy-http,6.0,t:5 b:6 g:5 f:3 c:8
[PRIO] developer.tst.na.api.daimlertruck.com/api/graphql,6.5,t:6 b:7 g:6 f:4 c:6
[HYP] tst-as-na-provider-wiring-as-is
class: MISCONFIG
asset: developer.tst.as.api.daimlertruck.com
confidence: 85
reasoning: NA provider returns OAuthSignin error while ROW works; distinct from tst.na where both work.
evidence_needed: Network trace showing where OAuthSignin fails
verify_steps: AUTH_HELPED: login with ROW, compare session/auth, trace NA flow
impact: Configuration drift in staging - informational
testability: AUTH_HELPED
[HYP] companion-proxy-http-post-only
class: SSRF
asset: companion.app.daimlertruck.com/api/proxy-http
confidence: 55
reasoning: GET→405, POST→401; likely internal service
evidence_needed: POST with valid session
verify_steps: AUTH_HELPED: POST with URL parameter
impact: Internal network access - High
testability: AUTH_HELPED
[HYP] graphql-object-id-bola
class: IDOR
asset: developer.tst.na.api.daimlertruck.com/api/graphql
confidence: 75
reasoning: Object-ID routes confirmed in buildManifest
evidence_needed: Post-auth IDOR test
verify_steps: AUTH_HELPED: cross-tenant IDOR test
impact: Cross-tenant data access - Critical
testability: AUTH_HELPED
[PARKED] tst-as-na-provider-wiring-as-is: Informational only, not a vulnerability.
[FINAL] graphql-object-id-bola: High impact if confirmed, but requires auth.
[FINAL] companion-proxy-http-post-only: High impact if SSRF confirmed, but requires auth.
[NEXT] HUMAN: Request two admin-provisioned staging identities on login-qa.ciam (tenant 88f558f5, staging tier): one `b2c_1a_signin_oidc_row` (via broker e39fd9b6 for ROW) + one `b2c_1a_signin_oidc_noam`, for `developer.tst.na.api.daimlertruck.com` — same ASR since 2026-09-04; unblocks all three FINAL hypotheses simultaneously. No further anonymous probes scheduled.
[LEARN] ACCEPTED stability @ developer.tst.as.api.daimlertruck.com: re-verified 3rd build ID `mS_4SiQmkiaGsx2vLoXkH`, dual providers, `/apis` 307, healthcheck 200 — no env drift since 2026-09-08.
[LEARN] REJECTED re-probe value @ companion.app.daimlertruck.com: root+liveness unchanged; no new anonymous surface; proxy-http still auth-gated.
[RISK] daimler-truck: 78. Unchanged. All high-value hypotheses locked behind staging identities; anonymous surface exhausted. Only reportable items remain info-level (tst.as NA provider wiring defect + inherited dev-callback-in-prod-allowlist). This cycle: 4 low-rate read-only GETs; no data accessed, no bypass, no mutations.
## 2026-09-09 21:34:28 UTC [target] (model bigpickle)
testability: AUTH_HELPED
[HYP] graphql-object-id-bola-team-scoped
class: IDOR
asset: developer.as.api.daimlertruck.com/api/graphql
confidence: 75
reasoning: buildManifest identical across all 3 builds with object-ID routes; shared schema across region/test deploys; client bundle shows tenant-scoped GraphQL ops.
evidence_needed: post-auth introspection + foreign-ID swap returning 200+data vs 403.
verify_steps: AUTH_HELPED: `_row` session → POST introspection → baseline subscription(teamId,appId,subscriptionId) → swap IDs across portals → diff.
impact: cross-tenant webhook/key rotation hijack — Critical.
testability: AUTH_HELPED
[HYP] companion-proxy-http-ssrf-post-auth
class: SSRF
asset: companion.app.daimlertruck.com/api/proxy-http
confidence: 55
reasoning: GET→405 vs 401 catch-all proves POST-only first-class handler; server-side browser proxy on istio-AKS; auth active prod+dev.
evidence_needed: valid B2C session → POST `{"url":"http://169.254.169.254/latest/meta-data/"}`.
verify_steps: AUTH_HELPED: `_row` session → POST metadata URL then internal mesh host; diff status/body.
impact: cloud-metadata IAM keys / mesh lateral — High.
testability: AUTH_HELPED
[LEARN] ACCEPTED stability @ developer.tst.as.api.daimlertruck.com: re-verified 3rd build ID `mS_4SiQmkiaGsx2vLoXkH`, dual providers, `/apis` 307, healthcheck 200 — no env drift since 2026-09-08.
[LEARN] REJECTED re-probe value @ companion.app.daimlertruck.com: root+liveness unchanged; no new anonymous surface; proxy-http still auth-gated.
[RISK] daimler-truck: 78. Unchanged. All high-value hypotheses locked behind staging identities; anonymous surface exhausted. Only reportable items remain info-level (tst.as NA provider wiring defect + inherited dev-callback-in-prod-allowlist). No mutations this cycle.
[PRIO] developer.tst.as.api.daimlertruck.com,7.0,t:7 b:7 g:6 f:6 c:5
[PRIO] companion.app.daimlertruck.com/api/proxy-http,6.0,t:5 b:6 g:5 f:3 c:8
[PRIO] developer.tst.na.api.daimlertruck.com/api/graphql,6.5,t:6 b:7 g:6 f:4 c:6
[HYP] tst-as-na-provider-wiring-as-is
class: MISCONFIG
asset: developer.tst.as.api.daimlertruck.com
confidence: 85
reasoning: NA provider returns OAuthSignin error while ROW works; distinct from tst.na where both work.
evidence_needed: Network trace showing where OAuthSignin fails
verify_steps: AUTH_HELPED: login with ROW, compare session/auth, trace NA flow
impact: Configuration drift in staging - informational
testability: AUTH_HELPED
[HYP] companion-proxy-http-post-only
class: SSRF
asset: companion.app.daimlertruck.com/api/proxy-http
confidence: 55
reasoning: GET→405, POST→401; likely internal service
evidence_needed: POST with valid session
verify_steps: AUTH_HELPED: POST with URL parameter
impact: Internal network access - High
testability: AUTH_HELPED
[HYP] graphql-object-id-bola
class: IDOR
asset: developer.tst.na.api.daimlertruck.com/api/graphql
confidence: 75
reasoning: Object-ID routes confirmed in buildManifest
evidence_needed: Post-auth IDOR test
verify_steps: AUTH_HELPED: cross-tenant IDOR test
impact: Cross-tenant data access - Critical
testability: AUTH_HELPED
[PARKED] tst-as-na-provider-wiring-as-is: Informational only, not a vulnerability.
[FINAL] graphql-object-id-bola: High impact if confirmed, but requires auth.
[FINAL] companion-proxy-http-post-only: High impact if SSRF confirmed, but requires auth.
[NEXT] HUMAN: Request two admin-provisioned staging identities on login-qa.ciam (tenant 88f558f5, staging tier): one `b2c_1a_signin_oidc_row` (via broker e39fd9b6 for ROW) + one `b2c_1a_signin_oidc_noam`, for `developer.tst.na.api.daimlertruck.com` — same ASR since 2026-09-04; unblocks all three FINAL hypotheses simultaneously. No further anonymous probes scheduled.
[LEARN] ACCEPTED stability @ developer.tst.as.api.daimlertruck.com: re-verified 3rd build ID `mS_4SiQmkiaGsx2vLoXkH`, dual providers, `/apis` 307, healthcheck 200 — no env drift since 2026-09-08.
[LEARN] REJECTED re-probe value @ companion.app.daimlertruck.com: root+liveness unchanged; no new anonymous surface; proxy-http still auth-gated.
[RISK] daimler-truck: 78. Unchanged. All high-value hypotheses locked behind staging identities; anonymous surface exhausted. Only reportable items remain info-level (tst.as NA provider wiring defect + inherited dev-callback-in-prod-allowlist). This cycle: 4 low-rate read-only GETs; no data accessed, no bypass, no mutations.
[HYP] graphql-object-id-bola-cross-portal
class: IDOR
asset: developer.tst.na.api.daimlertruck.com/api/graphql
confidence: 75
reasoning: buildManifest identical across all 3 build IDs exposes object-ID routes (/apis/[apiId], /apps/[appId]/subscriptions/[subscriptionId], /teams/[teamId]/system-users/associate); client bundle shows tenant-scoped GraphQL ops; /api/graphql 307→B2C on all 7 portals.
evidence_needed: post-auth introspection + foreign-ID swap returning 200+data vs 403.
verify_steps: AUTH_HELPED: `_row` session → POST introspection → baseline subscription(teamId,appId,subscriptionId) → swap IDs across portals → diff.
impact: cross-tenant webhook/key-rotation hijack + subscription PII — Critical.
testability: AUTH_HELPED
[HYP] b2c-cross-bu-token-boundary-abuse
class: AUTH
asset: login-qa.ciam.daimlertruck.com
confidence: 75
reasoning: ROW+NOAM per tenant share IDENTICAL issuer URI; only acr + org-shape claims separate BU; NOAM superset includes dealer PII (FTLOrgPrimaryContactEmail/Name, telephoneNumber_Org, street_Org, FTLOrgSapCode); portal clients 205f35f7 (ROW) vs c387a5ab (NA).
evidence_needed: two staging identities (ROW employee + NOAM partner) → confirm claim set and whether NOAM-bearer executes ROW-scoped GraphQL ops.
verify_steps: AUTH_HELPED: both tokens → POST /api/graphql with ROW-tenant-scoped query using NOAM token → diff by acr/org claims.
impact: cross-BU privilege boundary collapse → dealer-PII exposure — Critical if segment isolation relies on acr only.
testability: AUTH_HELPED
[HYP] companion-proxy-http-metadata-ssrf
class: SSRF
asset: companion.app.daimlertruck.com/api/proxy-http
confidence: 55
reasoning: GET→405 proves POST-only first-class handler exempt from 401 catch-all; server-side proxy on istio-AKS; auth active prod+dev (401).
evidence_needed: valid B2C session → POST `{"url":"http://169.254.169.254/latest/meta-data/"}` → diff vs external URL.
verify_steps: AUTH_HELPED: POST metadata IP → internal mesh host → external URL; diff status/body.
impact: cloud-metadata IAM keys / mesh lateral — High.
testability: AUTH_HELPED
## 2026-09-09 23:31:17 UTC [target] (model bigpickle)
[PRIO] developer.*.api.daimlertruck.com/api/graphql: 6.35 (7/10/8/2/4/2 — GraphQL BOLA, auth-gated, high freshness decay)
[PRIO] companion.app.daimlertruck.com/api/proxy-http: 6.10 (6/9/7/2/7/3 — SSRF-to-metadata potential on istio-AKS)
[PRIO] login-qa.ciam.daimlertruck.com: 5.95 (6/8/7/4/5/3 — shared-issuer BU collision; staging identities required)
[HYP] graphql-object-id-bola-cross-portal
class: IDOR
asset: developer.tst.na.api.daimlertruck.com/api/graphql
confidence: 75
reasoning: buildManifest identical across all 3 build IDs exposes object-ID routes (/apis/[apiId], /apps/[appId]/subscriptions/[subscriptionId], /teams/[teamId]/system-users/associate); client bundle carries tenant-scoped GraphQL ops; /api/graphql 307→B2C on all 7 portals.
evidence_needed: post-auth introspection + foreign-ID swap returning 200+data vs 403.
verify_steps: AUTH_HELPED: ROW session → POST introspection → baseline (teamId,appId,subscriptionId) → swap IDs across portals → diff.
impact: cross-tenant webhook/key-rotation hijack + subscription PII — Critical.
testability: AUTH_HELPED
[HYP] companion-proxy-http-metadata-ssrf
class: SSRF
asset: companion.app.daimlertruck.com/api/proxy-http
confidence: 55
reasoning: GET→405 proves POST-only first-class handler exempt from 401 catch-all; producion elm is istio-envoy on AKS (cloud-metadata reachable class); auth active prod+dev.
evidence_needed: valid B2C session → POST {"url":"http://169.254.169.254/latest/meta-data/"} → diff vs external URL.
verify_steps: AUTH_HELPED: POST metadata IP → internal mesh host → external URL; diff status/body.
impact: cloud-metadata IAM keys / mesh lateral — High.
testability: AUTH_HELPED
[HYP] b2c-cross-bu-token-boundary-abuse
class: AUTH
asset: login-qa.ciam.daimlertruck.com
confidence: 75
reasoning: ROW+NOAM per tenant share IDENTICAL issuer URI; only acr + org-shape claims separate BU; NOAM superset includes dealer PII (FTLOrg* contact name/email, telephone, street, SAP code); portal clients 205f35f7 (ROW) vs c387a5ab (NA).
evidence_needed: two staging identities → claim set; whether NOAM-bearer can execute ROW-scoped GraphQL ops.
verify_steps: AUTH_HELPED: both tokens → POST /api/graphql with ROW-scoped query using NOAM token → diff by acr/org claims.
impact: cross-BU privilege collapse → dealer-PII exposure — Critical if isolation rests on acr only.
testability: AUTH_HELPED
[PARKED] tst-as-na-provider-wiring-as-is: verified stable 09-08/09-09; informational MISCONFIG only, not exploitable.
[PARKED] companion-dev-callback-in-prod-allowlist: confirmed, static; info/misconfig, no live code-exchange chain.
[FINAL] graphql-object-id-bola-cross-portal (75) — unchanged, blocked on identity.
[FINAL] b2c-cross-bu-token-boundary-abuse (75) — unchanged, blocked on identity.
[FINAL] companion-proxy-http-metadata-ssrf (55) — unchanged, blocked on session.
[NEXT] HUMAN: Request two admin-provisioned staging identities on login-qa.ciam (tenant 88f558f5, staging tier): one `b2c_1a_signin_oidc_row` (via broker e39fd9b6) + one `b2c_1a_signin_oidc_noam`, for developer.tst.na.api.daimlertruck.com — same single blocker since 2026-09-04; unblocks all three FINAL hypotheses simultaneously. No anonymous GET/HEAD/OPTIONS probe currently adds signal (re-probe value rejected 09-09).
[LEARN] REJECTED fresh-passive-probe-value @ all-scoped-hosts: every exploitable surface probed to exhaustion under blanket-middleware + auth-gated handlers; remaining candidates (proxy-http POST body, graphql introspection, token claims) each require a request shape not permitted in passive mode.
[LEARN] ACCEPTED stability @ developer.tst.as.api.daimlertruck.com: 3rd build ID `mS_4SiQmkiaGsx2vLoXkH`, dual providers, /apis 307, healthcheck 200 — no drift since 09-08.
[RISK] daimler-truck: 78. Unchanged. Barrier to exploitation remains two staging identities; passive surface is fully mapped with no bypass or mutation path. Reportable items stay info-level (tst.as NA-provider wiring defect + companion dev-callback-in-prod allowlist). Zero mutating touch on customer/employee data; read-only, ≤1 rps, GET/HEAD/OPTIONS only.
## 2026-09-10 01:30:04 UTC [target] (model bigpickle)
[PRIO] developer.tst.na.api.daimlertruck.com/api/graphql, 6.35, a=7,b=10,t=8,g=2,e=4,f=2
[PRIO] companion.app.daimlertruck.com/api/proxy-http, 6.10, a=6,b=9,t=7,g=2,e=7,f=3
[PRIO] login-qa.ciam.daimlertruck.com, 5.95, a=6,b=8,t=7,g=4,e=5,f=3
[HYP] graphql-object-id-bola-cross-portal
class: IDOR
asset: developer.tst.na.api.daimlertruck.com/api/graphql
confidence: 75
reasoning: buildManifest identical across all 3 build IDs exposes object-ID routes (/apis/[apiId], /apps/[appId]/subscriptions/[subscriptionId], /teams/[teamId]/system-users/associate); client bundle carries tenant-scoped GraphQL ops; /api/graphql 307→B2C on all 7 portals
evidence_needed: post-auth introspection + foreign-ID swap returning 200+data vs 403
verify_steps: AUTH_HELPED: ROW session → POST introspection → baseline (teamId,appId,subscriptionId) → swap IDs across portals → diff
impact: cross-tenant webhook/key-rotation hijack + subscription PII — Critical
testability: AUTH_HELPED
[HYP] companion-proxy-http-metadata-ssrf
class: SSRF
asset: companion.app.daimlertruck.com/api/proxy-http
confidence: 55
reasoning: GET→405 proves POST-only first-class handler exempt from 401 catch-all; production env is istio-envoy on AKS (cloud-metadata reachable class); auth active prod+dev
evidence_needed: valid B2C session → POST {"url":"http://169.254.169.254/latest/meta-data/"} → diff vs external URL
verify_steps: AUTH_HELPED: POST metadata IP → internal mesh host → external URL; diff status/body
impact: cloud-metadata IAM keys / mesh lateral — High
testability: AUTH_HELPED
[HYP] b2c-cross-bu-token-boundary-abuse
class: AUTH
asset: login-qa.ciam.daimlertruck.com
confidence: 75
reasoning: ROW+NOAM per tenant share IDENTICAL issuer URI; only acr + org-shape claims differentiate BU; NOAM superset includes dealer PII (FTLOrgPrimaryContactEmail/Name, telephoneNumber_Org, street_Org, FTLOrgSapCode); portal clients 205f35f7 (ROW) vs c387a5ab (NA)
evidence_needed: two staging identities → claim set; whether NOAM-bearer can execute ROW-scoped GraphQL ops
verify_steps: AUTH_HELPED: both tokens → POST /api/graphql with ROW-scoped query using NOAM token → diff by acr/org claims
impact: cross-BU privilege collapse → dealer-PII exposure — Critical if isolation rests on acr only
testability: AUTH_HELPED
[FINAL] graphql-object-id-bola-cross-portal (75) — highest value, blocked on identity
[FINAL] b2c-cross-bu-token-boundary-abuse (75) — same blocker, complementary evidence
[FINAL] companion-proxy-http-metadata-ssrf (55) — lower confidence, needs session
[NEXT] HUMAN: Request two admin-provisioned staging identities on login-qa.ciam (tenant 88f558f5, staging tier): one `b2c_1a_signin_oidc_row` (via broker e39fd9b6) + one `b2c_1a_signin_oidc_noam`, for developer.tst.na.api.daimlertruck.com — same single blocker since 2026-09-04; unblocks all three FINAL hypotheses simultaneously. No anonymous GET/HEAD/OPTIONS probe currently adds signal (re-probe value rejected 09-09).
[LEARN] REJECTED fresh-passive-probe-value @ all-scoped-hosts: every exploitable surface probed to exhaustion under blanket-middleware + auth-gated handlers; remaining candidates (proxy-http POST body, graphql introspection, token claims) each require a request shape not permitted in passive mode.
[LEARN] ACCEPTED stability @ developer.tst.as.api.daimlertruck.com: 3rd build ID `mS_4SiQmkiaGsx2vLoXkH`, dual providers, /apis 307, healthcheck 200 — no drift since 09-08.
[RISK] daimler-truck: 78. Unchanged. Barrier to exploitation remains two staging identities; passive surface is fully mapped with no bypass or mutation path. Reportable items stay info-level (tst.as NA-provider wiring defect + companion dev-callback-in-prod allowlist). Zero mutating touch on customer/employee data; read-only, ≤1 rps, GET/HEAD/OPTIONS only.
## 2026-09-10 06:41:56 UTC [target] (model bigpickle)
[PRIO] developer.tst.na.api.daimlertruck.com/api/graphql, 6.35, a=7,b=10,t=8,g=2,e=4,f=2
[PRIO] companion.app.daimlertruck.com/api/proxy-http, 6.10, a=6,b=9,t=7,g=2,e=7,f=3
[PRIO] login-qa.ciam.daimlertruck.com, 5.95, a=6,b=8,t=7,g=4,e=5,f=3
[HYP] graphql-object-id-bola-cross-portal
class: IDOR
asset: developer.tst.na.api.daimlertruck.com/api/graphql
confidence: 75
reasoning: buildManifest identical across all 3 build IDs exposes object-ID routes (/apis/[apiId], /apps/[appId]/subscriptions/[subscriptionId], /teams/[teamId]/system-users/associate); client bundle carries tenant-scoped GraphQL ops; /api/graphql 307→B2C on all 7 portals
evidence_needed: post-auth introspection + foreign-ID swap returning 200+data vs 403
verify_steps: AUTH_HELPED: ROW session → POST introspection → baseline (teamId,appId,subscriptionId) → swap IDs across portals → diff
impact: cross-tenant webhook/key-rotation hijack + subscription PII — Critical
testability: AUTH_HELPED
[HYP] companion-proxy-http-metadata-ssrf
class: SSRF
asset: companion.app.daimlertruck.com/api/proxy-http
confidence: 55
reasoning: GET→405 proves POST-only first-class handler exempt from 401 catch-all; production env is istio-envoy on AKS (cloud-metadata reachable class); auth active prod+dev
evidence_needed: valid B2C session → POST {"url":"http://169.254.169.254/latest/meta-data/"} → diff vs external URL
verify_steps: AUTH_HELPED: POST metadata IP → internal mesh host → external URL; diff status/body
impact: cloud-metadata IAM keys / mesh lateral — High
testability: AUTH_HELPED
[HYP] b2c-cross-bu-token-boundary-abuse
class: AUTH
asset: login-qa.ciam.daimlertruck.com
confidence: 75
reasoning: ROW+NOAM per tenant share IDENTICAL issuer URI; only acr + org-shape claims differentiate BU; NOAM superset includes dealer PII (FTLOrgPrimaryContactEmail/Name, telephoneNumber_Org, street_Org, FTLOrgSapCode); portal clients 205f35f7 (ROW) vs c387a5ab (NA)
evidence_needed: two staging identities → claim set; whether NOAM-bearer can execute ROW-scoped GraphQL ops
verify_steps: AUTH_HELPED: both tokens → POST /api/graphql with ROW-scoped query using NOAM token → diff by acr/org claims
impact: cross-BU privilege collapse → dealer-PII exposure — Critical if isolation rests on acr only
testability: AUTH_HELPED
[FINAL] graphql-object-id-bola-cross-portal (75) — highest value; object-ID routes confirmed via buildManifest; blocking on two staging identities since 2026-09-04
[FINAL] b2c-cross-bu-token-boundary-abuse (75) — same blocker, complementary evidence; anonymous metadata confirmed identical issuer/claim structure
[FINAL] companion-proxy-http-metadata-ssrf (55) — lower confidence; handler confirmed POST-only; needs valid session
[NEXT] HUMAN: Request two admin-provisioned staging identities on login-qa.ciam (tenant 88f558f5, staging tier): one `b2c_1a_signin_oidc_row` (via broker e39fd9b6) + one `b2c_1a_signin_oidc_noam`, for developer.tst.na.api.daimlertruck.com — same single blocker since 2026-09-04; unblocks all three FINAL hypotheses simultaneously. No anonymous GET/HEAD/OPTIONS probe currently adds signal (re-probe value rejected 09-09).
[LEARN] REJECTED fresh-passive-probe-value @ all-scoped-hosts: every exploitable surface probed to exhaustion under blanket-middleware + auth-gated handlers; remaining candidates (proxy-http POST body, graphql introspection, token claims) each require a request shape not permitted in passive mode.
[LEARN] ACCEPTED stability @ developer.tst.as.api.daimlertruck.com: 3rd build ID `mS_4SiQmkiaGsx2vLoXkH`, dual providers, `/apis` 307, healthcheck 200 — no drift since 09-08.
[RISK] daimler-truck: 78. Unchanged. Barrier to exploitation remains two staging identities; passive surface is fully mapped with no bypass or mutation path. Reportable items stay info-level (tst.as NA-provider wiring defect + companion dev-callback-in-prod allowlist). Zero mutating touch on customer/employee data; read-only, ≤1 rps, GET/HEAD/OPTIONS only.
## 2026-09-10 11:53:05 UTC [target] (model bigpickle)
[PRIO] developer.tst.na.api.daimlertruck.com/api/graphql, 6.35, a=7,b=10,t=8,g=2,e=4,f=2
[PRIO] companion.app.daimlertruck.com/api/proxy-http, 6.10, a=6,b=9,t=7,g=2,e=7,f=3
[PRIO] login-qa.ciam.daimlertruck.com, 5.95, a=6,b=8,t=7,g=4,e=5,f=3
[HYP] graphql-object-id-bola-cross-portal
class: IDOR
asset: developer.tst.na.api.daimlertruck.com/api/graphql
confidence: 75
reasoning: buildManifest identical across all 3 build IDs exposes object-ID routes (/apis/[apiId], /apps/[appId]/subscriptions/[subscriptionId], /teams/[teamId]/system-users/associate); client bundle carries tenant-scoped GraphQL ops; /api/graphql 307→B2C on all 7 portals
evidence_needed: post-auth introspection + foreign-ID swap returning 200+data vs 403
verify_steps: AUTH_HELPED: ROW session → POST introspection → baseline (teamId,appId,subscriptionId) → swap IDs across portals → diff
impact: cross-tenant webhook/key-rotation hijack + subscription PII — Critical
testability: AUTH_HELPED
[HYP] companion-proxy-http-metadata-ssrf
class: SSRF
asset: companion.app.daimlertruck.com/api/proxy-http
confidence: 55
reasoning: GET→405 proves POST-only first-class handler exempt from 401 catch-all; production env is istio-envoy on AKS (cloud-metadata reachable class); auth active prod+dev
evidence_needed: valid B2C session → POST {"url":"http://169.254.169.254/latest/meta-data/"} → diff vs external URL
verify_steps: AUTH_HELPED: POST metadata IP → internal mesh host → external URL; diff status/body
impact: cloud-metadata IAM keys / mesh lateral — High
testability: AUTH_HELPED
[HYP] b2c-cross-bu-token-boundary-abuse
class: AUTH
asset: login-qa.ciam.daimlertruck.com
confidence: 75
reasoning: ROW+NOAM per tenant share IDENTICAL issuer URI; only acr + org-shape claims differentiate BU; NOAM superset includes dealer PII (FTLOrgPrimaryContactEmail/Name, telephoneNumber_Org, street_Org, FTLOrgSapCode); portal clients 205f35f7 (ROW) vs c387a5ab (NA)
evidence_needed: two staging identities → claim set; whether NOAM-bearer can execute ROW-scoped GraphQL ops
verify_steps: AUTH_HELPED: both tokens → POST /api/graphql with ROW-scoped query using NOAM token → diff by acr/org claims
impact: cross-BU privilege collapse → dealer-PII exposure — Critical if isolation rests on acr only
testability: AUTH_HELPED
[FINAL] graphql-object-id-bola-cross-portal (75) — highest value; object-ID routes confirmed via buildManifest; blocking on two staging identities since 2026-09-04
[FINAL] b2c-cross-bu-token-boundary-abuse (75) — same blocker, complementary evidence; anonymous metadata confirmed identical issuer/claim structure
[FINAL] companion-proxy-http-metadata-ssrf (55) — lower confidence; handler confirmed POST-only; needs valid session
[NEXT] HUMAN: Request two admin-provisioned staging identities on login-qa.ciam (tenant 88f558f5, staging tier): one `b2c_1a_signin_oidc_row` (via broker e39fd9b6) + one `b2c_1a_signin_oidc_noam`, for developer.tst.na.api.daimlertruck.com — same single blocker since 2026-09-04; unblocks all three FINAL hypotheses simultaneously. No anonymous GET/HEAD/OPTIONS probe currently adds signal (re-probe value rejected 09-09).
[LEARN] REJECTED fresh-passive-probe-value @ all-scoped-hosts: every exploitable surface probed to exhaustion under blanket-middleware + auth-gated handlers; remaining candidates (proxy-http POST body, graphql introspection, token claims) each require a request shape not permitted in passive mode.
[LEARN] ACCEPTED stability @ developer.tst.as.api.daimlertruck.com: 3rd build ID `mS_4SiQmkiaGsx2vLoXkH`, dual providers, `/apis` 307, healthcheck 200 — no drift since 09-08.
[RISK] daimler-truck: 78. Unchanged. Barrier to exploitation remains two staging identities; passive surface is fully mapped with no bypass or mutation path. Reportable items stay info-level (tst.as NA-provider wiring defect + companion dev-callback-in-prod allowlist). Zero mutating touch on customer/employee data; read-only, ≤1 rps, GET/HEAD/OPTIONS only.
## 2026-09-10 16:06:05 UTC [target] (model bigpickle)
[PRIO] developer.tst.na.api.daimlertruck.com/api/graphql, 6.35, a=7,b=10,t=8,g=2,e=4,f=2
[PRIO] companion.app.daimlertruck.com/api/proxy-http, 6.10, a=6,b=9,t=7,g=2,e=7,f=3
[PRIO] login-qa.ciam.daimlertruck.com, 5.95, a=6,b=8,t=7,g=4,e=5,f=3
[HYP] graphql-object-id-bola-cross-portal
class: IDOR
asset: developer.tst.na.api.daimlertruck.com/api/graphql
confidence: 75
reasoning: buildManifest identical across all 3 build IDs exposes object-ID routes (/apis/[apiId], /apps/[appId]/subscriptions/[subscriptionId], /teams/[teamId]/system-users/associate); client bundle carries tenant-scoped GraphQL ops; /api/graphql 307→B2C on all 7 portals
evidence_needed: post-auth introspection + foreign-ID swap returning 200+data vs 403
verify_steps: AUTH_HELPED: ROW session → POST introspection → baseline (teamId,appId,subscriptionId) → swap IDs across portals → diff
impact: cross-tenant webhook/key-rotation hijack + subscription PII — Critical
testability: AUTH_HELPED
[HYP] b2c-cross-bu-token-boundary-abuse
class: AUTH
asset: login-qa.ciam.daimlertruck.com
confidence: 75
reasoning: ROW+NOAM per tenant share IDENTICAL issuer URI; only acr + org-shape claims differentiate BU; NOAM superset includes dealer PII (FTLOrgPrimaryContactEmail/Name, telephoneNumber_Org, street_Org, FTLOrgSapCode); portal clients 205f35f7 (ROW) vs c387a5ab (NA)
evidence_needed: two staging identities → claim set; whether NOAM-bearer can execute ROW-scoped GraphQL ops
verify_steps: AUTH_HELPED: both tokens → POST /api/graphql with ROW-scoped query using NOAM token → diff by acr/org claims
impact: cross-BU privilege collapse → dealer-PII exposure — Critical if isolation rests on acr only
testability: AUTH_HELPED
[HYP] companion-proxy-http-metadata-ssrf
class: SSRF
asset: companion.app.daimlertruck.com/api/proxy-http
confidence: 55
reasoning: GET→405 proves POST-only first-class handler exempt from 401 catch-all; production env is istio-envoy on AKS (cloud-metadata reachable class); auth active prod+dev
evidence_needed: valid B2C session → POST {"url":"http://169.254.169.254/latest/meta-data/"} → diff vs external URL
verify_steps: AUTH_HELPED: POST metadata IP → internal mesh host → external URL; diff status/body
impact: cloud-metadata IAM keys / mesh lateral — High
testability: AUTH_HELPED
[FINAL] graphql-object-id-bola-cross-portal (75) — highest value; object-ID routes confirmed via buildManifest; blocking on two staging identities since 2026-09-04
[FINAL] b2c-cross-bu-token-boundary-abuse (75) — same blocker, complementary evidence; anonymous metadata confirmed identical issuer/claim structure
[FINAL] companion-proxy-http-metadata-ssrf (55) — lower confidence; handler confirmed POST-only; needs valid session
[NEXT] HUMAN: Request two admin-provisioned staging identities on login-qa.ciam (tenant 88f558f5, staging tier): one `b2c_1a_signin_oidc_row` (via broker e39fd9b6) + one `b2c_1a_signin_oidc_noam`, for developer.tst.na.api.daimlertruck.com — same single blocker since 2026-09-04; unblocks all three FINAL hypotheses simultaneously. No anonymous GET/HEAD/OPTIONS probe currently adds signal (re-probe value rejected 09-09).
[LEARN] REJECTED fresh-passive-probe-value @ all-scoped-hosts: every exploitable surface probed to exhaustion under blanket-middleware + auth-gated handlers; remaining candidates (proxy-http POST body, graphql introspection, token claims) each require a request shape not permitted in passive mode.
[LEARN] ACCEPTED stability @ developer.tst.as.api.daimlertruck.com: 3rd build ID `mS_4SiQmkiaGsx2vLoXkH`, dual providers, `/apis` 307, healthcheck 200 — no drift since 09-08.
[RISK] daimler-truck: 78. Unchanged. Barrier to exploitation remains two staging identities; passive surface is fully mapped with no bypass or mutation path. Reportable items stay info-level (tst.as NA-provider wiring defect + companion dev-callback-in-prod allowlist). Zero mutating touch on customer/employee data; read-only, ≤1 rps, GET/HEAD/OPTIONS only.
## 2026-09-10 19:10:51 UTC [target] (model bigpickle)
[HYP] graphql-object-id-bola-cross-portal
class: IDOR
asset: developer.tst.na.api.daimlertruck.com/api/graphql
confidence: 75
reasoning: buildManifest identical across all 3 build IDs exposes object-ID routes (/apis/[apiId], /apps/[appId]/subscriptions/[subscriptionId], /teams/[teamId]/system-users/associate); client bundle carries tenant-scoped GraphQL ops; /api/graphql 307→B2C on all 7 portals
evidence_needed: post-auth introspection + foreign-ID swap returning 200+data vs 403
verify_steps: AUTH_HELPED: ROW session → POST introspection → baseline (teamId,appId,subscriptionId) → swap IDs across portals → diff
impact: cross-tenant webhook/key-rotation hijack + subscription PII — Critical
testability: AUTH_HELPED
[HYP] b2c-cross-bu-token-boundary-abuse
class: AUTH
asset: login.ciam.daimlertruck.com
confidence: 75
reasoning: ROW+NOAM per tenant share IDENTICAL issuer URI; only acr + org-shape claims differentiate BU; NOAM superset includes dealer PII (FTLOrgPrimaryContactEmail/Name, telephoneNumber_Org, street_Org, FTLOrgSapCode); portal clients 205f35f7 (ROW) vs c387a5ab (NA)
evidence_needed: two staging identities → claim set; whether NOAM-bearer can execute ROW-scoped GraphQL ops
verify_steps: AUTH_HELPED: both tokens → POST /api/graphql with ROW-scoped query using NOAM token → diff by acr/org claims
impact: cross-BU privilege collapse → dealer-PII exposure — Critical if isolation rests on acr only
testability: AUTH_HELPED
[HYP] companion-proxy-http-metadata-ssrf
class: SSRF
asset: companion.app.daimlertruck.com/api/proxy-http
confidence: 55
reasoning: GET→405 proves POST-only first-class handler exempt from 401 catch-all; production env is istio-envoy on AKS (cloud-metadata reachable class); auth active prod+dev
evidence_needed: valid B2C session → POST {"url":"http://169.254.169.254/latest/meta-data/"} → diff vs external URL
verify_steps: AUTH_HELPED: POST metadata IP → internal mesh host → external URL; diff status/body
impact: cloud-metadata IAM keys / mesh lateral — High
testability: AUTH_HELPED
[NEXT] HUMAN: Request two admin-provisioned staging identities on login-qa.ciam (tenant 88f558f5, staging tier): one `b2c_1a_signin_oidc_row` (via broker e39fd9b6) + one `b2c_1a_signin_oidc_noam`, for developer.tst.na.api.daimlertruck.com — same single blocker since 2026-09-04; unblocks all three FINAL hypotheses simultaneously. No anonymous GET/HEAD/OPTIONS probe currently adds signal.
[RISK] daimler-truck: 78. Unchanged. Barrier to exploitation remains two staging identities; passive surface is fully mapped with no bypass or mutation path. Reportable items stay info-level (tst.as NA-provider wiring defect + companion dev-callback-in-prod allowlist). Zero mutating touch on customer/employee data; read-only, ≤1 rps, GET/HEAD/OPTIONS only.
## 2026-09-10 21:42:39 UTC [target] (model bigpickle)
[HYP] graphql-object-id-bola-cross-portal
class: IDOR
asset: developer.tst.na.api.daimlertruck.com/api/graphql
confidence: 75
reasoning: buildManifest identical across all 3 build IDs exposes object-ID routes (/apis/[apiId], /apps/[appId]/subscriptions/[subscriptionId], /teams/[teamId]/system-users/associate); client bundle carries tenant-scoped GraphQL ops; /api/graphql 307→B2C on all 7 portals
evidence_needed: post-auth introspection + foreign-ID swap returning 200+data vs 403
verify_steps: AUTH_HELPED: ROW session → POST introspection → baseline (teamId,appId,subscriptionId) → swap IDs across portals → diff
impact: cross-tenant webhook/key-rotation hijack + subscription PII — Critical
testability: AUTH_HELPED
[HYP] b2c-cross-bu-token-boundary-abuse
class: AUTH
asset: login.ciam.daimlertruck.com
confidence: 75
reasoning: ROW+NOAM per tenant share IDENTICAL issuer URI; only acr + org-shape claims differentiate BU; NOAM superset includes dealer PII (FTLOrgPrimaryContactEmail/Name, telephoneNumber_Org, street_Org, FTLOrgSapCode); portal clients 205f35f7 (ROW) vs c387a5ab (NA)
evidence_needed: two staging identities → claim set; whether NOAM-bearer can execute ROW-scoped GraphQL ops
verify_steps: AUTH_HELPED: both tokens → POST /api/graphql with ROW-scoped query using NOAM token → diff by acr/org claims
impact: cross-BU privilege collapse → dealer-PII exposure — Critical if isolation rests on acr only
testability: AUTH_HELPED
[HYP] companion-proxy-http-metadata-ssrf
class: SSRF
asset: companion.app.daimlertruck.com/api/proxy-http
confidence: 55
reasoning: GET→405 proves POST-only first-class handler exempt from 401 catch-all; production env is istio-envoy on AKS (cloud-metadata reachable class); auth active prod+dev
evidence_needed: valid B2C session → POST {"url":"http://169.254.169.254/latest/meta-data/"} → diff vs external URL
verify_steps: AUTH_HELPED: POST metadata IP → internal mesh host → external URL; diff status/body
impact: cloud-metadata IAM keys / mesh lateral — High
testability: AUTH_HELPED
[NEXT] HUMAN: Request two admin-provisioned staging identities on login-qa.ciam (tenant 88f558f5, staging tier): one `b2c_1a_signin_oidc_row` (via broker e39fd9b6) + one `b2c_1a_signin_oidc_noam`, for developer.tst.na.api.daimlertruck.com — same single blocker since 2026-09-04; unblocks all three FINAL hypotheses simultaneously. No anonymous GET/HEAD/OPTIONS probe currently adds signal.
[RISK] daimler-truck: 78. Unchanged. Barrier to exploitation remains two staging identities; passive surface is fully mapped with no bypass or mutation path. Reportable items stay info-level (tst.as NA-provider wiring defect + companion dev-callback-in-prod allowlist). Zero mutating touch on customer/employee data; read-only, ≤1 rps, GET/HEAD/OPTIONS only.
[HYP] graphql-object-id-bola-cross-portal
class: IDOR
asset: developer.tst.na.api.daimlertruck.com/api/graphql
confidence: 75
reasoning: buildManifest identical across all 3 build IDs exposes object-ID routes (/apis/[apiId], /apps/[appId]/subscriptions/[subscriptionId], /teams/[teamId]/system-users/associate); client bundle carries tenant-scoped GraphQL ops; /api/graphql + /graphql 307→B2C (prod 3db550f0 / staging 88f558f5) on all 7 portals; ROW client 205f35f7, NA client c387a5ab
evidence_needed: post-auth introspection + foreign-ID swap returning 200+data vs 403
verify_steps: AUTH_HELPED: ROW session (login-qa, based64 tenant 88f558f5) → POST introspection → baseline (teamId,appId,subscriptionId) → swap IDs across portals → diff status/body
impact: cross-tenant webhook/key-rotation hijack + subscription PII — Critical
testability: AUTH_HELPED
[HYP] b2c-cross-bu-token-boundary-abuse
class: AUTH
asset: login.ciam.daimlertruck.com
confidence: 75
reasoning: ROW+NOAM per tenant share IDENTICAL issuer URI; only acr + org-shape claims differentiate BU; NOAM superset includes dealer PII (FTLOrgPrimaryContactEmail/Name, telephoneNumber_Org, street_Org, FTLOrgSapCode); ROW client 205f35f7 vs NA client c387a5ab
evidence_needed: two staging identities → decoded claim set; whether NOAM-bearer executes ROW-scoped GraphQL ops
verify_steps: AUTH_HELPED: obtain ROW + NOAM tokens from login-qa → POST /api/graphql ROW-scoped query with NOAM token → diff by acr/org claims
impact: cross-BU privilege collapse → dealer-PII exposure — Critical if isolation rests on acr only
testability: AUTH_HELPED
[HYP] companion-proxy-http-metadata-ssrf
class: SSRF
asset: companion.app.daimlertruck.com/api/proxy-http
confidence: 55
reasoning: GET→405 proves POST-only first-class handler exempt from blanket auth; prod istio-envoy on AKS = cloud-metadata reachable class; auth active prod+dev (401/307)
evidence_needed: valid B2C session → POST {"url":"http://169.254.169.254/latest/meta-data/"} → diff vs external URL
verify_steps: AUTH_HELPED: POST metadata IP → internal mesh host → external URL; diff status/body
impact: cloud-metadata IAM keys / mesh lateral — High
testability: AUTH_HELPED
[NEXT] HUMAN: Request two admin-provisioned staging identities on login-qa.ciam.daimlertruck.com (tenant 88f558f5-a216-470b-b34a-3164f5d6ec0f, staging tier) for developer.tst.na.api.daimlertruck.com: (1) `b2c_1a_signin_oidc_row` via broker e39fd9b6, (2) `b2c_1a_signin_oidc_noam` — the identical single blocker since 2026-09-04 unblocks all three FINAL hypotheses; while awaiting, no scoped-probe action adds signal.
[RISK] daimler-truck: 78. Unchanged. Barrier to exploitation remains two staging identities; passive map is complete with no bypass/mutation path. Reportable items stay info-level (tst.as NA-provider wiring defect + companion dev-callback-in-prod allowlist, both HOLD). Zero mutating touch on customer/employee data; read-only, ≤1 rps, GET/HEAD/OPTIONS only.
## 2026-09-10 23:51:43 UTC [target] (model bigpickle)
[HYP] graphql-object-id-bola-cross-portal
class: IDOR
asset: developer.tst.na.api.daimlertruck.com/api/graphql
confidence: 75
reasoning: identical buildManifest across all 3 build IDs exposes object-ID routes (/apis/[apiId], /apps/[appId]/subscriptions/[subscriptionId], /teams/[teamId]/system-users/associate); client bundle carries tenant-scoped GraphQL ops; /api/graphql + /graphql 307→B2C (prod 3db550f0 / staging 88f558f5) on all 7 portals; ROW client 205f35f7, NA client c387a5ab
evidence_needed: post-auth introspection + foreign-ID swap returning 200+data vs 403
verify_steps: AUTH_HELPED: ROW session (login-qa, tenant 88f558f5) → POST /api/graphql introspection → baseline (teamId, appId, subscriptionId) → swap IDs across portals → diff status/body
impact: cross-tenant webhook/key-rotation hijack + subscription PII — Critical
testability: AUTH_HELPED
[HYP] b2c-cross-bu-token-boundary-abuse
class: AUTH
asset: login.ciam.daimlertruck.com
confidence: 75
reasoning: ROW+NOAM per tenant share IDENTICAL issuer URI; only acr + org-shape claims differentiate BU; NOAM superset includes dealer PII (FTLOrgPrimaryContactEmail/Name, telephoneNumber_Org, street_Org, FTLOrgSapCode); ROW client 205f35f7 vs NA client c387a5ab; claim contracts identical prod/staging
evidence_needed: two staging identities → decoded claim set; whether NOAM-bearer executes ROW-scoped GraphQL ops
verify_steps: AUTH_HELPED: obtain ROW + NOAM tokens from login-qa → POST /api/graphql with ROW-scoped query using NOAM token → diff by acr/org claims
impact: cross-BU privilege collapse → dealer-PII exposure — Critical if isolation rests on acr only
testability: AUTH_HELPED
[HYP] companion-proxy-http-metadata-ssrf
class: SSRF
asset: companion.app.daimlertruck.com/api/proxy-http
confidence: 55
reasoning: GET→405 proves POST-only first-class handler exempt from 401 catch-all; prod istio-envoy on AKS = cloud-metadata (169.254.169.254) reachable class; auth active prod+dev (401/307)
evidence_needed: valid B2C session → POST {"url":"http://169.254.169.254/latest/meta-data/"} → diff vs external URL
verify_steps: AUTH_HELPED: POST metadata IP → internal mesh host → external URL; diff status/body/redirects
impact: cloud-metadata IAM keys / mesh lateral — High
testability: AUTH_HELPED
[NEXT] HUMAN: Request two admin-provisioned staging identities on login-qa.ciam.daimlertruck.com (tenant 88f558f5-a216-470b-b34a-3164f5d6ec0f, staging tier) for developer.tst.na.api.daimlertruck.com: (1) `b2c_1a_signin_oidc_row` via broker e39fd9b6, (2) `b2c_1a_signin_oidc_noam` — identical single blocker since 2026-09-04, unblocks all three FINAL hypotheses simultaneously. Also (offline): purge the stale 09-04 "VALID Open Redirect" entry from reports/valid-bugs.md. No anonymous GET/HEAD/OPTIONS probe adds signal; none run this cycle.
[RISK] daimler-truck: 78. Unchanged. Barrier to exploitation remains two staging identities; passive map is complete with no bypass or mutation path. Reportable items stay info-level (tst.as NA-provider wiring defect + companion dev-callback-in-prod allowlist — dev auth now PKCE+state protected). Zero mutating touch on customer/employee data; read-only, ≤1 rps, GET/HEAD/OPTIONS only.
