# Confidence calibration

Load when: numeric confidence required, forecasting, prediction, "how likely?"

## When to attach a number

Most claims need a tag, not a number. Use numeric confidence (`p ≈ 0.7 ± 0.1`) only when:
- Claim is quantitative ("the rate is X").
- Confidence is decision-changing (different recommendation at .6 vs. .8).
- User explicitly asked.
- Stakes ≥ L3 and claim is load-bearing.

Otherwise tag suffices and a number is false precision.

## Calibration ≠ accuracy

Calibrated forecaster: when they say 70%, ~70% of those claims turn out true. **Calibration is verifiable over many forecasts.** Single point estimates can't be calibrated, only the forecaster.

Reference: Tetlock [TETLOCK-EPJ], [TETLOCK-SUPER]; Good Judgment Project [GJP].

## Brier score, the canonical metric

For a single binary prediction: `(forecast − outcome)²`. Lower = better. 0 = perfect, 0.25 = chance for binary 50/50.

Decomposes into reliability (calibration), resolution (extremity), uncertainty (base-rate variance). Reference: Brier 1950 [BRIER-1950].

For research-skill self-eval: track Brier on predictions made via this skill across sessions.

## Reference-class forecasting

Kahneman / Lovallo: outside view beats inside view for project estimates. To predict X:
1. Identify reference class, set of similar past cases.
2. Distribution of outcomes in the class.
3. Adjust for case-specific factors *only after* anchoring on reference class.

Common error: starting with case-specific reasoning (inside view) → planning fallacy.

For research: "what's the base rate of this kind of finding being replicated?" anchors confidence before inspecting the specific paper.

## Reference-class problem (Hájek)

Every probability is reference-class-dependent. "What's the probability of this AI tool being adopted?" depends on whether reference class is "all dev tools," "all AI dev tools," "AI dev tools by this vendor," "AI dev tools at this maturity stage." Specify or refuse to estimate.

## Numeric tag formats

- `p ≈ 0.7`, point estimate, uncertainty informal.
- `p = 0.7 ± 0.1`, point + range, range reflects calibration uncertainty.
- `p ∈ [0.5, 0.8]`, interval; refuse central tendency when uninformative.
- `OR ≈ 1.4 [95% CI 1.1–1.8]`, for empirical effect sizes.

## What-would-change-my-mind threshold

For each numeric prediction, name the evidence + magnitude that would shift you across a decision band:

> Claim: this dependency is safe to add. p ≈ 0.85.
> Would-change: a CVE in this package or any post-`2023-01` advisory → p < 0.5.
> Would-change: a user complaint thread with verified maintainer non-response → p ≈ 0.6.

Without this clause, the number is unfalsifiable hedging.

## Common forecasting pathologies

| Pathology | Symptom | Fix |
|---|---|---|
| Anchor on first estimate | All later answers cluster near first | Generate fresh, blind to prior |
| 50/50 hedge | Default to 50% under uncertainty | Honest 50% requires reference-class evidence; otherwise tag `[unknown]` not `p=0.5` |
| Overconfidence | Stated 90% are correct ~70% of time | Apply Tetlock-style "mush in 10% to 30%" prior |
| Anchoring on extreme | First evidence pushes to 95%+; later evidence under-updates | Bayesian update; final ≠ first |
| Vague confidence | "high confidence" without number when number is decision-relevant | Numeric or refuse |
| False precision | "p = 0.7234" from no calibration data | Round; show range |
| Single-source confidence | High confidence from one source | Independent retrieval before high p |
| Refusal as uncertainty | "I cannot determine" used to dodge L3 obligations | Distinguish refusal from honest [unknown] |

## Many-analyst caveat (Silberzahn 2018 [SILBERZAHN-2018])

Single-analyst conclusions underestimate uncertainty. When a research question is presented to N analysts, conclusions vary substantially even with identical data. Single-pass research → confidence should be lower than the analyst's subjective confidence.

Heuristic: at L3+ with single-pass output, lower numeric confidence by ~10–20% from "what the evidence alone supports" to reflect single-analyst variance. Or run a second pass with different framing to test.

## Aggregation across sources

If 3 independent T1 sources agree, confidence is *not* the product of individual confidences (that would underestimate). Use Bayesian / log-odds aggregation, or informal: 3 independent T1 + no T1 disconfirmer → typically `[T1-verified, replicated]` and high confidence.

If 3 sources agree but trace to a common origin (citation laundering, single primary), treat as 1 source.

## Calibration self-test (offline)

Periodically: gather 30 past predictions made with numeric confidence, score Brier. If Brier > 0.25 on binary 70%-confidence predictions, you are mis-calibrated; widen ranges or back off extreme claims.
