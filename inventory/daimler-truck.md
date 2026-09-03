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
