# Agentic research, worktrees, harnesses, parallel branches, RAG, debate

Load when: parallel hypothesis exploration, harness/budget constraints, RAG strategy, multi-agent debate, large search space.

## Why this exists

A research skill running inside an agentic harness has tools the v1 predecessor skill (then called `ai-research`) ignored: parallel branches, retrieval pipelines, search-tree expansion, debate-as-verifier. Single-thread linear loops underuse the harness.

## Pattern 1, Worktree fan-out for hypothesis exploration

When the question has ≥2 plausible framings or answers and stakes are L3+:

```
git worktree add ../research-h1 main
git worktree add ../research-h2 main
git worktree add ../research-h3 main
```

In each worktree, instruct one sub-pass to pursue **one** framing/hypothesis to its strongest conclusion. Then compare:
- Which framing produced the most disconfirmable, sourced output?
- Which converged on the same primary sources (signal of robustness)?
- Which diverged (signal of contested / underdetermined question)?

Reference: Anthropic Claude Code worktree pattern [ANTHROPIC-WORKTREE]; Aider [AIDER-WORKFLOWS] for git-aware iterative editing.

**Key:** worktrees give *independent priors per branch*. Without independence, parallelism = mode-collapse amplifier.

## Pattern 2, Multi-agent debate (when worktrees overkill)

For L3 claims where "should I trust this?" matters:

1. **Prosecutor**: argue the claim is wrong / weakly supported. Find the strongest disconfirming evidence.
2. **Defender**: argue the claim holds. Find the strongest supporting evidence.
3. **Judge**: weigh prosecutor + defender on shared evidence base. Output verdict + remaining uncertainty.

Each role gets its own retrieval pass with role-aligned queries. References: Du et al. 2023 [DU-DEBATE-2023]; Khan et al. 2024 [KHAN-DEBATE-2024]; Irving et al. 2018 [IRVING-DEBATE-2018].

**Failure mode (Huang 2023 §4):** bare multi-agent debate at N agents empirically *underperforms* self-consistency at N samples on GSM8K when all roles share model and context ([HUANG-SELFCORRECT-2023] §4, replicating Du et al. 2023 setup with 3 agents, 2 rounds, on the full test set). Direct quote: "rather than labeling the multi-agent debate as a form of 'debate' or 'critique', it is more appropriate to perceive it as a means to achieve 'consistency' across multiple model generations." The value of debate, if any, comes from asymmetry across roles (independent retrieval per role, different prompt templates, ideally different models), NOT from the critique mechanism. Without that asymmetry, prefer self-consistency over majority vote: fewer model calls, simpler attribution. **Counterpoint:** Khan et al. 2024 ([KHAN-DEBATE-2024], read:abstract) studies an *asymmetric* setup where stronger models ("experts") possess information the judge lacks and argue for opposing answers; weaker non-expert judges then select the answer. In that setup, debate yields 76% accuracy for non-expert model judges (88% for humans) versus 48-60% naive baseline; optimising debaters for persuasiveness *increases* the judge's truth-finding. The asymmetry across roles is the engine. When arguers and judge sit at the same capability with no information differential, Huang §4's negative result still applies.

## Pattern 3, Tree / Graph of Thoughts for decomposable questions

For multi-step problems (literature review with sub-questions, comparative analysis, decision tree):

- **ToT** [YAO-TOT-2023]: branch on subgoals, score each branch, prune.
- **GoT** [BESTA-GOT-2023]: allow merging across branches; useful when sub-answers feed each other.
- **Self-Refine** [MADAAN-SELFREF-2023]: generate, critique, revise loop. Works on subjective-quality / human-preference tasks (dialog, code style) where verifier-asymmetry holds.
- **Reflexion** [SHINN-REFLEXION-2023]: verbal critique stored as memory across episodes. Works *because* HumanEval has an executable verifier; consistent with the Feng/Huang verifier-asymmetry principle (`llm-failure-modes.md` § *When critique is cost-justified*).

**Verifier-asymmetry warning:** Self-Refine and Reflexion gains do NOT transfer to verifier-symmetric tasks (free-form reasoning, opinion, plans whose correctness depends on the same reasoning that produced them). Huang 2023 [HUANG-SELFCORRECT-2023] shows critique on those tasks degrades accuracy. Before applying these patterns, ask: *is there an external verifier (test, proof, retrievable fact, human ground-truth) that scores the candidate output independently of the generator?* If no, skip the critique step or substitute self-consistency (Wang [WANG-SC-2022]) over independent samples.

Default for `palamedes`: depth ≤3 ToT. Deeper search rarely worth token cost vs. better retrieval.

## Pattern 4, Retrieval strategies (when web/file search is the bottleneck)

| Strategy | When |
|---|---|
| Direct keyword search | Known terminology, specific source. |
| Hybrid retrieval (BM25 + dense) | Fuzzy / semantic match needed. |
| HyDE [GAO-HYDE-2022] | When you can imagine the ideal source's text but don't know its keywords. |
| Query decomposition (Self-Ask, ReAct [YAO-REACT-2022]) | Multi-hop questions ("which CEO of X attended Y?"). |
| FLARE [JIANG-FLARE-2023] / IRCoT [IRCOT-2022] | Iterative retrieval driven by partial generation. IRCoT: up to 21pt retrieval / 15pt QA gain on HotpotQA / MuSiQue / IIRC / 2WikiMultihopQA. |
| Re-ranking with cross-encoder | Many results, need top-3. |
| DSPy-style compilation [KHATTAB-DSPY-2023] | Repeated research workflows worth optimizing. |

Default: hybrid + re-rank. Most "the LLM didn't find it" is failure of query, not of corpus.

## Pattern 5, ReAct-style tool-use loop

For research that requires multiple tools (file read, grep, web fetch, calculation):

```
Thought: <state goal of next action>
Action: <tool call>
Observation: <result>
Thought: <interpret>
... repeat ...
Final: <answer with cites>
```

Anti-patterns:
- Hidden Thought (silent reasoning when stakes high, auditability lost).
- Action-without-Thought (greedy tool-spam).
- Thought-without-Action (rumination loop).

References: Yao et al. ReAct [YAO-REACT-2022].

## Pattern 6, Adversarial collaboration / pre-mortem

Before committing to a recommendation:

- **Pre-mortem** [KLEIN-PREMORTEM]: imagine the recommendation went catastrophically wrong. Why? Each plausible failure → confidence reduction or mitigation.
- **Adversarial collaboration**: with the user (or another agent), agree on what evidence would change each side's mind, then look for that evidence specifically. (Mercier & Sperber argumentative theory; Kahneman late-career interest.)

## Harness awareness, what your runtime can / cannot do

Map this in P1:

| Capability | Implication if available | Implication if absent |
|---|---|---|
| Web fetch / browsing | Tier-1 retrieval feasible. | Heavy reliance on `[priors-only]`; tag accordingly. |
| File read / write | Auditable artifact. | Conversation transcript only; less durable. |
| Subagent / parallel task | Real adversarial pass possible. | Serialize; tag `[self-verified]`. |
| Long context (>100k) | Long-doc retrieval. | Chunk + retrieve + summarize. |
| Tool budget (turns / tokens) | More passes affordable. | Aggressive prioritization. |
| Memory persistence | Calibration over sessions. | Each session cold-start. |
| Verifier (code exec, math eval) | Low-hallucination domain. | Reasoning-only on verifiable claims. |

State the harness profile at session start when stakes ≥ L3.

## Eval / benchmark literacy

If the user asks "is benchmark X meaningful for Y?":

- Check training contamination, was X in pretraining? SWE-bench [SWE-BENCH-2023] has known contamination concerns per follow-up work — note: that meta-claim is `[inferred:meta-research]` (from SWE-bench Verified by OpenAI 2024 and repo-level analyses, not from the original Jimenez et al. paper). Held-out splits matter.
- Check distribution match, does X resemble production traffic?
- GAIA [GAIA-2023], τ-bench [TAU-BENCH-2024], MLE-bench [MLE-BENCH-2024], METR [METR-2025] are agentic-task benchmarks; results lag production speed by 6–18 months `[inferred:author-heuristic]` (this lag-estimate is the skill author's; not from the benchmark papers themselves). METR's empirical doubling-time finding (~7 months for 50%-success task length) gives the closest grounded estimate.
- "SOTA on X" is rarely transferable to your domain unless X resembles your domain.

## Refusal: when not to use this skill

- One-line factual question → direct answer; don't load the full loop.
- User explicitly asks for vibes / brainstorming → loop suppresses generativity; offer skill bypass.
- Question fits a more specific skill (`review-rigor` for code review; `epistemic-planning` for plan drafts; `vibe-check` for AI-PR detection). Defer.
