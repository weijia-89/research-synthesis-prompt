# Bias catalog, cognitive + research-design + LLM-instrument

Load when: every multi-claim report, before P3 adversarial pass.

Required output: name ≥1 plausible bias per major claim during P3.

## A. Cognitive biases (claimant + reader + LLM)

| Bias | Effect | Counter |
|---|---|---|
| Confirmation | Seek/weight evidence supporting prior. | Pre-register prediction; actively seek disconfirming. |
| Anchoring | First number / answer biases all subsequent. | Ask the question fresh; multiple framings. |
| Availability | Recent vivid examples treated as base-rate. | Look up base-rate; reference-class. |
| Hindsight ("knew it all along") | Past events feel inevitable post-hoc. | Pre-register; date-stamp predictions. |
| Narrative fallacy | Fit data to a coherent story; reject anomalies. | Tabulate raw data; check unfit cases. |
| Survivorship | Sample only the surviving units; miss the dropouts. | Enumerate dropouts and failures explicitly. |
| Selection | Non-random recruitment skews sample. | Specify recruitment + exclusions. |
| Recall | Self-reported past biased toward present beliefs. | Prefer contemporaneous records. |
| Halo / horns | One trait colors judgment of unrelated traits. | Score each criterion independently. |
| Sunk-cost | Past investment biases future decision. | Frame as zero-base decision. |
| Status-quo | Prefer current arrangement. | Pre-mortem on status quo. |
| Bandwagon / authority | Believe because many / important people do. | Find a primary skeptic with credentials. |
| Galileo gambit | "I'm a heretic so I'm right." | Most heretics are simply wrong. |
| Overconfidence | Subjective confidence > calibration. | Calibration training; Brier score. |
| Dunning–Kruger | Low skill → high confidence. (Note: effect partly statistical artefact, Nuhfer; Gignac 2020 [GIGNAC-2020]. Concept useful, named-effect contested.) | Calibration check, not the named label. |
| Motte-and-bailey | Defend strong claim with weak fallback when challenged. | Pin down which claim is being defended. |
| Isolated demand for rigor | Apply skepticism asymmetrically. | Same standard to liked + disliked claims. |
| Selective skepticism | Skeptical of disliked, credulous of liked. | List liked + disliked claims; same questions. |
| Texas sharpshooter | Cluster found post-hoc; target drawn around it. | Pre-specify hypothesis. |
| No-true-Scotsman | Define category to exclude counterexamples. | Definitions before examples. |
| Bulverism | "You only believe X because you're a Y." | Address the argument, not the arguer. |
| Tu quoque | Reject argument because critic is hypocritical. | Argument's validity independent of arguer. |
| Affect heuristic | Like/dislike drives risk/benefit estimate. | Numerical estimate before affect. |
| Planning fallacy | Underestimate time/cost. | Reference-class forecasting. |
| Optimism / pessimism | Systematic shift in expectations. | Calibrate against base-rate. |
| Naive realism | "I see things as they are." | Steelman opposite. |
| Bikeshedding | Spend effort on trivial decisions. | Allocate by stakes ladder. |

## B. Research-design biases (in studies you read)

| Bias | Effect | Diagnostic |
|---|---|---|
| Selection | Non-random sample skews effect. | Ask for recruitment + scope. |
| Publication / file-drawer | Null results unpublished. | Funnel plot, Egger, p-curve. |
| Allocation | Treatment assignment correlated with outcome. | Random + concealed allocation? |
| Performance | Treatment groups handled differently. | Blinded? |
| Detection | Outcome ascertainment differs by group. | Blinded outcome assessment? |
| Attrition | Differential dropout. | Compare baseline of completers vs. dropouts. |
| Reporting | Outcomes selectively reported. | Preregistered outcome set vs. reported? |
| Recall | Cases remember exposures differently than controls. | Prospective design? |
| Confounding | Third variable causes both. | DAG; adjust on backdoors only (`causal-inference.md`). |
| Collider stratification (Berkson) | Conditioning on common effect creates spurious assoc. | Don't adjust for colliders. |
| Reverse causation | Y → X, not X → Y. | Temporality (Bradford Hill 4). |
| Immortal time | Time before exposure misclassified. | Time-zero defined correctly? |
| Lead-time | Earlier detection mistaken for longer survival. | Adjust for screening. |
| Length bias | Slow-progressing cases over-sampled. | Incidence design vs. prevalence. |
| Healthy-user / healthy-worker | Volunteers / employees healthier than population. | Adjust or restrict. |
| Funder / COI | Industry funding tilts toward sponsor (Lundh, Cochrane). | Tag COI; replicate. |
| Citation cartel | Co-citation circles inflate impact. | Independent citations? |
| HARKing | Hypothesis after results known. | Preregistration. |
| p-hacking / forking paths | Analytic flexibility inflates Type I. | Preregistered analysis plan. |
| Multiple comparisons | k tests at α inflate false positives. | Correction (Bonferroni / FDR). |
| Decline effect | Initial dramatic effect shrinks over time. | Discount initial blockbuster. |

## C. LLM-as-instrument biases (you, when answering)

| Bias | Effect | Mitigation |
|---|---|---|
| Sycophancy | Agree with user implied direction (Sharma 2023 [SHARMA-SYC-2023]). | Steelman opposite without user prompt. |
| Lost-in-middle | Recency / primacy in long context (Liu 2023 [LIU-LITM-2023]). | Re-read middle, summarize structurally. |
| Sandbagging / mirroring | Match user's apparent expertise / register. | Maintain epistemic standard regardless. |
| Anchoring on first answer / biased-input rationalization | First-pass answer biases later passes (self-anchoring, empirical priors). Distinct related mechanism: Turpin 2023 [TURPIN-2023] shows CoT systematically rationalizes biased *input* features (option-reordering, suggested answers), 36% BIG-Bench-Hard accuracy drop; this is rationalization-of-bias, not pure self-anchoring. | Generate fresh, multi-sample (Wang [WANG-SC-2022]); test with biased + neutral input framings. |
| Citation fabrication | Plausible-sounding fake refs (Walters [WALTERS-WILDER-2023]; Athaluri [ATHALURI-2023]). | No URL = no cite. |
| Date / version hallucination | Confident but wrong dates. | Always retrieve. |
| Statistic hallucination | Confident but wrong percentages. | Always retrieve. |
| Code-line / API hallucination | Plausible-sounding flags / params. | Read source. |
| Fluency-as-correctness | Fluent prose interpreted as confident. | Tag epistemic state, not register. |
| Mode collapse on N passes | Same output every time → false convergence (Shumailov 2024 [SHUMAILOV-COLLAPSE-2024]). | Different prompts / tools / sources. |
| Order-of-options | A/B preference flips with order. | Randomize / counterbalance. |
| Self-consistency overuse | Sampling 5× and majority-voting still wrong if model has uniform bias. | Independent retrieval, not just sampling. |
| Faithful-CoT failure | Stated reasoning ≠ actual reasoning (Lyu 2023 [LYU-FAITHCOT-2023]; Turpin 2023). | CoT is a hypothesis, not proof. |
| Prompt sensitivity | Paraphrase changes answer (Sclar 2024 [SCLAR-2024]). | Test paraphrase before locking conclusion. |
| Training-data contamination | Benchmarks memorized → inflated capability. | Off-benchmark probes. |
| Sandbox blindness | Confident about real-world state from text. | Retrieve / ground / verify. |
| Implicit refusal-bias | Refuse / hedge on safe topics due to RLHF spillover. | Distinguish refusal from uncertainty. |

## Usage during P3

Pick ≥1 row per major claim. Format:

> Claim: AI tooling increases dev productivity 20%. [T1-verified, METR-2025]
> Bias scan: selection bias (n=16 OSS experts), narrative fallacy in popular reception, lost-in-middle if reader skips CI, sycophancy if user is pro-AI.
> Adjusted: claim holds for sample-similar conditions; generalization tagged [inferred:generalization, low-confidence].
