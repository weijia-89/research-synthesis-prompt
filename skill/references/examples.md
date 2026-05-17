# Examples, anchor calibration

Load when: first time using this skill in a domain, calibration baseline needed.

Each example shows prompt → bad output (typical LLM) → good output (skill-applied) with the failure-mode call-outs.

> **Demonstrative-only notice.** The bracketed citation tags below (e.g. `[T1-verified, METR-2025]`) appear inside *example outputs* to illustrate the protocol in operation. They are not real registry rows. Do not import them into `REFERENCES.md`. Real research sessions populate `REFERENCES.md` lazily, with `Verified: <today>` rows, per the citation protocol in `SKILL.md` §3.

---

## Example 1, Empirical claim with citation laundering

**Prompt:** "Does AI improve developer productivity? I want to write this in a memo."

### Bad output (typical)

> AI tools have been shown to improve developer productivity by approximately 55%. A study by GitHub demonstrated that developers using Copilot completed tasks faster and reported higher job satisfaction. Other research from 2024 suggests up to a 75% improvement on certain coding tasks. AI is reshaping how engineering teams work, and adoption is accelerating across the industry.

**Failures:**
- Citation laundering ("a study," "other research from 2024").
- Numbers without source, "55%", "75%", fabricated or not retrievable.
- No effect-size + CI.
- No sample-scope caveat (Copilot study was on a specific task, implementing an HTTP server in JS).
- No mention of countervailing evidence (METR-2025 found −19% with experienced devs).
- Sycophancy toward implied direction (user is writing a positive memo).
- No tier tags. Fluent prose presented as truth.
- No what-would-change-my-mind clause.

### Good output (skill-applied)

> **TL;DR.** Direction-of-effect is contested and population-dependent. Strongest current evidence finds a **negative** effect for experienced devs on familiar codebases (METR-2025; n=16; CI +2% to +37% slowdown). Strongest pro-productivity evidence (GitHub Copilot RCT 2022) is on a narrow synthetic task. DORA-2025 frames AI as an *amplifier* of pre-existing dev-team maturity, not a uniform boost. Stakes here are L2 (memo). Confidence: AI productivity-effect is **highly population- and task-dependent**, single point-estimate is misleading. `[T1-verified, METR-2025; T1-verified, GITHUB-COPILOT-RCT; T1-verified, DORA-2025]`
>
> **Pre-registered prediction.** Before retrieval, my prior was "small positive on average, large variance." Finding largely confirms variance; mean shifted toward zero or negative for senior segment.
>
> **Findings.**
> - METR 2025 RCT [T1-verified, METR-2025]: 16 experienced OSS devs, on their own large repos. Actual outcome **−19%** (i.e. slower with AI). Self-perceived **+20%**. CI on actual: **+2% to +37% slowdown**. Note the perception–reality gap.
> - GitHub Copilot RCT 2022 [T1-verified]: ~95 devs, narrow task (build a JS HTTP server). Treatment group ~55% faster. Sample scope: synthetic task, junior–mid devs.
> - DORA 2025 [T1-verified, DORA-2025]: AI is *amplifier*, teams with strong platform-engineering practices benefit; teams without get net drag.
>
> **Limits / threats to validity.**
> - METR sample = 16; small n; experienced devs only; familiar repos only. Cannot generalize to junior devs, novel domains, or well-defined tasks.
> - Copilot RCT task is unrepresentative of mainline industrial work.
> - Both are pre-2026 model generations; effect sizes may have shifted.
> - Industry-funded studies (vendor RCTs) systematically tilt positive, apply Lundh-style adjustment.
> - **Bias scan:** sycophancy (user wrote a positive-framed memo prompt); selection bias in OSS-dev sample; publication bias (we hear about positive vendor studies more).
>
> **What would change my mind.**
> - 2+ preregistered RCTs with n>200 each, mixed seniority, mainline industrial tasks, finding a robust positive effect with CI excluding zero → shift to "modestly net positive."
> - METR follow-up [METR-2026-update] resolving generalization concerns → update accordingly.
>
> **Open questions.** Effect on junior devs (under-studied); effect on code quality vs. velocity (DORA gestures at this); effect on long-horizon maintainability (no RCT exists).
>
> **Sources.** METR-2025, METR-2025-paper, METR-2026-update, GITHUB-COPILOT-RCT, DORA-2025, all in `REFERENCES.md`.
>
> **Self-instrument check.** Sycophancy: flagged + steelmanned opposite. Anchoring: deliberately retrieved before drafting. Citations: every `[T1-verified]` claim has URL retrieved this session [or marked `[unverified-this-session]` if not]. Self-consistency: ran two passes with different framings; convergence on direction-is-mixed.

---

## Example 2, Predictive question, version-sensitive

**Prompt:** "Should I use vector search or BM25 for my RAG system?"

### Bad output (typical)

> Vector search is generally better for semantic queries while BM25 is better for keyword matches. Hybrid search combining both is the modern best practice. Many production RAG systems use a hybrid approach with re-ranking, which has been shown to improve retrieval quality significantly. As of 2024, this is the recommended pattern.

**Failures:**
- "Has been shown to improve", no source.
- "Significantly", no effect size.
- "As of 2024", version drift; not retrieved.
- Generic "best practice" claim without context.
- No conditional, depends on corpus, query distribution, latency budget.
- No alternative considered (sparse-only, hybrid-with-different-rerankers, learned-sparse like SPLADE).

### Good output (skill-applied)

> **TL;DR.** "Better" is conditional. For corpus < ~50k docs with technical jargon and exact-token queries, BM25 alone is often near-state-of-the-art and trivially deployable. For larger corpora and natural-language queries, hybrid (BM25 + dense) + cross-encoder rerank is the dominant published pattern as of mid-2024. **Confidence:** medium; this is a moving target. Stakes: L2 (advisory). `[T2-verified, multiple]`
>
> **Pre-registered prediction.** Hybrid + rerank wins on most published benchmarks; BM25 surprisingly hard to beat on niche/technical corpora. (Held.)
>
> **Findings.**
> - BM25 baseline strength: well-documented; in BEIR benchmark [T1-verified] BM25 alone beats many dense retrievers on out-of-domain tasks.
> - Hybrid (BM25 + dense) + cross-encoder rerank: dominant pattern in production-RAG literature 2023–2024 [T2-verified]; gains over BM25-only typically modest (5–15 nDCG@10 points) but variance huge across datasets.
> - HyDE [T1-verified, GAO-HYDE-2022]: useful when query and doc vocab diverge.
> - Learned sparse (SPLADE) [T1-verified, paper]: competitive with hybrid at lower latency in some benchmarks.
> - Tradeoff: dense + rerank costs more latency and infra than BM25; for L4-latency-sensitive (sub-100ms p99), BM25-only is often the right choice despite quality cost.
>
> **Limits / threats to validity.**
> - "Best practice" varies by corpus type; benchmark performance ≠ your-corpus performance.
> - Field rotates fast; this answer is `[stale:6mo]` if not re-retrieved.
> - **Bias scan:** availability heuristic favors hybrid (more papers); BM25-only deployments under-published.
> - Vendor docs lean toward whichever method the vendor sells.
>
> **What would change my mind.**
> - A 2025+ benchmark on a corpus matching the user's domain showing BM25 dominance → recommend BM25-only.
> - Latency budget < 50ms p99 → BM25-only regardless of quality cost.
> - Specialized domain (legal, medical) with strict citation needs → re-evaluate, possibly graph RAG.
>
> **Open questions.** What is the user's corpus size, query distribution, latency budget, infra constraints? Without these, the answer is necessarily generic.
>
> **Sources.** GAO-HYDE-2022, BEIR-paper, SPLADE-paper, `REFERENCES.md`.
>
> **Self-instrument check.** Version drift: flagged. Citation: BEIR claim retrieved; SPLADE specifics from priors → tagged `[priors-only, recommend retrieval]`. Self-consistency: pass, second framing ("which to default to?") gave same conditional answer.

---

## Failure-mode contrasts visible in both examples

- **Citation laundering** → name primary sources or downgrade tag.
- **Numbers without source** → cite or refuse.
- **Sycophancy / direction-leaning prompt** → steelman opposite.
- **Generalization beyond sample** → tag `[inferred:generalization]`.
- **Version drift** → re-retrieve or tag `[stale]`.
- **No what-would-change-my-mind** → claim unfalsifiable.
- **No conditionality** → "best practice" claims should be conditional or refused.
