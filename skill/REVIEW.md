# Adversarial review log, `palamedes` skill (formerly `ai-research`)

This file records the brutal adversarial reviews that produced the current skill. Append-only audit trail.

## 2026-05-14, Pass 1 review

**Reviewer persona:** experienced research methodologist + AI agentic-tooling practitioner (per user request).

**Subject:** original `SKILL.md` (54 lines, 5-step "Validate / Dialectic / Unknowns / Refine / DK guard" loop).

### Verdict
The skill was **prose-hygiene with a 5-step ritual stapled on**. None of the actual machinery of empirical research was present.

### Critical gaps (Pass 1)

1. No source-quality hierarchy / tier system.
2. Replication crisis lens absent, no effect-size + CI requirement, no preregistration, no p-hacking awareness, no Ioannidis 2005 PPV math, no Nosek/OSC 2015 replication base rates.
3. Bias taxonomy missing, no cognitive / research-design / LLM bias catalog.
4. Causal inference absent, no DAGs, confounder/mediator/collider, Bradford Hill, Quine–Duhem.
5. Statistical literacy absent, no base-rate, Simpson, Berkson, Goodhart, calibration, multiple-comparisons.
6. "Steelman opposite" undercooked, no Lakatos, Kuhn, Quine–Duhem.
7. "Diminishing returns" unmeasured, no info-gain proxy.
8. LLM-as-instrument awareness entirely missing, no sycophancy, lost-in-middle, fabricated cites, prompt sensitivity.
9. Citation discipline weakly enforced, no URL/DOI requirement, no read-the-body rule, no primary-source registry.
10. Tags `[verified|inferred|speculative|unknown]` insufficient, no tier distinction, no read-this-session distinction, no priors-only.
11. No worktree / parallel-branch / debate / RAG / harness awareness.
12. No version awareness for fast-moving fields.
13. Three near-identical copies of the skill, drift hazard. Canonical was *less complete* than sibling.
14. No worked example.
15. No failure log.
16. "Silent loop" mandate in tension with auditability.
17. Trigger "incorporate" misplaced.
18. "DK guard" invokes contested-effect named after artefact-prone study.
19. "Stop when passes 2–3 converge" rewards mode collapse / sycophancy.
20. No question-type routing.
21. No pre-registration analog.
22. No many-analyst caveat.
23. No Type-III error guard.
24. Anti-pattern list missing motte-and-bailey, isolated-rigor, selective skepticism, Galileo gambit, Texas sharpshooter, citation laundering.

### Pass 1 fixes applied

- **Rewrote `SKILL.md`**, 11 sections, question-type routing, P1–P4 loop with operationalized steps, 10-tag system, citation protocol, LLM-as-instrument check section, output schema reference, anti-sprawl, host-shape adapter, install + sync notes, skill-meta with known limits.
- **Created `REFERENCES.md`**, primary-source registry mirroring `code-skills-overhaul/REFERENCES.md` pattern, populated with ~40 named primary sources across replication, causal inference, calibration, bias, LLM failure modes, agentic patterns, harness benchmarks. All flagged `unverified-this-session` until retrieved.
- **Created `references/source-grading.md`**, T1/T2/T3 criteria, provenance checklist, citation-laundering pattern, industry-funder caveat.
- **Created `references/replication-and-validity.md`**, repcrisis facts, effect-size requirements, sample-scope, p-hacking / forking paths, multiple comparisons, Bradford Hill, Type I/II/III/IV.
- **Created `references/bias-catalog.md`**, cognitive + research-design + LLM-instrument biases (~70 entries).
- **Created `references/causal-inference.md`**, DAGs, identification strategy strength order, Bradford Hill, Quine–Duhem, mechanism-vs-effect, reference-class problem.
- **Created `references/llm-failure-modes.md`**, citation / number / date / name fabrication, self-instrument biases (sycophancy, lost-in-middle, anchoring, faithful-CoT failure, etc.), per-domain hallucination priors, mandatory self-instrument checklist.
- **Created `references/agentic-research.md`**, worktree fan-out, multi-agent debate, ToT/GoT, retrieval strategies, ReAct, pre-mortem, harness awareness, eval / benchmark literacy, refusal conditions.
- **Created `references/output-schemas.md`**, stake-tier rigor floors, full report schema, host-shape adapter, length discipline.
- **Created `references/confidence-calibration.md`**, when to attach a number, Brier, reference-class forecasting, what-would-change-my-mind, common forecasting pathologies, many-analyst caveat, aggregation across sources.
- **Created `references/examples.md`**, two worked examples (empirical claim with citation laundering; predictive question with version sensitivity), each with bad-output / good-output contrast and failure-mode call-outs.
- **Created `references/failure-log.md`**, 10 bootstrap failure modes documented; format for production observations.

---

## 2026-05-14, Pass 2 review (of revised skill)

### Critical gaps found in Pass 1 rewrite

- **S1 (most critical):** `REFERENCES.md` shipped pre-populated with ~40 entries all marked `unverified-this-session`. The skill enforces "no URL = no `[T1-verified]` tag" but its own registry violated this, replicating the citation-laundering pattern the skill exists to prevent.
- **S5:** "Read" was binary; skill didn't distinguish skim / abstract / body / full. Most empirical-citation failures aren't fabrication, they're "read the abstract, got direction right, magnitude / scope wrong."
- **S6:** Stakes ladder asserted but not operationalized. Who decides L4? User can understate stakes on a health/legal question.
- **S9:** Adversarial-collaboration weak. Steelman ≥1 paragraph good, but didn't require the steelman to produce *different evidence requirements*, without that, it's rephrasing.
- **S11:** Mode-collapse warned about, no detection heuristic.
- **S13:** "Load-bearing" + "decision-changing" used heavily, undefined.
- **S14:** 24-item anti-pattern list unprioritized, equivalent to no priority.
- **S15:** Refusal conditions (when NOT to invoke) buried in `agentic-research.md`, not surfaced in `SKILL.md`.
- **S16:** No invocation marker; user can't tell if skill loaded.
- **S17:** `[contested]` tag present, no aggregation rule for output.
- **S19:** No version / changelog despite skill being under active development.
- **S21:** No protocol for user-asserted evidence (paste, screenshot, PDF excerpt).
- Plus 12 minor issues (S2–S4, S7, S8, S10, S12, S18, S20, S22, S23, S24).

### Pass 2 fixes applied

- **`REFERENCES.md` rewritten.** Now starts empty with author-year hint section, populated lazily on first use. Reference docs use author-year form (not pre-allocated tags). Audit invariants block added.
- **`SKILL.md` updated** with:
  - Definitions block (load-bearing, decision-changing, stakes, read-depth, independent retrieval, mode collapse).
  - §0 invocation preamble (forced 1-line preamble committing to type + stakes + budget).
  - §0 stakes-recognition heuristic (auto-classification overrides user understatement, "use higher").
  - §0 refusal conditions (when NOT to invoke).
  - §0.1 question-type routing extended (Methodological + Synthetic types, false-premise guard).
  - P2 read-depth tagging mandatory; `[user-asserted]` tag introduced.
  - P3 steelman must produce different evidence requirements; falsifier in operational form ("if I observed X, confidence drops from N to M"); L3+ debate pattern wired; mode-collapse detection (overlap > 50% downgrades aggregate confidence).
  - Stop conditions per stake-tier with operational halt rule (3-of-3, not 2-of-3).
  - §2 tag table updated: `read:<depth>` mandatory in T1/T2 tags; `[user-asserted]` added; stale relevance windows specified per domain.
  - §2 `[contested]` aggregation rule with output schema (Position A / Position B / weighting / decision-rule for user).
  - §8 anti-patterns prioritized: top-3 absolute / major / minor-hygiene.
  - §11 known-limits expanded.
  - §12 version (v2.0.0) + changelog policy.

---

## 2026-05-14, Pass 3 review (final hardening)

### Remaining issues

- **S22 (carryover):** reference docs still mix author-year form with bracketed shorthands like `[SHARMA-SYC-2023]` in body text. After Pass 2's REFERENCES.md rewrite, those brackets look like un-registered claim tags. Decision: leave as **registry-tag-shorthands** (forward-link to "if registered, this is the proposed tag"), document the convention.
- **P3-1:** `read:title` is a defined depth but probably never sufficient for any verified tag, should be tagged separately as `[priors-only, title-checked]` or removed from the depth ladder.
- **P3-2:** Skill assumes the host has any retrieval at all. For pure-LLM hosts (no tools), the skill must fall back gracefully, currently §0 budget line is the only nod to this. Could be sharper.
- **P3-3:** No explicit pointer to `code-skills-overhaul/REFERENCES.md` pattern as the user's own reference for citation discipline. Cross-reference would help future skill maintenance.
- **P3-4:** `examples.md` example outputs cite tags (`[T1-verified, METR-2025]`) but those aren't in REFERENCES.md (post-Pass-2 the registry is empty). The examples are *illustrative*, should add a header note clarifying that the cites in examples are demonstrative-only and not meant to populate the registry.
- **P3-5:** No top-of-file "what to read first" pointer. A new user opening the directory needs an entry point. README would help.
- **P3-6:** `~/.claude/skills/palamedes/SKILL.md` and the Cursor `.mdc` mirror are still v1. Drift documented but not resolved.
- **P3-7:** No script / test that the skill self-consistency checks pass (e.g., every `[T*-verified]` in any skill artifact has a corresponding REFERENCES.md row). Manual discipline only.

### Pass 3 fixes applied

- Add registry-tag-shorthand convention note to REFERENCES.md.
- Drop `read:title` from valid depths for verified tags (still valid for `[priors-only]` annotation).
- Add `examples.md` header note: "cites in examples are demonstrative-only".
- Create `README.md` in `ai-research/` as entry point with reading order + map.
- Note: `~/.claude/skills/palamedes/SKILL.md` and Cursor `.mdc` need sync; flag in README.

---

## 2026-05-14, Closing notes

- **Three passes complete.** Skill went from 54 lines / 5-step ritual to 12-section SKILL.md + REFERENCES.md registry + 9 reference docs + examples + failure log + REVIEW audit trail + README.
- **Bar matches user's `code-skills-overhaul` standard:** tiered citations, falsifiable thresholds, named primary sources, self-aware verification debt management.
- **Honest residual gaps:**
  - The skill is now testable in principle but not yet tested in production.
  - Reference docs use author-year cites that are not yet retrieved this session, they're scaffolding, not ground truth.
  - Mirrors at `~/.claude/skills/` and Cursor `.mdc` are stale.
  - No automated self-consistency check exists.
- **Next steps (deferred):** invoke skill on a real research question to surface unmodeled failure modes; sync mirrors; consider a self-consistency lint script.
