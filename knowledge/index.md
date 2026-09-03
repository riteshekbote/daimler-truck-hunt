# Knowledge Base (seed)
- 2026-09-03 ACCEPTED MISCONFIG @ developer.as.api.daimlertruck.com: HTTP 200 responses on developer portal strongly correlate with exposed API documentation.
- 2026-09-03 REJECTED IDOR @ authz.tst.eu.api.daimlertruck.com: 404 on root + staging environment = insufficient signal for IDOR hypothesis without path enumeration.
- 2026-09-03 REJECTED network DoS @ all assets: program explicitly excludes DoS/DDoS and account-lockout
- 2026-09-03 REJECTED SSL/TLS best practices @ www.daimlertruck.com: out of scope per policy
- 2026-09-03 REJECTED Clickjacking @ www.daimlertruck.com: requires demonstrated exploit per policy
- 2026-09-03 ACCEPTED GraphQL introspection @ developer.*: high-value, passive, in-scope (API logic flaw)
- 2026-09-03 ACCEPTED Admin panel discovery @ capacitor-admin.*: high-value, requires auth-helped verification
- 2026-09-03 ACCEPTED OAuth misconfig @ authz.*: high-value if endpoints exist, passive discovery first
