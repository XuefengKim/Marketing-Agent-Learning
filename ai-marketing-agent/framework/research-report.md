# AI Marketing Agents & Automated Advertising — Landscape Map and Curated Reading

**For:** a product-background builder creating an automated ad-delivery system 0→1, starting with Meta and/or multiple channels, long-term vision fully autonomous operation without human intervention.
**Scope:** English-language (and other non-Chinese) sources only. All 12 curated articles were opened and verified to resolve on 2026-11-17. Vendor performance figures are Meta/Google/TikTok's own claims unless stated otherwise — treat them as direction, not guarantees.

## Summary

The field splits into two different automations that are often confused. **Platform automation** (Meta Advantage+, Google Performance Max / AI Max, TikTok Smart+) already does bidding, audience expansion, placement and much of creative assembly inside each walled garden — your system does not replace it, it steers it. **Agent automation** is the layer you build on top: perceiving performance, deciding what to change, and executing through the platforms' APIs, with guardrails that cap what an agent may spend or publish. The single most important design fact is that, since Meta's Andromeda (Dec 2024), **creative is the targeting input** — the retrieval engine reads the ad itself to decide who it can reach — so a 0→1 system should invest first in creative diversity + clean conversion signal, not in cleverer manual targeting. The second is that **platform-reported ROAS is not causal**: geo-holdout benchmarks put median incremental ROAS at 2.31x vs. much higher dashboard numbers, with branded search at 0.70x — so measurement (geo holdouts + MMM calibrated by experiments) must be built in from day one, not bolted on. Genuine full autonomy is still rare: Gartner via Adweek (Oct 2026) puts highly autonomous deployments at <5% of marketing use cases in 2025, forecast 15% by 2028 — plan a staged ladder (manual → assisted → semi-autonomous → autonomous) with approval gates that shrink, not disappear overnight.

---

## Findings

### 1. Landscape map — what "marketing agents" / "agentic AI in advertising" actually covers

| Function | What the agent/system does | What the platform already does for you | What you must still build |
|---|---|---|---|
| **Creative generation** | Brief → angles/hooks → copy, image/video variants, localisation, resizing; retrieval-grounded so claims come from the product DB, not the model | Meta Advantage+ Creative, Google AI Max text customisation, TikTok Symphony (in Smart+) generate/expand/remix assets | Brand-truth source (guidelines, claims DB), generation prompts, multi-layer review (see §4), fatigue detection, variant→archetype tracking |
| **Media buying / bidding** | Set/adjust budgets, bids, caps; pause/kill/scale ad sets; choose campaign structure | Smart Bidding (Google), Advantage+ budget/placement optimisation, Smart+ CBO — all inside the black box | Decision policy: *when* to intervene, with what thresholds, and hard caps the LLM cannot override |
| **Budget allocation** | Cross-channel / cross-campaign reallocation by marginal incremental return | Within-campaign allocation only (e.g. PMax allocates across Google channels) | Cross-channel allocator fed by incrementality-corrected returns, not dashboard ROAS; pacing + daily/weekly caps |
| **Audience / segmentation** | Build/refresh segments from 1st-party data, lookalikes, intent signals; suppress existing customers | Advantage+ Audience treats inputs as *suggestions* and expands; AI Max keywordless matching; Smart+ auto-targeting | Audience definitions as data, exclusion/suppression logic, privacy-compliant identity (no user-level tracking dependence) |
| **Measurement / incrementality** | Geo holdouts, lift studies, MMM; convert results into a correction multiplier per channel | Platform lift studies (Meta Conversion Lift, Google Conversion Lift, min $5,000 user-based) — the platform grades its own homework | Independent measurement: your own sales/CRM as source of truth, GeoLift / Meridian GeoX, experiment-calibrated MMM |
| **Reporting / insight** | Natural-language performance narratives, anomaly detection, root-cause, next-action proposals | Ads Manager / Google Ads reporting, channel-level PMax reporting (2025) | Unified cross-channel truth table (spend, attributed vs. incremental revenue, margin), audit log of every agent action |

**Two clarifications that prevent wasted work.** (1) "Agentic" ≠ "automated": a rules engine that pauses ads at CPA > $X is automation; an agent reasons over unstructured state (creative, market, funnel) and chooses among actions via tools. Anthropic's framing is the standard vocabulary here (see article #1). (2) There are *two* agent markets: agents that buy ads *for advertisers* inside walled gardens (your system), and agent-to-agent media buying *between* buyers and sellers (AdCP/AAMP — see §5), which is earlier-stage and mostly CTV/publisher so far.

### 2. How the major platforms automate today + APIs for programmatic control

| Platform | Automation product | What it decides | What control you keep | Key facts |
|---|---|---|---|---|
| **Meta** | **Advantage+** (Sales/App/Leads campaigns; Audience, Placements, Creative, Catalog) on **Andromeda** (retrieval) + **GEM** (ranking foundation model) + Lattice | Who is eligible to see an ad (retrieval shortlist), ranking/auction, budget, placement, creative variation | Objective, budget/caps, creative assets, brand controls, audience *suggestions*, exclusions | Andromeda, Engineering at Meta, 2 Dec 2024: retrieval narrows "tens of millions of ad candidates into a few thousand"; +6% recall, +8% ads quality on selected segments; 10,000x model capacity; on NVIDIA Grace Hopper + MTIA. Advantage+ automates "audience creation, optimal budget allocation, dynamic placement … and creative generation". GEM, 10 Nov 2025 (index): LLM-scale teacher model distilled into rankers; Meta claims +5% Instagram / +3% Facebook Feed conversions in Q2 2025. Meta, Apr 2025: "creative diversification [is] the best lever to find the most relevant audiences" — creative is now the targeting. Advantage+ is now the default for new sales/leads/app campaigns. |
| **Google** | **Performance Max** (full campaign type, all inventory) + **AI Max for Search** (feature suite inside Search campaigns) + Smart Bidding | Channel, audience, asset mix, bid, landing page (final URL expansion), query matching beyond keywords | Budget, goal (tCPA/tROAS), asset groups + audience signals, brand inclusions/exclusions, negatives, URL controls, location-of-interest | PMax: one campaign across Search, YouTube, Display, Discover, Gmail, Maps; assets grouped in Asset Groups; Google's AI assembles ads in real time. AI Max, Google, 6 May 2025: search-term matching (broad + keywordless), text customisation, final URL expansion; Google claims typically +14% conversions at similar CPA/ROAS, +27% for exact/phrase-heavy campaigns. Power Pack (2025) = PMax + AI Max together. Channel-level + full search-terms reporting for PMax arrived Apr–Aug 2025. |
| **TikTok** | **Smart+** (successor to Smart Performance Campaign, Apr 2024) + **Symphony** generative AI | Targeting, budget/bid (CBO), placement, creative combinations | Objective/KPI, budget, goal, geography/language, assets; since Oct 2025 automation is **module-by-module** (toggle targeting/budget/creative/placement separately) | TikTok, 13 May 2026 (upd 20 Oct 2026): up to 30 ad groups/campaign, 30 asset groups/ad group, 50 creatives/asset group; Smart+ Catalog Ads claimed −36% CPA vs manual in closed beta (TikTok internal data, 2025); Symphony Automation = Recommended Creatives + Automatic Enhancements (incl. dubbing into 50+ languages). |
| **Emerging** | Meta **Muse for Small Business** (29 Sep 2026, index); Google **Ads Advisor / Analytics Advisor** (12 Nov 2025, index); Amazon **Ads Agent** (Nov 2025, index) | Conversational agent inside the platform: analyse account, draft campaigns, recommend changes | Human approval: Meta's rule is "nothing publishes, sends, or spends without your approval" | These are assistants/analysts today, not autonomous buyers — useful as a reference for approval UX, not as infrastructure to depend on. |

**APIs — the execution layer you actually program against.** All three are free with an ad account; auth is OAuth 2.0 in each case (plus a developer token for Google).

| API | What it exposes | Notes for a builder |
|---|---|---|
| **Meta Marketing API** — `developers.facebook.com/docs/marketing-api/overview` (index) | Campaign / ad set / ad CRUD, custom audiences, Ads Insights, Conversions API (server-side events) — same object hierarchy as Ads Manager | Graph API, versioned (~every 4 months; v26.0 released 29 Jul 2026 per secondary guides — re-check at build time); permissions `ads_read` / `ads_management`; system-user tokens for server use; rate limits by access tier. Meta also opened its **Ads MCP server** to any developer (Jul 2026, index) — an MCP wrapper over this API. |
| **Google Ads API** — `developers.google.com/google-ads/api/performance-max` (index) | Campaigns, asset groups, budgets, bidding, GAQL reporting; PMax creation via atomic bulk `Mutate` (`CampaignBudget` + `Campaign` + `AssetGroup` + assets); AI Max settings via API (v21+) | Developer token + OAuth; client libraries in Python/Java/PHP/.NET etc. What it does *not* expose: Smart Bidding internals, auction data, competitors' bids. |
| **TikTok Marketing API** — `business-api.tiktok.com` (index) | Campaign / ad group / ad management, Spark Ads, video upload, audience sync | App approval + business verification required; rate limits apply. |
| **Protocol layer (new)** | **MCP** (Anthropic, Nov 2024, now Linux Foundation) = agent↔tool; **A2A** (Google, Apr 2025) = agent↔agent; **AdCP** (Oct 2025, AgenticAdvertising.org) = agent media-buying tasks on MCP/A2A; **IAB Tech Lab AAMP / ARTF** = agentic RTB + management protocols | For a Meta/Google/TikTok-first 0→1 build, use the native APIs + MCP servers first; treat AdCP/AAMP as the interoperability bet to watch, not the starting point (see article #9). |

**Signal plumbing (non-negotiable):** server-side conversion events (Meta Conversions API, Google enhanced conversions / offline conversion import, TikTok Events API) feeding the platforms *and* your own warehouse. Platform optimisation is only as good as the conversion signal you send — and your measurement must use your own sales/CRM data, never platform conversions alone.

### 3. Architecture of an autonomous ad system

```
┌─ DATA LAYER ─────────────────────────────────────────────────────┐
│ 1st-party: CRM/orders, product catalog + claims DB, brand assets │
│ Platform: Insights/GAQL/TikTok reports (spend, delivery)        │
│ Signal back: CAPI / enhanced conversions / Events API            │
│ Warehouse: unified spend × revenue × margin, geo × day grain    │
└──────────────┬───────────────────────────────────────────────────┘
               ▼
┌─ DECISION LAYER (agents + deterministic policy) ─────────────────┐
│ Perception: stream/batch metrics, fatigue, anomalies, pacing    │
│ Reasoning: LLM agents (analyst → strategist) + ML models        │
│   (saturation curves, MMM marginal ROI, creative scoring)       │
│ Policy (CODE, not LLM): budget caps, kill/scale rules,          │
│   brand & compliance checks, approval routing                   │
└──────────────┬───────────────────────────────────────────────────┘
               ▼
┌─ EXECUTION LAYER ────────────────────────────────────────────────┐
│ Tool wrappers over Meta / Google / TikTok APIs (+ MCP servers)  │
│ Idempotent, dry-run-able mutations; every call logged           │
└──────────────┬───────────────────────────────────────────────────┘
               ▼
┌─ FEEDBACK / EVALUATION LOOP ─────────────────────────────────────┐
│ Short loop: platform metrics → reallocate / refresh creative    │
│ Long loop: geo holdouts + MMM → correction multipliers →        │
│   retrain allocator priors, creative strategy, budget split     │
│ Evals: offline (historical replay, golden sets) + online        │
│   (shadow mode, holdouts); trace every decision (LangSmith etc.)│
└──────────────────────────────────────────────────────────────────┘
        GUARDRAILS wrap all layers: hard caps, allow-lists,
        claim verification, human approval gates (see §4)
```

**Autonomy ladder — ship one rung at a time, with explicit gates:**

| Stage | Agent does | Human does | Exit criteria to next stage |
|---|---|---|---|
| **L0 Manual** | Nothing; dashboards + alerts only | All decisions and execution | Reliable data layer; one channel live via API (read-only) |
| **L1 Assisted (recommend)** | Drafts actions + rationale; no writes | Approves/rejects each action in-app | >90% of recommendations accepted or correctly rejected; evals green |
| **L2 Semi-autonomous (bounded writes)** | Executes pre-approved action classes within caps (e.g. pause CPA > 3× target, shift ≤15% budget/day) | Reviews queue daily; approves new creative / budget increases | 4–8 weeks without a guardrail breach; shadow-mode parity |
| **L3 Autonomous (supervised)** | Full loop incl. creative generation + launch, within budget envelope and brand lock | Sets goals/caps, weekly review, exception handling | Incrementality-validated returns; incident plan tested |
| **L4 Fully autonomous** | Operates within envelope indefinitely; escalates only anomalies | Governance: goals, caps, brand truth, audits | — (vision; <5% of marketing use cases are highly autonomous today, Gartner via Adweek Oct 2026) |

**Build order for 0→1 (recommended):** (1) read-only data + reporting agent on Meta; (2) deterministic rules (pause/kill/pacing) with human approval; (3) creative-generation pipeline with the 5-layer review; (4) budget allocator on incrementality-corrected returns; (5) add Google, then TikTok, behind one channel abstraction; (6) widen autonomy per the ladder, one action class at a time. Do not start with a free-running multi-agent swarm that can spend money.

### 4. Key concepts a builder must know

- **Workflows vs agents (Anthropic, Dec 2024):** workflows = LLMs/tools on *predefined code paths*; agents = LLM *dynamically directs* its own process and tool use. Five composable workflow patterns — prompt chaining, routing, parallelisation, orchestrator-workers, evaluator-optimizer — cover most real needs. Rule: use the simplest pattern that passes evaluation; reserve full agents for open-ended tasks where you cannot hardcode the path but *can* verify progress. Start by calling LLM APIs directly; frameworks (LangGraph, CrewAI, Strands, Claude Agent SDK) add abstraction that obscures prompts and makes debugging harder.
- **Tool use / agent-computer interface:** design tools as carefully as a UI — precise names, schemas, docs, typed/structured outputs, idempotency keys. The LLM must never decide *whether* to spend: code decides caps, counts and posting; the LLM does semantic work (ranking, drafting) inside those bounds. (See the dev.to "Doug" case in notes — deterministic control plane + bounded semantic reasoning — and article #1.)
- **Evaluation is the product:** offline evals (golden creative sets, historical replay), online evals (shadow mode — agent proposes, human executes, compare), and decision traces. If you cannot measure an agent's decision quality, you cannot safely widen autonomy. Anthropic's three principles: simplicity, transparency (show planning steps), careful tool design.
- **Hallucination / claim risk:** models invent prices, specs, discounts and statistics persuasively. Mitigation is retrieval-grounding (generate only from the product/claims DB) + a claim-verification layer that rejects anything unverifiable against the source of truth + sampled batch audits. See article #12 for a working five-layer pipeline.
- **Brand safety:** two layers — *suitability* (where ads appear; platform brand-safety modules) and *identity* (whether the creative is on-brand: voice, hex, fonts, claims). At volume the killer is not catastrophic errors but the long tail of mildly off-brand variants diluting the brand within a quarter. Emerging fix: `brand.json` — a machine-readable brand-truth file on the brand's own domain (AdCP 3.0) — see article #11.
- **Budget guardrails (deterministic, in code, outside the LLM):** hard daily/account caps, per-action change caps (e.g. ±15% budget/day), minimum ROAS / maximum CPA floors that trigger auto-pause, kill rules (e.g. 3× target CPA with no conversions), pacing alerts, and a global kill-switch. Log every mutation with actor, rationale and pre/post state. Runaway-spend incidents (e.g. Meta campaigns spending 150%+ of daily budget in the first hour, reported Nov 2025) are the failure mode to design against first.
- **Incrementality vs attribution vs MMM — triangulate, don't pick one:**
  - *Attribution* (last-click / MTA): which touchpoints claimed a conversion. Good for daily ops; systematically over-credits retargeting and branded search; degraded by privacy changes (30–40% of conversions missed by browser-only tracking).
  - *Incrementality* (geo holdouts, audience holdouts, platform lift): what the spend *caused*, vs a counterfactual. Gold standard for budget decisions; slow (weeks), coarse (channel-level).
  - *MMM* (Meridian, Robyn): regresses sales on spend over time with saturation/adstock; always-on, cross-channel, privacy-durable (aggregated data). Correlational unless calibrated with experiment results as priors.
  - Operating rule: attribution for day-to-day, geo tests quarterly per major channel (output = a correction multiplier on platform ROAS), MMM for cross-channel allocation — and re-test, because multipliers drift as platforms change.
- **Creative is the targeting; fatigue is the clock:** under Andromeda, diverse creative (different angles, hooks, formats, audiences) is the main lever for reach; near-duplicates count as one creative. Creative dies fast — one 2026 analysis of 2.9M Meta ads put median life at 3 days — so the system needs a continuous refresh cadence, not a one-off batch.
- **Learning phase / signal quality:** platforms need ~50 conversion events/week per ad set to exit learning; edits reset learning. Your agent must batch changes, respect learning windows, and never churn settings. Clean, deduplicated server-side conversion signal matters more than any bidding trick.

### 5. Notable startups, tools and open frameworks

*Status caveat: this is a fast-moving, vendor-claim-heavy space; funding/performance figures below are press/vendor-reported (index sources), not independently verified. Treat the list as a map of archetypes, not endorsements.*

| Archetype | Examples | What they do |
|---|---|---|
| **Agentic media-buying platforms** | **Apostra** (ex-Scope3, AdCP originator) — agent-to-agent buys across Meta/Google/Pinterest/Reddit/Spotify/ChatGPT, ~$1.2m in 3 months to Oct 2026; **PubMatic AgenticOS** (Jan 2026); **Magnite** buyer agent + orchestration layer; **The Trade Desk** conversational interface routing to specialist agents; **Kargo Project Kera** | Plan/negotiate/execute media via agents; mostly CTV/programmatic so far, small budgets, humans still watching |
| **Cross-channel AI ad managers** | **Synter Media** (MCP server, 27 platforms incl. Google/Meta/TikTok/TTD); **MINT.ai**; **Abijany** (Meta/Google/TikTok/YouTube via official APIs, guardrail-led) | One system operating several walled gardens — closest analogue to what you are building; study their guardrail/approval UX |
| **Creative-generation agents** | **Runway Ads** (video variations localised for Meta/Google/TikTok); **Celtra** (creative automation + governance, brand.json co-author); **VIMDRIVE / VIMC01**; **AdCreative.ai, Jasper, Writer** | Scale variant production; differentiation is now governance/claim-checking, not generation speed |
| **Measurement / incrementality** | **Haus** (640 Meta tests analysed; incremental attribution), **Stella / Measured** (geo benchmarks), **Northbeam**, **Google Meridian + GeoX** (open source), **Meta GeoLift** (open source, 2021), **Meta Robyn** (open-source MMM) | The measurement stack your allocator depends on; GeoLift/Meridian are free and runnable by a small team |
| **Agent frameworks (open)** | **LangGraph** (stateful graphs, best for long-running ad loops), **CrewAI** (role-based crews: Analyst/Strategist/Executive), **AutoGen** (multi-agent conversation), **MCP** (tool standard), **n8n / Temporal** (reliable execution/scheduling — keep the money-moving path in deterministic workflow tooling, not in an LLM loop) | Orchestration + tool layer; pair with LangSmith/observability for traces |
| **Standards bodies** | **AgenticAdvertising.org / AdCP** (brand.json, AdCP 3.0), **IAB Tech Lab AAMP / ARTF**, **Prebid.org** (reference sales agent) | Where agent-to-agent buying is being standardised; monitor, don't build on yet for walled-garden buying |
| **Platform-native agents** | Meta Muse for Small Business, Google Ads Advisor / Analytics Advisor, Amazon Ads Agent | Assistants inside each platform; approval-gated; useful as a UX reference |

---

## Curated articles (12 — all verified to resolve, 2026-11-17)

| # | Article | Source / date | Why read it |
|---|---|---|---|
| 1 | Building effective AI agents | Anthropic, 19 Dec 2024 | Foundational agent design |
| 2 | Meta Andromeda: Supercharging Advantage+ automation… | Engineering at Meta, 2 Dec 2024 | Platform deep dive — Meta |
| 3 | Introducing AI Max for Search campaigns | Google (The Keyword), 6 May 2025 | Platform deep dive — Google |
| 4 | Smart+: AI-Powered Solution To Maximise Ad Campaign Results | TikTok for Business, 13 May 2026 (upd 20 Oct 2026) | Platform deep dive — TikTok |
| 5 | Building an Agentic MarTech Stack: A Technical Deep Dive | Medium — Brian James Curry, Feb 2026 | System architecture |
| 6 | The PPC marketer's guide to Google's Meridian | Search Engine Land, 9 Oct 2026 | Measurement / MMM |
| 7 | Incrementality Testing on a Small Budget: The Geo Holdout Method (2026) | Orbit, 25 Sep 2026 | Measurement / geo holdouts |
| 8 | How to Test For Incremental Impact: Paid Media & Incrementality Testing | Stella, updated June 2026 | Measurement / benchmarks |
| 9 | A Guide to the New, Wide World of Agentic Advertising and Commerce Protocols | Adweek, 28 May 2026 | Forward-looking / standards |
| 10 | AI Agents Are Increasingly Getting Their Hands on Ad Dollars | Adweek, 6 Oct 2026 | Real-world case study / reality check |
| 11 | Agentic Advertising Without Governance Is Just Chaos at Scale | Celtra, 21 Apr 2026 | Governance / brand safety |
| 12 | Brand-safe AI creative — what we learned from 50,000 ad variants | VIMDRIVE, 8 Apr 2026 | Real-world teardown / guardrails |

### 1. Building effective AI agents — Anthropic, 19 Dec 2024
https://www.anthropic.com/engineering/building-effective-agents
Anthropic draws the field's standard line between *workflows* (LLMs/tools on predefined code paths) and *agents* (the LLM dynamically directs its own process and tool use), then gives five composable workflow patterns — prompt chaining, routing, parallelisation, orchestrator-workers, evaluator-optimizer — plus guidance to start with direct API calls, keep designs simple and transparent, design tools carefully, and evaluate before adding complexity. (Page now notes the tooling landscape has changed since Dec 2024; the patterns have not.)
**Why it matters for a 0→1 builder:** this is the design vocabulary that stops you building a free-running "agent swarm" on day one — most of an ad system should be workflows with gates, with true agents reserved for open-ended tasks you can verify.

### 2. Meta Andromeda: Supercharging Advantage+ automation with the next-gen personalized ads retrieval engine — Engineering at Meta, 2 Dec 2024
https://engineering.fb.com/2024/12/02/production-engineering/meta-andromeda-advantage-automation-next-gen-personalized-ads-retrieval-engine/
Meta's own engineering account of Andromeda: retrieval is the first stage of ad delivery, narrowing tens of millions of candidates to a few thousand before ranking; a custom deep network on NVIDIA Grace Hopper (and MTIA) gives 10,000x model capacity, +6% retrieval recall and +8% ads quality on selected segments, with a hierarchical index built to absorb the explosion of Advantage+ / generative-AI creative volume.
**Why it matters:** it explains *why* creative is now the targeting input and why your system must generate diverse, genuinely different creative at volume — the retrieval layer, not your audience settings, decides who can see an ad.

### 3. Introducing AI Max for Search campaigns — Google (The Keyword), 6 May 2025, by Brian Burdick
https://blog.google/products/ads-commerce/google-ai-max-for-search-campaigns/
Google announces AI Max as a one-click suite inside Search campaigns: search-term matching (broad + keywordless), text customisation (auto-generated headlines/descriptions) and final URL expansion, with brand/location controls and richer search-terms reporting; Google claims typically +14% conversions at similar CPA/ROAS (+27% for exact/phrase-heavy campaigns), with L'Oréal (2x conversion rate, −31% cost/conversion) and MyConnect (+16% leads) as launch cases.
**Why it matters:** the canonical statement of Google's side of the bargain — keyword lists matter less, landing-page/asset quality and intent coverage matter more — and it defines the controls your system can still set programmatically versus what Google now owns.

### 4. Smart+: AI-Powered Solution To Maximise Ad Campaign Results — TikTok for Business, 13 May 2026 (updated 20 Oct 2026)
https://ads.tiktok.com/business/en-GB/blog/smart-plus-ai-performance-solution
TikTok's official Smart+ overview: automation across targeting, budget/bid (CBO), placement and creative for Traffic, App, Lead Gen and Sales, now toggleable *module by module* rather than all-or-nothing; up to 30 ad groups / 30 asset groups / 50 creatives per asset group; Smart+ Catalog Ads (claimed −36% CPA in closed beta) and Symphony Automation (Recommended Creatives, Automatic Enhancements incl. dubbing into 50+ languages).
**Why it matters:** TikTok is the third leg of a multi-channel build and the only platform that has moved *back* toward granular control — a useful model for how your own system should expose automation as per-module switches, not a single on/off.

### 5. Building an Agentic MarTech Stack: A Technical Deep Dive — Medium, Brian James Curry, Feb 2026
https://medium.com/@brian-curry-research/building-an-agentic-martech-stack-a-technical-deep-dive-07d51931336a
A full-stack blueprint: orchestration (LangGraph/CrewAI), agent layer (campaign optimisation, content generation, audience intelligence, budget allocation, monitoring, A/B testing), reasoning layer (LLM + vector search + ML models), perception layer (Kafka/Flink/Redis), action layer (Google/Meta/TikTok/email/CMS/CRM) and observability — with LangGraph code, a perception–reasoning–action loop, and a staged rollout (start with the highest-impact, lowest-risk agent; 10% → 50% → 100% of budget over 6 weeks). *Caution:* its "30–40% lower CPA" figures are attributed only to unnamed early adopters.
**Why it matters:** the closest thing to a buildable reference architecture in this list — copy its layering and its rollout discipline, not its unsourced performance claims.

### 6. The PPC marketer's guide to Google's Meridian — Search Engine Land, 9 Oct 2026
https://searchengineland.com/google-meridian-ppc-guide-494563
Meridian (introduced 2024, open to all early 2025) is a Python-based, fully Bayesian, open-source MMM: geo-level hierarchical modelling, Google Query Volume as a control for organic demand, time-varying intercepts, and — the key mechanism — calibration of priors with incrementality-experiment results; the guide gives a 5-step implementation (data prep, specification with adstock/saturation, MCMC training/diagnostics, calibration, scenario optimisation) and honest warnings on data cleanliness and technical bar.
**Why it matters:** MMM is the allocator's long-loop brain — this is the practical on-ramp to building budget decisions on causal, privacy-durable data instead of dashboard ROAS, with code you can inspect rather than a vendor black box.

### 7. Incrementality Testing on a Small Budget: The Geo Holdout Method (2026) — Orbit, 25 Sep 2026
https://orbitllm.com/resources/guides/incrementality-testing-geo-holdout
A small-budget, vendor-free recipe for geo holdouts: build a synthetic control from untouched markets, run a power analysis *before* spending (GeoLift or Meridian GeoX, both free/open source), hold out 2–5 markets for at least one purchase cycle plus a post-period, and output a *multiplier* (incremental ROAS ÷ platform ROAS) to correct every platform number thereafter; it documents the traps — reading results at the midpoint (Haus: Advantage+ looked 9% better mid-test, 12% worse by the end), leakage, short tests — and the new cheap thresholds (Google user-based lift from $5,000).
**Why it matters:** the most actionable measurement piece here — a 0→1 builder can run this with regional sales data and free tooling, and the multiplier it produces is exactly the input a cross-channel budget allocator needs.

### 8. How to Test For Incremental Impact: Paid Media & Incrementality Testing — Stella, updated June 2026
https://www.stellaheystella.com/blog/how-to-test-for-incremental-impact-paid-media-incrementality-testing
Stella defines incremental revenue (actual minus counterfactual), ranks five test designs (geo holdout, audience holdout, platform lift, switchback, experiment-calibrated MMM) by auditability, and publishes its 225-test DTC benchmark (Aug 2024–Dec 2025): median iROAS 2.31x (IQR 1.36–3.24x), branded search 0.70x, Meta 2.92x, Performance Max 2.98x, TikTok 0.94x — with explicit caveats that the sample is self-selected and that platform ROAS can mislead in *both* directions (Meta averaged 21% *more* incremental than reported in 46 inverse holdouts).
**Why it matters:** it gives you both the decision framework (which test, which KPI — total revenue vs new-customer vs gross profit changes the answer) and the only public cross-channel iROAS benchmarks to sanity-check your first tests against.

### 9. A Guide to the New, Wide World of Agentic Advertising and Commerce Protocols — Adweek, 28 May 2026
https://www.adweek.com/media/a-guide-to-the-new-wide-world-of-agentic-advertising-and-commerce-protocols/
A map of the protocol stack: infrastructure (MCP, Nov 2024; A2A, Google, Apr 2025), agentic advertising (AdCP, Oct 2025, built on MCP, overseen by AgenticAdvertising.org; IAB Tech Lab's ARTF/AAMP as the RTB-modernising alternative; Agentic Audiences for privacy-safe signal exchange), and agentic commerce (OpenAI/Stripe ACP, Google UCP, AP2, Visa TAP, x402).
**Why it matters:** before writing a line of integration code, know which layer each standard occupies — your 0→1 build should sit on native APIs/MCP now, and this tells you what to monitor (and what not to bet the company on) as agent-to-agent buying standardises.

### 10. ADWEEK Tech Advantage: AI Agents Are Increasingly Getting Their Hands on Ad Dollars — Adweek, 6 Oct 2026
https://www.adweek.com/programmatic/adweek-tech-advantage-ai-agents-are-increasingly-getting-their-hands-on-ad-dollars/
A grounded status report: Apostra's agents handled ~$1.2m of buys in three months; Rouge Care credits agents with 5x ROAS on $25k of CTV via PubMatic's AgenticOS; The Trade Desk, Magnite, Kargo and Runway are all shipping agent products — but "no one is doing this at scale", highly autonomous deployments are <5% of marketing use cases in 2025 (Gartner; 15% forecast by 2028), and the open questions are accountability ("if agents reallocate $10m and performance drops, who's accountable?") and protocol fragmentation.
**Why it matters:** the antidote to hype for a 0→1 plan — real money is moving, but in small, human-supervised amounts, which validates the staged autonomy ladder in §3 and warns you to design accountability/audit trails from the start.

### 11. Agentic Advertising Without Governance Is Just Chaos at Scale — Celtra (Pia Malovrh), 21 Apr 2026
https://celtra.com/blog/agentic-advertising-without-governance-is-just-chaos-at-scale/
Celtra's product lead argues the bottleneck is not AI capability but a *governance gap*: creative agents today have no authoritative source of brand truth, so they scrape websites and guess at colours, fonts and tone — and reactive human QA then expands to absorb the errors, defeating the purpose of going agentic. The proposed fix is `brand.json`, a machine-readable brand identity file published on the brand's own domain (part of AdCP 3.0), so governance is enforced *before* generation, and humans become "governance architects" rather than error-correctors.
**Why it matters:** it reframes brand safety from a review checklist into infrastructure — for your system, the equivalent is a machine-readable brand/claims source that generation must resolve against, not a human reading every variant.

### 12. Brand-safe AI creative — what we learned from 50,000 ad variants — VIMDRIVE, 8 Apr 2026
https://vimdrive.com/blog/brand-safe-ai-creative-50000-variants/
A practitioner teardown from running a creative agent (VIMC01) at volume: brand safety is a five-layer pipeline — (1) deterministic hard constraints, (2) a fine-tuned brand-voice classifier, (3) claim verification against the product database, (4) sampled batch audit (10–15%, stratified), (5) brand lock at the *archetype* level (50 locked archetypes cover 5,000 variants) — plus counter-intuitive findings: win-rates only bend past ~1,000 variants and flatten past ~5,000; the audit layer is "the steering wheel", and removing it produces homogenous, diluted creative that looks fine ad-by-ad.
**Why it matters:** the most concrete guardrail design in this set — it tells you exactly what to build around generation, what to measure (the survival ratio through the layers), and how much volume is actually worth producing.

---

## Could not verify / open questions

- **API version numbers move fast.** Meta Marketing API v26.0 (Jul 2026) and Google Ads API versions are from index/secondary sources; re-check the official docs at build time rather than trusting versions quoted here. TikTok Marketing API rate limits / version (v1.3) likewise index-only.
- **Vendor performance claims are unverified.** All "+14% / +22% / −36% CPA" figures are the platforms' own tests; independent results are consistently more mixed (e.g. Haus: Advantage+ over-reported vs manual by 12pp; 58% of brands saw higher iROAS on manual). No independent head-to-head of Advantage+ vs PMax vs Smart+ on identical budgets was found.
- **Meta GEM details** (10 Nov 2025 engineering post) and **Meta's creative-diversification guidance** (22 Apr 2025) were corroborated via search/secondary sources but not opened directly; the Andromeda post itself was verified live and supports the core claims.
- **Pricing / availability of platform-native agents** (Meta Muse tiers, Google Ads Advisor rollout) is still shifting and partly US-only; not relied on for the framework.
- **No Chinese-language sources were used**, per the mission; the search was worldwide English-language.
- **What this research did not do:** no live testing in Ads Manager / Ads API, no account-level data, and no verification of any startup's claimed funding or customer results beyond press reporting.

## Sources

- Curated articles #1–#12 above — each opened and verified live 2026-11-17; full verification log in `notes/curated-articles.md`.
- Landscape / framework sources (platform engineering posts, API docs via index, AdCP timeline, startup map) — `notes/landscape-sources.md`.
- Key index-only sources: Meta GEM (Engineering at Meta, 10 Nov 2025); Meta Marketing API overview, `developers.facebook.com/docs/marketing-api/overview`; Google Ads API Performance Max docs, `developers.google.com/google-ads/api/performance-max`; Meta Muse for Small Business (Meta Newsroom, 29 Sep 2026, via PPC Land); AdCP timeline (Alkimi / PPC Land / Star Global).
