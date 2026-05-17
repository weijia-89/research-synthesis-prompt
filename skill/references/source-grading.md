# Source grading, tier criteria + provenance audit

Load when: tier dispute, new domain, source provenance question, primary-vs-secondary unclear.

## Tier definitions

### T1, Primary research / canonical / standards body
**Criteria (any one qualifies):**
- Peer-reviewed empirical paper with original data or analysis (RCT, cohort, meta-analysis, replication study).
- Standards body publication: W3C TR, IETF RFC, NIST SP, ISO, ANSI, OWASP project doc, MITRE CWE/CVE.
- Government statute / regulation / official agency report (CDC, FDA, ECDC, BEA, BLS, Eurostat).
- Original dataset with documented provenance (Eurostat, OECD, World Bank, US Census).
- Canonical work in the field (textbook in widespread course-adoption use; foundational paper despite age).
- Vendor authoritative spec for that vendor's product (AWS docs for AWS APIs, Stripe docs for Stripe API).

**Disqualifiers:**
- Press release derived from a paper (cite the paper, not the press release).
- Preprint without independent replication (acceptable but tag `[T1-verified, preprint]`).
- Industry-funded research with disclosed COI must be flagged: `[T1-verified, COI:<funder>]`.
- Retracted papers, never cite, even with retraction note. Cite the retraction notice itself if retraction is the point.

### T2, Credible secondary
**Criteria:**
- Reputable journalism with named author + linked primary sources (NYT, WSJ, FT, Reuters, AP, BBC, Wired, Ars Technica with primary linkage).
- Established expert blog with track-record + verifiable identity (e.g. Krebs on Security, Stratechery, Andy Matuschak's notes, Schneier).
- Established vendor blog with named engineer authors (Stripe Engineering, Cloudflare Blog, Anthropic Research, OpenAI Research).
- Widely-cited textbook in non-canonical state (newer textbook adopted in <50 known courses).
- Encyclopedia entry with citations (Wikipedia counts as T2 when its citations themselves are T1; cite the T1 directly).

**Disqualifiers:**
- No author byline.
- No primary source linked / citable.
- Author has track record of corrections without correction notes.

### T3, Tertiary / forum / unverified
**Criteria:** anything that doesn't qualify above. Forum post, anonymous blog, single tweet, Reddit thread, ChatGPT output, conversation transcript, screenshot.

**Use only with explicit downgrade note in citation.** Do not aggregate T3 sources into a `[T1-verified]` claim by counting agreement.

## Provenance audit checklist

Before citing any source:

1. **Author identifiable?** Name + affiliation + ORCID/Google Scholar link. If not, T2 max.
2. **Primary source available?** "Press release about study" → fetch the study. "Blog about RFC" → fetch the RFC. Citation laundering is the most common failure here.
3. **Funding / COI declared?** For empirical claims, check funding statement. Industry-funded studies systematically favor sponsor (Lundh et al. 2017 Cochrane methodological review; cite that meta-analysis when caveating). `[verified, COI:Pfizer]` not `[verified]`.
4. **Date appropriate?** Relevance window: AI tooling 6–24mo, security threat-intel 30 days, replication crisis evergreen, mathematics evergreen, medicine 5–10y for non-fast-moving topics.
5. **Retraction / correction check?** PubPeer, Retraction Watch, journal errata page. For high-stakes claims this is non-optional.
6. **Citation count + replication count.** A 2010 paper cited 5000× but never replicated is *less* trustworthy than a paper cited 200× with 3 successful replications.
7. **Domain norms.** Physics: replication implicit, cite-counts trustworthy. Nutrition / social psych: cite-counts misleading without replication evidence (cf. OSC-2015).

## Industry-funded research caveat (load on health / energy / tech / nutrition / pharma research)

Industry funding shifts effect sizes toward sponsor in the empirical literature. Mechanism: funder selection of which studies to publish, study design choices, outcome selection, statistical analysis choices. (Lundh et al., Cochrane Methodology; Bes-Rastrollo et al. on nutrition and beverage industry funding.)

When citing industry-funded work:
- Tag `[T1-verified, COI:<funder>]`.
- Adjust effect-size estimate toward null by ~20–40% as a heuristic prior. (No precise correction factor exists, flag as `[inferred:meta-research-priors]`.)
- Look for independent replication.

## "Citation laundering" pattern

A claim travels: paper → press release → news article → blog → tweet → another blog → eventually cited as fact. Each hop drops caveats and amplifies the headline. Trace upward to T1, or downgrade tag.

Common laundering targets in tech/AI:
- "Studies show AI improves productivity by X%", usually traces to a vendor blog about a single survey, or to a single RCT with narrow scope (METR-2025: n=16 experienced OSS devs on familiar repos; cannot generalize to junior devs / novel domains).
- "75% of AI code has bugs", often unsourced or single-vendor sample.
- "ChatGPT scored top X% on Y exam", often training-set contamination or unrepresentative test format.

## Pre-2020 vs. post-2023 caveat

Models trained pre-2023 lack post-cutoff knowledge but also lack post-cutoff *contamination*. Models trained post-2023 may have memorized common benchmarks and citations, increasing surface-plausibility of fabrications. Both regimes hallucinate, in different shapes.
