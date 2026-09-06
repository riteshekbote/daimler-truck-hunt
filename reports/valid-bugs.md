# Validated findings (running count 0)

- 2 lead(s) marked VALID at 2026-09-04 20:01:12 UTC
  - **Verdict: VALID**
  - | NextAuth.js Open Redirect | **VALID** | 6.1 | Report to bugs.olivermaicher.eu |

- 3 lead(s) marked VALID at 2026-09-06 09:05:03 UTC
  - | Q2 Attacker reachable? | **NO** — requires valid Azure AD B2C session (307 redirect confirmed) |
  - **Verdict: HOLD** — Requires AUTH_HELPED (valid B2C test account) to confirm. Cannot verify without authentication.
  - | Q2 Attacker reachable? | **NO** — requires valid session (307 redirect confirmed) |

- 4 lead(s) marked VALID at 2026-09-06 15:54:25 UTC
  - **Verdict: VALID**
  - **Verdict: HOLD** — Requires AUTH_HELPED (valid Azure AD B2C test account) to confirm. Cannot verify passively.
  - | Q2 Attacker reachable? | **NO** — requires valid Azure AD B2C session |
  - | 1 | NextAuth.js Open Redirect (callbackUrl) | **VALID** | 6.1 | None — ready to report |
