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

- 3 lead(s) marked VALID at 2026-09-11 21:15:32 UTC
  - | Q2 Reachable? | AUTH_HELPED | Requires valid Azure AD B2C session to access /graphql |
  - | Q4 Provable? | NO | Requires authenticated session with valid test account to test cross-tenant access |
  - | Q2 Reachable? | AUTH_HELPED | /api/auth/session returns 200 {} unauthenticated; needs valid session to analyze JWT |

- 4 lead(s) marked VALID at 2026-09-12 05:50:38 UTC
  - **Verdict: VALID**
  - **Verdict: VALID**
  - | 1 | B2C Error Info Leak (companion-dev disclosure) | **VALID** | 5.3 |
  - | 2 | Companion-Dev Exposed + CORS + Debug Features | **VALID** | 5.3–6.5 |

- 6 lead(s) marked VALID at 2026-09-14 07:56:45 UTC
  - | Q4 Provable non-invasively? | Partially — requires valid B2C session (AUTH_HELPED); authenticated introspection + mutation test with cross-tenant IDs is read-only (query introspection) + low-impact 
  - **Verdict: HOLD** — Cannot prove passively (requires valid B2C test account). Recommend obtaining 2 test accounts on `login-qa.ciam.daimlertruck.com` (ROW + NOAM policies) to complete authenticated pr
  - | Q4 Provable non-invasively? | Partially — requires valid B2C session (AUTH_HELPED); GET requests to object-ID routes with cross-tenant IDs are read-only. Cannot prove without auth account |
  - | Q3 Real impact? | Medium — dev environment registered as valid callback in prod B2C client. If dev environment is compromised or has weaker controls, attacker could leverage the valid callback for O
  - **Verdict: VALID**
  - | 6 | Dev Callback in Prod Allowlist | **VALID** | 5.3 | Report to program |
