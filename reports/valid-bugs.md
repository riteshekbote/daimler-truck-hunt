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

- 5 lead(s) marked VALID at 2026-09-10 06:38:59 UTC
  - | Q3 Impact? | **Low-Medium** — dev environment could be used to obtain valid prod tokens via the registered callback; however, dev B2C signin now wired to prod B2C with PKCE S256 + state, reducing ex
  - | Q7 Reasonable triager? | **Borderline** — valid config issue but low direct impact due to PKCE + state + same-origin redirect_uri validation. Without evidence of actual exploitation path, triagers m
  - **Verdict: HOLD** — Valid misconfiguration but requires PoC showing actual token theft or cross-environment exploitation to be accepted. Recommend: attempt to obtain a prod token via companion-dev aut
  - | Q7 Reasonable triager? | **Yes** — if proof shows cross-BU token acceptance, this is a valid auth bypass. Requires AUTH_HELPED verification. |
  - | 3 | Companion-Dev Callback in Prod B2C | **HOLD** | Valid misconfig, needs exploitation PoC |

- 7 lead(s) marked VALID at 2026-09-11 18:35:36 UTC
  - **Verdict: VALID**
  - | Q7 | YES | Reasonable triager accepts allowlist hygiene as a valid misconfig finding |
  - **Verdict: VALID**
  - **Verdict: VALID**
  - | 1 | NextAuth.js Open Redirect (callbackUrl) | **VALID** | 6.1 |
  - | 3 | Dev Callback in Prod Allowlist | **VALID** | 4.0 |
  - | 4 | B2C Cross-BU Token Boundary | **VALID** | 5.3 |
