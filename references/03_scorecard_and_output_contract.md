# Scorecard & Output Contract

Every audit produced by `kirby-chatgpt-retrieval-auditor` MUST follow this exact output structure. Never omit the Evidence Log or invent unverified checks.

---

## 1. Evidence Log (Required First Section)
Document what was actually inspected before presenting scores:
- **Target URL / Draft**: exact URL or filename
- **Page Class**: `Recency-Sensitive Commercial/Pricing/Comparison` OR `Evergreen Guide` OR `Entity/Trust Hub`
- **Inspection Timestamp**: UTC/local date
- **Direct Observations**:
  - HTTP status & headers (`HTTP 200`, `X-Robots-Tag`, CDN/WAF): `[Observed value | UNVERIFIED]`
  - `robots.txt` (`OAI-SearchBot`, `ChatGPT-User`, `GPTBot`, `OAI-AdsBot`): `[Observed rules | UNVERIFIED]`
  - Live ChatGPT readability test: `[Confirmed | Blocked | UNVERIFIED - user action needed]`
  - Canonical tag & UTM/variant hygiene: `[Observed href | UNVERIFIED]`
  - SSR raw-HTML presence of capsule & tables: `[Confirmed | Missing | UNVERIFIED]`
  - Date-Triad (`Visible` / `dateModified` / `<lastmod>`): `[Exact dates | UNVERIFIED]`

---

## 2. 5-Lever Retrieval Audit Scorecard
Render the summary scorecard as a clean Markdown table:

| Lever | Rating | Observed Evidence | Priority Action |
| :--- | :--- | :--- | :--- |
| **1. Freshness & Date-Triad** | `PASS` / `NEEDS_WORK` / `AT_RISK` / `UNVERIFIED` | Exact dates across visible text, JSON-LD, sitemap | Specific substantive refresh + Date-Triad sync |
| **2. Top-200-Word `<main>` Capsule** | `PASS` / `NEEDS_WORK` / `AT_RISK` / `UNVERIFIED` | Exact word index where entity, offer, proof/pricing appear | Exact BLUF capsule rewrite / top table move |
| **3. Standalone Chunks & HTML Tables** | `PASS` / `NEEDS_WORK` / `AT_RISK` / `UNVERIFIED` | Section word counts, orphan pronouns, `<table>` vs image/`<div>` | Exact chunk splits + semantic `<table>` replacement |
| **4. Canonical, SSR & OpenAI Access** | `PASS` / `NEEDS_WORK` / `AT_RISK` / `UNVERIFIED` | Canonical href, HTTP/meta directives, SSR HTML, 4-bot status | Exact `robots.txt`, WAF, SSR, or canonical fix |
| **5. Secondary Verification & Trust** | `PASS` / `NEEDS_WORK` / `AT_RISK` / `UNVERIFIED` | Plain-HTML pricing, third-party proof, credentials, contact, schema check | Plain-HTML trust additions; remove any banned `AggregateRating` |

---

## 3. Prioritized Fix Queue (Exact Rewrites Required)
List fixes strictly by severity (`P0` -> `P1` -> `P2`). **Every fix MUST include exact copy-paste replacement text, HTML, JSON-LD, or `robots.txt` rules**, never vague advice like *"improve clarity"* or *"add freshness."*

- **P0: Critical Crawl, WAF, Canonical & SSR Blockers** (Lever 4)
- **P1: Extraction, Top-200w Capsule & Standalone Chunk Rewrites** (Levers 2 & 3)
- **P2: Substantive Freshness, Date-Triad & Secondary Verification Surfaces** (Levers 1 & 5)

If Mode B was invoked, append a **Week 1-4 Commercial Cluster Roadmap** table mapping each priority URL to its target fixes.

---

## 4. Gotchas & Anti-Patterns Checklist (Verify Before Finalizing)
Confirm none of these failure modes slipped into your audit or recommendations:
1. **Heuristic vs Fact Confusion**: Directional heuristics (`rank #36`, `~22-24` seats, `~88 tokens`, `150-175 words`, `< 33d` commercial freshness) are explicitly framed as practitioner estimates, not official OpenAI constants.
2. **Retrieval != Citation**: Being fetched during fan-out queries does not guarantee a citation; weak capsules or missing verification still lose final selection.
3. **Fake Freshness**: Never recommend changing dates without genuine material updates (pricing, FAQs, specs, proof).
4. **Blanket 30-Day Evergreen Churn**: Apply `< 33d` targets to commercial/pricing/comparison pages, and `60-90d` substantive review cycles to evergreen guides.
5. **Banned `AggregateRating` / `Review` JSON-LD**: Never recommend competitor, scraped third-party, or self-serving comparison/service review JSON-LD; use plain HTML text/tables instead.
6. **Reddit-Only Reliance**: Do not rely on third-party Reddit mentions in place of clean owned `/pricing`, `/about`, and `/facts` surfaces.
7. **Unverified Guessing**: Every uninspected live check (`robots.txt`, WAF, `sitemap.xml`, live ChatGPT read) is explicitly flagged `UNVERIFIED` with exact user verification commands.
