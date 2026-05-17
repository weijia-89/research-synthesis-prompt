# Output schemas, by stake tier

Load when: drafting report, formatting findings, deciding section depth.

## Stake-tier rigor floor

| Tier | Description | Min sections | Cite floor |
|---|---|---|---|
| L0 | Idle / curiosity | TL;DR | none required |
| L1 | Informational | TL;DR + Findings | T2 for load-bearing |
| L2 | Advisory | TL;DR + Findings + Limits | T2; T1 if claim contested |
| L3 | Decision-driving | Full schema below | T1 for load-bearing; ≥2 independent retrievals per |
| L4 | Irreversible | Full schema + worktree-style adversarial pass + many-analyst caveat | T1 only; pre-registered prediction logged |

## Full schema (L3+)

### 1. TL;DR
- One paragraph (≤4 lines).
- Headline confidence: numeric or `[T*-verified]` aggregated.
- Reversibility: revertable / costly-to-revert / irreversible.
- Stakes tier (L0–L4).

### 2. Pre-registered prediction → finding diff
- What I predicted at P1 + confidence.
- What I found.
- Diff. If finding ≠ prediction by >1 confidence band, name what changed my mind.

### 3. Findings (tagged, sourced)
- One claim per row or short bullet.
- Each claim tagged: `[T1-verified, TAG]` / `[T2-verified, TAG]` / `[inferred:basis]` / `[priors-only]` / `[unknown]` / etc.
- Numeric confidence only for decision-changing claims.
- Effect sizes + CI for empirical claims.

### 4. Limits / threats to validity
- Sample / scope / generalization limits.
- Bias scan results from P3 (cognitive + research-design + LLM-instrument).
- Quine–Duhem: which auxiliary assumptions are also under test.
- Industry / funder COI flagged where present.
- "What this skill cannot do here", explicit.

### 5. What would change my mind
- Operational: name the evidence type + magnitude.
- "If a preregistered RCT with n>200 found null, I would shift from .7 to .3 confidence."
- Avoid epistemic cowardice ("I would update if I learned more").

### 6. Open questions / next avenues
- Specific, actionable, with proposed next tool / source.

### 7. Sources
- Pull from `REFERENCES.md`. Tag-shorthand → row reference.
- Verified date column.
- Tier per source.

### 8. Self-instrument check (mandatory, may be terse)
- Sycophancy: pass / flag.
- Anchoring: pass / flag.
- Retrieval coverage: every `[T*-verified]` claim has URL retrieved this session: yes / no.
- Self-consistency (≥2 passes for L3): pass / flag.
- Many-analyst caveat (single-pass at L3+): disclosed.

## Adapter, host-shape conversion

Reshape per host without dropping load-bearing sections:

| Host | Adaptation |
|---|---|
| `CLAUDE.md` repo contract | Tags inline; sections collapsed if host requires; never drop tags. |
| `AGENTS.md` | Same. |
| `.cursorrules` | Brevity-first; full report → `localonly/<topic>-research.md`, summary in chat. |
| Plain user chat | Sections elided when stakes ≤ L2; tags retained on load-bearing claims. |

**Invariant:** epistemic tags survive every shape transformation. Drop sections, never tags.

## Length discipline

| Tier | Target length |
|---|---|
| L0–L1 | ≤300 words |
| L2 | ≤800 words |
| L3 | ≤2000 words; longer only if user requests |
| L4 | ≤4000 words; if longer needed → file artifact, not chat |

Budget is for *output*. Process artifacts (scratch, ledger, audit trail) live in `localonly/` without word limits.

## Style invariants (always)

- Bullets / tables > prose. One idea per line. Fragments OK.
- No transitions ("additionally," "furthermore," "in summary").
- No hedging without new info. No prompt restatement.
- Numbers cite source. Quotes verbatim or `paraphrase:`.
- Cut any line adding no information.

## Worked output examples

See `references/examples.md` for one good and one bad full report.
