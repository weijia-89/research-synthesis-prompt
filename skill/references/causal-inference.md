# Causal inference, load when "X causes Y" or observational data

Load when: causal claim, observational data, "linked to," "associated with," "may cause."

## Correlation ≠ causation, but *why* it can fail

Five mechanisms produce non-causal correlation:
1. **Chance**, small sample, random alignment.
2. **Reverse causation**, Y causes X.
3. **Common cause / confounding**, Z causes both X and Y.
4. **Selection**, sampling on outcome or correlate of outcome.
5. **Mediation mistaken for cause**, X → M → Y vs. X → Y.

For each correlational claim consumed, ask: which of the five is most plausible?

## DAGs (Pearl), the 30-second version

Three node types in a causal path:
- **Chain**: X → M → Y. Conditioning on M *blocks* the path.
- **Fork (confounder)**: Z → X, Z → Y. Conditioning on Z *blocks* the path.
- **Collider**: X → C ← Y. Conditioning on C *opens* a spurious path (Berkson's bias).

**Backdoor criterion:** to estimate the causal effect of X on Y, condition on a set Z that blocks all backdoor paths (paths into X) without opening any collider paths.

**The most common error in observational research** is conditioning on colliders or mediators thinking they are confounders. Adjusting for everything available is *worse* than adjusting for nothing if any of those variables is a collider.

Reference: Pearl, *Causality* [PEARL-CAUSALITY]; Hernán & Robins, *Causal Inference: What If* (free) [HERNAN-ROBINS].

## Identification strategies, strongest to weakest

| Strategy | Strength | Required assumptions |
|---|---|---|
| RCT (randomized controlled trial) | Strongest in principle | Compliance, no spillover, blinding, valid outcomes |
| Cluster RCT | Strong | Cluster-level randomization correctly modeled |
| Natural experiment / IV | Strong if instrument valid | Instrument relevance + exclusion + monotonicity |
| Regression discontinuity (RDD) | Strong locally | Local continuity at cutoff |
| Difference-in-differences (DiD) | Moderate | Parallel trends pre-treatment |
| Synthetic control | Moderate | Donor pool unaffected by treatment |
| Mendelian randomization | Moderate (genetics) | Genetic instrument valid; no pleiotropy |
| Propensity-score matching | Weaker than often presented | No unmeasured confounding (untestable) |
| Multivariable regression "controlling for" | Weakest in observational | Correct DAG, no unmeasured confounding |

A "controlled-for" multivariable regression in observational data is an *assumption* of the DAG, not a *test* of it. Headlines that say "even after controlling for X" overstate causal certainty.

## Bradford Hill criteria, when observational evidence may carry causation

[BRADFORD-HILL-1965]: not a checklist. Aggregators of evidence:
- Strength · Consistency · Specificity · Temporality · Dose-response · Plausibility · Coherence · Experimental support · Analogy.

Specificity is the weakest of the nine; temporality the strongest. Use as a heuristic frame for "is this association plausibly causal," not as proof.

## Quine–Duhem holism, falsification's hidden cost

A hypothesis is never tested in isolation. To test "X causes Y," you also test:
- The measurement of X is valid.
- The measurement of Y is valid.
- The intervention manipulated X and not other things.
- The model linking X to Y is correctly specified.
- No selection bias in sample.

A failed test could indict any of these auxiliaries, not necessarily the headline hypothesis. (Quine 1951 [QUINE-1951]; see also Lakatos research-programmes [LAKATOS-1970] for "protective belt" of auxiliaries.)

When evaluating claimed falsifications, ask: which auxiliary could be wrong instead?

## Mechanism vs. effect

Knowing **that** X causes Y (effect) is different from knowing **how** (mechanism). Strong-mechanism claims require finer-grained evidence than effect claims. Public-health interventions often have known effect with debated mechanism (cf. fluoride, statins, SSRI mechanism debates).

For tech / AI claims: "this prompt technique improves benchmark X" (effect) vs. "this prompt works because LLMs reason via Y" (mechanism). The latter requires causal evidence on the mechanism (probing studies, ablations, mechanistic interpretability), not just benchmark deltas.

## Interventional vs. counterfactual claims

- **Interventional** ("if we do X, Y happens"): supported by RCT.
- **Counterfactual** ("if X had not happened, Y would not have"): requires more, assumes individual-level causal effect, requires assumptions like consistency, no-interference, positivity (Hernán-Robins).

Most policy claims are counterfactual but evidenced only interventionally. Mind the gap.

## Reference-class problem

Forecasters and causal claimants must pick a reference class. "What is the success rate of mergers like this?", depends on what counts as "like this." Hájek 2007 ("Reference Class Problem is Your Problem Too") generalizes: every probability claim is reference-class-dependent.

When making a causal-effect estimate, state the reference class explicitly.

## Common claim shapes, fast triage

| Claim form | First question |
|---|---|
| "X is associated with Y" | Confounding / selection / reverse, which? |
| "X reduces Y by Z%" | RCT or observational? Effect size + CI? Sample scope? |
| "Even after controlling for A, B, C" | DAG specified? Are A/B/C confounders or colliders? Unmeasured confounding plausible? |
| "X causes Y in mice/humans/etc." | Translation to other species / settings is inferential; tag accordingly. |
| "Studies show X works" | Citation laundering, name the studies. |
| "X is necessary / sufficient for Y" | Counterexample search; necessity ≠ sufficiency. |
| "Without X, Y would not have happened" | Counterfactual, requires causal model + scope. |
