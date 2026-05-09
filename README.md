# research-synthesis-prompt

A multi-agent research prompt iterated over several months to produce epistemic research reports. No more getting 'Well actually' when you talk about your favorite new habit you picked up, now you'll be the one going on about the replication crises and the importance of the hiearchy of evidence. 

Each run coordinates three independent LLM research agents, then passes their combined output to a separate adversarial synthesis agent before anything reaches the report. This is a process-based evaluation: each reasoning step is validated before the next one runs. The system can't surface a high-confidence claim without showing the source text that backs it, and it's even instructed to adversarially self-review, to disconfirm instead of just taking everything it outputs as truth.

---

## What it does

<img src="assets/sketchplanations-thesis-antithesis-synthesis.png" width="420" alt="Thesis, Antithesis, Synthesis">

*[Thesis, Antithesis, Synthesis](https://sketchplanations.com/thesis-antithesis-synthesis) by Sketchplanations, [CC BY-NC 4.0](https://creativecommons.org/licenses/by-nc/4.0/)*

You'll use this prompt on separate research agents independently. A downstream agent ingests all three outputs and applies Hegelian dialectics: strongest case for each claim, strongest case against it, synthesis that survives both. You then run the phases again if you want an even stronger evidence base.

Cross-agent disagreements surface as [CONFLICT] flags and the agent is instructed to evaluate based on a confidence matrix that has source tiers, hiearchy of evidence epistemology, and basic frequentist and Bayesian statistical rigor baked in along with a cursory analysis of best practices when it comes to methodology.

The part that took the most work wasn't the synthesis logic but building in the understanding that three agents agreeing doesn't mean three independent data points. Just because all the people at your gym drink pre-workout doesn't mean that the 2000% of your DV of taurine they're taking will be anything more than placebo effect. The goal of this prompt is to incorporate skepticism, not to normalize global confusion.

The output is an HTML research report with a sticky navigation TOC, theoretical foundations section, tactical implementation guide, inline evidence ledger, and a summary block in atomic CLAIM / CONFIDENCE / SOURCE / QUOTE / STATUS format, grouped by [AGREEMENT], [CONFLICT], and [SINGLE SOURCE]. The summary format exists specifically so downstream synthesis agents can ingest it without re-reading the whole report whereas the HTML5 FE wrapper is just to make your time reading the output less overwhelming.

---

## Architecture

**Verifier model**
A dedicated synthesis agent checks whether claims are supported by cited sources. Claim generation and claim verification are separate steps. The same model that produced a claim doesn't get to decide whether it's trustworthy.

**Structured uncertainty**
Claims that can't be anchored to source text get held, not surfaced. Claims that are rated at ≥70 confidence require a verbatim excerpt from the source. If that excerpt is reconstructed from memory rather than direct text, it's labeled `[QUOTE UNVERIFIED]`. This is an explicit guardrail against AI hallucination, not study design.

**Meta-evaluation loop**
The prompt was calibrated against topics with known ground truths based on my own deep nerding out on exercise science. Shoutout to the MASS Research folks for compiling a lay-person accessible research journal on the latest in the science of weight training. Confidence thresholds were adjusted until scores tracked actual accuracy. Each version was adversarially reviewed: weaknesses identified, patched, then the next version built on top.

**Consensus ≠ compounded confidence**
Three agents citing the same three papers is one data point, not three. Shared citation bases are flagged explicitly so downstream synthesis agents don't overweight replicated-looking evidence. Only true replication gets a bonus and that's still less weight than a straight up well-designed meta-analysis with a bunch of RCTs as sources.

**Temporal gate**
Claims where the primary evidence may fall within 12 months of an LLM's training cutoff are labeled `[RECENCY RISK]` and treated as provisional until verified against live sources. More important for some fields than others but a good callout regardless.

---

## Evidence methodology

<img src="assets/Drawn_image_illustrating_the_Hierarchy_of_Evidence.png" width="420" alt="Hierarchy of Evidence">

*[Hierarchy of Evidence](https://commons.wikimedia.org/wiki/File:Drawn_image_illustrating_the_Hierarchy_of_Evidence.png) by Wikimedia contributors, [CC BY-SA 4.0](https://creativecommons.org/licenses/by-sa/4.0/)*

Every source is classified by study design before a confidence score is assigned. The tier is the ceiling; quality issues (underpowered study, suspected HARKing, high COI) reduce from there.

| Study design tier | Confidence ceiling |
|---|---|
| Meta-analysis, I² < 50%, funnel symmetry reported | 95 |
| Multiple RCTs or systematic review, I² < 75% | 85 |
| Single unreplicated RCT → flagged `[SINGLE-RCT]` | 70 |
| Cross-sectional or case-control | 60 |
| Expert opinion or case study only | 45 |

Per-source checks:

- Pre-registration is checked via ClinicalTrials.gov, PROSPERO, and OSF. Unregistered RCTs get flagged `[UNREGISTERED-RCT]`; it's the clearest signal against p-hacking and HARKing.
- Statistical power: N ≥ 30 per group with a power analysis present. Underpowered studies produce false negatives as readily as false positives.
- For ≥70 confidence claims, effect size is required alongside p-values (Cohen's d, partial η², odds ratio, RR, or mean difference with units). A p-value alone doesn't get there.
- HARKing gets flagged when the hypothesis shows up in Discussion rather than Introduction, when post-hoc subgroups are framed as primary findings, or when the hypothesis-result alignment is suspiciously clean. All of these trigger `[HARKING-SUSPECTED]` and a one-tier reduction.
- For meta-analyses: I² value, whether the fixed or random effects model was used and why, funnel plot symmetry, whether the same underlying RCTs are getting double-counted across synthesized meta-analyses, and CI interpretation (a CI crossing zero means the effect may be zero).

This doesn't include all the ways data gets manipulated. Future iterations will be stricter but there's also a tradeoff where exceptionally niche research gets no results.

---

## Output structure

The HTML report includes:

- Sticky navigation TOC
- Theoretical foundations and key concepts (non-expert accessible)
- Tactical implementation guide
- Evidence ledger with full source URLs, confidence scores, justifications, verbatim quotes
- Learning resources
- Future research directions with confidence scores
- `<summary>` block (atomic `CLAIM / CONFIDENCE / SOURCE / QUOTE / STATUS` format, grouped by `[AGREEMENT]` / `[CONFLICT]` / `[SINGLE SOURCE]`)

---

## Usage

Paste `prompt.md` as the system prompt. Replace `{{RESEARCH_TOPIC}}`. Run three agents in parallel, then pass all three outputs to a fourth synthesis agent running the same prompt.

After synthesis, copy the `<summary>` block from the output and paste it into `adversarial-review-prompt.md`. That stage starts from finished claims and tries to break them, not continue building on them. Two modes:

Mode A runs all eight phases in one pass; you need a model with live web access (Claude, Gemini, Perplexity) and you get a revised claims table with deltas and attack ratings at the end. Mode B generates five self-contained prompts you distribute across specialized tools: Perplexity for disconfirmation search, GPT-4o for indirectness reasoning, Claude for re-synthesis. It opens with a model-selection step to account for architectural similarity between whatever you're running, and Prompt 5 is always the final step regardless of how you ran the others.

The two stages are doing different work: synthesis finds sources and builds a picture from them; the adversarial pass comes in after and tries to poke holes in what was built.

---

## Version history

**v1:** Source discovery across academic databases, preprints, grey lit, citation chains (forward and backward), contrarian sources, Retraction Watch. Per-source trustworthiness assessment on venue, author credentials, funding and COI, methodology, replication status, recency, and citation context. Confidence scoring 0–100 with per-claim justification. Adversarial self-review pass.

**v2:** v1 could find sources but had no way to tell whether three agents agreeing meant "this is well-established" or "all three trained on the same papers." Added verbatim quote passthrough for ≥70 confidence claims, requiring direct source text, labeled `[QUOTE UNVERIFIED]` if reconstructed from memory. Added the atomic handoff format (`CLAIM / CONFIDENCE / SOURCE / QUOTE / STATUS`) so downstream synthesis agents could ingest without re-reading the full report. Added `[CONFLICT]` flags for cross-agent disagreement and `[RECENCY RISK]` for claims with evidence that may fall within 12 months of training cutoff.

**v3:** Evidence methodology layer. v2 could find sources but had no enforcement of a consistent evidence hierarchy and no way to catch common methodological failure modes. Added study design tier-based confidence ceilings, pre-registration checks, effect size gates, HARKing detection, and the meta-analysis quality checklist (I², fixed vs. random effects model selection, funnel plot symmetry, double-counting of underlying RCTs, CI interpretation). The tier-ceiling system makes the rubric internally consistent. A claim can't reach 85 on expert opinion alone.

**v3.1:** Backport of fixes found during adversarial review of the downstream prompt. URL verification was checking whether a link resolved, not whether the source actually said what was being cited. A 200 OK pointing to a real paper on a related topic is not verification. Added two gates: HTTP status first, then parse the body text and find the specific assertion. Not in the source text: drops to speculative regardless of tier. Same gap in zombie stat tracing: the check was looking for the source, not confirming the number was in it. Fixed. The calibration check was targeting the wrong direction: LLMs skew toward high confidence, not the middle, so the check now flags when more than half the final scores come in at ≥80 and forces a downward pressure pass. Added attack rating taxonomy to adversarial self-review. Strengthened the consensus rule with actual correlated error data: same-provider model pairs agree about 60% of the time when both get something wrong (arXiv 2506.07962), so convergence from similar models is not the independent signal it looks like.

**v4:** Separate adversarial review stage: `adversarial-review-prompt.md`. The synthesis prompt could put together a coherent initial report but had no mechanism for actively trying to break what it built. This stage starts from finished claims and tries to prove them wrong: zombie stat tracing, disconfirmation searches, cross-tool indirectness checks, attack rating on every counterargument. Two modes. Mode A runs all eight phases in one pass for models with live web access. Mode B generates five prompts for distributing phases across specialized tools.

**v4.1:** Adversarially reviewed the adversarial review prompt and found five things wrong or overstated. The calibration check was targeting middle compression instead of the actual documented failure direction (overconfidence toward high scores). URL verification was still just a URL-existence check; a model can hallucinate a citation pointing to a real paper on a related domain and call it verified. Phase 1 and Prompt 1 now parse body text and look for the specific assertion. The "Mode A is broken" classification was too broad: Huang et al. (ICLR 2024) is about intrinsic self-correction with no external feedback, so phases with URL fetching partially escape it. Phase 6 (pure reasoning, no tools) is the specific failure point. "Genuinely independent" for Mode B was overstated: arXiv 2506.07962 puts cross-model error correlation at about 60% for same-provider pairs. Mode B now opens with a model-selection prompt so the diversity tradeoff is visible before you run anything. Promoted the fresh context Final Gate to the primary structural escape from anchoring bias: pass only the revised claims table to a new session with no reasoning chain.

---

## Related concepts

process-based evaluation · verifier model · back-out options · confidence calibration · meta-evaluation · sandwiching · hallucination reduction · evidence synthesis · multi-agent research · epistemic calibration · structured uncertainty · systematic review · hierarchy of evidence · HARKing · publication bias · effect size · pre-registration
