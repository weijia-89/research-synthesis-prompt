# LLM-as-instrument failure modes

Load every session, the self-instrument check is mandatory in P3.

The LLM running this skill **is the research instrument**. All instruments have known biases. Calibrate before using.

## Top failure modes, by domain

### Citations
- **Fabricated references**, plausible-looking author + year + title combos that do not exist. Walters & Wilder 2023 [WALTERS-WILDER-2023]: empirical study of ChatGPT bibliographic fabrication. Athaluri 2023 [ATHALURI-2023]: scientific citation hallucination measured.
- **Citation laundering**, real source, fake claim about it. The model has read the abstract / title and generates a plausible content summary that doesn't match the paper.
- **Year drift**, correct paper, wrong year (esp. ±2y around publication).
- **Author-list scrambling**, correct topic + venue, wrong author list.

**Mitigation:** No URL = no cite. Retrieve every cited paper or downgrade tag. Re-cite from primary, not from memory.

### Numbers
- **Statistic hallucination**, confident-sounding percentage where the underlying study uses different framing. Common in productivity / health / AI-adoption claims.
- **Effect-size confusion**, quoting the headline-grabbing version (relative risk) when the policy-relevant version is absolute risk, or vice versa.
- **Sample size hallucination**, confident "n=10,000" when actual is "n=160."

**Mitigation:** Numbers must cite file:line or URL. (Inherits from `review-rigor` S5/S8.)

### Dates / versions
- **Cutoff confusion**, claims about events near or past training cutoff stated with high confidence.
- **Version drift**, "as of 2024" when the source is from 2021.
- **Recency bias**, overweight the most recent training data even when older sources are authoritative (e.g. RFC).

**Mitigation:** Always retrieve dates. Tag `[stale:<date>]` if relevance window unclear.

### Names / API surfaces
- **API flag hallucination**, plausible-sounding `--enable-foo` flags / parameter names that do not exist.
- **Library function hallucination**, plausible-sounding methods that don't exist (slopsquatting risk for code generation; SLOP-arXiv 5.2% commercial / 21.7% OSS rate).
- **Person-attribution**, quote attributed to wrong author (esp. Twain / Einstein / Lincoln template).

**Mitigation:** Read source. Verify in package index for libraries. Verify in primary text for quotes.

## Self-instrument biases (the "you" in this skill)

| Mode | Description | Detection cue | Mitigation |
|---|---|---|---|
| Sycophancy [SHARMA-SYC-2023] | Adjust answer toward user's apparent view. | User used loaded language; you agreed quickly. | Steelman opposite without user prompt. |
| Lost-in-middle [LIU-LITM-2023] + non-literal long-context decay [NOLIMA-2025] | Privilege start/end of long input; on tasks without literal-match cues, accuracy collapses with length even at edges. NoLiMa body: 10/12 frontier models drop to ≤50% of base score at 32K; Llama 3.1 70B: 94.3%→42.7%. **Caveat**: NoLiMa is deliberately harder than RULER/BABILong (same Llama 3.1 70B retains 16-32K effective length there). Decay is real but workload-dependent; effective-length is a function of literal-match availability, not just token count. | Long retrieved doc; cited intro/conclusion only; long context with low literal overlap to question. | Force structural re-read of middle. Prefer focused/retrieved context over dumped context. Compress before stuff. |
| Anchoring | First-pass answer biases later passes. | Successive passes converge without new evidence. | Generate alternative answers fresh, blind to prior. |
| Order sensitivity / prompt sensitivity [SCLAR-2024], [LU-ORDER-2022] | Prompt formatting (whitespace, separators) changes answer; few-shot example order can flip near-SOTA to near-random performance. | Single-prompt single-answer; no formatting / order variation tested. | Run paraphrase + reformat + reorder test before locking. |
| Self-consistency mode collapse [SHUMAILOV-COLLAPSE-2024] | Sampling N times and voting can amplify uniform bias. | All samples agree; you treat as confidence. | Independent retrieval, not internal sampling. |
| Intrinsic self-correction on reasoning [HUANG-SELFCORRECT-2023], [STECHLY-SELFVERIF-2024] | Single-pass self-critique without external grounding reduces reasoning accuracy; the critique itself biases away from the often-correct initial answer. Distinct mechanism from sycophancy, anchoring, and mode-collapse. | Critique pass had no new retrieval, no tool, no oracle, no human-elicited context, no independent debate partner. | **Ask the user first** when human knowledge is the most authoritative external evidence available. Otherwise: tool, retrieval, multi-agent debate, or skip the critique. Self-consistency (Wang 2022) over independent samples is a separate technique and remains useful. |
| Faithful-CoT failure [TURPIN-2023] (diagnostic; [LYU-FAITHCOT-2023] is constructive mitigation) | Stated reasoning ≠ actual reasoning; CoT systematically rationalizes biased-input answers (Turpin: 36% accuracy drop on BIG-Bench Hard from biasing features). | CoT very fluent; conclusion happens to match priors or input ordering. | Treat CoT as hypothesis; verify each step. Faithful-CoT (Lyu) reformulates as symbolic reasoning + deterministic solver. |
| Fluency-as-correctness | User reads fluent prose as confident truth. | You wrote a smooth paragraph. | Tag epistemic state explicitly. |
| Refusal-spillover | Hedge on safe topics due to RLHF generalization. | You added "I cannot determine" on a verifiable claim. | Separate refusal from uncertainty. |
| Sandbagging / mirroring register | Adjust expertise level to user's apparent level. | You wrote casually for an expert or formally for a novice. | Maintain epistemic standard regardless of register. |
| Domain over-/under-confidence | Calibration varies by domain (high in code-with-tests, low in humanities citations). | You treated all claims with same confidence band. | Adjust per-domain prior. |
| Training-data contamination | Memorized benchmark / paper / quote. | You "know" the answer instantly. | Off-benchmark probe; verify primary. |
| Reasoning-bypass via pattern-match | Recognize question shape, emit canned answer. | Answer arrived too fast for the apparent reasoning. | Re-derive at least once. |

## When critique is cost-justified

Critique step is positive-EV only when the verifier's success criterion is hard to game from the inside ([FENG-VERIF-2024]). Verifier quality > generator quality on the target task is the operational threshold. The verifier does not need to be smarter than the generator overall ([BURNS-W2S-2023], read:abstract: GPT-2-level supervision + auxiliary confidence loss recovers close to GPT-3.5 performance on NLP tasks when finetuning GPT-4); it needs to be more reliable on the specific judgment.

- **Domains where critique helps:** math with proof check, code with executable tests, structured claims with retrievable sources, factual recall with grounded retrieval, claims the user can confirm or deny from their own context.
- **Domains where critique may not help or may hurt:** free-form reasoning without ground truth, opinion, style judgments, plans whose correctness depends on the same reasoning that produced them ([HUANG-SELFCORRECT-2023]).
- **Corollary on gameable verifiers:** a verifier whose success criterion the generator can satisfy without satisfying the underlying goal produces verifier-side collapse (reward hacking). Self-consistency over independent samples is more robust than single-pass critique when no external grounding exists.
- **Default elicitation rule:** when the verifier is uncertain and the human has access to ground-truth context (codebase state, prior decisions, undocumented intent, value tradeoffs), ASK before emitting. This is retrieval, not permission-fishing. See `SKILL.md` §P1 Human-elicitation pass.
- **Bare multi-agent debate is not better than self-consistency:** at matched sample count, the Du 2023 debate setup empirically underperforms self-consistency majority voting on GSM8K ([HUANG-SELFCORRECT-2023] §4). Multi-agent debate adds value only when agents bring asymmetric evidence (independent retrieval per role, role-specific prompts, ideally different model families). Without that asymmetry, debate is self-consistency with a fancier voting mechanism and worse efficiency. See `agentic-research.md` Pattern 2 for the conditions under which debate remains worth invoking.

## Hallucination rate by task, heuristic priors

(Empirical priors; tag `[inferred:meta-research-priors]` if cited.)

| Task | Hallucination risk |
|---|---|
| Code with executable verifier | Low |
| Math with proof check | Low–medium |
| Pattern matching / classification | Low |
| Summarization of provided text | Low if grounded; medium without grounding |
| Recent dates (≥2 years past training) | High |
| Bibliographic citations from memory | Very high (GPT-3.5 ~55%, GPT-4 ~18% full fabrication rate; ~24-43% of real citations carry substantive errors; [WALTERS-WILDER-2023], [ATHALURI-2023]. Model-version caveat: numbers are GPT-3.5 / GPT-4 era; later models may differ.) |
| Specific statistics from memory | Very high |
| Names of real people (esp. minor figures) | Medium–high |
| Library / package function signatures | Medium–high |
| Causal explanations of observed phenomena | Medium |
| Predictions about future | Calibration low; tag `[speculative]` |

## Required self-instrument check (every session)

Before emitting any L2+ output:

1. **Did I retrieve, or am I emitting from priors?** If priors-only on load-bearing claims, downgrade tags.
2. **Did the user imply a direction?** If yes, sycophancy check: would I have written this if they implied opposite?
3. **Citations:** can I produce the URL/DOI for every `[T*-verified]` tag right now?
4. **Numbers:** can I name the source line for every percentage / count / threshold?
5. **Dates:** are any dates within ±2y of training cutoff? If yes, retrieval required.
6. **Names:** any minor-figure attributions? Verify or downgrade.
7. **Self-consistency:** would a fresh pass with different framing give the same answer?
8. **Convergence-as-truth:** if multiple passes agreed, did they have access to *independent* evidence, or were they re-using the same priors?

Pass = output may emit. Fail any → fix or downgrade tag.

## Domains where this skill is most likely to fail

(See `failure-log.md` for tracked instances.)

- **Recent AI tooling research** (rotates every 6 months; training cutoffs lag).
- **Specific empirical statistics** (most LLMs hallucinate stats with high confidence).
- **Bibliographic citations** (canonical failure mode).
- **Litigation / case law** (jurisdiction + recency + outcome variations).
- **Medical specifics** (dosage, contraindications, drug interactions).
- **Fast-moving security threats** (CVEs, supply-chain incidents, see code-skills-overhaul Shai-Hulud chronology error).

These domains require retrieval + tier-1 source. No exceptions.
