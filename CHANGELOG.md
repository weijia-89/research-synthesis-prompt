# Changelog

## v4.2 (May 2026)

Stripped model-specific references from `prompt.md`, `adversarial-review-prompt.md`, and `README.md`. The prompts now describe **capability classes** (live-web search model, deep-research model with broad web access, strong reasoning model, highest-reasoning long-context model) rather than naming specific providers or model versions. The Mode B selection step asks the user to name the actual provider and model variant for each phase, so the model-diversity warning lands on whatever the user picked instead of going stale every six months when a provider rebrands.

The empirical model-diversity claim (~60% correlated error on same-provider pairs, arXiv 2506.07962) stays. That's a finding about architecture similarity, not about any specific provider.

Added an MIT `LICENSE`.

---

## v4.1 (May 2026)

Adversarially reviewed adversarial-review-prompt.md and found five things wrong or overstated.

The calibration check was targeting middle compression: "flag if 70% of scores fall in 72–85." That's not what the literature shows. The failure direction is overconfidence toward high scores, not the middle. The old check would have flagged legitimate scoring while missing the actual problem. Fixed the threshold and direction.

URL verification was still just a URL-existence check. A model can hallucinate a citation pointing to a real paper on a related domain, fetch it successfully, and call it verified. Phase 1 and Prompt 1 now parse body text and look for the specific assertion. [CLAIM NOT IN SOURCE] drops to speculative. [ABSTRACT-ONLY] for paywalled pages where you can't verify methodology. [CLAIM AMBIGUOUS IN SOURCE] when the source discusses the same domain but not the specific number.

The "Mode A is broken" classification was too broad. Huang et al. (ICLR 2024) is specifically about intrinsic self-correction with no external feedback. Phases with URL fetching have external grounding that partially escapes it. Phase 6 — pure reasoning, no tools — is the specific failure point. Narrowed it there.

"Genuinely independent" for Mode B was overstated. arXiv 2506.07962 puts cross-model error correlation at about 60% for same-provider pairs. Better than single-model, not independent. Added a model diversity warning and a model-selection prompt so the tradeoff is visible before you run anything.

The context isolation instruction was listed as the primary fix for anchoring bias. Instruction-based debiasing has weak evidence for deep anchoring. Promoted the fresh context Final Gate — pass the revised claims table to a new session with no reasoning chain — as the actual structural escape.

---

## v4 (May 2026)

Separate adversarial review stage: adversarial-review-prompt.md.

The synthesis prompt could put together a coherent initial report but had no mechanism for actively trying to break what it built. This stage starts from finished claims and tries to prove them wrong. Zombie stat tracing, disconfirmation searches, cross-tool indirectness checks, attack rating on every counterargument.

Two modes. Mode A runs all eight phases in one pass for models with live web access. Mode B generates five prompts for distributing phases across capability classes — a live-web search model for source verification and disconfirmation, a strong reasoning model for indirectness, and the highest-reasoning long-context model available for re-synthesis. Mode B Prompt 5 is always the final step regardless of how you ran phases 1–4.

---

## v3.1 (May 2026)

Ported the fixes from adversarial-review-prompt back into prompt.md. Some of these should've been there from the start.

URL verification in the synthesis stage was checking whether a link resolved, not whether the source actually said what I was citing it for. A 200 OK pointing to a real paper on a related topic is not verification. Two gates now: HTTP status first, then parse the body text and find the specific assertion. Not in the source text: drops to speculative regardless of tier. Paywalled (abstract-only): can't verify methodology or population — one-tier downgrade and flagged.

Same gap in zombie stat tracing. I was looking for the source, not confirming the number was actually in it. Fixed.

The calibration failure direction was wrong. LLMs skew toward high confidence, not the middle. Added a check: if more than half the final scores come in at 80+, run a downward pressure pass. Three independent papers confirm the overconfidence direction (arXiv 2502.11028, 2508.06225, 2603.09985).

Added the attack rating taxonomy to adversarial self-review: [strong — downgrade] / [partial — survives with caveats] / [no viable attack found]. And the "consensus ≠ compounded confidence" rule now includes the correlated error data — same-provider models agree about 60% of the time when both get something wrong (arXiv 2506.07962), so convergence from architecturally similar models isn't the independent signal it looks like.

---

## v3 (May 2026)

Evidence methodology layer. v2 could find sources but had no enforcement of a consistent evidence hierarchy and no way to catch common methodological failure modes.

Study design hierarchy with tier-based confidence ceilings — tier sets the maximum possible score, quality issues reduce from there. Pre-registration checks (ClinicalTrials.gov, PROSPERO, OSF). Effect size gate: ≥70 confidence claims need an effect size alongside any p-value. HARKing detection — hypothesis appearing in Discussion rather than Introduction, post-hoc subgroups framed as primary findings, suspiciously clean hypothesis-result alignment. Meta-analysis quality checklist: I², fixed vs. random effects model selection, funnel plot symmetry, double-counting of underlying RCTs, CI interpretation.

The tier-ceiling system makes the rubric internally consistent. A claim can't reach 85 on expert opinion alone.

---

## v2 (April 2026)

v1 could find sources but had no way to tell whether three agents agreeing meant "this is well-established" or "all three trained on the same papers."

Verbatim quote passthrough for ≥70 confidence claims — direct source text required, not paraphrase. Reconstructed from memory: [QUOTE UNVERIFIED]. Atomic handoff format for downstream synthesis agents (CLAIM / CONFIDENCE / SOURCE / QUOTE / STATUS), grouped by [AGREEMENT] / [CONFLICT] / [SINGLE SOURCE]. [CONFLICT] flagging for cross-agent disagreement. [RECENCY RISK] for claims where primary evidence may fall within 12 months of training cutoff.

---

## v1 (early 2026)

Initial version. Source discovery across academic databases (PubMed, JSTOR, Google Scholar, Semantic Scholar, Scopus), preprints (arXiv, SSRN, bioRxiv, OSF), citation chains forward and backward, grey literature, lateral disciplines, non-English lit, contrarian sources, Retraction Watch. Per-source trustworthiness: venue quality, author credentials, funding/COI, methodology, replication status, recency, citation context. Confidence scoring 0–100. Adversarial self-review pass. Structured HTML output with evidence ledger.
