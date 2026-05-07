# Changelog

## v3 (May 2026)

Evidence methodology layer, built from systematic review of research methodology literature (MASS research guides on study design, statistics, and interpretation).

**Added:**
- Study design hierarchy with tier-based confidence ceilings. Tier determines the maximum possible confidence score; quality issues reduce from there.
- Pre-registration check (ClinicalTrials.gov, PROSPERO, OSF). Unregistered RCTs flagged `[UNREGISTERED-RCT]`.
- Statistical power requirement. Underpowered studies (N < 30/group, no power analysis) flagged explicitly.
- Effect size gate. ≥70 confidence claims must report effect size alongside p-values. p-value alone no longer sufficient.
- HARKing detection as a named failure mode. Signs: hypothesis in Discussion not Introduction, post-hoc subgroups as primary findings, suspiciously clean alignment. Flags `[HARKING-SUSPECTED]` and reduces confidence one tier.
- Meta-analysis quality checklist: I² and heterogeneity interpretation, fixed vs. random effects model selection, funnel plot symmetry (`[FUNNEL-UNREPORTED]` if absent), double-counting check across synthesized meta-analyses (`[DOUBLE-COUNTED]`), CI interpretation (CI crossing zero = effect may be zero).

**Rationale:** v2 had strong source discovery and confidence infrastructure but didn't enforce a consistent evidence hierarchy or catch common methodological failure modes. The tier-ceiling system makes the confidence rubric internally consistent — a claim can't reach 85 on expert opinion alone.

---

## v2 (April 2026)

**Added:**
- Verbatim quote passthrough for ≥70 confidence claims. Claims must include ≤3 sentences of direct source text. Reconstructed or paraphrased excerpts labeled `[QUOTE UNVERIFIED]`.
- Atomic handoff format for downstream synthesis agents: `CLAIM / CONFIDENCE / SOURCE / QUOTE / STATUS`, grouped by `[AGREEMENT]` / `[CONFLICT]` / `[SINGLE SOURCE]`.
- `[CONFLICT]` flagging. Downstream agents instructed to flag any claim where independent research contradicts the report, with source and confidence score.
- `[RECENCY RISK]` temporal gate. Claims where primary evidence may fall within 12 months of the model's training cutoff labeled provisional.
- Consensus ≠ compounded confidence rule. Three agents citing the same underlying papers treated as one data point, not three.

**Rationale:** v1 had strong research depth but no mechanism to distinguish "three agents all found this because it's well-established" from "three agents all found this because they all trained on the same papers." The quote passthrough forces the system to hold high-confidence claims to a higher evidentiary bar.

---

## v1 (early 2026)

Initial version.

- Source discovery: academic databases (PubMed, JSTOR, Google Scholar, Semantic Scholar, Scopus), preprints (arXiv, SSRN, bioRxiv, OSF), citation chains (forward + backward), grey literature, lateral disciplines, non-English literature, contrarian sources, retraction checks.
- Per-source trustworthiness: venue quality, author credentials, funding/COI, methodology (N, controls, blinding), replication status, recency, citation context.
- Confidence scoring 0–100 with per-claim justification (study type, replication status, sample size, methodology quality, recency).
- Adversarial self-review: after synthesis, argues against own conclusions; reports where counterarguments succeed.
- Structured output: HTML report with evidence ledger, inline citations, learning resources, future research directions.
