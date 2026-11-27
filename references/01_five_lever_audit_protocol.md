# Five-Lever Audit Protocol

Evaluate every target URL or draft against all 5 levers below. Assign one deterministic rating per lever:
- `PASS`: Verified against observable evidence and meets all criteria.
- `NEEDS_WORK`: Verified present/accessible, but structural weaknesses limit retrieval or extraction.
- `AT_RISK`: Verified blocker or severe structural defect prevents crawl, indexing, canonical consolidation, or safe extraction.
- `UNVERIFIED`: Cannot be observed from supplied inputs (e.g., live `robots.txt`, WAF behavior, or `sitemap.xml` on an offline draft). Never guess or upgrade `UNVERIFIED` to `PASS`.

---

## Lever 1: Freshness, Recency & Date-Triad Alignment

### What to Check
Inspect all three timestamp surfaces and confirm they agree:
1. **Visible page timestamp**: `"Updated [Month Day, Year]"` or `"Last reviewed [Month Day, Year]"` near the top of `<main>` / `<article>`.
2. **Structured data timestamp**: `dateModified` (and `datePublished`) in `Article` or `WebPage` JSON-LD.
3. **XML Sitemap timestamp**: `<lastmod>` entry in `sitemap.xml` for the exact canonical URL.

### Page-Class Recency Thresholds (Recency-Sensitive vs Evergreen)
Do not apply a blanket 30-day rule to every page:
- **Recency-Sensitive Commercial, Pricing, Comparison & News Pages**:
  - Target `< 33 days` since last substantive update (matching `kirby-aiseo-skill` Module 2 §2.50 pricing verification threshold).
  - `PASS`: Date-triad aligned and `< 33 days` old with verifiable current pricing/specs/facts.
  - `NEEDS_WORK`: Target is `33-60 days` old on fast-moving offers/comparisons, OR visible date and JSON-LD `dateModified` agree (`< 33 days`) while `sitemap.xml` `<lastmod>` is missing or lagging.
  - `AT_RISK`: `> 60 days` stale on pricing/comparison pages OR direct contradiction between visible date and JSON-LD `dateModified`.
- **Evergreen Educational, How-To Guides, & Entity/Trust Hubs**:
  - Practitioner benchmark observations (directional, unverified) suggest established pages in the `30-89 day` window often outperform brand-new `< 30d` churned pages on evergreen queries.
  - Target substantive `60-90 day` review cycles; only update dates when adding genuine new substance.
  - `PASS`: Date-triad aligned, `<= 90 days` since substantive review, accurate facts.
  - `NEEDS_WORK`: Date-triad mismatch or `91-180 days` without substantive review, OR `sitemap.xml` `<lastmod>` missing/lagging while visible date and JSON-LD agree.
  - `AT_RISK`: `> 180 days` without substantive review, OR outdated claims/fake date-bumping at any age, OR direct contradiction between visible date and JSON-LD `dateModified`.

### Anti-Fake-Freshness Rule
Never recommend bumping `Updated`, `dateModified`, or `<lastmod>` without genuine material changes (current pricing, new buyer FAQs, updated specs/benchmarks, or fresh third-party proof). Empty date churn wastes trust signals and risks quality penalties.

---

## Lever 2: Top-200-Word `<main>` Content Capsule

### Scoring Criteria
- **BLUF Density**: Within the opening `200 words` of `<main>`, the page MUST explicitly state:
  1. Target entity / product / company name
  2. Core offering and target buyer/use case
  3. Concrete differentiator (how it differs from alternatives)
  4. Grounded proof or pricing/scope anchor (numbers, tiers, SLAs, or verified constraints)
- **What to Check**: Measure word count strictly on `<main>` or `<article>` body copy. Exclude global navigation, header chrome, breadcrumbs, cookie banners, and sidebars.
- **Comparison / Alternatives Pages**: A structured native HTML comparison table MUST sit immediately near the top (within or right below the opening capsule), never buried after 500+ words of throat-clearing narrative. On `[Competitor] Alternatives` hubs (unbranded entry), verify an explicit internal link sequence into `[Brand] vs [Competitor]` 1v1 spokes (branded evaluation, `OUEELIis-do`).

- **Ratings**:
  - `PASS`: Core entity, offering, differentiator, and pricing/proof appear in the first `200 words` of `<main>` (plus immediate comparison table on comparison pages).
  - `NEEDS_WORK`: Topic introduced, but pricing, differentiator, or comparison table is pushed below word 200.
  - `AT_RISK`: Opening `200 words` are vague brand storytelling, generic industry definitions, or buried behind JS tabs/accordions.

---

## Lever 3: Standalone Chunk Architecture & Semantic HTML Tables

### Two-Tier Chunk Budget (Directional Heuristic)
Reconcile excerpt-window limits with readable section sizing and `kirby-aiseo-skill` Module 2 §2.20 (`200-500 token` RAG windows and direct BLUF answer layer) using a two-tier structure:
1. **Atomic Extractable Answer Core (`60-90 words` / `~80-120 tokens`)**: Immediately beneath every `<h2>` or `<h3>`, open with a self-contained paragraph stating `Entity + Attribute + Metric + Scope` so an isolated snippet preserves full semantic meaning even if truncated (aligns with `kirby-aiseo-skill` §2.20 BLUF layer).
2. **Self-Contained Section Block (`150-175 words` total)**: Keep the full `<h2>` / `<h3>` section compact (`150-175 words`, roughly `~200-235 tokens`, sitting cleanly inside `kirby-aiseo-skill` §2.20's `200-500 token` RAG window), with supporting caveats or bullet evidence immediately following the atomic core.

### Ambiguity & Context-Free Check
- Flag and replace ambiguous pronouns or orphan openers at the start of sections (`"It"`, `"This option"`, `"They"`, `"The platform"`, `"Here is how"`).
- Every chunk must stand alone if read without seeing the page title or parent section.

### Native Semantic HTML Table Rule
- Verify all comparison matrices, pricing tiers, spec sheets, and feature tables use real native HTML: `<table>`, `<thead>`, `<tbody>`, `<tr>`, `<th>`, `<td>`, and ensure any comparison graphics are also rendered in highlightable DOM HTML rather than text-locked PNGs (`OUEELIis-do`).
- Never use PNG/JPG screenshots of tables or client-side JS-hydrated `<div>` grid soup that disappears in raw HTML.

- **Ratings**:
  - `PASS`: All core sections are `<= 175w` with `60-90w` atomic cores, zero ambiguous openers, and native HTML tables.
  - `NEEDS_WORK`: Sections span `176-220 words`, start with vague pronouns, or mix prose where a table belongs.
  - `AT_RISK`: Sections exceed `>220 words` (wall-of-text), tables are image-based, or tables are JS-only `<div>` grids invisible in raw HTML.

---

## Lever 4: Canonical URL Hygiene, SSR Raw-HTML Visibility & OpenAI Crawler Access

Canonical OpenAI crawler governance policy belongs to `kirby-aiseo-skill` Module 1 §1.22 (and site-wide multi-vector crawler scoring to `kirby-grounded-seo-auditor`). This lever applies page-level diagnostic checks:

### Canonical & Parameter Hygiene
- Confirm a single self-referencing `<link rel="canonical" href="...">` matching the exact clean URL.
- Check for split equity across UTM/ad/tracking parameters, trailing slash variants, `http` vs `https`, or `www` vs apex redirects.
- Confirm zero canonical chains (`A -> B -> C`) and no accidental cross-domain or staging canonicals.

### HTTP Status, Meta Directives & SSR Raw-HTML Check
- Confirm `HTTP 200 OK` (no `3xx` loops, `403`, `429`, `5xx`, or Cloudflare JS/Turnstile/bot challenges).
- Confirm absence of `noindex` (both `<meta name="robots">` and `X-Robots-Tag` HTTP header).
- Confirm absence of `nosnippet` or `max-snippet:0` (web-standard directives honored by Google/Bing and downstream search-backed fetches; undocumented for `OAI-SearchBot`).
- **SSR / Raw-HTML Verification**: Inspect raw server HTML (`curl -s` without JS execution) to confirm the top-200-word capsule, pricing, and semantic HTML tables are present in initial HTML, not hidden behind client-side hydration or interactive-only accordions.

### 4-Bot OpenAI Crawler Taxonomy
Check `robots.txt` stanzas and CDN/WAF rules separately for OpenAI's documented crawlers:
1. `OAI-SearchBot`: ChatGPT Search indexing and citation crawler. MUST be allowed (`Allow: /` or unblocked) in `robots.txt` and WAF. Note: `robots.txt` updates can take up to ~24 hours to propagate.
2. `ChatGPT-User`: Live on-demand user-triggered fetcher when users paste links or ChatGPT fetches pages in real time. Note: Per OpenAI docs, `robots.txt` rules may not apply to `ChatGPT-User` and disallowing it in `robots.txt` does not block ChatGPT Search citations; however, **WAF/CDN IP or bot-challenge blocks on `ChatGPT-User`** break live pasted-link reads.
3. `GPTBot`: Model training crawler. Site owners may `Disallow: /` for `GPTBot` without losing ChatGPT Search citations, provided `OAI-SearchBot` remains allowed (and note that if both are allowed, OpenAI may reuse results from one crawl).
4. `OAI-AdsBot`: Ads quality verification crawler (note in `robots.txt` audits if ads landing pages are audited).

### Recommended Live-Readability Check (IP-Allowlist Verification)
Remember that `curl -A "OAI-SearchBot"` only tests User-Agent string rules on your server; it does **not** verify OpenAI's official IP-range allowlisting (`openai.com/searchbot.json`, `openai.com/chatgpt-user.json`, `openai.com/gptbot.json`, `openai.com/adsbot.json`) at Cloudflare/CDN/WAF layers. Recommend the user run a live-readability test (public URLs only; never paste staging, preview, draft, or auth-token URLs into ChatGPT: paste target URL directly into ChatGPT and ask it to quote/summarize the pricing or comparison table) to confirm no silent WAF/IP block exists.

- **Ratings**:
  - `PASS`: Clean self-referencing canonical, `HTTP 200` on raw GET check, SSR HTML complete, zero `noindex`/`nosnippet`/`max-snippet:0`, `OAI-SearchBot` allowed in `robots.txt`, and no WAF challenge observed (live ChatGPT read recommended to verify CDN IP allowlisting).
  - `NEEDS_WORK`: Minor parameter/canonical hygiene gap, `ChatGPT-User` disallowed in `robots.txt` (non-blocking for Search citations, document nuance), or `sitemap.xml` canonical mismatch without blocking crawl.
  - `AT_RISK`: `OAI-SearchBot` blocked in `robots.txt`, `OAI-SearchBot` or `ChatGPT-User` blocked at WAF/CDN (`403`/`5xx`/Turnstile challenge), `noindex`/`nosnippet`/`max-snippet:0`, canonical mismatch to another URL, or core content missing from raw SSR HTML.
  - `UNVERIFIED`: Offline draft or live `robots.txt`/headers/WAF unreachable.

---

## Lever 5: Secondary Verification & Trust Surface Signals

### What to Check
Confirm indexable, plain-HTML visibility (never gated behind login walls or images) for:
1. **Transparent Pricing / Scope**: Real numbers, tiers, billing cadence, or explicit quote ranges.
2. **Third-Party Review Consensus & Proof**: Named sources (G2, Capterra, Trustpilot, GitHub, industry benchmarks) with specific counts/dates in plain HTML text or tables.
3. **About / Entity Credentials**: Legal business name, leadership/authors (with outbound **LinkedIn** and **X/Twitter** profile links on comparison/editorial bylines, with the author's consent where such a profile exists, `OUEELIis-do`), verifiable domain ownership, and real-world footprint.
4. **Clear Contact Details & Fair Comparison Signals**: Physical address/jurisdiction, support email, unambiguous contact paths, and—on competitive/alternatives pages—explicit self-bias disclosure (*"Position #1 is ours, discount accordingly"*), a dedicated `"When [Competitor] May Still Be the Better Fit"` section, and **switching-friction objection handling** (`OUEELIis-do`).

### Scoped Schema Safety Guardrail (`AggregateRating` / `Review`)
Aligning with `kirby-technical-aeo` (`references/00_dispatcher.md:101`) and `kirby-aiseo-skill` §2.50:
- **NEVER recommend adding `AggregateRating` or `Review` JSON-LD** for competitor entities, scraped third-party platform ratings, or self-serving comparison/service pages (conflicts with Google structured-data spam/self-serving review policies).
- Legitimate first-party `Product`/`SoftwareApplication` review markup must obey Google's self-serving review rules; for audit remediation, default conservatively to rendering third-party review counts, consensus summaries, and citations as **visible plain HTML text or `<table>` rows** with outbound verification links.

### Anti-Reddit-Only Reliance Rule
Reddit threads and community mentions help build brand awareness, but never treat Reddit mentions as a substitute for clean, verifiable first-party trust surfaces (`/pricing`, `/about`, `/facts`, `/trust`) and structured owned pages.

- **Ratings**:
  - `PASS`: Plain-HTML pricing, third-party proof, entity credentials, and contact details verified with safe schema.
  - `NEEDS_WORK`: Missing pricing transparency, weak third-party citations, or thin About/contact footprint.
  - `AT_RISK`: Hidden pricing + zero proof, unverified claims, or banned competitor/scraped/self-serving `AggregateRating`/`Review` JSON-LD on audited pages.
