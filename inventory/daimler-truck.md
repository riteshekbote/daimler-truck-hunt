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
