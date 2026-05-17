# Replication & validity, empirical-claims toolkit

Load when: empirical claim, "study shows X," effect-size, RCT, statistical claim, "research suggests."

## The replication crisis, load-bearing facts

- **Open Science Collaboration 2015** [OSC-2015]: ~36% of psych studies replicated at original effect-size; mean effect size in replications was half the original.
- **Camerer et al. 2018** [CAMERER-2018]: 13 of 21 social-science *Nature* and *Science* findings replicated. Replicated effects were on average ~50% of original size.
- **Reproducibility Project: Cancer Biology** [RPCB]: of 53 high-impact preclinical cancer papers, only ~46% of effects could be reproduced; many couldn't even reproduce the methods.
- **Ioannidis 2005** [IOANNIDIS-2005]: positive predictive value (PPV) of a published finding depends on prior probability, statistical power, bias, and number of teams chasing the same hypothesis. For typical underpowered fields, **most published findings are false**.
- Worst-affected fields by replication rate: nutrition epidemiology, social psychology, candidate-gene studies, fMRI, behavioral economics (some), much of preclinical biomedicine.
- Best: physics (effects usually large + repeatable), parts of clinical medicine (preregistered RCTs, especially Phase III), parts of econometrics with natural experiments.

## Effect size, always demand

Significance ≠ size. A p<.001 with d=0.05 is statistically real and practically irrelevant.

| Measure | Use for | Sanity ranges |
|---|---|---|
| Cohen's d | Mean difference, continuous outcomes | 0.2 small, 0.5 medium, 0.8 large; <0.1 noise; >1.5 implausibly large for behavioral |
| Odds ratio (OR) | Binary outcome | 1.0 = null; <1.5 weak; >3 strong; >10 medical-grade strong |
| Risk ratio (RR) | Cohort, prospective | as OR but interpretable directly |
| r (Pearson) | Correlation | 0.1 small, 0.3 medium, 0.5 large in social sci; physics often >0.95 |
| Hazard ratio | Survival | as OR for time-to-event |

Always demand **CI**, not just point estimate. CI width signals power. A "95% CI: 0.85 to 4.2" is essentially uninformative.

## Sample scope, always demand

Generalization beyond sample = inference, not finding. Required scope items:
- N (and effective N if cluster / repeated measures).
- Population: who, where, when (especially when, context drift).
- Recruitment: convenience / random / stratified / WEIRD?
- Exclusions: pre- and post-randomization. Especially attrition pattern.

The METR-2025 case (cited heavily in `code-skills-overhaul/00-ASSESSMENT.md`) is canonical: n=16, experienced OSS devs, on their own large repos. Cannot generalize to junior devs, novel domains, or well-defined tasks.

## p-hacking / garden of forking paths

Even with no malice, **degrees of freedom in analysis** inflate false-positive rate (Simmons et al. 2011 [SIMMONS-2011]; Gelman & Loken 2013 [GELMAN-LOKEN-2013]).

Red flags:
- Analyses not preregistered.
- Multiple outcome measures, only "the one that worked" reported.
- Subgroup analyses presented without correction (Bonferroni, FDR, BH).
- "Marginally significant" (p < .10) treated as significant.
- Sample size not preregistered; "we collected until significance" (optional stopping).
- Outliers excluded post-hoc without prespecified rule.
- Covariates added in some analyses but not others.
- Direction of effect "predicted" only after results known (HARKing, Kerr 1998 [KERR-1998]).

For each empirical claim, ask: **could a researcher with no malice arrive at this number through any of the forking paths above?** If yes → confidence reduction.

## Multiple comparisons

Running 20 tests at α=.05 → expected ~1 false positive. Bonferroni divides α by k. False discovery rate (Benjamini-Hochberg) controls expected proportion of false discoveries among rejected nulls. **No correction = expect false positives proportional to number of tests run.**

## Funder / COI bias

Industry-funded research systematically tilts toward sponsor (Lundh et al. Cochrane methodology review, multiple updates). Mechanism: study selection, design, outcome selection, framing. Do not treat industry-funded effect sizes as ground truth without independent replication.

`[T1-verified, COI:<funder>]` is the correct tag form.

## Publication bias / file-drawer

Null and small-effect studies are systematically less likely to be published. Funnel plot asymmetry, Egger's test, trim-and-fill, p-curve are diagnostics. For meta-analyses, ask: was a funnel plot or PET-PEESE adjustment performed? If not, the meta-analytic point estimate is biased upward in magnitude.

## Decline effect

Effect sizes for the same hypothesis tend to shrink over time as more independent replications accumulate (Schooler 2011; Lehrer 2010 New Yorker piece; consistent with regression to true effect plus publication-bias dynamics). Recent giant effect from a single lab → expect shrinkage.

## Causal vs. correlational

See `references/causal-inference.md`. Most observational claims are not causal even when the press release says "linked to."

## Bradford Hill criteria, when observational evidence may support causation

[BRADFORD-HILL-1965]: not a checklist for proof, but a heuristic for *plausibility*:
1. **Strength** of association.
2. **Consistency** across studies, populations, designs.
3. **Specificity** of association.
4. **Temporality**, cause precedes effect.
5. **Biological / mechanistic gradient**, dose-response.
6. **Plausibility** of mechanism.
7. **Coherence** with existing knowledge.
8. **Experiment**, can the cause be manipulated?
9. **Analogy** to similar known causes.

Hill himself warned against treating these as required individually, they are evidence aggregators.

## Type-I / Type-II / Type-III / Type-IV errors

- **Type I:** false positive, reject true null.
- **Type II:** false negative, fail to reject false null. Power = 1 − β. Underpowered studies that find nothing tell you nothing.
- **Type III:** correctly answering the wrong question. (Mosteller 1948 / Kimball 1957.) Often the most consequential error in applied research.
- **Type IV:** correct answer to right question, applied wrongly in policy / decision.

For each empirical claim consumed: which error type is most likely?

## Quick triage for empirical claim

When you see "study shows X":
1. **Find the study** (citation laundering, see `source-grading.md`).
2. **Effect size + CI?** If only p-value, downgrade.
3. **Sample scope match?** Generalizing? Tag `[inferred:generalization]`.
4. **Preregistered?** If not, multiply forking-path skepticism.
5. **Replicated independently?** If not, halve effect-size in your prior.
6. **Funded by interested party?** Adjust toward null.
7. **Recent or stale?** Decline-effect prior.
8. **Statistical or substantive significance?** Different things.
