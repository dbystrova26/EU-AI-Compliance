# EU AI Act Compliance Audit — A&R Artist Intelligence Agent

**Prepared by:** Daria Bystrova  
**Date:** May 2026  
**Project:** A&R Artist Intelligence Agent (Believe — Ironhack AI Consulting Bootcamp, Project 3)  
**Live system:** https://ar-agent-zkjw.onrender.com  
**GitHub:** https://github.com/dbystrova26/project3_autonomous_agent

---

## Phase 1: System Brief

### What does the system do?

The A&R Artist Intelligence Agent is an autonomous research and decision-support tool for Believe, a global independent music distribution company. Its purpose is to help A&R (Artists & Repertoire) managers decide whether to pursue signing a new artist.

When a user enters an artist name, the system automatically pulls data from four external sources — Spotify, Last.fm, NewsAPI, and YouTube — and runs two consecutive AI agents:

1. **Triage Agent** (fast, ~15 seconds): scores the artist from 0 to 100 across five dimensions and returns one of three decisions — SIGN, WATCH, or PASS.
2. **Research Agent** (deep, ~2 minutes): generates a structured 9-section report with full analysis, risk factors, and a formal recommendation.

All evaluations are logged to Google Sheets. SIGN and WATCH decisions trigger automated Slack alerts to the A&R team.

### What inputs does it take?

- **Artist name** (text, entered by the user)
- **Streaming data**: monthly listeners, weekly listeners, playcount, velocity proxy — fetched from Spotify Web API and Last.fm API
- **Press data**: article count, outlet tier, recency — fetched from NewsAPI
- **Video data**: subscriber count, upload frequency, last upload date — fetched from YouTube Data API
- **Roster similarity**: the system compares the artist against 25 Believe artist profiles stored as vectors in Pinecone

None of the data is personal data in the GDPR sense — it is all publicly available professional/commercial information about artists acting in their professional capacity as public figures.

### What does it output?

- A **score (0–100)** calculated from weighted signals
- A **decision** (SIGN / WATCH / PASS)
- A **plain-language reasoning** paragraph explaining the decision
- A **9-section PDF/Markdown report** for SIGN, WATCH, and PASS decisions
- An **automated Slack message** for SIGN and WATCH
- A **row appended to Google Sheets** for every decision

### Who is affected by the output?

The immediate users are Believe's A&R managers and consulting staff. The output influences which artists are considered for a distribution/signing deal.

Artists are indirectly affected: a PASS decision means they will not be contacted or signed through this pipeline. However, the system operates as a **pre-screening tool**, not a final decision. A human A&R manager reviews all outputs before any contact or contract is made.

### Does a human review the output before any action is taken?

**Yes.** The system includes a built-in "Approve Signing" field in the Watchlist & Universe tab, where a human manager can independently mark an artist as approved (✓ Sign) or rejected (✗ Pass), add notes, and override the agent's recommendation at any point. No contract, outreach, or business action is taken automatically by the system. The agent's decision is explicitly framed as a **suggestion**, not a final determination.

### Who built it?

Built by Daria Bystrova, an Ironhack AI Consulting Bootcamp student, as a client project for Believe. The system uses third-party AI APIs: Anthropic Claude (reasoning and report synthesis), OpenAI (embeddings for roster similarity), Spotify, Last.fm, NewsAPI, YouTube, and Pinecone.

### Who would use it in production?

Believe's internal A&R team and management. Access would be restricted to authorised staff. The system would not be exposed to the general public or to the artists being evaluated.

---

## Phase 2: Risk Tier Classification

| Question | Answer |
|---|---|
| Does this system fall under any prohibited category (Article 5)? | No. The system does not perform social scoring, real-time biometric surveillance, subliminal manipulation, or exploitation of vulnerabilities. |
| Does this system operate in any of the eight Annex III areas? | Partially. Annex III includes "employment, workers management and access to self-employment" (Area 4). Signing decisions are commercially analogous to employment access decisions for working musicians. |
| If Annex III: does it "significantly influence" decisions in that area, or is it narrow/preparatory? | Preparatory/narrow. The system produces a recommendation, not a binding decision. A human A&R manager reviews every output before any action is taken. The system does not send outreach, trigger contracts, or communicate with the artist. |
| Does this system interact with end users or generate content requiring disclosure (Article 50)? | Limited. The system does not interact with artists (the affected parties). It interacts only with internal Believe staff. No AI-generated content is presented to the public. |
| **First-pass risk tier** | **Limited risk / minimal risk — borderline** |
| One-sentence justification | The system operates in a domain adjacent to Annex III Area 4 (employment/self-employment access) but functions as a preparatory research tool with mandatory human review before any action, placing it closer to minimal risk; however, its influence on commercial access for artists warrants treating it as **limited risk** and applying proportionate transparency measures. |

**Ambiguity note:** Whether signing an artist to a distribution deal constitutes "access to self-employment" under Annex III Area 4 is genuinely ambiguous. If a regulator or court interpreted it that way, the system could be classified as **high-risk**, triggering the full Article 9–15 obligation set. This question should be escalated to legal review before any production deployment. For this audit, the more cautious limited-risk classification is used and high-risk obligations are partially assessed.

---

## Phase 3: Role Map

| Role | Entity | Key AI Act obligations |
|---|---|---|
| **Provider** | Daria Bystrova / Ironhack (system builder) | Technical documentation (Art. 11), transparency information (Art. 13), human oversight design (Art. 14), accuracy and robustness (Art. 15), conformity assessment if high-risk (Art. 43) |
| **Deployer** | Believe (the client using the system in a professional context) | Ensure human oversight is exercised in practice (Art. 26), monitor for risks, do not use the system for purposes beyond its intended scope, maintain logs |
| **Vendor — Anthropic** | Anthropic (Claude API) | Anthropic is a provider of a general-purpose AI model (GPAI) under Art. 51–53. They provide technical documentation and usage policies. Their obligations do not transfer to us, but we must use their model within its documented intended purposes. |
| **Vendor — OpenAI** | OpenAI (embeddings API) | GPAI provider. Same framework as Anthropic. Embeddings are used only for vector similarity, not for decision-making directly. |
| **Vendor — Pinecone, Spotify, Last.fm, NewsAPI, YouTube** | Various data/infrastructure providers | These are not AI systems under the Act. They are data sources. No AI Act obligations apply to them in this context. |

---

## Phase 4: Obligation Checklist (High-Risk Assessment — Precautionary)

Given the legal ambiguity about Annex III classification, a precautionary high-risk checklist is completed below.

| Obligation | Article | Status | Note |
|---|---|---|---|
| Risk management system | 9 | **Partial** | Informal risk thinking was done (major label check, fallback logic, human override). No formal documented risk management process exists. |
| Data and data governance | 10 | **Partial** | Data sources are documented. No formal data governance policy. All data is public/commercial — no personal data. Pinecone dataset is 25 simulated profiles, not real Believe roster data. |
| Technical documentation | 11 | **Partial** | README, code comments, and this audit exist. No formal EU-format technical documentation package. |
| Record-keeping and logging | 12 | **Met** | Every evaluation is logged to Google Sheets with timestamp, artist, score, decision, reasoning, data source, and listeners. Render logs retain API call history. |
| Transparency and user information | 13 | **Partial** | The interface makes clear the output is an AI agent recommendation. No formal transparency notice explaining the system's logic, data sources, or limitations to users. |
| Human oversight | 14 | **Met** | The Watchlist & Universe tab has explicit "Approve Signing" (✓ Sign / ✗ Pass) controls. No action is taken without human approval. The agent's decision is framed as a suggestion throughout the UI. |
| Accuracy, robustness, cybersecurity | 15 | **Partial** | Retry logic and Last.fm fallback provide some robustness. No formal accuracy benchmarking. No security audit. Free-tier Render deployment has no authentication. |
| Conformity assessment | 43 | **Gap** | Not completed. Would require third-party or self-assessment depending on final risk classification. |
| EU declaration of conformity + CE marking | 47–48 | **Gap** | Not completed. Required only if system is formally classified as high-risk. |
| Registration | 49 | **Gap** | Not registered. Required only if classified as high-risk. |
| Post-market monitoring | 72 | **Gap** | No formal post-market monitoring plan. Google Sheets log provides a basic audit trail but no systematic monitoring process. |

---

## Phase 5: Gap Analysis and Remediation Plan

### Gap 1

**Obligation:** Risk management system (Article 9)  
**Current state:** Risk thinking was implicit in design decisions (major label check, human override, scoring thresholds) but never formally documented.  
**Required state:** A documented, iterative risk management process covering foreseeable risks to artists and to Believe, mitigation measures, and residual risk assessment.  
**Remediation:** Create a one-page risk register documenting identified risks (e.g. false PASS for an eligible independent artist, data quality degradation if Last.fm is unavailable, scoring bias toward genres with better press coverage), mitigations already in place, and residual risks.  
**Escalation needed:** No — can be done internally, but legal review recommended before production deployment.

---

### Gap 2

**Obligation:** Technical documentation (Article 11)  
**Current state:** README, code comments, and this audit exist. No formal EU-format technical file.  
**Required state:** Documentation covering system description, development process, training data (if applicable), intended purpose, performance metrics, and known limitations — structured to allow a conformity assessment.  
**Remediation:** Compile the existing README, scoring model description, and this audit into a structured technical file. Add a limitations section explicitly noting: data is a proxy (Last.fm weekly × 4), velocity is estimated not measured, press coverage may undercount electronic-specific outlets.  
**Escalation needed:** No for documentation; yes for conformity assessment.

---

### Gap 3

**Obligation:** Transparency and user information (Article 13)  
**Current state:** The UI shows the agent's recommendation with reasoning. The project README documents proxy metric limitations (Last.fm weekly ×4 proxy, estimated velocity, potential press undercounting for electronic artists). However, this information is not surfaced inside the interface at the point of use.  
**Required state:** Users should receive clear information about the system's capabilities and limitations, how scores are calculated, and what human oversight applies.  
**Remediation:** Add a "How this works" modal or help tooltip inside the interface, pointing users to the README documentation and summarising the key limitations at the point of use: proxy metrics, estimated velocity, potential press undercounting for electronic artists. The README already contains the content — it just needs to be accessible within the tool itself.  
**Escalation needed:** No.

---

### Gap 4

**Obligation:** Accuracy, robustness, cybersecurity (Article 15)  
**Current state:** No authentication on the deployed interface. No formal accuracy testing. Robustness relies on retry logic and fallback to Last.fm.  
**Required state:** The system should be accessible only to authorised users. Accuracy should be benchmarked. Known failure modes should be documented.  
**Remediation:** (1) Add authentication (API key or OAuth) before any production use. (2) Document known accuracy limitations: Last.fm monthly listener proxy has ±20% error vs Spotify direct. (3) Add a "data confidence" indicator to the interface showing when Last.fm fallback was used.  
**Escalation needed:** No for authentication and documentation; yes for formal security audit.

---

### Gap 5

**Obligation:** Post-market monitoring (Article 72)  
**Current state:** Google Sheets log exists but no monitoring process.  
**Required state:** A process to detect and report serious incidents and to monitor for performance drift or misuse.  
**Remediation:** Define a quarterly review process: check Sheets log for systematic patterns (e.g. disproportionate PASS rates for specific genres), verify data source availability, and review any cases where the human manager overrode the agent's recommendation. Document outcomes.  
**Escalation needed:** No for monitoring process; yes if serious incident occurs.

---

### Gap 6

**Obligation:** Conformity assessment, EU declaration of conformity, registration (Articles 43, 47–49)  
**Current state:** None of these exist.  
**Required state:** Required only if the system is formally classified as high-risk.  
**Remediation:** This gap cannot be closed without first resolving the legal ambiguity about Annex III classification (see Phase 2 ambiguity note). If classified as high-risk, a notified body or self-assessment process would be required.  
**Escalation needed:** **Yes — legal review required** before production deployment to determine whether Annex III Area 4 applies.

---

## Phase 6: Compliance Memo

**TO:** Head of Product, Believe  
**FROM:** Daria Bystrova, AI Consulting  
**RE:** EU AI Act Compliance Assessment — A&R Artist Intelligence Agent  
**DATE:** May 2026  

---

**System classification**

The A&R Artist Intelligence Agent has been assessed as **limited risk** under the EU AI Act, on the basis that it functions as a preparatory research tool with mandatory human review before any business action is taken. However, there is a genuine legal ambiguity: signing decisions are commercially analogous to access to self-employment, which is covered under Annex III Area 4 (high-risk). This ambiguity must be resolved by legal counsel before the system is used in production.

**Role map**

The system was built by the consulting team (provider role). Believe, as the organisation deploying the system in a professional context, holds the deployer role. Third-party AI services — Anthropic Claude and OpenAI embeddings — are general-purpose AI providers under the Act; their obligations do not transfer to Believe, but the system must be used within their documented intended purposes.

**Key findings**

1. **No formal risk documentation exists.** Design decisions were made thoughtfully, but no documented risk register, technical file, or conformity assessment has been produced. If the system is reclassified as high-risk, this would be the most significant compliance gap.

2. **The deployed interface has no authentication.** Any person with the URL can access the system. Before any production use with real artist data or internal decisions, access must be restricted to authorised Believe staff.

3. **The scoring model uses proxy metrics that are partially disclosed.** Monthly listeners are estimated from weekly data (×4 multiplier), velocity is inferred from playcount ratios, and press coverage may undercount electronic-specialist outlets. These limitations are documented in the project README. However, they are not surfaced to users at the point of use — inside the interface itself. A user relying on the agent's recommendation without reading the README would not be aware of these limitations. A brief in-app disclosure is recommended.

**Recommended next steps**

1. **Immediately:** Restrict access via authentication before any use with real business decisions.
2. **Within 2 weeks:** Engage legal counsel to determine whether the system falls under Annex III Area 4. This is the single most important compliance question.
3. **Within 1 month:** Add a transparency notice to the interface explaining data sources, scoring weights, and known limitations.
4. **Within 1 month:** Compile a basic technical file (system description, scoring model, data sources, limitations) and a one-page risk register.
5. **Ongoing:** Establish a quarterly monitoring review using the existing Google Sheets log.
6. **If classified high-risk:** Commission a conformity assessment and register the system before deployment.

**Caveats**

This memo is not a legal opinion, a conformity assessment, or a certification. It is a first-pass compliance assessment produced by a consultant for internal planning purposes. The legal determination of risk tier — particularly the Annex III question — requires qualified legal advice. No reliance should be placed on this memo for regulatory purposes without independent legal review.

---

## Reinforce: Design Decision Review

**Component I minimized:** The Pinecone roster similarity search uses 25 **simulated** artist profiles, not real Believe data. In the audit, this matters because the similarity scores used to validate recommendations are based on fictional data. If a real Believe roster were used, the system's outputs would be more reliable — but it would also introduce real artist data into the pipeline, which could raise data governance questions if those artists had not consented to being used as benchmark profiles.

**What I would have done differently:** I would have documented the scoring model and data sources in a structured technical file from the start of the project, rather than reconstructing it for compliance purposes. Designing with documentation-by-default would have made this audit significantly faster and more accurate.

---

## Stretch: Human Oversight Procedure

**Artifact: Human Oversight Procedure — A&R Agent Signing Decisions**

**Scope:** Applies to all SIGN and WATCH decisions produced by the A&R Artist Intelligence Agent.

**Who reviews:** The assigned A&R manager for the relevant market/genre.

**What they check:**
1. Is the data source reliable? (Was Last.fm fallback used? If so, verify streaming numbers independently where possible.)
2. Does the press coverage reflect actual music press, or is it lifestyle/general coverage?
3. Is the artist still independent? (Verify current label status via a direct check — the agent's major label check is based on Claude's knowledge and may not reflect recent signings.)
4. Does the score reflect the full picture? (Consider live activity, social presence, and touring data not captured by the agent.)

**How overrides are recorded:** The Watchlist & Universe tab provides an "Approve Signing" field (✓ Sign / ✗ Pass) and a free-text Notes field. The manager's decision and reasoning must be recorded before any outreach is initiated.

**Who can override:** Any authorised A&R manager. No technical restriction prevents overriding the agent's recommendation in either direction.

**Audit trail:** All agent decisions and human override decisions are retained in Google Sheets with timestamps. This log should be reviewed quarterly.
