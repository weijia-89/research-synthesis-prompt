
name: palamedes

description: Rigorous research / analysis / fact-check / literature-review loop for empirical, conceptual, predictive, normative, investigative, comparative, methodological, synthetic questions. Tier-graded sources, zero-fabrication citations, replication-aware, LLM-as-instrument calibrated, multi-pass, dialectic. Triggers research, investigate, analyze, validate, compare, deep-dive, second-opinion, audit, fact-check, literature-review, lit-review. 

Mergesplit trigger: "incorporate" → run loop on incoming paste, then merge.


# palamedes

Loop is process. Output is product. Both are auditable artifacts. Reasoning may be quiet but must be **written somewhere** the user can inspect (in-message tags, scratch file, or report). Silent ≠ undocumented.

## Definitions (referenced throughout)

- **Load-bearing claim** = a claim such that, if removed or flipped, the report's conclusion or recommendation changes. Non-load-bearing claims are flavor / context / hedging and do not require citation discipline.
- **Decision-changing** = a confidence shift or claim change that would route the user to a different action.
- **Stakes ladder L0–L4** = severity of acting on the output. See §0.
- **Read-depth** = how thoroughly a source was consulted: `read:full` (cover-to-cover or relevant chapter end-to-end) > `read:body` (relevant section / methods + results read) > `read:abstract` (abstract + skim). `read:title` (metadata only) is **not a valid depth for a verified tag**; it implies `[priors-only, title-checked]` and the claim must be tagged `[priors-only]`.
- **Independent retrieval** = different source, different query, OR different tool. Two passes of the same query against the same source ≠ independent.
- **Mode collapse** = N passes converge because they share priors / context / source-set, not because of independent confirmation.

## 0. Invocation preamble (1 line, mandatory at session start)

First output line of any session that triggers this skill:

```
palamedes v3 engaged · type=<empirical|conceptual|predictive|normative|investigative|comparative|methodological|synthetic> · stakes=<L0|L1|L2|L3|L4> · budget=<tools-allowed>
```

Forces commitment to type + stakes routing. The user (and you) can audit the routing. Skip preamble = skill not invoked.

### Stakes-recognition heuristic (independent of user framing)

The user may understate stakes. The skill must auto-classify:

| Stakes | Triggers (any) |
|---|---|
| L4, irreversible | Health / dosage / contraindication. Legal advice with statute-of-limitations or filing-deadline implication. Financial advice on positions >% of net worth. Security: incident response, key rotation, prod migration. Code change with `delete`, `drop`, `migrate`, `rotate`, `revoke`, `production`. |
| L3, decision-driving | Hiring / firing. Architecture choice. Vendor / library lock-in. Policy / procedure change. Public statement. |
| L2, advisory | Memo / recommendation under review. Personal-stakes choice (job offer, lease, etc.). Strategy debate. |
| L1, informational | "What is X?" / "How does Y work?" with no immediate decision. |
| L0, idle | Curiosity, brainstorm, vibes. Skip the loop, offer bypass. |

When user framing and auto-classification disagree, **use higher**. Disclose: "User framed as L1; classified L3 due to [trigger]. Applying L3 floor."

### When NOT to invoke this skill (refusal conditions)

- L0 idle / brainstorm, offer bypass: "Loop suppresses generativity; reply unstructured?"
- One-line factual recall the user can verify in 5 seconds, answer directly with `[priors-only]` tag.
- Question fits a more specific skill: `review-rigor` (code review), `epistemic-planning` (multi-step plan), `vibe-check` (AI-PR detection), `systematic-debugging` (bug investigation). Defer.
- Pure creative / generative task (story, brainstorm, naming). Loop deforms generativity.
- User explicitly asks for vibes / quick / no-rigor mode.

## 0.1 Question-type routing

| Type | Example | Rigor anchor |
|---|---|---|
| Empirical | "does X cause Y?" / "is X effective?" | `references/replication-and-validity.md` + `references/causal-inference.md` |
| Conceptual | "what does X mean?" / "is X a Y?" | `references/source-grading.md` + define-then-test |
| Predictive | "what will X do?" / "how likely?" | `references/confidence-calibration.md` + reference-class |
| Normative | "should we X?" | values disclosed + alternatives + cost asymmetry |
| Investigative | "why did X happen?" | flow-of-evidence + counterfactuals (`review-rigor` flow-map) |
| Comparative | "X vs. Y" | criteria pre-stated + symmetric scrutiny + `references/bias-catalog.md` (selective skepticism) |
| Methodological | "how should I research X?" | this skill, recursively, on the meta-question |
| Synthetic | "summarize the literature on X" | `references/replication-and-validity.md` + structured-search + per-paper effect-size |

If type unclear → ask once, then commit. Type drives stop conditions.

### False-premise guard

If the question contains a load-bearing premise the skill cannot verify ("why does X cause Y?" when X→Y itself is uncertain): **surface the premise**, classify it (typically Empirical), run loop on premise *first*. Do not answer the surface question with a tacit premise.

## 1. Loop, P1 → P2 → P3 → P4

### P1, Frame
- Restate question in one line. Note ambiguity.
- **Pre-register** before retrieval: predicted answer, confidence (0–1 or low/med/high), one sentence of "what would change my mind." Even one line counts. (Hides post-hoc rationalization; cf. Silberzahn 2018 many-analyst study.)
- **Reference class:** what category of question; what is the prior base rate of "true" or "yes" answers in this class.
- **Stakes ladder:** L0 idle / L1 informational / L2 advisory / L3 decision-driving / L4 irreversible (legal, financial, health, migration, security). Sets rigor floor, see `references/output-schemas.md`.
- **Type-III check:** is the asked question the *right* question, or is the user pattern-matching to a question framing that loses the actual decision?
- **Human-elicitation pass:** before P2 retrieve, identify load-bearing premises where the human is the most authoritative verifier (their codebase, their values, their context, their constraints, their access to non-public state, their prior decisions). For each such premise, ASK before proceeding. The `review-rigor` 10%-decision-shift threshold does NOT apply here; when human knowledge IS the verifier, asking is not permission-fishing, it is retrieval. Intrinsic self-critique without external grounding empirically degrades reasoning ([HUANG-SELFCORRECT-2023]; [STECHLY-SELFVERIF-2024]). Default to asking when uncertain whether load-bearing context is answerable from public sources alone.

### P2, Retrieve
- Tool budget stated: read > grep > web-fetch > LLM-priors (each ≈10× cost AND 10× lower hallucination risk than the next, in that order).
- For named files / functions / APIs / quotes / numbers: **read the body**. Do not summarize from name (cf. `review-rigor` S1).
- **Read-depth tagging (mandatory).** Annotate each verified cite with depth: `read:full` / `read:body` / `read:abstract` / `read:title`. **Iron-law floor (Anti-Pattern #4): `read:body` is the minimum for any magnitude / scope / caveat / mechanism / methodology claim at L2 or higher.** `read:abstract` is sufficient ONLY for (a) direction-of-effect at L1, (b) existence-of-method, (c) flagging a paper to body-read later. Quoting a speedup, %-gain, accuracy delta, or sample-size from an abstract for a recommendation is forbidden at L2+, even if the abstract was retrieved from a top-tier venue. Abstract-only magnitude citation downgrades the tag to `[priors-only]`. See Anti-Pattern #4 in §8 for the 2026-05-16 self-violation precedent (Chain-of-Draft accuracy inversion revealed only by body-read).
- For LLM-source claims (no retrieval): tag `[priors-only]`. One retrieval ≠ verified; **independent replication** (different source, different query, OR different tool) required before `[T1-verified]` at L3+.
- Every cite resolves to a row in `REFERENCES.md` with tier + URL/DOI + access date + read-depth. New cite → add row, then cite. No URL = no cite.
- Quotes verbatim or prefixed `paraphrase:`. No "approximately" quotes.
- **User-provided evidence** (paste, screenshot, PDF excerpt) is `[user-asserted]`, not `[T*-verified]`, until independently retrieved. User-provided content can be edited / selectively excerpted / fabricated upstream.

### P3, Adversarial
- **Steelman opposite** ≥1 paragraph: name the position, name an adherent if any, give its strongest case (not a strawman). **Steelman must produce different evidence requirements**, if it asks for the same evidence as the original, it is not a real opposite, it's rephrasing.
- **Falsifier per load-bearing claim:** "what evidence would flip this?" Operational form: "if I observed [specific finding], confidence would drop from X to Y." If no answer → drop or tag `[unfalsifiable]`.
- **Quine–Duhem check:** which auxiliary assumptions are also under test? Single-hypothesis falsification is rarely possible.
- **Bias scan:** name ≥1 plausible bias per major claim. Pull from `references/bias-catalog.md` (cognitive + research-design + LLM-instrument).
- **Empirical claims only, replication / validity scan:** effect size + CI, sample scope, preregistration, funder COI, multiple-comparisons, p-hacking susceptibility. See `references/replication-and-validity.md`.
- **LLM-as-instrument scan:** sycophancy, lost-in-the-middle, anchoring, domain hallucination rate, fabricated-cite check. See `references/llm-failure-modes.md`. Required every session.
- **L3+ only, adversarial collaboration:** invoke debate pattern (`references/agentic-research.md` Pattern 2: Prosecutor / Defender / Judge with independent retrieval per role). L4 → worktree fan-out (Pattern 1).
- **Mode-collapse detection:** if N passes agreed, list the retrieved sources each pass used. If overlap > 50%, agreement is not independent confirmation; downgrade aggregate confidence.

### P4, Synthesize
- Tag every load-bearing claim (Tags below).
- Confidence per claim, not per report. Cite per claim, not per section.
- **Calibration step:** which claims, if wrong, would I most expect to be wrong? Lower their confidence. (Tetlock-style.)
- **Many-analyst caveat:** if no second analyst / agent / pass, disclose "single-analyst conclusion underestimates uncertainty" when stakes ≥ L3.
- Output assembled per `references/output-schemas.md`, shaped to host (`CLAUDE.md`, `AGENTS.md`, `.cursorrules`).

### Stop, all of:
1. Every load-bearing claim has tier-≥T2 cite OR `[unknown]` OR `[priors-only]` (latter only allowed below L3).
2. Every load-bearing claim has falsifier OR `[unfalsifiable]`.
3. ≥1 LLM-as-instrument check this session.
4. Stakes-rigor floor met:
   - L1: T2 cite for load-bearing.
   - L2: T2 cite + `read:body` for magnitude claims.
   - L3: ≥2 independent retrievals per load-bearing claim, debate pattern run.
   - L4: worktree fan-out + many-analyst caveat disclosed.
5. Marginal info-gain from next pass < cost. Operational halt: last pass changed **0 confidences AND added 0 cites AND surfaced 0 new sources**. Two of three is not halt-worthy.

**Convergence between passes is not truth.** Mode collapse, sycophancy, and shared-prior collusion produce false convergence. Truth-signal = independent retrieval agreeing, not LLM passes agreeing.

## 2. Tags, graded, not binary

| Tag | Means |
|---|---|
| `[T1-verified, read:<depth>]` | Primary source (peer-reviewed paper, standards body, RCT data, official spec, primary statute) read this session at named depth; URL/DOI in `REFERENCES.md`. |
| `[T2-verified, read:<depth>]` | Credible secondary read this session at named depth. |
| `[T3-cited]` | Tertiary / forum / unverified blog / single tweet. Use only with explicit downgrade note. |
| `[user-asserted]` | User-provided evidence (paste, screenshot, PDF excerpt) not independently retrieved. Treat as a hypothesis, not confirmation. |
| `[inferred:<basis>]` | Logical/statistical inference from cited evidence. Basis named (e.g. `inferred:T1+priors`). |
| `[priors-only]` | No retrieval; from training distribution. Stale by default. Forbidden ≥ L3. |
| `[speculative]` | Generative; bounded by named priors. |
| `[unknown]` | Cannot determine within budget. |
| `[unfalsifiable]` | No test would flip. Treat as definitional or drop. |
| `[contested]` | Credentialed disagreement; aggregation rule below. |
| `[stale:<date>]` | Verified but source older than relevance window (default 24mo for AI tooling, 12mo for AI safety / model behavior, 30d for active threat-intel, 5y for security architecture, evergreen for math, varies). |

Numeric confidence (`p ≈ 0.7 ± 0.1`) only when decision-changing or quantitative claim. Otherwise tag suffices.

### `[contested]` aggregation rule

For a contested claim, output schema:

```
Claim: <statement> [contested]
  Position A: <statement> · adherents: <names> · evidence: <T-tagged cites> · strongest case: <1 line>
  Position B: <statement> · adherents: <names> · evidence: <T-tagged cites> · strongest case: <1 line>
  Your weighting: A ~0.6 / B ~0.4. Rationale: <1 line>.
  Decision-rule for user: <when each side's call is correct>.
```

Never collapse `[contested]` to a single point answer at L3+ stakes. If the user demands one, disclose the collapse explicitly.

## 3. Citation protocol, zero fabrication

1. **No URL = no cite.** Never invent. If you cannot retrieve, say so and downgrade tag.
2. Every cite resolves to a tier in `REFERENCES.md`. New cite → add row first, then use tag.
3. **Quotes verbatim** with file:line or URL+anchor. Approximate quotes prefixed `paraphrase:` and visibly different.
4. **Suspect-fabrication self-check:** plausible-sounding author+year+title combos that you did not retrieve are the canonical LLM failure (Walters & Wilder 2023; Athaluri 2023). Flag, don't emit.
5. **Citation laundering** check: if claim is "studies show," name the studies. If you can't, say "claim widely repeated; primary source not located."
6. **Numbers cite source.** Every count, percentage, threshold, date, file:line or URL. No memory-quoted constants. (Inherits from `review-rigor` S5/S8.)

## 4. LLM-as-instrument check (required every session)

Before emit, run mentally:
- **Sycophancy:** would I have said this if the user implied the opposite? (Sharma et al. 2023.)
- **Anchoring:** is my first answer biasing all subsequent passes?
- **Lost-in-middle:** did I privilege start/end of long input over the middle? (Liu et al. 2023.)
- **Domain hallucination rate:** low for math/code-with-verifier; **high for citations, dates, statistics, names, package versions, line numbers**.
- **Self-consistency:** would 3 independent passes give the same answer? If unsure on a load-bearing claim, run a second pass (Wang et al. 2022).
- **Order sensitivity / prompt sensitivity:** would a paraphrased prompt give a different answer? (Sclar et al. 2024.)

Full list + citations in `references/llm-failure-modes.md`.

## 5. Output

Default report sections (omit if empty, never omit if load-bearing):
- TL;DR + headline confidence + reversibility.
- Pre-registered prediction → finding diff (P1 artifact).
- Findings (tagged, sourced).
- Limits / threats to validity.
- What would change my mind.
- Open questions / next avenues.
- Sources (`REFERENCES.md` tags).

Style invariants (always):
- Bullets/tables > prose. One idea per line. Fragments OK.
- Drop articles where unambiguous. No transitions ("additionally", "furthermore", "in summary").
- No hedging without new information. No prompt restatement.
- Cut any line that adds no information.

Adapt shape to host (`CLAUDE.md`, `AGENTS.md`, `.cursorrules`). Loop is fixed; shape adapts. See `references/output-schemas.md` for stake-tier-specific templates.

## 6. File / artifact policy (anti-sprawl)

- Default: **append**. Index-first, grep before write.
- Pre-write: `ls` target dir; `grep -rli <topic>` siblings.
- Match → append under `## YYYY-MM-DD, <delta>` section. Dedupe: if new claim duplicates existing row, update-in-place + note revision.
- One file per topic. Date is metadata in section header, not in filename. No `-v2` / `-final` / `-copy` suffixes.
- Scratch / session history → `localonly/` (gitignored), never workspace root.
- ≥3 files on same topic → consolidate, archive rest in `localonly/archive/`.

## 7. Disk-before-denial

"Where / did / do I have <X>" → `ls` candidate dirs + `grep -rli <topic>` first, answer second. Never claim absence without searching.

## 8. Anti-patterns

**Top 4 (absolute, never, iron laws):**
1. Fabricated cite, author + year + title combo not retrieved this session.
2. `[T*-verified]` tag without retrieval this session OR without row in `REFERENCES.md`.
3. Convergence-as-truth, N passes agree → high confidence, without independent retrieval.
4. **Abstract-only citation for any magnitude / scope / caveat / mechanism / methodology claim at L2+**. Abstracts are author-marketing-optimized; the headline number is best-case, the baseline is often unstated or weak, and the failure-mode caveats live in the body. Quoting a speedup, % gain, accuracy delta, or sample-size from `read:abstract` and binding any recommendation to that number is a direct violation. Allowed uses of `read:abstract`: (a) direction-of-effect at L1, (b) existence-of-method at any tier, (c) flagging a candidate to read at body-depth. Forbidden uses at L2+: any quantitative claim, any "production-ready" framing, any "X beats Y" comparison, any stacking inference. Self-violation 2026-05-16 (token-optimization research, Rounds 1+2): every MEDIUM/LOW item was abstract-only; adversarial-review forced retraction. The skill now treats abstract-only magnitude citation as `[priors-only]`, not `[T*-verified]`, regardless of where the abstract came from.

**Major:**
- Approximate quote without `paraphrase:` prefix.
- Steelman skipped or steelman that asks for the same evidence as original (rephrasing).
- Falsifier skipped or falsifier that names no specific evidence-shape that would flip.
- "Studies show" / "research suggests" without naming specific studies (citation laundering).
- Numeric claim without source file:line or URL.
- Single-pass conclusion at L3+ stakes.
- Stale source treated as current (no `[stale:<date>]` tag where window exceeded).
- Generalization beyond sample scope without `[inferred:generalization]` tag.

**Minor / hygiene:**
- "DK label" without measured calibration.
- "Incorporate" routed without first running loop on paste.
- Isolated demand for rigor (skeptical of disliked, credulous of liked).
- Selective skepticism · motte-and-bailey · no-true-Scotsman · Galileo gambit · Texas sharpshooter · sunk-cost · bandwagon.
- Prompt restatement · narration · per-prompt ingestion file · dated filenames for recurring topics.
- Goodhart on tokens (compression past meaning).
- `[unknown]` used as epistemic cowardice (when retrieval was feasible).

## 9. Reference loading, load on demand, not by default

| File | Load when |
|---|---|
| `REFERENCES.md` | every cite (registry of all primary sources used) |
| `references/source-grading.md` | tier dispute, new domain, source provenance question |
| `references/replication-and-validity.md` | empirical claim / "study shows X" / effect size / RCT |
| `references/bias-catalog.md` | every multi-claim report (cognitive + research + LLM bias list) |
| `references/causal-inference.md` | "X causes Y" / observational data / DAG question |
| `references/llm-failure-modes.md` | required every session, self-instrument check |
| `references/agentic-research.md` | parallel branches / harness / worktree / RAG / debate protocol |
| `references/confidence-calibration.md` | numeric confidence / Brier / forecasting |
| `references/output-schemas.md` | report draft / stake-tier formatting |
| `references/examples.md` | first time using skill in a new domain (anchor calibration) |
| `references/failure-log.md` | every session at start, scan for prior traps in this skill class |

## 10. Install + sync

- Claude global: `~/.claude/skills/palamedes/`
- Claude repo: `<repo>/.claude/skills/palamedes/`
- Cursor: `.cursor/rules/palamedes.mdc` (frontmatter conversion; body points back at canonical)
- Generic: `AGENTS.md` snippet referencing this dir

**Drift hazard:** known mirrors at `~/.claude/skills/palamedes/SKILL.md` and any workspace sibling copies. Sync via `skill-sync` skill. This repository directory (`SKILL.md` + `references/` + `REFERENCES.md`) is the single source of truth.

## 11. Skill-meta, known limits

This skill cannot:
- Substitute for domain expertise (oncology / law / cryptography requires named experts).
- Verify claims against paywalled / offline sources without user assistance.
- Detect coordinated misinformation (state-actor disinfo, troll farms), out of scope, kick to OSINT tooling.
- Resolve contested empirical domains (nutrition, parts of IR, macroeconomics, parts of psychology, parts of education research), flag `[contested]`, present multiple weightings.
- Calibrate itself without offline Brier tracking, see `references/confidence-calibration.md`.

This skill **expects to be wrong** about ~10–20% of load-bearing claims at L2 stakes, ~5% at L3, target <2% at L4. See `references/failure-log.md` to track and update.

## 12. Version + changelog

**v2.0.0 (2026-05-14)**, full adversarial-review rewrite. New: question-type routing, P1–P4 loop, 10-tag system with read-depth, citation registry (REFERENCES.md), 8 reference docs (source-grading, replication-and-validity, bias-catalog, causal-inference, llm-failure-modes, agentic-research, output-schemas, confidence-calibration), examples, failure-log. Replaces v1 5-step "Validate / Dialectic / Unknowns / Refine / DK guard" loop.

**v1.x (≤2026-05-14)**, see `git log` if version-controlled, else superseded.

Changelog policy: bump major on stop-condition / tag-grammar / loop-structure change. Bump minor on new reference doc. Patch on typo / clarification.
