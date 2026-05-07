# research-synthesis-prompt

A multi-agent research prompt iterated over several months to produce epistemic research reports. No more getting 'Well actually' when you talk about your favorite new habit you picked up, now you'll be the one going on about the replication crises and the importance of the hiearchy of evidence. 

Each run coordinates three independent LLM research agents, then passes their combined output to a separate adversarial synthesis agent before anything reaches the report. This is a process-based evaluation: each reasoning step is validated before the next one runs. The system can't surface a high-confidence claim without showing the source text that backs it, and it's even instructed to adversarially self-review, to disconfirm instead of just taking everything it outputs as truth.

---

## What it does

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

Every source is classified by study design before a confidence score is assigned. The tier sets the ceiling — quality issues (underpowered study, suspected HARKing, high COI) reduce from there.

| Study design tier | Confidence ceiling |
|---|---|
| Meta-analysis, I² < 50%, funnel symmetry reported | 95 |
| Multiple RCTs or systematic review, I² < 75% | 85 |
| Single unreplicated RCT → flagged `[SINGLE-RCT]` | 70 |
| Cross-sectional or case-control | 60 |
| Expert opinion or case study only | 45 |

Per-source checks:

- **Pre-registration** — ClinicalTrials.gov, PROSPERO, OSF. Unregistered RCTs flagged `[UNREGISTERED-RCT]`. Pre-registration is the clearest signal against p-hacking and HARKing.
- **Statistical power** — N ≥ 30 per group and power analysis present. Underpowered studies produce false negatives as readily as false positives.
- **Effect size** — required alongside p-values for ≥70 confidence claims (Cohen's d, partial η², odds ratio, RR, or mean difference with units). A p-value alone doesn't support a high-confidence claim.
- **HARKing detection** — hypothesis in Discussion not Introduction, post-hoc subgroups framed as primary findings, suspiciously clean hypothesis-result alignment all trigger `[HARKING-SUSPECTED]` and a one-tier confidence reduction.
- **Meta-analysis quality** — I² value, fixed vs. random effects model selection, funnel plot symmetry, double-counting of underlying RCTs across synthesized meta-analyses, CI interpretation (CI crossing zero means the effect may be zero).

This doesn't include all the ways data gets manipulated. Future iterations will be stricter but there's also a tradeoff where exceptionally niche research gets no results.
---

## Output structure

The HTML report includes:

- Sticky navigation TOC
- Theoretical foundations and key concepts (non-expert accessible)
- Tactical implementation guide
- Evidence ledger — full source URLs, confidence scores, justifications, verbatim quotes
- Learning resources
- Future research directions with confidence scores
- `<summary>` block (atomic `CLAIM / CONFIDENCE / SOURCE / QUOTE / STATUS` format, grouped by `[AGREEMENT]` / `[CONFLICT]` / `[SINGLE SOURCE]`)

---

## Version history

**v1** — Source discovery across databases, preprints, grey lit, citation chains, contrarian sources. Per-source trustworthiness assessment (venue, author, funding/COI, methodology, replication, recency, citation context). Confidence scoring 0–100 with per-claim justification. Adversarial self-review pass.

**v2** — Verbatim quote passthrough for ≥70 confidence claims. Atomic handoff format for downstream synthesis agents. `[CONFLICT]` flags for cross-agent disagreement. `[RECENCY RISK]` temporal gate.

**v3 (current)** — Full evidence methodology layer: study design hierarchy with tier-based confidence ceilings, pre-registration checks, effect size gates, HARKing detection, meta-analysis quality checklist (I², model selection, funnel plot, double-counting, CI interpretation).

---

## Usage

Paste the contents of `prompt.md` as the system prompt for your research agent (Gemini Deep Research, ChatGPT Deep Research, or Claude). Replace `{{RESEARCH_TOPIC}}` with your topic. Run three agents in parallel. Pass all three outputs to a synthesis agent with the same prompt.

---

## Related concepts

process-based evaluation · verifier model · back-out options · confidence calibration · meta-evaluation · sandwiching · hallucination reduction · evidence synthesis · multi-agent research · epistemic calibration · structured uncertainty · systematic review · hierarchy of evidence · HARKing · publication bias · effect size · pre-registration
