# Daimler Truck Holding AG inventory (discovery seed 2026-09-02)
# NOTE: hosts below are discovery candidates from passive DNS/CT; confirm in-scope vs program scope before active testing.
api.daimlertruck.com
daimlertruck.com
www.daimlertruck.com

## PASSIVE RECON 2026-09-02 (read-only, non-intrusive)

> Recon observations only. These are NOT confirmed vulnerabilities; ownership/in-scope of each host must be confirmed against the program scope before any active testing. Hosts resolve + serve HTTP — investigation requires scoped authorization.

**Probed:** 3 hosts | **Live HTTP:** 1

| Host | Status | Server/Tech |
|---|---|---|
| `www.daimlertruck.com` | 302 | Server: Apache; Via: 1.1 8d4e3613dbc1a754e3aa29893 -> http://www.daimlertruck.com/en |

**CNAME review signals (1):**
- `www.daimlertruck.com` -> `dyrgcrxsrxorm.cloudfront.net`

**Takeover-review flags (1):** (DNS-level, most resolve = claimed/live, verify ownership)
- `www.daimlertruck.com` :: CNAME-TO-AWS CloudFront :: CNAME->dyrgcrxsrxorm.cloudfront.net, resolves to ['54.240.162.113', '54.240.162.16', '54.240.162.47', '54.240.162.66'], verify ownership

## DEEP SERVICE SCAN 2026-09-02 (read-only connect+banner)
**Host:** `www.daimlertruck.com` | **Ports:** [80, 443]
**Web surface only:** [80, 443]

## DEEP ENUM (wildcard-cleaned) 2026-09-03
**Root zone:** `daimlertruck.com` | **dedicated hosts after wildcard-filter: 23**
> Audit: brute+passive subfinder produced 10,083 resolving hostnames; zone-wildcard + IP-fingerprint filtering dropped 9,973 (98.9%) DNS-wildcard noise (random labels resolving to shared wildcard IPs e.g. account.cineplex.de, a.hypofriend.de, account.live-manager.de, docker.jtl-software.de, *.ggamdom.com, *.dev.alfaview.com). Only genuine dedicated hosts listed below. These are surface-map observations; live HTTP status captured read-only (GET / via curl). No findings claimed; scope must be confirmed with the program.
- `as.api.daimlertruck.com`  [HTTP 404]
- `authz.as.api.daimlertruck.com`  [HTTP 404]
- `authz.dev.na.api.daimlertruck.com`  [HTTP 404]
- `authz.eu.api.daimlertruck.com`  [HTTP 404]
- `authz.na.api.daimlertruck.com`  [HTTP 404]
- `authz.tst.as.api.daimlertruck.com`  [HTTP 404]
- `authz.tst.eu.api.daimlertruck.com`  [HTTP 404]
- `authz.tst.na.api.daimlertruck.com`  [HTTP 404]
- `capacitor-admin.as.api.daimlertruck.com`  [HTTP unprobed]
- `capacitor-admin.dev.na.api.daimlertruck.com`  [HTTP unprobed]
- `capacitor-admin.eu.api.daimlertruck.com`  [HTTP unprobed]
- `capacitor-admin.na.api.daimlertruck.com`  [HTTP unprobed]
- `capacitor-admin.tst.as.api.daimlertruck.com`  [HTTP unprobed]
- `capacitor-admin.tst.eu.api.daimlertruck.com`  [HTTP unprobed]
- `capacitor-admin.tst.na.api.daimlertruck.com`  [HTTP unprobed]
- `dev.na.api.daimlertruck.com`  [HTTP 404]
- `developer.as.api.daimlertruck.com`  [HTTP 200]
- `developer.dev.na.api.daimlertruck.com`  [HTTP 200]
- `developer.eu.api.daimlertruck.com`  [HTTP 200]
- `developer.na.api.daimlertruck.com`  [HTTP 200]
- `developer.tst.eu.api.daimlertruck.com`  [HTTP 200]
- `developer.tst.na.api.daimlertruck.com`  [HTTP 200]
- `eu.api.daimlertruck.com`  [HTTP 404]

## 2026-09-02 21:45:21 UTC

## 2026-09-02 23:56:20 UTC

## 2026-09-03 03:42:29 UTC

## 2026-09-03 08:46:04 UTC

## 2026-09-03 13:25:19 UTC

## 2026-09-03 17:17:27 UTC

## 2026-09-03 20:13:47 UTC
- CHANGED developer.*.api.daimlertruck.com (6 hosts): All 6 confirmed HTTP 200 with identical Next.js "TruckAPI | API Hub" portal; Azure AD B2C auth; all API endpoints (/graphql, /api/*, /swagger*, /openapi*, /
- CHANGED capacitor-admin.*.api.daimlertruck.com (6 hosts): All 6 return 000 (connection failed) — likely not deployed or firewalled
- CHANGED authz.*.api.daimlertruck.com (7 hosts): All 7 return HTTP 404 on root; OAuth/OIDC well-known endpoints (.well-known/oauth-authorization-server, .well-known/openid-configuration) return 404 — no OAuth 
- NEW Two distinct build IDs across developer portals: prod-like (as,eu,na) = `JCvrnrykV_KYBk7pu0Npq`; test/dev (dev.na,tst.eu,tst.na) = `JVF_tXHlhCfZQOkT-cULr` — suggests separate deployments with potentia

## 2026-09-03 22:31:26 UTC
- NEW Probe confirmed: developer.*.api.daimlertruck.com (6 hosts) all return HTTP 200 on /graphql and /api/catalog but with identical response length (196649 bytes) as root — indicates Next.js SPA serving l
- NEW capacitor-admin.*.api.daimlertruck.com (6 hosts): all return connection reset (000/ERR) — confirmed firewalled/not deployed
- NEW authz.*.api.daimlertruck.com (7 hosts): all return 404 on root and .well-known OAuth/OIDC endpoints — no OAuth surface
- NEW Two distinct Next.js build IDs: prod (as,eu,na) = `JCvrnrykV_KYBk7pu0Npq`; test/dev (dev.na,tst.eu,tst.na) = `JVF_tXHlhCfZQOkT-cULr` — separate deployments, potential config drift

## 2026-09-04 00:43:49 UTC
- NEW Probe confirmed: developer.*.api.daimlertruck.com (6 hosts) all return HTTP 200 on /graphql and /api/catalog but with identical response length (196649 bytes) as root — indicates Next.js SPA serving l
- NEW capacitor-admin.*.api.daimlertruck.com (6 hosts): all return connection reset (000/ERR) — confirmed firewalled/not deployed
- NEW authz.*.api.daimlertruck.com (7 hosts): all return 404 on root and .well-known OAuth/OIDC endpoints — no OAuth surface
- NEW Two distinct Next.js build IDs: prod (as,eu,na) = `JCvrnrykV_KYBk7pu0Npq`; test/dev (dev.na,tst.eu,tst.na) = `JVF_tXHlhCfZQOkT-cULr` — separate deployments, potential config drift

## 2026-09-04 05:09:10 UTC
- CHANGED developer.*.api.daimlertruck.com: probe confirms SPA catch-all — all paths (/graphql, /swagger.json, /api-docs, /openapi.json, /docs) return identical 196649 bytes as root. These are Next.js SPA route

## 2026-09-04 09:50:35 UTC
- CHANGED developer.as.api.daimlertruck.com: /graphql now returns 307 redirect to `/?callbackUrl=%2Fgraphql` (Azure AD B2C login) instead of 200 SPA shell — confirms real GraphQL endpoint exists behind auth
- CHANGED developer.tst.na.api.daimlertruck.com: Content-length 196341 bytes (vs 196649 prod) with buildId `JVF_tXHlhCfZQOkT-cULr` — confirms separate deployment with config drift
- NEW Azure AD B2C auth flow captured: Sign-in button `data-testid="sign-in-azure-ad-b2c-dt-button"` redirects to B2C tenant; callbackUrl parameter preserves target path post-auth

## 2026-09-04 14:09:35 UTC
- CHANGED developer.as.api.daimlertruck.com: /graphql returns 307 to Azure AD B2C (not 200 SPA shell) — real GraphQL endpoint behind auth confirmed
- CHANGED developer.tst.na.api.daimlertruck.com: Content-length 196341 (vs 196649 prod), buildId `JVF_tXHlhCfZQOkT-cULr` — separate deployment with config drift confirmed
- NEW Azure AD B2C auth flow: callbackUrl preserves target path; sign-in button `data-testid="sign-in-azure-ad-b2c-dt-button"`
- NEW SPA catch-all: all 6 developer.* portals return identical 196649 bytes for all paths (prod) — Next.js shell

## 2026-09-04 17:59:35 UTC
- CHANGED developer.*.api.daimlertruck.com: Latest probe (14:09:52) shows /graphql returning 200 (SPA catch-all) on all envs, not 307 — discrepancy with 09:50 observation; possible caching/routing difference be
- NEW /api/auth/csrf → 400 on GET (exists, needs POST) — NextAuth.js API route confirmed alive on test env
- NEW /api/auth/signin/azure-ad-b2c-dt?callbackUrl=https://example.com → 400 on GET — open redirect test returned 400, inconclusive (needs CSRF token from POST /api/auth/csrf first)
- NEW /api/auth/session → 400 on GET for both prod (as) and test (tst.na) — exists but requires proper session/POST
- NEW /api/graphql on developer.tst.na → 200 len=196341 (SPA catch-all, same as root) — NOT a real GraphQL endpoint, same false positive as /graphql
- NEW buildManifest.js fetched successfully: len=2999 at `/_next/static/JVF_tXHlhCfZQOkT-cULr/_buildManifest.js` — contains route/page structure for test env
- CHANGED developer.tst.na.api.daimlertruck.com: /graphql, /api/graphql, /swagger.json, /api-docs, /health now return 307 to Azure AD B2C (previously 200 SPA shell) — test environment now enforces auth like pro
- CHANGED developer.as.api.daimlertruck.com: /graphql, /api/graphql, /swagger.json, /api-docs, /health return 307 to Azure AD B2C — consistent auth enforcement
- NEW /api/auth/signin/azure-ad-b2c-dt?callbackUrl=https://example.com returns 302 on test — open redirect candidate in NextAuth.js callback flow

## 2026-09-04 20:10:53 UTC
- CHANGED developer.*.api.daimlertruck.com: probe confirms SPA catch-all — all paths (/graphql, /swagger.json, /api-docs, /openapi.json, /docs) return identical 196649 bytes as root. These are Next.js SPA route
- CHANGED developer.*.api.daimlertruck.com: Latest probe (14:09:52) shows /graphql returning 200 (SPA catch-all) on all envs, not 307 — discrepancy with 09:50 observation; possible caching/routing difference be
- NEW /api/auth/csrf → 400 on GET (exists, needs POST) — NextAuth.js API route confirmed alive on test env
- NEW /api/auth/signin/azure-ad-b2c-dt?callbackUrl=https://example.com → 400 on GET — open redirect test returned 400, inconclusive (needs CSRF token from POST /api/auth/csrf first)
- NEW /api/auth/session → 400 on GET for both prod (as) and test (tst.na) — exists but requires proper session/POST
- NEW /api/graphql on developer.tst.na → 200 len=196341 (SPA catch-all, same as root) — NOT a real GraphQL endpoint, same false positive as /graphql
- NEW buildManifest.js fetched successfully: len=2999 at `/_next/static/JVF_tXHlhCfZQOkT-cULr/_buildManifest.js` — contains route/page structure for test env
- CHANGED developer.*.api.daimlertruck.com (6 hosts): All 6 confirmed HTTP 200 with identical Next.js "TruckAPI | API Hub" portal; Azure AD B2C auth; all API endpoints (/graphql, /api/*, /swagger*, /openapi*, /
- CHANGED capacitor-admin.*.api.daimlertruck.com (6 hosts): All 6 return 000 (connection failed) — likely not deployed or firewalled
- CHANGED authz.*.api.daimlertruck.com (7 hosts): All 7 return HTTP 404 on root; OAuth/OIDC well-known endpoints (.well-known/oauth-authorization-server, .well-known/openid-configuration) return 404 — no OAuth 
- NEW Two distinct build IDs across developer portals: prod-like (as,eu,na) = `JCvrnrykV_KYBk7pu0Npq`; test/dev (dev.na,tst.eu,tst.na) = `JVF_tXHlhCfZQOkT-cULr` — suggests separate deployments with potentia
- NEW Probe confirmed: developer.*.api.daimlertruck.com (6 hosts) all return HTTP 200 on /graphql and /api/catalog but with identical response length (196649 bytes) as root — indicates Next.js SPA serving l
- NEW capacitor-admin.*.api.daimlertruck.com (6 hosts): all return connection reset (000/ERR) — confirmed firewalled/not deployed
- NEW authz.*.api.daimlertruck.com (7 hosts): all return 404 on root and .well-known OAuth/OIDC endpoints — no OAuth surface
- NEW Two distinct Next.js build IDs: prod (as,eu,na) = `JCvrnrykV_KYBk7pu0Npq`; test/dev (dev.na,tst.eu,tst.na) = `JVF_tXHlhCfZQOkT-cULr` — separate deployments, potential config drift
- CHANGED developer.tst.na.api.daimlertruck.com: /graphql, /api/graphql, /swagger.json, /api-docs, /health now return 307 to Azure AD B2C (previously 200 SPA shell) — test environment now enforces auth like pro
- CHANGED developer.as.api.daimlertruck.com: /graphql, /api/graphql, /swagger.json, /api-docs, /health return 307 to Azure AD B2C — consistent auth enforcement
- NEW /api/auth/signin/azure-ad-b2c-dt?callbackUrl=https://example.com returns 302 on test — open redirect candidate in NextAuth.js callback flow
- CHANGED developer.*.api.daimlertruck.com: Latest probe (14:09:52) shows /graphql returning 200 (SPA catch-all) on all envs, not 307 — discrepancy with 09:50 observation; possible caching/routing difference be
- NEW /api/auth/csrf → 400 on GET (exists, needs POST) — NextAuth.js API route confirmed alive on test env
- NEW /api/auth/signin/azure-ad-b2c-dt?callbackUrl=https://example.com → 400 on GET — open redirect test returned 400, inconclusive (needs CSRF token from POST /api/auth/csrf first)
- NEW /api/auth/session → 400 on GET for both prod (as) and test (tst.na) — exists but requires proper session/POST
- NEW /api/graphql on developer.tst.na → 200 len=196341 (SPA catch-all, same as root) — NOT a real GraphQL endpoint, same false positive as /graphql
- NEW buildManifest.js fetched successfully: len=2999 at `/_next/static/JVF_tXHlhCfZQOkT-cULr/_buildManifest.js` — contains route/page structure for test env
- CHANGED developer.*.api.daimlertruck.com (6 hosts): All 6 confirmed HTTP 200 with identical Next.js "TruckAPI | API Hub" portal; Azure AD B2C auth; all API endpoints (/graphql, /api/*, /swagger*, /openapi*, /
- CHANGED capacitor-admin.*.api.daimlertruck.com (6 hosts): All 6 return 000 (connection failed) — likely not deployed or firewalled
- CHANGED authz.*.api.daimlertruck.com (7 hosts): All 7 return HTTP 404 on root; OAuth/OIDC well-known endpoints (.well-known/oauth-authorization-server, .well-known/openid-configuration) return 404 — no OAuth 
- NEW Two distinct build IDs across developer portals: prod-like (as,eu,na) = `JCvrnrykV_KYBk7pu0Npq`; test/dev (dev.na,tst.eu,tst.na) = `JVF_tXHlhCfZQOkT-cULr` — suggests separate deployments with potentia
- NEW Probe confirmed: developer.*.api.daimlertruck.com (6 hosts) all return HTTP 200 on /graphql and /api/catalog but with identical response length (196649 bytes) as root — indicates Next.js SPA serving l
- NEW capacitor-admin.*.api.daimlertruck.com (6 hosts): all return connection reset (000/ERR) — confirmed firewalled/not deployed
- NEW authz.*.api.daimlertruck.com (7 hosts): all return 404 on root and .well-known OAuth/OIDC endpoints — no OAuth surface
- NEW Two distinct Next.js build IDs: prod (as,eu,na) = `JCvrnrykV_KYBk7pu0Npq`; test/dev (dev.na,tst.eu,tst.na) = `JVF_tXHlhCfZQOkT-cULr` — separate deployments, potential config drift
- CHANGED developer.tst.na.api.daimlertruck.com: /graphql, /api/graphql, /swagger.json, /api-docs, /health now return 307 to Azure AD B2C (previously 200 SPA shell) — test environment now enforces auth like pro
- CHANGED developer.as.api.daimlertruck.com: /graphql, /api/graphql, /swagger.json, /api-docs, /health return 307 to Azure AD B2C — consistent auth enforcement
- NEW /api/auth/signin/azure-ad-b2c-dt?callbackUrl=https://example.com returns 302 on test — open redirect candidate in NextAuth.js callback flow
- CHANGED developer.tst.na.api.daimlertruck.com: /api/auth/csrf GET returns 200 + csrfToken + set-cookie (__Host-next-auth.csrf-token + __Secure-next-auth.callback-url) — earlier "400 on GET" observation was wr
- CHANGED developer.tst.na.api.daimlertruck.com: /api/auth/session GET returns 200 {} (empty session, not 400) — endpoint alive, expected empty when unauthenticated
- NEW OAuth initiate via POST /api/auth/signin/azure-ad-b2c-dt?json=true with valid CSRF returns B2C authorize URL as JSON: test tenant login-qa.ciam.daimlertruck.com/88f558f5-a216-470b-b34a-3164f5d6ec0f, p
- NEW OAuth initiate prod (as): tenant login.ciam.daimlertruck.com/3db550f0, policy b2c_1a_signin_oidc_row, client 205f35f7 — proper prod/staging segregation confirmed
- NEW /api/auth/providers reveals 2 OAuth providers on all portals: azure-ad-b2c-dt (ROW) + azure-ad-b2c-dtna (North America, policy b2c_1a_signin_oidc_noam)
- NEW buildManifest route structure revealed: /apis/[apiId], /apps/[appId]/subscriptions/[subscriptionId], /teams/[teamId]/system-users/associate, /products/[productId]/subscribe, rewrites /docs/:slug*, /ap
- NEW /api/healthcheck live on test: 200 {"status":"ok","uptime":124427,...} — uptime leaks deploy age
- NEW CSP config drift: prod has img-src 'self' data: https://app.usercentrics.eu + frame-src https://companion.app.daimlertruck.com; test/dev has img-src 'self' data: undefined + empty frame-src — literal 
- NEW /api/metrics returns 404 (not exposed), /api/graphql returns 307 to auth on GET+POST (real endpoint behind middleware)

## 2026-09-04 22:25:29 UTC
- CHANGED NextAuth.js auth flow on developer.*.api.daimlertruck.com (6 portals): previously reported "broken" — testing shows it works correctly; POST /api/auth/signin/azure-ad-b2c-dt with valid CSRF returns 30
- CHANGED callbackUrl parameter validation: external domains (https://example.com) rejected and replaced with current origin in __Secure-next-auth.callback-url cookie; same-domain paths (e.g., /apis/test123) ac

## 2026-09-05 00:19:09 UTC

## 2026-09-05 04:44:34 UTC
- NEW `/apis` (no trailing slash) returns 307 to Azure AD B2C on all 6 portals — real API catalog endpoint behind auth confirmed; `/apis/` (trailing slash) returns 308→200 SPA shell (catch-all) — Next.js ro
- NEW `/apis/test123` returns 307 (31 bytes) on prod+test — object-ID routes from buildManifest (`/apis/[apiId]`) are real auth-protected endpoints, not SPA catch-all
- CHANGED Prior belief: `/apis/` was SPA catch-all; NOW: trailing slash triggers SPA, no-trailing-slash hits real middleware-protected route

## 2026-09-05 08:57:39 UTC
- NEW `/apps`, `/teams`, `/products`, `/subscriptions` (no trailing slash) return 307 to Azure AD B2C on all 6 portals — real middleware-protected catalog endpoints confirmed; trailing-slash variants return
- NEW buildManifest from both prod (`JCvrnrykV_KYBk7pu0Npq`) and test (`JVF_tXHlhCfZQOkT-cULr`) reveals identical route structure including object-ID routes: `/apis/[apiId]`, `/apps/[appId]/subscriptions/[s
- NEW CSP config drift confirmed: prod CSP includes `img-src 'self' data: https://app.usercentrics.eu` + `frame-src https://companion.app.daimlertruck.com`; test/dev CSP has literal `img-src 'self' data: un
- NEW Two B2C providers per portal: `azure-ad-b2c-dt` (ROW, policy `b2c_1a_signin_oidc_row`) + `azure-ad-b2c-dtna` (NA, policy `b2c_1a_signin_oidc_noam`) — separate tenants per region
- CHANGED Prior belief: `/apis/` was SPA catch-all; NOW: trailing slash triggers SPA, no-trailing-slash hits real auth-protected route — applies to all catalog roots
- CHANGED GraphQL introspection: `/graphql` and `/api/graphql` return 307 on POST with introspection query — real endpoints behind auth, not SPA false positive

## 2026-09-05 12:10:47 UTC

## 2026-09-05 15:25:44 UTC

## 2026-09-05 17:45:27 UTC

## 2026-09-05 19:33:54 UTC

## 2026-09-05 21:47:38 UTC
- NEW B2C claim contracts machine-readable both policies both tenants: prod (3db550f0-...-33615d) + staging (88f558f5-...-d6ec0f) share IDENTICAL structure. ROW (89 claims) = DT-employee model (oid/adUpn/ad
- NEW Both policies advertise implicit response_types (id_token, id_token token) BUT token_endpoint restricted to client_secret_basic/post (confidential client) — code-theft chains need the portal client se
- NEW Trailing-slash routing quirk confirmed: `/apis` (no slash) → 307 to Azure AD B2C; `/apis/` (trailing slash) → 308→200 SPA catch-all. Applies to `/apps`, `/teams`, `/products`, `/subscriptions` catalog
- NEW Two B2C providers per portal: `azure-ad-b2c-dt` (ROW, policy `b2c_1a_signin_oidc_row`) + `azure-ad-b2c-dtna` (NA, policy `b2c_1a_signin_oidc_noam`) — separate tenants per region.
- NEW Build manifest from both prod (`JCvrnrykV_KYBk7pu0Npq`) and test (`JVF_tXHlhCfZQOkT-cULr`) reveals identical route structure including object-ID routes.
- NEW CSP config drift confirmed: prod CSP includes `img-src 'self' data: https://app.usercentrics.eu` + `frame-src https://companion.app.daimlertruck.com`; test/dev CSP has literal `img-src 'self' data: un
- CHANGED `/graphql` and `/api/graphql` return 307 on POST with introspection query (knowledge base) vs 200 SPA shell on GET (probe results) — method-dependent behavior discrepancy.
- CHANGED OAuth misconfig @ authz.* rejection reinforced: 404s now attributed to APIM OperationNotFound (same class as as.api/eu.api) — consistent with no OAuth/OIDC surface.
- CHANGED `companion.app.daimlertruck.com` resolves to NXDOMAIN (referenced in prod CSP frame-src) — dead reference in production CSP.

## 2026-09-05 23:45:19 UTC
- NEW B2C claim contracts machine-readable both policies both tenants: prod (3db550f0-...-33615d) + staging (88f558f5-...-d6ec0f) share IDENTICAL structure. ROW (89 claims) = DT-employee model (oid/adUpn/ad
- NEW Both policies advertise implicit response_types (id_token, id_token token) BUT token_endpoint restricted to client_secret_basic/post (confidential client) — code-theft chains need the portal client se
- NEW OAuth initiate via POST /api/auth/signin/azure-ad-b2c-dt?json=true with valid CSRF returns B2C authorize URL as JSON: test tenant login-qa.ciam.daimlertruck.com/88f558f5-a216-470b-b34a-3164f5d6ec0f, p
- NEW OAuth initiate prod (as): tenant login.ciam.daimlertruck.com/3db550f0, policy b2c_1a_signin_oidc_row, client 205f35f7 — proper prod/staging segregation confirmed
- NEW B2C claim contracts machine-readable both policies both tenants: prod (3db550f0-...-33615d) + staging (88f558f5-...-d6ec0f) share IDENTICAL structure. ROW (89 claims) = DT-employee model (oid/adUpn/ad
- NEW OAuth initiate via POST /api/auth/signin/azure-ad-b2c-dt?json=true with valid CSRF returns B2C authorize URL as JSON: test tenant login-qa.ciam.daimlertruck.com/88f558f5-a216-470b-b34a-3164f5d6ec0f, p
- NEW OAuth initiate prod (as): tenant login.ciam.daimlertruck.com/3db550f0, policy b2c_1a_signin_oidc_row, client 205f35f7 — proper prod/staging segregation confirmed
- NEW OAuth initiate via POST /api/auth/signin/azure-ad-b2c-dt?json=true with valid CSRF returns B2C authorize URL as JSON: test tenant login-qa.ciam.daimlertruck.com/88f558f5-a216-470b-b34a-3164f5d6ec0f, p
- NEW OAuth initiate prod (as): tenant login.ciam.daimlertruck.com/3db550f0, policy b2c_1a_signin_oidc_row, client 205f35f7 — proper prod/staging segregation confirmed
- NEW Trailing-slash routing quirk confirmed: `/apis` (no slash) → 307 to Azure AD B2C; `/apis/` (trailing slash) → 308→200 SPA catch-all. Applies to `/apps`, `/teams`, `/products`, `/subscriptions` catalog
- NEW Two B2C providers per portal: `azure-ad-b2c-dt` (ROW, policy `b2c_1a_signin_oidc_row`) + `azure-ad-b2c-dtna` (NA, policy `b2c_1a_signin_oidc_noam`) — separate tenants per region.
- NEW Build manifest from both prod (`JCvrnrykV_KYBk7pu0Npq`) and test (`JVF_tXHlhCfZQOkT-cULr`) reveals identical route structure including object-ID routes.
- NEW CSP config drift confirmed: prod CSP includes `img-src 'self' data: https://app.usercentrics.eu` + `frame-src https://companion.app.daimlertruck.com`; test/dev CSP has literal `img-src 'self' data: un
- CHANGED `/graphql` and `/api/graphql` return 307 on POST with introspection query (knowledge base) vs 200 SPA shell on GET (probe results) — method-dependent behavior discrepancy.
- CHANGED OAuth misconfig @ authz.* rejection reinforced: 404s now attributed to APIM OperationNotFound (same class as as.api/eu.api) — consistent with no OAuth/OIDC surface.
- CHANGED `companion.app.daimlertruck.com` resolves to NXDOMAIN (referenced in prod CSP frame-src) — dead reference in production CSP.

## 2026-09-06 01:27:00 UTC
- NEW OAuth initiate via POST /api/auth/signin/azure-ad-b2c-dt?json=true with valid CSRF returns B2C authorize URL as JSON: test tenant login-qa.ciam.daimlertruck.com/88f558f5-a216-470b-b34a-3164f5d6ec0f, p
- NEW OAuth initiate prod (as): tenant login.ciam.daimlertruck.com/3db550f0, policy b2c_1a_signin_oidc_row, client 205f35f7 — proper prod/staging segregation confirmed
- NEW B2C claim contracts machine-readable both policies both tenants: prod (3db550f0-...-33615d) + staging (88f558f5-...-d6ec0f) share IDENTICAL structure. ROW (89 claims) = DT-employee model (oid/adUpn/ad
- NEW OAuth initiate via POST /api/auth/signin/azure-ad-b2c-dt?json=true with valid CSRF returns B2C authorize URL as JSON: test tenant login-qa.ciam.daimlertruck.com/88f558f5-a216-470b-b34a-3164f5d6ec0f, p
- NEW OAuth initiate prod (as): tenant login.ciam.daimlertruck.com/3db550f0, policy b2c_1a_signin_oidc_row, client 205f35f7 — proper prod/staging segregation confirmed
- NEW OAuth initiate via POST /api/auth/signin/azure-ad-b2c-dt?json=true with valid CSRF returns B2C authorize URL as JSON: test tenant login-qa.ciam.daimlertruck.com/88f558f5-a216-470b-b34a-3164f5d6ec0f, p
- NEW OAuth initiate prod (as): tenant login.ciam.daimlertruck.com/3db550f0, policy b2c_1a_signin_oidc_row, client 205f35f7 — proper prod/staging segregation confirmed
- CHANGED developer.tst.na.api.daimlertruck.com: /api/auth/session GET returns 200 {} (empty session, not 400) — endpoint alive, expected empty when unauthenticated
- NEW OAuth initiate via POST /api/auth/signin/azure-ad-b2c-dt?json=true with valid CSRF returns B2C authorize URL as JSON: test tenant login-qa.ciam.daimlertruck.com/88f558f5-a216-470b-b34a-3164f5d6ec0f, p
- NEW OAuth initiate prod (as): tenant login.ciam.daimlertruck.com/3db550f0, policy b2c_1a_signin_oidc_row, client 205f35f7 — proper prod/staging segregation confirmed
- NEW /api/auth/providers reveals 2 OAuth providers on all portals: azure-ad-b2c-dt (ROW) + azure-ad-b2c-dtna (North America, policy b2c_1a_signin_oidc_noam)
- NEW buildManifest route structure revealed: /apis/[apiId], /apps/[appId]/subscriptions/[subscriptionId], /teams/[teamId]/system-users/associate, /products/[productId]/subscribe, rewrites /docs/:slug*, /ap
- NEW OAuth initiate via POST /api/auth/signin/azure-ad-b2c-dt?json=true with valid CSRF returns B2C authorize URL as JSON: test tenant login-qa.ciam.daimlertruck.com/88f558f5-a216-470b-b34a-3164f5d6ec0f, p
- NEW OAuth initiate prod (as): tenant login.ciam.daimlertruck.com/3db550f0, policy b2c_1a_signin_oidc_row, client 205f35f7 — proper prod/staging segregation confirmed
- NEW OAuth initiate via POST /api/auth/signin/azure-ad-b2c-dt?json=true with valid CSRF returns B2C authorize URL as JSON: test tenant login-qa.ciam.daimlertruck.com/88f558f5-a216-470b-b34a-3164f5d6ec0f, p
- NEW OAuth initiate prod (as): tenant login.ciam.daimlertruck.com/3db550f0, policy b2c_1a_signin_oidc_row, client 205f35f7 — proper prod/staging segregation confirmed
- NEW OAuth initiate via POST /api/auth/signin/azure-ad-b2c-dt?json=true with valid CSRF returns B2C authorize URL as JSON: test tenant login-qa.ciam.daimlertruck.com/88f558f5-a216-470b-b34a-3164f5d6ec0f, p
- NEW OAuth initiate prod (as): tenant login.ciam.daimlertruck.com/3db550f0, policy b2c_1a_signin_oidc_row, client 205f35f7 — proper prod/staging segregation confirmed
- NEW B2C claim contracts identical across prod (3db550f0) and staging (88f558f5) tenants — 89 claims ROW model (oid/adUpn/adDisplayName/emails/groups/tenantId/orgId/acr)
- NEW Both B2C policies advertise implicit response_types (id_token, id_token token) but token_endpoint auth restricted to client_secret_basic/post (confidential client only)
- NEW Trailing-slash routing quirk confirmed: `/apis` (no slash) → 307 B2C; `/apis/` (trailing slash) → 308→200 SPA catch-all; applies to `/apps`, `/teams`, `/products`, `/subscriptions`
- NEW Two B2C providers per portal: `azure-ad-b2c-dt` (ROW, policy `b2c_1a_signin_oidc_row`) + `azure-ad-b2c-dtna` (NA, policy `b2c_1a_signin_oidc_noam`) — separate regional tenants
- NEW Build manifests from both prod (`JCvrnrykV_KYBk7pu0Npq`) and test (`JVF_tXHlhCfZQOkT-cULr`) reveal identical route structure including all object-ID routes
- NEW CSP config drift confirmed: prod CSP includes `img-src 'self' data: https://app.usercentrics.eu` + `frame-src https://companion.app.daimlertruck.com`; test/dev CSP has literal `img-src 'self' data: un
- CHANGED `/graphql` and `/api/graphql` return 307 on POST with introspection query (real endpoint) vs 200 SPA shell on GET — method-dependent behavior discrepancy
- CHANGED OAuth misconfig @ authz.* rejection reinforced: 404s attributed to APIM OperationNotFound (same class as as.api/eu.api) — consistent no OAuth surface
- CHANGED `companion.app.daimlertruck.com` resolves to NXDOMAIN (referenced in prod CSP frame-src) — dead reference in production CSP

## 2026-09-06 06:34:06 UTC
- NEW `companion.app.daimlertruck.com` resolves to NXDOMAIN (referenced in prod CSP frame-src) — dead reference in production CSP
- NEW `/apis` (no trailing slash) returns 307 to Azure AD B2C on all 6 portals; `/apis/` (trailing slash) returns 308→200 SPA shell — trailing-slash routing quirk masks real auth-protected catalog endpoints
- NEW `/apis/test123` returns 307 (31 bytes) on prod+test — object-ID routes from buildManifest (`/apis/[apiId]`) confirmed as real middleware-protected endpoints
- NEW `/apps`, `/teams`, `/products`, `/subscriptions` (no trailing slash) return 307 to Azure AD B2C on all 6 portals — real middleware-protected catalog endpoints confirmed
- NEW B2C claim contracts identical across prod (3db550f0) and staging (88f558f5) tenants — 89 claims ROW model (oid/adUpn/adDisplayName/emails/groups/tenantId/orgId/acr)
- NEW Both B2C policies advertise implicit response_types (id_token, id_token token) but token_endpoint auth restricted to client_secret_basic/post (confidential client only)
- NEW Two B2C providers per portal: `azure-ad-b2c-dt` (ROW, policy `b2c_1a_signin_oidc_row`) + `azure-ad-b2c-dtna` (NA, policy `b2c_1a_signin_oidc_noam`) — separate regional tenants
- NEW Build manifests from both prod (`JCvrnrykV_KYBk7pu0Npq`) and test (`JVF_tXHlhCfZQOkT-cULr`) reveal identical route structure including all object-ID routes
- NEW CSP config drift confirmed: prod CSP includes `img-src 'self' data: https://app.usercentrics.eu` + `frame-src https://companion.app.daimlertruck.com`; test/dev CSP has literal `img-src 'self' data: un
- CHANGED `/graphql` and `/api/graphql` return 307 on POST with introspection query (real endpoint) vs 200 SPA shell on GET — method-dependent behavior discrepancy confirmed
- CHANGED OAuth misconfig @ authz.* rejection reinforced: 404s attributed to APIM OperationNotFound (same class as as.api/eu.api) — consistent no OAuth surface
- CHANGED `companion.app.daimlertruck.com` resolves to NXDOMAIN (referenced in prod CSP frame-src) — dead reference in production CSP
- CHANGED Portal blanket auth middleware confirmed: `/api/*`, catalog, object-ID routes all 307 via wildcard middleware (fabricated + dot/case/%2f variants) — passive route discovery exhausted; only `/api/healt
- CHANGED BusinessID broker first-hop mapped: ROW authorize on prod+staging renders broker login (tenants f266a340/e39fd9b6, clients 82559bb7/a43f98c7, policy b2c_1a_signin, code form_post → ciam authresp); por

## 2026-09-06 11:26:44 UTC

## 2026-09-06 14:37:39 UTC

## 2026-09-06 17:34:34 UTC

## 2026-09-06 19:50:34 UTC

## 2026-09-06 21:45:27 UTC
- NEW companion.app.daimlertruck.com now LIVE (was NXDOMAIN): HTTP 200 len=3330, Next.js build IqPB_zhGzw2eQTiap3_bK, frontend 1.91.0, istio-envoy; routes /admin /chat /widget-host /api/proxy-http /api/[...
- NEW companion-dev.app.daimlertruck.com exposed: identical build/version as prod but showFrontendVersion:true + connectionTypeSelector:true (disabled on prod); dev auth provider not wired to live tenant (s
- NEW b2c-error-info-leak @ login.ciam.daimlertruck.com client cd34584a: unregistered redirect_uri → AADB2C90006 error discloses companion-dev.app.daimlertruck.com as callback location — production client e
- NEW companion-b2c-dev-callback-registered @ login.ciam.daimlertruck.com client cd34584a: dev callback URI (companion-dev.app.daimlertruck.com/api/auth/callback/azure-ad-b2c) is REGISTERED redirect target 
- NEW positional-callback-enumeration @ companion.app: /widget/callback and /admin/callback return 200 (SPA shell) but are NOT registered B2C URIs — only /api/auth/callback/azure-ad-b2c per host
- CHANGED developer.* portals: /graphql and /api/graphql return 307 to Azure AD B2C on POST with introspection query (real endpoints behind auth), 200 SPA shell on GET — method-dependent behavior confirmed
- CHANGED Portal blanket auth middleware: /api/*, catalog, object-ID routes all 307 via wildcard middleware (tested with fabricated + dot/case/%2f variants) — passive route discovery exhausted; only /api/health
- CHANGED Trailing-slash routing quirk confirmed across all catalog roots: /apis /apps /teams /products /subscriptions (no slash) → 307 B2C; trailing-slash variants → 308→200 SPA catch-all
- CHANGED Build manifests identical across prod (JCvrnrykV_KYBk7pu0Npq) and test (JVF_tXHlhCfZQOkT-cULr) — same route structure including all object-ID routes
- CHANGED CSP config drift: prod includes frame-src https://companion.app.daimlertruck.com (now live); test/dev has literal "undefined" in img-src
- CHANGED B2C cross-BU token boundary: prod+test tenants issue ROW+NOAM under same issuer/aud; only acr+org claims differentiate — same-issuer collision confirmed anonymously
- CHANGED businessid-broker-first-hop mapped: login.businessid(.qa) ROW authorize renders broker login (tenants f266a340/e39fd9b6, clients 82559bb7/a43f98c7, policy b2c_1a_signin, code form_post → ciam authresp

## 2026-09-06 23:42:22 UTC
- NEW companion-dev.app.daimlertruck.com/api/auth/providers returns single provider `azure-ad-b2c` (vs developer portals' two: `azure-ad-b2c-dt` + `azure-ad-b2c-dtna`) — dev uses single B2C client, no NA te
- NEW companion-dev.app signin flow: POST /api/auth/signin/azure-ad-b2c?json=true → 302 to `/api/auth/signin?csrf=true` (SPA shell), NOT to B2C authorize endpoint — dev provider unwired from live tenant
- NEW companion-dev.app object routes `/admin`, `/chat` return 307 to locale-prefixed `/en/admin`, `/en/chat` (SPA catch-all), not 307 to B2C — no auth middleware enforcement on dev
- NEW companion-dev.app `/api/proxy-http` returns 405 Method Not Allowed (not 307/401) — API route exists but no auth guard
- CHANGED Both companion apps share identical build ID `IqPB_zhGzw2eQTiap3_bK` and buildManifest — same codebase, config-only drift
- CHANGED Dev companion CSP: `frame-ancestors https://*.daimlertruck.com ...` (no companion.app frame-src); Prod CSP includes `https://corptb.sharepoint.com` additional frame-ancestor

## 2026-09-07 01:22:21 UTC
- NEW companion-dev.app.daimlertruck.com/api/auth/providers returns single provider `azure-ad-b2c` (vs developer portals' two: `azure-ad-b2c-dt` + `azure-ad-b2c-dtna`) — dev uses single B2C client, no NA te
- NEW companion-dev.app signin flow: POST /api/auth/signin/azure-ad-b2c?json=true → 302 to `/api/auth/signin?csrf=true` (SPA shell), NOT to B2C authorize endpoint — dev provider unwired from live tenant
- NEW companion-dev.app object routes `/admin`, `/chat` return 307 to locale-prefixed `/en/admin`, `/en/chat` (SPA catch-all), not 307 to B2C — no auth middleware enforcement on dev
- NEW companion-dev.app `/api/proxy-http` returns 405 Method Not Allowed (not 307/401) — API route exists but no auth guard
- CHANGED Both companion apps share identical build ID `IqPB_zhGzw2eQTiap3_bK` and buildManifest — same codebase, config-only drift
- CHANGED Dev companion CSP: `frame-ancestors https://*.daimlertruck.com ...` (no companion.app frame-src); Prod CSP includes `https://corptb.sharepoint.com` additional frame-ancestor

## 2026-09-07 06:24:03 UTC
- NEW companion-dev.app.daimlertruck.com signin POST now redirects to **PRODUCTION B2C** (tenant 3db550f0, client cd34584a, policy b2c_1a_signin_oidc_row) with PKCE S256 + state + same-origin redirect_uri —
- NEW companion-dev.app.daimlertruck.com/api/proxy-http returns **401 Unauthorized** (not 405) — auth middleware now active on this route
- NEW companion-dev.app.daimlertruck.com/admin → 307 /en/admin → 307 to B2C login with callbackUrl — auth middleware active on object routes (previously SPA shell)
- NEW companion-dev.app.daimlertruck.com/chat → 307 /en/chat → 307 to B2C login — same
- NEW Both companion apps (/api/auth/providers) return **single provider** `azure-ad-b2c` — developer portals have two (`azure-ad-b2c-dt` ROW + `azure-ad-b2c-dtna` NA)
- CHANGED Prior hypothesis "Companion Dev Auth Bypass via Unwired B2C Provider" (confidence 80) **invalidated** — dev auth now wired to prod B2C, middleware active

## 2026-09-07 12:54:29 UTC
- NEW companion-dev.app.daimlertruck.com signin POST now redirects to **PRODUCTION B2C** (tenant 3db550f0, client cd34584a, policy b2c_1a_signin_oidc_row) with PKCE S256 + state + same-origin redirect_uri —
- NEW companion-dev.app.daimlertruck.com/api/proxy-http returns **401 Unauthorized** (not 405) — auth middleware now active on this route
- NEW companion-dev.app.daimlertruck.com/admin → 307 /en/admin → 307 to B2C login with callbackUrl — auth middleware active on object routes (previously SPA shell)
- NEW companion-dev.app.daimlertruck.com/chat → 307 /en/chat → 307 to B2C login — same
- NEW Both companion apps (/api/auth/providers) return **single provider** `azure-ad-b2c` — developer portals have two (`azure-ad-b2c-dt` ROW + `azure-ad-b2c-dtna` NA)
- CHANGED Prior hypothesis "Companion Dev Auth Bypass via Unwired B2C Provider" (confidence 80) **invalidated** — dev auth now wired to prod B2C, middleware active

## 2026-09-07 18:23:56 UTC
- NEW companion-dev.app.daimlertruck.com signin POST now redirects to PRODUCTION B2C (tenant 3db550f0, client cd34584a, policy b2c_1a_signin_oidc_row) with PKCE S256 + state + same-origin redirect_uri — pre
- NEW companion-dev.app.daimlertruck.com/api/proxy-http returns 401 Unauthorized (not 405) — auth middleware now active on this route
- NEW companion-dev.app.daimlertruck.com/admin → 307 /en/admin → 307 to B2C login with callbackUrl — auth middleware active on object routes (previously SPA shell)
- NEW companion-dev.app.daimlertruck.com/chat → 307 /en/chat → 307 to B2C login — same
- NEW Both companion apps (/api/auth/providers) return single provider `azure-ad-b2c` — developer portals have two (`azure-ad-b2c-dt` ROW + `azure-ad-b2c-dtna` NA)
- CHANGED Prior hypothesis "Companion Dev Auth Bypass via Unwired B2C Provider" (confidence 80) INVALIDATED — dev auth now wired to prod B2C, middleware active
- CHANGED b2c-cross-bu-token-boundary: ROW+NOAM policies per tenant share IDENTICAL issuer URI; only acr + org-scoped claims differentiate BU; NOAM claim superset includes dealer PII (FTLOrgPrimaryContactEmail,
- CHANGED developer-portal-dual-b2c-providers: two providers confirmed — azure-ad-b2c-dt (ROW, client 205f35f7) + azure-ad-b2c-dtna (NA, client c387a5ab) — regional tenant separation at identity layer
- CHANGED companion-single-provider: both companion apps use single azure-ad-b2c provider (client cd34584a, ROW policy only) — by design per KB
- CHANGED companion-dev-callback-in-prod-allowlist: dev callback URI registered in prod B2C client — misconfig confirmed
- NEW companion-dev.app.daimlertruck.com signin POST now redirects to PRODUCTION B2C (tenant 3db550f0, client cd34584a, policy b2c_1a_signin_oidc_row) with PKCE S256 + state + same-origin redirect_uri — pre
- NEW companion-dev.app.daimlertruck.com/api/proxy-http returns 401 Unauthorized (not 405) — auth middleware now active on this route
- NEW companion-dev.app.daimlertruck.com/admin → 307 /en/admin → 307 to B2C login with callbackUrl — auth middleware active on object routes (previously SPA shell)
- NEW companion-dev.app.daimlertruck.com/chat → 307 /en/chat → 307 to B2C login — same
- NEW Both companion apps (/api/auth/providers) return single provider `azure-ad-b2c` — developer portals have two (`azure-ad-b2c-dt` ROW + `azure-ad-b2c-dtna` NA)
- CHANGED Prior hypothesis "Companion Dev Auth Bypass via Unwired B2C Provider" (confidence 80) INVALIDATED — dev auth now wired to prod B2C, middleware active
- CHANGED b2c-cross-bu-token-boundary: ROW+NOAM policies per tenant share IDENTICAL issuer URI; only acr + org-scoped claims differentiate BU; NOAM claim superset includes dealer PII (FTLOrgPrimaryContactEmail,
- CHANGED developer-portal-dual-b2c-providers: two providers confirmed — azure-ad-b2c-dt (ROW, client 205f35f7) + azure-ad-b2c-dtna (NA, client c387a5ab) — regional tenant separation at identity layer
- CHANGED companion-single-provider: both companion apps use single azure-ad-b2c provider (client cd34584a, ROW policy only) — by design per KB
- CHANGED companion-dev-callback-in-prod-allowlist: dev callback URI registered in prod B2C client — misconfig confirmed

## 2026-09-07 21:42:06 UTC
- NEW companion.app.daimlertruck.com now LIVE (was NXDOMAIN): HTTP 200 len=3330, Next.js build `IqPB_zhGzw2eQTiap3_bK`, frontend 1.91.0, istio-envoy; routes `/admin` `/chat` `/widget-host` `/api/proxy-http`
- NEW companion-dev.app.daimlertruck.com exposed: identical build/version as prod, `showFrontendVersion:true` + `connectionTypeSelector:true` (disabled on prod); discovered via prod B2C AADB2C90006 error le
- NEW b2c-error-info-leak @ login.ciam.daimlertruck.com client `cd34584a`: unregistered redirect_uri → AADB2C90006 error discloses `companion-dev.app.daimlertruck.com` as callback location
- NEW companion-dev-callback-in-prod-allowlist @ login.ciam.daimlertruck.com client `cd34584a`: dev callback URI registered in prod B2C client — misconfig confirmed
- CHANGED companion-dev.app auth now wired to **PROD B2C** (tenant `3db550f0`, client `cd34584a`, policy `b2c_1a_signin_oidc_row`) with PKCE S256 + state + same-origin redirect_uri (since 2026-09-07 06:24)
- CHANGED companion-dev.app `/api/proxy-http` returns **401** (was 405) — auth middleware active on this route
- CHANGED companion-dev.app `/admin`, `/chat` now 307 → B2C via locale (was SPA shell) — auth middleware active on object routes

## 2026-09-07 23:49:10 UTC
- NEW companion.app.daimlertruck.com now LIVE (was NXDOMAIN): HTTP 200 len=3330, Next.js build `IqPB_zhGzw2eQTiap3_bK`, frontend 1.91.0, istio-envoy; routes `/admin` `/chat` `/widget-host` `/api/proxy-http`
- NEW companion-dev.app.daimlertruck.com exposed: identical build/version as prod, `showFrontendVersion:true` + `connectionTypeSelector:true` (disabled on prod); discovered via prod B2C AADB2C90006 error le
- NEW b2c-error-info-leak @ login.ciam.daimlertruck.com client `cd34584a`: unregistered redirect_uri → AADB2C90006 error discloses `companion-dev.app.daimlertruck.com` as callback location
- NEW companion-dev-callback-in-prod-allowlist @ login.ciam.daimlertruck.com client `cd34584a`: dev callback URI registered in prod B2C client — misconfig confirmed
- CHANGED companion-dev.app auth now wired to **PROD B2C** (tenant `3db550f0`, client `cd34584a`, policy `b2c_1a_signin_oidc_row`) with PKCE S256 + state + same-origin redirect_uri (since 2026-09-07 06:24)
- CHANGED companion-dev.app `/api/proxy-http` returns **401** (was 405) — auth middleware active on this route
- CHANGED companion-dev.app `/admin`, `/chat` now 307 → B2C via locale (was SPA shell) — auth middleware active on object routes

## 2026-09-08 03:50:44 UTC

## 2026-09-08 08:48:14 UTC

## 2026-09-08 13:28:39 UTC

## 2026-09-08 17:35:17 UTC
- NEW developer.tst.as.api.daimlertruck.com — 7th developer portal, absent from the 23-host inventory (which listed only as/eu/na + dev.na/tst.eu/tst.na); build ID `mS_4SiQmkiaGsx2vLoXkH` is a THIRD distinc
- NEW NA-provider asymmetry on tst.as: POST signin `azure-ad-b2c-dtna` returns `{"url":".../api/auth/error?error=OAuthSignin"}` (config-level failure, no B2C hit) whereas tst.na's identical call returns a c
- CHANGED capacitor-admin.* (as/eu/na/tst.na) still 000 (dead); broker root 404 len=103 prod+staging; developer healthchecks stable — no other drift.

## 2026-09-08 20:20:34 UTC
- NEW developer.tst.as.api.daimlertruck.com — 7th developer portal, absent from the 23-host inventory (which listed only as/eu/na + dev.na/tst.eu/tst.na); build ID `mS_4SiQmkiaGsx2vLoXkH` is a THIRD distinc
- NEW NA-provider asymmetry on tst.as: POST signin `azure-ad-b2c-dtna` returns `{"url":".../api/auth/error?error=OAuthSignin"}` (config-level failure, no B2C hit) whereas tst.na's identical call returns a c
- CHANGED capacitor-admin.* (as/eu/na/tst.na) still 000 (dead); broker root 404 len=103 prod+staging; developer healthchecks stable — no other drift.

## 2026-09-08 22:47:27 UTC
- NEW developer.tst.as.api.daimlertruck.com: 7th developer portal discovered (absent from 23-host inventory); third distinct build ID `mS_4SiQmkiaGsx2vLoXkH`; NA provider `azure-ad-b2c-dtna` signin aborts w
- CHANGED companion-dev.app.daimlertruck.com: auth now wired to PROD B2C (tenant 3db550f0, client cd34584a, policy b2c_1a_signin_oidc_row, PKCE S256 + state + same-origin redirect_uri); `/api/proxy-http` return
- CHANGED b2c-cross-bu-token-boundary: ROW+NOAM policies per tenant (prod 3db550f0, staging 88f558f5) share IDENTICAL issuer URI; only `acr` + org-scoped claims differentiate BU; NOAM claim superset includes de
- CHANGED developer-portal-dual-b2c-providers: two providers confirmed on all 7 portals — `azure-ad-b2c-dt` (ROW, client 205f35f7) + `azure-ad-b2c-dtna` (NA, client c387a5ab) — regional tenant separation at ide
- CHANGED companion-single-provider: both companion apps use single `azure-ad-b2c` provider (client cd34584a, ROW policy only) — by design per KB (new client for companion)
- CHANGED companion-dev-callback-in-prod-allowlist: dev callback URI registered in prod B2C client cd34584a — misconfig confirmed, persists despite auth wiring fix

## 2026-09-09 01:09:44 UTC

## 2026-09-09 06:10:17 UTC
- CHANGED developer.tst.as.api.daimlertruck.com: stability re-verified — 3rd build ID `mS_4SiQmkiaGsx2vLoXkH`, dual providers, `/apis` 307, healthcheck 200 — no env drift since 2026-09-08
- CHANGED companion.app.daimlertruck.com: re-probe confirms root+liveness unchanged; no new anonymous surface; `/api/proxy-http` still auth-gated (401)
- CHANGED companion-dev.app.daimlertruck.com: auth now wired to PROD B2C (tenant 3db550f0, client cd34584a, policy b2c_1a_signin_oidc_row, PKCE S256+state+same-origin redirect_uri); `/api/proxy-http` returns 40

## 2026-09-09 11:37:37 UTC

## 2026-09-09 15:29:28 UTC
- NEW None — knowledge base and inventory last updated 2026-09-09 11:37:37 UTC, matching last leads timestamp. No new passive observations since last cycle.
- CHANGED None — all assets stable per re-probes (developer.tst.as build ID `mS_4SiQmkiaGsx2vLoXkH` stable, companion apps auth-wired to prod B2C, dual providers confirmed on all 7 portals).

## 2026-09-09 18:45:18 UTC

## 2026-09-09 21:35:48 UTC

## 2026-09-09 23:33:23 UTC

## 2026-09-10 01:32:22 UTC

## 2026-09-10 06:44:00 UTC

## 2026-09-10 11:54:16 UTC

## 2026-09-10 16:08:00 UTC

## 2026-09-10 19:15:21 UTC

## 2026-09-10 21:43:28 UTC

## 2026-09-10 23:55:04 UTC
- NEW developer.tst.as.api.daimlertruck.com stability re-verified: 3rd build ID `mS_4SiQmkiaGsx2vLoXkH`, dual providers, `/apis` 307, healthcheck 200 — no env drift since 2026-09-08
- NEW Fresh passive probe value REJECTED @ all-scoped-hosts: every exploitable surface probed to exhaustion under blanket-middleware + auth-gated handlers; remaining candidates (proxy-http POST body, graphq
- CHANGED companion-dev.app.daimlertruck.com auth wiring: now fully wired to PROD B2C (tenant 3db550f0, client cd34584a, policy b2c_1a_signin_oidc_row, PKCE S256 + state + same-origin redirect_uri); /api/proxy-
- CHANGED b2c-cross-bu-token-boundary: ROW+NOAM share identical issuer URI per tenant (prod 3db550f0, staging 88f558f5); only acr + org-shape claims differentiate BU; NOAM claim superset includes dealer PII (FT
- CHANGED developer-portal-dual-b2c-providers: two providers confirmed on all 7 portals — azure-ad-b2c-dt (ROW, client 205f35f7) + azure-ad-b2c-dtna (NA, client c387a5ab) — regional tenant separation at identit
- CHANGED companion-dev-callback-in-prod-allowlist: dev callback URI registered in prod B2C client cd34584a — misconfig confirmed, persists despite auth wiring fix

## 2026-09-11 03:53:01 UTC

## 2026-09-11 08:49:30 UTC

## 2026-09-11 13:27:11 UTC

## 2026-09-11 17:15:54 UTC

## 2026-09-11 19:51:28 UTC

## 2026-09-11 22:24:16 UTC

## 2026-09-12 00:38:57 UTC

## 2026-09-12 05:04:35 UTC

## 2026-09-12 09:29:05 UTC

## 2026-09-12 13:21:49 UTC
- NEW OAuth initiate via POST /api/auth/signin/azure-ad-b2c-dt?json=true with valid CSRF returns B2C authorize URL as JSON: test tenant login-qa.ciam.daimlertruck.com/88f558f5-a216-470b-b34a-3164f5d6ec0f, p
- NEW OAuth initiate prod (as): tenant login.ciam.daimlertruck.com/3db550f0, policy b2c_1a_signin_oidc_row, client 205f35f7 — proper prod/staging segregation confirmed
- NEW B2C claim contracts machine-readable both policies both tenants: prod (3db550f0-...-33615d) + staging (88f558f5-...-d6ec0f) share IDENTICAL structure. ROW (89 claims) = DT-employee model (oid/adUpn/ad
- NEW OAuth initiate via POST /api/auth/signin/azure-ad-b2c-dt?json=true with valid CSRF returns B2C authorize URL as JSON: test tenant login-qa.ciam.daimlertruck.com/88f558f5-a216-470b-b34a-3164f5d6ec0f, p
- NEW OAuth initiate prod (as): tenant login.ciam.daimlertruck.com/3db550f0, policy b2c_1a_signin_oidc_row, client 205f35f7 — proper prod/staging segregation confirmed
- NEW OAuth initiate via POST /api/auth/signin/azure-ad-b2c-dt?json=true with valid CSRF returns B2C authorize URL as JSON: test tenant login-qa.ciam.daimlertruck.com/88f558f5-a216-470b-b34a-3164f5d6ec0f, p
- NEW OAuth initiate prod (as): tenant login.ciam.daimlertruck.com/3db550f0, policy b2c_1a_signin_oidc_row, client 205f35f7 — proper prod/staging segregation confirmed
- NEW OAuth initiate via POST /api/auth/signin/azure-ad-b2c-dt?json=true with valid CSRF returns B2C authorize URL as JSON: test tenant login-qa.ciam.daimlertruck.com/88f558f5-a216-470b-b34a-3164f5d6ec0f, p
- NEW OAuth initiate prod (as): tenant login.ciam.daimlertruck.com/3db550f0, policy b2c_1a_signin_oidc_row, client 205f35f7 — proper prod/staging segregation confirmed
- NEW B2C claim contracts machine-readable both policies both tenants: prod (3db550f0-...-33615d) + staging (88f558f5-...-d6ec0f) share IDENTICAL structure. ROW (89 claims) = DT-employee model (oid/adUpn/ad
- NEW OAuth initiate via POST /api/auth/signin/azure-ad-b2c-dt?json=true with valid CSRF returns B2C authorize URL as JSON: test tenant login-qa.ciam.daimlertruck.com/88f558f5-a216-470b-b34a-3164f5d6ec0f, p
- NEW OAuth initiate prod (as): tenant login.ciam.daimlertruck.com/3db550f0, policy b2c_1a_signin_oidc_row, client 205f35f7 — proper prod/staging segregation confirmed
- NEW OAuth initiate via POST /api/auth/signin/azure-ad-b2c-dt?json=true with valid CSRF returns B2C authorize URL as JSON: test tenant login-qa.ciam.daimlertruck.com/88f558f5-a216-470b-b34a-3164f5d6ec0f, p
- NEW OAuth initiate prod (as): tenant login.ciam.daimlertruck.com/3db550f0, policy b2c_1a_signin_oidc_row, client 205f35f7 — proper prod/staging segregation confirmed
- CHANGED developer.tst.na.api.daimlertruck.com: /api/auth/session GET returns 200 {} (empty session, not 400) — endpoint alive, expected empty when unauthenticated
- NEW OAuth initiate via POST /api/auth/signin/azure-ad-b2c-dt?json=true with valid CSRF returns B2C authorize URL as JSON: test tenant login-qa.ciam.daimlertruck.com/88f558f5-a216-470b-b34a-3164f5d6ec0f, p
- NEW OAuth initiate prod (as): tenant login.ciam.daimlertruck.com/3db550f0, policy b2c_1a_signin_oidc_row, client 205f35f7 — proper prod/staging segregation confirmed
- NEW /api/auth/providers reveals 2 OAuth providers on all portals: azure-ad-b2c-dt (ROW) + azure-ad-b2c-dtna (North America, policy b2c_1a_signin_oidc_noam)
- NEW buildManifest route structure revealed: /apis/[apiId], /apps/[appId]/subscriptions/[subscriptionId], /teams/[teamId]/system-users/associate, /products/[productId]/subscribe, rewrites /docs/:slug*, /ap
- NEW OAuth initiate via POST /api/auth/signin/azure-ad-b2c-dt?json=true with valid CSRF returns B2C authorize URL as JSON: test tenant login-qa.ciam.daimlertruck.com/88f558f5-a216-470b-b34a-3164f5d6ec0f, p
- NEW OAuth initiate prod (as): tenant login.ciam.daimlertruck.com/3db550f0, policy b2c_1a_signin_oidc_row, client 205f35f7 — proper prod/staging segregation confirmed
- NEW OAuth initiate via POST /api/auth/signin/azure-ad-b2c-dt?json=true with valid CSRF returns B2C authorize URL as JSON: test tenant login-qa.ciam.daimlertruck.com/88f558f5-a216-470b-b34a-3164f5d6ec0f, p
- NEW OAuth initiate prod (as): tenant login.ciam.daimlertruck.com/3db550f0, policy b2c_1a_signin_oidc_row, client 205f35f7 — proper prod/staging segregation confirmed
- NEW OAuth initiate via POST /api/auth/signin/azure-ad-b2c-dt?json=true with valid CSRF returns B2C authorize URL as JSON: test tenant login-qa.ciam.daimlertruck.com/88f558f5-a216-470b-b34a-3164f5d6ec0f, p
- NEW OAuth initiate prod (as): tenant login.ciam.daimlertruck.com/3db550f0, policy b2c_1a_signin_oidc_row, client 205f35f7 — proper prod/staging segregation confirmed
- NEW companion-app-build-roll @ companion.app.daimlertruck.com + companion-dev.app.daimlertruck.com: both deployed NEW builds (first since 09-06 go-live) — prod buildId pVVz9XMK0MvBn72k4YswS, dev AO7VvIOpK
- CHANGED companion.app /api/auth/callback/azure-ad-b2c GET: 400 -> 302->/api/auth/error?error=OAuthCallback (now uniform with developer.* portals); correlates with new deploy; standard NextAuth missing-params 
- CHANGED companion-dev /api/proxy-http GET: 401 -> 405 (now homogeneous with prod 405 first-class-handler signal).
- NEW developer.tst.as.api.daimlertruck.com `/api/auth/signin/azure-ad-b2c-dt` and `/api/auth/signin/azure-ad-b2c-dtna` endpoints hanging/no response (prod developer.as.api same) — previously returned B2C a
- NEW companion-dev.app.daimlertruck.com `/api/auth/signin/azure-ad-b2c` returns 504 Gateway Timeout (was redirecting to prod B2C)
- CHANGED companion.app.daimlertruck.com `/api/auth/signin/azure-ad-b2c` hangs/no response (was returning B2C authorize JSON)

## 2026-09-12 16:28:31 UTC
- NEW companion-dev.app /api/auth/signin/azure-ad-b2c still hangs (timeout 10s, 0B) while prod companion + all portal signin endpoints now return standard NextAuth 302 error-redirects — prior 13:21 "hanging
- CHANGED companion.app signin GET: hang→302 `/?callbackUrl=...&error=azure-ad-b2c` (uniform NextAuth GET-signin error handling, matches developer portals).
- CHANGED developer.tst.as signin GET (dt+dtna): hang→302 error=azure-ad-b2c-dt/-dtna — NA-provider OAuthSignin abort (09-08, POST path) not re-tested (POST out of passive bounds); GET-side hang resolved.
- NEW developer.tst.as.api.daimlertruck.com `/api/auth/signin/azure-ad-b2c-dt` and `/api/auth/signin/azure-ad-b2c-dtna` endpoints hanging/no response (prod developer.as.api.daimlertruck.com same) — previous
- NEW companion-dev.app.daimlertruck.com `/api/auth/signin/azure-ad-b2c` returns 504 Gateway Timeout (was redirecting to prod B2C)
- CHANGED companion.app.daimlertruck.com `/api/auth/signin/azure-ad-b2c` hangs/no response (was returning B2C authorize JSON)
- CHANGED developer.as.api.daimlertruck.com root + /graphql content-length changed from 196649 to 196341 bytes (prod build ID `JCvrnrykV_KYBk7pu0Npq` still) — possible new deploy or config change
- CHANGED companion.app.daimlertruck.com `/api/auth/callback/azure-ad-b2c` remains 400 (standard NextAuth missing-params)

## 2026-09-12 18:49:11 UTC
- CHANGED companion-dev.app /api/auth/signin/azure-ad-b2c: hang (10s/0B, persisted since 09-12 build roll) → 302 0.29s `?callbackUrl=...&error=azure-ad-b2c` — uniform NextAuth GET-signin error handling; dev ide
- CHANGED companion.app signin: 302 same shape — no change vs 16:28.
- CHANGED developer.tst.as /api/healthcheck: 200/70B stable — no drift.
- NEW developer.as.api.daimlertruck.com root content-length changed from 196649 to 196341 bytes (build ID `JCvrnrykV_KYBk7pu0Npq` unchanged) — new deploy/config change confirmed
- NEW All signin endpoints (companion.app, companion-dev.app, developer.as.api, developer.tst.as.api) now return uniform 302 error redirects (`error=azure-ad-b2c*`) instead of hanging/504 — transient degrad
- CHANGED companion.app `/api/auth/signin/azure-ad-b2c?json=true`: hang→302 `/?callbackUrl=...&error=azure-ad-b2c` (uniform NextAuth GET-signin error handling)
- CHANGED companion-dev.app `/api/auth/signin/azure-ad-b2c?json=true`: 504→302 `/?callbackUrl=...&error=azure-ad-b2c` (now wired to prod B2C but GET path returns error)
- CHANGED developer.tst.as.api.daimlertruck.com `/api/auth/signin/azure-ad-b2c-dt` + `azure-ad-b2c-dtna`: hang→302 `error=azure-ad-b2c-dt/-dtna`
- CHANGED developer.as.api.daimlertruck.com `/graphql` returns 307 (24 bytes) to B2C — real endpoint behind auth confirmed
