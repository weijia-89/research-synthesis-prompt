# Failure log, known prior failures of this skill class

Load every session at start. Scan for prior traps in the current question's domain.

## Format

Each entry:
```
### YYYY-MM-DD, short title
- **Domain:** ...
- **Failure mode:** ...
- **Surface symptom:** ...
- **Root cause:** ...
- **Fix applied to skill:** ...
- **How to avoid in future:** ...
```

Append-only. Update existing entries with `## Update YYYY-MM-DD` sub-section.

---

## Bootstrap entries (failure modes documented in skill design, not yet observed in production)

### BOOT-1, Citation laundering ("studies show")
- **Domain:** any empirical claim domain.
- **Failure mode:** LLM emits "studies show X" with no traceable primary source.
- **Surface symptom:** `[T1-verified]` tag on a claim where the URL traces to a press release or another secondary, not a paper.
- **Root cause:** training-data exposure to repeated headlines without grounding in primary.
- **Fix applied:** P2 retrieval rule, every cite resolves to `REFERENCES.md` row with URL/DOI; no URL = no `[T1-verified]`.
- **How to avoid:** before tagging T1, retrieve URL this session.

### BOOT-2, Date / version drift
- **Domain:** AI tooling, security threat-intel, vendor APIs, regulations.
- **Failure mode:** confident claim about a recent date / version where source predates relevance window.
- **Surface symptom:** "As of 2024…" written in 2026.
- **Root cause:** training cutoff bias + recency illusion.
- **Fix applied:** `[stale:<date>]` tag; relevance windows in `source-grading.md`; retrieval before date claims.
- **How to avoid:** every numeric date or version → retrieve.

### BOOT-3, Convergence-as-truth across self-consistent passes
- **Domain:** any.
- **Failure mode:** N passes agree → reported as high confidence; passes shared priors / context.
- **Surface symptom:** "I ran two passes and both confirmed X" without independent retrieval.
- **Root cause:** mode collapse (Shumailov 2024); shared-prior collusion.
- **Fix applied:** stop-condition language, convergence ≠ truth; truth signal is independent retrieval agreement.
- **How to avoid:** for L3+ confidence, ≥2 independent retrievals from different sources / queries / tools.

### BOOT-4, Sycophancy toward implied direction
- **Domain:** any user-with-stake question.
- **Failure mode:** answer leans toward direction implied by user prompt phrasing.
- **Surface symptom:** "I want to write a positive memo on X" → answer disproportionately positive.
- **Root cause:** RLHF + helpful-assistant training (Sharma 2023).
- **Fix applied:** P3 sycophancy check; steelman opposite required regardless of user lean.
- **How to avoid:** test "would I have written this if user implied opposite?"

### BOOT-5, Single-source confidence inflation
- **Domain:** statistics, percentages, effect sizes.
- **Failure mode:** one citation supporting a number → high confidence emitted.
- **Surface symptom:** `p = 0.85` from one paper.
- **Root cause:** under-update on independent-replication need.
- **Fix applied:** L3 stake-tier requires ≥2 independent retrievals; aggregation rules in `confidence-calibration.md`.
- **How to avoid:** before emitting numeric confidence ≥ .8, name ≥2 independent T1 sources.

### BOOT-6, Generalization beyond sample
- **Domain:** social science, AI productivity claims, medical.
- **Failure mode:** finding from narrow sample stated as universal.
- **Surface symptom:** "AI tools improve productivity" from n=16 OSS-experienced-devs sample.
- **Root cause:** sample-scope omission; press-release framing.
- **Fix applied:** `replication-and-validity.md` checklist; `[inferred:generalization]` tag.
- **How to avoid:** state sample scope; tag generalization explicitly.

### BOOT-7, Berkson / collider conditioning
- **Domain:** observational empirical claims.
- **Failure mode:** "even after controlling for X" → spurious association produced by collider adjustment.
- **Surface symptom:** "controlling for hospitalization, smoking is protective."
- **Root cause:** DAG not specified; adjustment-as-purification fallacy.
- **Fix applied:** `causal-inference.md` DAG checklist; backdoor criterion.
- **How to avoid:** for any "controlled for" claim, sketch DAG.

### BOOT-8, Fluent prose mistaken for confidence
- **Domain:** any.
- **Failure mode:** smooth narrative read as well-supported.
- **Surface symptom:** report flows readably; tags / cites sparse.
- **Root cause:** style ≠ substance; user under-attends to absence of tags.
- **Fix applied:** "epistemic tags survive every output-shape transformation. Drop sections, never tags." (`output-schemas.md`).
- **How to avoid:** every load-bearing claim has a tag, or it doesn't ship.

### BOOT-9, DK label substituting for calibration
- **Domain:** any self-assessment.
- **Failure mode:** "DK guard" cited as if applying the named effect; actual calibration absent.
- **Surface symptom:** "DK-guarded" in skill but no Brier / calibration check.
- **Root cause:** named effect treated as procedure; effect itself partly statistical artefact (Nuhfer; Gignac 2020).
- **Fix applied:** renamed to "calibration check"; numeric calibration tracking in `confidence-calibration.md`.
- **How to avoid:** track Brier offline; never invoke named effect without measurement.

### BOOT-10, Skill-mirror drift
- **Domain:** the skill itself, not output.
- **Failure mode:** multiple copies of the `palamedes` skill (the canonical repo directory, the Claude global mirror, and a workspace sibling) plus a Cursor `.mdc` mirror; copies drifted apart.
- **Surface symptom:** the canonical was once *less complete* than a sibling that had picked up file-policy / anti-sprawl rules the canonical lost.
- **Root cause:** no sync mechanism; manual copy.
- **Fix applied:** explicit "single source of truth" declaration in SKILL.md §10; recommend `skill-sync` skill.
- **How to avoid:** sync on every edit; canonical = the repository root directory.

---

## Production-observed failures (append as encountered)

(None yet, appended to as the skill is exercised.)
