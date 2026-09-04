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
