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
