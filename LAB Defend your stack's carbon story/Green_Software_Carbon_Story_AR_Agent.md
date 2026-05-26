# Green Software Carbon Story — A&R Artist Intelligence Agent (Believe)

**Author:** Daria Bystrova  
**Path:** B — Green Retrofit Audit  
**Project:** A&R Artist Intelligence Agent  
**GitHub:** https://github.com/dbystrova26/project3_autonomous_agent  
**Date:** May 2026

---

## What This Lab Is About (Plain English)

This lab asks: **how much compute and energy does your AI system use, and how could you use less?**

The Green Software Foundation (GSF) defines four pillars for sustainable software:

| Pillar | What it means for the AR Agent |
|---|---|
| **Carbon efficiency** | Emit less CO₂ per artist evaluation |
| **Energy efficiency** | Use less electricity per evaluation |
| **Hardware efficiency** | Get more useful work from existing machines; reduce idle time |
| **Measurement** | Actually track what you're using — you can't improve what you don't measure |

The goal is not to claim "carbon neutral" — it is to reason honestly about where compute concentrates, propose concrete improvements, and define what you would measure to prove they worked.

---

## Path B: Retrofit Audit

**Artifact:** A&R Artist Intelligence Agent — `agents/triage_chain.py`, `agents/research_graph.py`, `api/main.py`

**What the system does:** A user enters an artist name. The system calls 4 external APIs (Spotify, Last.fm, NewsAPI, YouTube), runs a LangChain triage agent (~15s, one Claude API call), and optionally runs a LangGraph research agent (~2 min, multiple Claude API calls + OpenAI embedding + Pinecone query). Every evaluation is logged to Google Sheets and triggers n8n automation. Hosted on Render.com free tier, Frankfurt EU region.

---

## Slide 2: One Unit of Value (R)

> **"One unit of value for this system is: one completed artist evaluation returning a SIGN/WATCH/PASS decision with reasoning."**

This is the functional unit (R) for SCI-style thinking. Everything — API calls, tokens, compute time, energy — should be measured per R.

A **research report** (full 9-section PDF) is a second, heavier unit of value: approximately 8–10× more expensive than a triage evaluation in compute terms.

---

## Slide 3: Assessment Defence — Hotspots

Where compute, data movement, and idle capacity concentrate in the system:

### Hotspot 1 — Claude API calls (highest cost)
Every triage evaluation makes **2 Claude API calls**: one for major label check, one for reasoning synthesis. Every research report makes **1–3 additional Claude calls** for synthesis. Claude Sonnet 4.5 is a large frontier model. Each call processes a long context (up to ~3000 tokens input). This is the single largest energy and cost driver.

### Hotspot 2 — Render.com free tier idle spin-down
The service spins down after 15 minutes of inactivity. Every cold start takes ~30–50 seconds. This is **wasted compute** — the server wakes up, processes nothing useful for the first 30 seconds, then handles the request. Frequent cold starts also increase total wall-clock time, meaning the user waits longer and the machine runs longer per R.

### Hotspot 3 — Repeated identical API calls (no caching)
The system calls Spotify, Last.fm, NewsAPI, and YouTube for every evaluation. If the same artist is evaluated twice within a day or two, all four API calls are repeated. NewsAPI already implements a 24h in-process cache for press data — but the streaming data (Last.fm, Spotify) and video data (YouTube) are re-fetched every time with no caching.

### Hotspot 4 — Research agent parallelism vs sequential waste
The LangGraph research agent runs 4 nodes. In the current implementation, these run sequentially, not truly in parallel. Each node waits for the previous one to complete before the next API call is made. This means the machine is idle during each API response wait — wasted compute time while holding a server connection open.

### Hotspot 5 — OpenAI embedding call for every research run
Every research report triggers an OpenAI embedding call to query Pinecone for roster similarity. The embedding is generated from a short text string (artist genre + metrics). This is low-cost but entirely cacheable — the same artist with the same metrics always produces the same embedding.

---

## Slide 4: Assessment Defence — Assumptions (Honest Guesses)

| Assumption | What I know | What I don't know |
|---|---|---|
| **Region / grid intensity** | Render.com Frankfurt EU — German grid is ~400g CO₂/kWh average, but varies significantly by hour and season. Better than US average. | Actual Render data centre power mix; whether Render purchases renewable energy certificates. |
| **Model size** | Claude Sonnet 4.5 — mid-range model, not the largest. Not a small/cheap model. | Actual compute per token on Anthropic's infrastructure; whether Anthropic uses renewable-powered data centres. |
| **Call pattern** | Currently: synchronous, one evaluation at a time, no batching. | Actual number of evaluations per day in production; peak vs off-peak usage patterns. |
| **Token counts** | Estimated ~2000–3000 tokens input per Claude triage call. ~6000–8000 tokens input per research synthesis call. | Exact token counts per call — not currently logged anywhere. |
| **Cold start frequency** | Free tier spins down after 15 min idle. In a real A&R workflow, evaluations may cluster (morning sessions, demo day). | Actual usage patterns; how often cold starts occur vs warm requests. |

**Skeptic challenge (Slide 3–4):** *"Why should we believe these are the real hotspots?"*

Answer: The hotspots are identified from code structure (API call count, token size estimates, lack of caching code), not from measurement. The measurement plan in Slide 10 would validate or revise these assumptions with real data.

---

## Slide 5: Pillars Map

| Hotspot | Carbon | Energy | Hardware | Measurement |
|---|---|---|---|---|
| Claude API calls (large model, no caching) | ✓ | ✓ | | ✓ |
| Render cold starts (idle waste) | | ✓ | ✓ | ✓ |
| Repeated API calls (no streaming cache) | ✓ | ✓ | | ✓ |
| Sequential research nodes (idle wait) | | ✓ | ✓ | ✓ |
| OpenAI embedding (uncached) | ✓ | ✓ | | ✓ |

**Primary pillar:** Energy efficiency — reducing electricity consumed per evaluation  
**Secondary pillar:** Hardware efficiency — reducing idle compute and cold start waste  
**Measurement is cross-cutting** — none of the improvements can be validated without logging tokens, call counts, and latency per R

---

## Slide 6: Proposed Solution 1 — Cache Streaming Data (Quick Win)

**Problem:** Spotify, Last.fm, and YouTube are re-fetched for every evaluation of the same artist. If Caribou is evaluated twice in 48 hours, all API calls repeat identically.

**Change:** Add a simple in-memory or Redis cache for streaming and video data with a 24-hour TTL. The system already does this for NewsAPI — extend the same pattern to all four data sources.

**Expected impact:** Eliminates 3–4 API calls per repeat evaluation. More importantly, it removes the 3–5 second latency for cached artists, reducing server active time per R.

**Pillar:** Energy efficiency · Carbon efficiency  
**GSF Pattern:** [Static Content Compression / Response Caching](https://patterns.greensoftware.foundation/)  
**Business outcome:** Faster response for repeat evaluations; reduced API rate limit risk (especially Spotify, which is already rate-limited); lower cost per evaluation.

---

## Slide 7: Proposed Solution 2 — Right-Size the Model for Triage (Medium)

**Problem:** The triage agent uses Claude Sonnet 4.5 for the major label check — a binary yes/no classification that does not require a frontier model. The check asks: "Is this artist signed to Universal, Sony, or Warner?"

**Change:** Route the major label check to Claude Haiku (smallest, cheapest, fastest model) and reserve Sonnet for the reasoning synthesis step which genuinely benefits from a larger model. Add a quality check: sample 20 evaluations and confirm Haiku's label check matches Sonnet's output rate ≥95%.

**Expected impact:** Major label check is ~500 tokens input. Switching to Haiku reduces cost by ~10× for that call and reduces energy per R by approximately 5–10% (rough estimate based on relative model size).

**Pillar:** Energy efficiency · Carbon efficiency  
**GSF Pattern:** Right-sizing model to task  
**Business outcome:** Lower API cost per evaluation; faster triage (Haiku responds in ~1s vs ~3s for Sonnet); quality maintained with sampling check.

**Caveat:** Do not switch blindly — validate with sampled outputs before deploying. Label names change (acquisitions, distribution vs ownership). Sonnet's richer knowledge may catch edge cases Haiku misses.

---

## Slide 8: Proposed Solution 3 — Parallelise Research Agent Nodes (Medium)

**Problem:** The LangGraph research graph runs 4 data nodes (Spotify/Last.fm, NewsAPI, YouTube, Pinecone) sequentially. Total wall-clock time is ~90–120 seconds. Each node idles while waiting for the previous API response.

**Change:** Run the 4 research nodes in parallel using LangGraph's native parallel execution support. The `synthesise` node still runs last (it depends on all 4 results) but the data collection phase can complete in the time of the slowest single API call (~3–5 seconds) instead of the sum of all four (~15–20 seconds).

**Expected impact:** Research report time: 90–120 seconds → 60–80 seconds. More importantly, the server holds the connection open for less time per R. Machine idle time during API waits is reduced.

**Pillar:** Energy efficiency · Hardware efficiency  
**Business outcome:** Significantly faster report generation (a key user experience pain point); reduced server load per report.

**Note:** This is already partially implemented in the LangGraph StateGraph design — the nodes are defined but not currently executing in parallel. This is a code change, not an architecture change.

---

## Slide 9: Proposed Solution 4 — Address Cold Start Waste (Strategic)

**Problem:** Render.com free tier spins down after 15 minutes of inactivity. Cold start takes 30–50 seconds. During this time the server is running, consuming energy, but producing no value. The user also waits — bad UX and wasted compute.

**Change (Option A — Cheap):** Add a scheduled lightweight ping every 10 minutes (a simple cron job that calls `/health`) to keep the service warm during business hours (e.g. 7am–8pm Frankfurt time). Outside business hours, let it spin down.

**Change (Option B — Better):** Upgrade to Render paid tier ($7/month) which eliminates cold starts entirely. At any meaningful production usage level, the cost of a human A&R manager waiting 45 seconds for a cold start exceeds $7/month in lost time.

**Expected impact (Option A):** Eliminates most cold starts during working hours. Small energy cost from the ping (a `/health` call is ~1ms compute). Net energy win because it prevents the spike of a cold start.

**Pillar:** Hardware efficiency · Energy efficiency  
**Business outcome:** Consistent 15-second triage response; no "API offline" errors from cold start timeouts; better demo reliability.

---

## Slide 10: Measurement Plan

**What to track for 2 weeks:**

| Metric | How to measure | Target |
|---|---|---|
| **Tokens per triage R** | Log `usage.input_tokens` and `usage.output_tokens` from every Anthropic API response | Baseline first week; reduce ≥10% after model right-sizing |
| **API calls per R** | Log each tool call in `triage_chain.py` with a counter | Reduce by 50% for repeat artists after caching |
| **Cold start frequency** | Log timestamp of first request after spin-down (response time > 5s = cold start) | Reduce to ≤1 per working day with keep-warm ping |
| **Research report wall-clock time** | Log start and end time per research run | Reduce from ~120s to ~80s after parallel nodes |
| **Cost per R (as proxy for energy)** | Anthropic + OpenAI API cost per evaluation (visible in dashboard) | Reduce ≥15% after Haiku switch for label check |

**What counts as success:**
- Repeat artist evaluations return in <2 seconds (cache hit) vs ~15 seconds (cache miss) — measurable and user-visible
- Triage cost per evaluation reduced by ≥15% after model right-sizing — verifiable from API billing
- Research reports complete in <90 seconds — measurable from logs

**SCI framing:** We would measure E (energy) per R (one evaluation) — currently unknown because Anthropic does not publish per-call energy data. Cost per R is the closest available proxy.

---

## Slide 11: Caveats and Honest Limits

**What I cannot claim:**
- I cannot claim the current system is "carbon neutral" or even "low carbon" — I have no energy data from Anthropic, OpenAI, or Render
- I cannot claim a precise CO₂ saving from any proposed improvement — I do not have grid intensity data for the Anthropic or OpenAI data centres at the time of each API call
- Offsets are not SCI. If Anthropic purchases carbon offsets, that does not reduce the actual electricity consumed per evaluation — it is a separate financial transaction

**What I can claim:**
- Caching repeat API calls reduces the number of external API calls per R — verifiable from logs
- Switching the label check to Haiku reduces token consumption for that call — verifiable from API usage data
- Parallelising research nodes reduces wall-clock time per R — measurable from application logs

**What I would validate next:**
1. Log actual token counts per evaluation for one week (currently not logged anywhere)
2. Check Anthropic's published data centre energy mix (if available) to estimate CO₂/token
3. Check whether Render.com Frankfurt purchases renewable energy certificates (their sustainability page)
4. Run a 2-week A/B test comparing Sonnet vs Haiku for major label check accuracy on 50 sampled artists

**Honest uncertainty:** The biggest unknown is not the code — it is the infrastructure. The same Python code running on a coal-powered server vs a renewable-powered server has a ~10–20× difference in carbon impact. Right-sizing models matters, but region and energy mix matter more for actual CO₂ impact.

---

## Slide 12: Before / After Hypothesis

| Improvement | Metric | Before (estimate) | After (target) | Confidence |
|---|---|---|---|---|
| Cache streaming data | API calls per R for repeat artist | 4 external API calls | 0 external API calls | High — deterministic |
| Haiku for label check | Tokens consumed per triage R | ~2800 tokens (Sonnet × 2) | ~2300 tokens (Sonnet × 1 + Haiku × 1) | Medium — depends on actual token counts |
| Parallel research nodes | Wall-clock time per research R | ~120 seconds | ~80 seconds | Medium — depends on API latency variance |
| Keep-warm ping | Cold starts per day | ~5–8 (estimated) | ≤1 per day | High — deterministic during business hours |

**Composite hypothesis:** If all four improvements are implemented, cost per triage R should decrease by approximately 15–25%, and research report time should decrease by approximately 30%. These would be verified from 2-week before/after measurement using API billing and application logs.

---

## Appendix: Retrofit Audit Template

**Artifact:** A&R Artist Intelligence Agent — `api/main.py`, `agents/triage_chain.py`, `agents/research_graph.py`

**Primary user journey:** User enters artist name → triage agent runs (~15s) → SIGN/WATCH/PASS returned → optional research report (~2 min) → Google Sheets logged → Slack alert sent

**Hotspots (compute / data movement):**
1. Claude Sonnet API calls — 2 per triage, 1–3 per research report — highest energy cost
2. Render cold starts — 30–50s of wasted compute per cold start
3. Uncached streaming API calls — repeated for same artist, no benefit
4. Sequential research nodes — idle server time during API waits
5. OpenAI embedding — small but cacheable

**Green opportunities (prioritised):**
1. Cache streaming/video data (24h TTL) — quick win, no quality risk
2. Right-size label check to Haiku — medium effort, requires quality validation
3. Parallelise LangGraph research nodes — medium effort, existing architecture supports it
4. Keep-warm ping or paid tier upgrade — low effort, eliminates cold start waste

**What I would measure to prove improvement:**
- Tokens per R (from Anthropic API usage logs)
- API calls per R for repeat artists (application log counter)
- Wall-clock time per research R (application log timestamps)
- Cost per R (API billing as proxy for energy)

**Risks / tradeoffs:**
- Haiku for label check may miss edge cases — requires quality sampling before deployment
- Keep-warm ping adds a small continuous energy cost — net positive only if it prevents more than ~2 cold starts per day
- Caching introduces staleness risk — a 24h cache means the system may miss a label signing that occurred today (low probability but non-zero)
