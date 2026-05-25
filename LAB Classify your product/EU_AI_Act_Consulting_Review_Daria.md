# EU AI Act Approval Pack — Daria Bystrova
**Consulting Review — Partner's Four Cases | May 2026**

---

## Partner's Case Briefs

### Case 1 — European retail bank

A mid-sized European retail bank is facing pressure to speed up consumer lending decisions while reducing default rates. The bank wants to introduce an AI-supported assessment system that combines customer application forms, transaction history, employment data, repayment patterns, and external credit bureau information to generate a "financial reliability score." Loan officers receive the score together with a recommendation ranging from "strongly approve" to "high repayment risk." The bank says final decisions remain with human staff, but internal guidance encourages employees not to deviate from the system recommendation without justification. The tool is expected to process several thousand applications per week and would initially be used for unsecured personal loans and small business credit requests.

### Case 2 — Fintech startup

A fintech startup is expanding its mobile banking app and wants to reduce pressure on its customer support team. The company plans to deploy an AI-powered assistant that answers questions about savings products, explains fees, and helps users navigate account services through text and voice conversations. The assistant is built on top of a third-party large language model and is designed to sound conversational and informal to create a more "human" customer experience. During testing, some users believed they were chatting with a real support employee because the assistant introduces itself by name and responds in a highly personalised style. The company also wants the assistant to provide general guidance on whether customers "might be eligible" for certain financial products before they submit a formal application.

### Case 3 — National retail chain

A national retail chain wants to improve how it distributes products across more than 200 store locations. The company is considering an AI forecasting system that analyses historical sales figures, seasonal trends, local weather forecasts, shipment schedules, and regional shopping behaviour to recommend weekly inventory allocations. Store managers receive suggested stock orders through an internal dashboard and can accept, modify, or completely ignore the recommendations before placing supplier orders. The system is not connected to hiring, pricing, customer profiling, or employee monitoring functions, and the company states that the tool is intended only to reduce waste and improve stock availability during peak sales periods.

### Case 4 — Multinational logistics company

A multinational logistics company has struggled with high turnover among warehouse trainees and remote support staff during the first six months of employment. To improve retention, the company is piloting a workforce analytics platform during onboarding and weekly virtual briefings. The system analyses webcam footage, speech patterns, pauses, and facial movements to generate "engagement trend reports" that managers can review alongside attendance and productivity metrics. HR believes the tool could help identify employees who are losing motivation early so supervisors can intervene before performance drops. Employees are informed that meetings are recorded for "training and quality improvement purposes," but most staff do not receive a detailed explanation of how the analytics work. Supervisors can technically disregard the reports, although regional managers are expected to use them as part of performance follow-ups.

---

## Executive Summary

This review covers four client cases across all AI Act risk tiers. One case is prohibited and must stop immediately, two are approvable with specific controls, and one is a clean approval.

**Case 4 is the most urgent matter.** The workforce engagement analytics platform infers employee emotions from webcam and speech data during work meetings — prohibited under Art. 5(1)(f) since February 2, 2025. There is no compliance path. Both the vendor and the logistics company face fines of up to EUR 35 million or 7% of global annual turnover. Immediate action is required.

**Case 1 is approvable**, but the human oversight design must be fixed before go-live. Internal guidance that discourages loan officers from overriding the AI recommendation without justification makes the AI the de facto credit decision-maker — that fails Art. 14. The fix is operational, not technical, but it must be documented and enforced.

**Case 2 has a straightforward Art. 50 disclosure fix** for the chatbot itself, but the eligibility guidance feature needs a separate legal review before launch — it may be high-risk under Annex III area 5 depending on how influential the output is in practice.

**Case 3 is a clean approval.** The inventory forecasting system is outside any Annex III area, involves no personal data, and preserves full human override. Document the scope boundary so future extension into HR or customer profiling triggers immediate re-classification.

---

## Consulting Review Table

| Case | Likely category | Why this is your first-pass call | Proposed AI architecture | Provider / deployer / vendor | Required obligations or controls | Decision |
|---|---|---|---|---|---|---|
| **1** Bank credit scoring | **HIGH-RISK** Annex III, area 5 | Creditworthiness assessment AI directly influencing loan decisions. Annex III area 5 explicitly covers credit scoring. 'Human sign-off' does not reduce tier — internal guidance discouraging deviation makes AI the de facto decision-maker. | Inputs: application, transaction history, employment, credit bureau. AI scores application and returns tiered recommendation. Loan officer sees score → formal sign-off. All decisions logged. | Provider: vendor (11 obligations). Deployer: bank — qualifying financial institution, FRIA required. Third-party vendor: yes. | FRIA before go-live. Redesign internal guidance — genuine freedom to override. Art. 13 / GDPR Art. 22: inform applicants. Log retention 6 months. Vendor conformity docs. Dec 2, 2027. | ⚠️ **Approve with controls** |
| **2** Fintech chatbot | **LIMITED RISK** Art. 50(1) + possible HIGH-RISK (eligibility feature) | Chatbot uses human name, no disclosure — users believed they were talking to a real employee. Textbook Art. 50 violation. Eligibility guidance feature may additionally trigger Annex III area 5. | Inputs: user messages, account context, product knowledge base. LLM generates responses. Chatbot handles queries autonomously; escalates out-of-scope to human. Eligibility guidance inline — no human review before output. | Provider: fintech startup (downstream product provider). Deployer: also the startup (B2C direct). GPAI vendor carries GPAI obligations separately. 'Powered by X' does not shift product obligations. | Art. 50(1): AI disclosure at conversation start — mandatory from Aug 2, 2026. Eligibility feature: hold pending legal review; if high-risk, conformity assessment required. Human escalation path. GDPR Art. 22 review. | ⚠️ **Approve with controls** |
| **3** Retail inventory AI | **MINIMAL RISK** No AI Act obligations | Inventory forecasting with full human override. No Annex III area in play — system affects stock levels, not people. No personal data. Store managers accept, modify, or ignore every recommendation. | Inputs: historical sales, seasonal trends, weather, shipment schedules, regional behaviour. Model produces weekly stock recommendations. Manager reviews in dashboard → accepts / modifies / ignores → places order. | Provider: forecasting vendor (standard software obligations only). Deployer: retail chain — no specific AI Act deployer obligations for minimal-risk system. Third-party vendor: yes. | No AI Act obligations. Confirm no customer profiling embedded (GDPR check). Document system scope — if extended to HR or customer profiling, re-classify immediately. | ✅ **Approve** |
| **4** Workforce engagement analytics | **PROHIBITED** Art. 5(1)(f) | System infers employee emotions from webcam footage, facial movements, and speech patterns during work meetings. Art. 5(1)(f) prohibits emotion inference in workplace contexts — in force since Feb 2, 2025. Nominal manager override does not save a prohibited system. | Inputs: webcam video, speech audio, facial movement, pause patterns — captured during work meetings. System infers engagement/emotional state → weekly 'engagement trend report' per employee → used in performance follow-ups. | Provider: vendor. Deployer: logistics company. Both face liability. No contractual transfer of prohibition is possible. | DENY. No compliance path. Remove emotion inference entirely. Redesign with objective signals. Notify vendor immediately. Fines: up to EUR 35m or 7% global turnover. | ❌ **Deny and redesign** |

---

## Case 1 — European retail bank: financial reliability scoring

### First-pass classification

**HIGH-RISK — Annex III, area 5:** creditworthiness assessment and access to credit. A system generating a scored recommendation that directly influences consumer loan and SME credit decisions falls squarely within this category. The 'final decision remains with humans' framing does not remove the classification — risk tier is determined by the area of impact, not by whether a human formally signs off.

> **Note:** Internal guidance discouraging deviation without justification makes the AI the de facto decision-maker. This is nominal human oversight, not real oversight under Art. 14.

### AI architecture and operating model

| Element | Detail |
|---|---|
| **What it does** | Generates a 'financial reliability score' and tiered recommendation (strongly approve → high repayment risk) for each loan application. |
| **Inputs** | Application forms, transaction history, employment data, repayment patterns, external credit bureau data. |
| **Human in the loop** | Loan officers receive score and recommendation and formally sign off — but internal guidance discourages unaided deviation. Must be redesigned to be substantive, not nominal. |
| **Provider** | The bank if built internally. If procured from a vendor, the vendor is the provider; the bank is the deployer. |
| **Deployer** | The bank in either scenario. As a financial institution it is a qualifying deployer — FRIA required before go-live. |
| **Third-party vendor** | Likely. Vendor carries the 11 provider obligations; the bank carries deployer obligations and the FRIA. |

### Key obligations

Provider: all 11 obligations (risk management, data governance, technical documentation, logging, instructions for use, human oversight design, accuracy/robustness, conformity assessment, Declaration of Conformity, CE marking, registration). Deployer (bank): FRIA before deployment; genuine human oversight — loan officers must be empowered to override without justification penalty; log retention 6 months minimum; inform applicants an AI system was used (Art. 13 / GDPR Art. 22). **Deadline: December 2, 2027.**

### Decision

> ⚠️ **APPROVE WITH CONTROLS**
>
> Controls required: (1) redesign internal guidance so loan officers are genuinely free to override; (2) complete FRIA; (3) GDPR Art. 22 compliance — inform applicants and provide right to human review; (4) confirm vendor conformity assessment documentation.

---

## Case 2 — Fintech startup: AI customer support and eligibility guidance

### First-pass classification

**LIMITED RISK / TRANSPARENCY — Art. 50(1):** AI interacting with natural persons must disclose it is an AI. The chatbot introduces itself by name and behaves in a highly personalised way — testing confirmed users believed they were talking to a real employee. This is a textbook Art. 50 violation. The eligibility guidance feature may additionally push the system toward HIGH-RISK under Annex III, area 5 depending on how binding the output is in practice. Both layers must be addressed.

> **Note:** GPAI layer: the chatbot is built on a third-party LLM. The LLM provider carries GPAI-tier obligations. The fintech startup is the downstream product provider — 'powered by [model]' does not transfer product-level obligations upward.

### AI architecture and operating model

| Element | Detail |
|---|---|
| **What it does** | Answers customer support questions via text and voice; provides informal eligibility guidance on financial products before formal application. |
| **Inputs** | User messages (text/voice), account context, product and fee knowledge base. |
| **Human in the loop** | Not described — appears fully automated. A human escalation path must be added for complex queries and all eligibility guidance interactions. |
| **Provider** | The fintech startup — built and deployed under its own name on top of a third-party LLM. The startup is the downstream product provider. |
| **Deployer** | Also the fintech startup (direct B2C). Provider and deployer are the same entity. |
| **Third-party vendor** | Yes — the LLM provider carries GPAI-tier obligations. The startup cannot rely on this to reduce its own product-level obligations. |

### Key obligations

Art. 50(1): disclose clearly at the start of each conversation that the user is interacting with an AI — mandatory from August 2, 2026. For the eligibility guidance feature: if it constitutes a recommendation materially influencing a credit application, Annex III area 5 may apply — legal review required before launch. GDPR Art. 22 may also apply if the guidance constitutes automated decision-making with significant effect.

### Decision

> ⚠️ **APPROVE WITH CONTROLS**
>
> Support chatbot: approve with disclosure fix — add a clear AI identification statement at conversation start. Eligibility guidance feature: hold pending legal review; if high-risk, conformity assessment required before launch. Consider limiting eligibility guidance to factual product criteria only, not personalised assessments.

---

## Case 3 — National retail chain: AI inventory forecasting

### First-pass classification

**MINIMAL RISK — no specific AI Act obligations.** The system recommends weekly inventory allocations based on sales, seasonal trends, weather, and regional behaviour. Store managers can accept, modify, or ignore every recommendation. No Annex III area is in play — the system affects only stock levels, not people. No personal data involved.

> **Note:** The client has correctly scoped the system — no personal data of customers or employees, no consequential individual decisions, no Annex III area.

### AI architecture and operating model

| Element | Detail |
|---|---|
| **What it does** | Analyses sales, seasonal, weather, and regional data to recommend weekly stock allocations for 200+ store locations. |
| **Inputs** | Historical sales figures, seasonal trends, local weather forecasts, shipment schedules, regional shopping behaviour. |
| **Human in the loop** | Store managers review recommendations on a dashboard and decide to accept, modify, or ignore before placing supplier orders. Human decision is always the final step. |
| **Provider** | The forecasting platform vendor — standard software obligations only; no AI Act high-risk requirements. |
| **Deployer** | The retail chain — no specific AI Act deployer obligations for a minimal-risk system. |
| **Third-party vendor** | Likely. Standard commercial due diligence applies; no AI Act conformity assessment required. |

### Key obligations

No specific AI Act obligations. Confirm no customer-level profiling is embedded (GDPR check). If the system is ever extended to include employee performance tracking or customer profiling, re-classify immediately.

### Decision

> ✅ **APPROVE**
>
> Clean approval. Recommend the client document the system scope in writing — specifically that it does not connect to HR, pricing, customer profiling, or employee monitoring — so scope creep triggers re-classification.

---

## Case 4 — Multinational logistics company: workforce engagement analytics

### First-pass classification

**PROHIBITED — Art. 5(1)(f):** AI systems that infer the emotions of natural persons in the context of the workplace. The system analyses webcam footage, facial movements, speech patterns, and pauses during work meetings to generate 'engagement trend reports' used in performance follow-ups. This is emotion inference in an employment context — exactly what Art. 5(1)(f) prohibits. Supervisors can technically disregard the reports: irrelevant — the prohibition covers the act of inferring emotions, not the downstream use. In force since February 2, 2025.

> **Note:** Unlike Case 1, there is no redesign that preserves the emotion-inference feature and becomes compliant. The feature must be removed entirely.

### AI architecture and operating model

| Element | Detail |
|---|---|
| **What it does** | Analyses webcam footage, facial movements, speech patterns, and pauses during onboarding and virtual briefings to generate weekly 'engagement trend reports' per employee. |
| **Inputs** | Webcam video, speech audio, facial movement data, pause patterns — captured during work meetings. |
| **Human in the loop** | Supervisors can review reports and technically disregard them, but regional managers are expected to use them in performance follow-ups. Oversight does not save a prohibited system. |
| **Provider** | The workforce analytics platform vendor. If the client commissioned a bespoke build, the client may also carry provider responsibility. |
| **Deployer** | The logistics company — deploying in an employment context and directly executing a prohibited practice, regardless of vendor origin. |
| **Third-party vendor** | Yes. The vendor cannot contractually transfer prohibition liability to the client. |

### Decision

> ❌ **DENY AND REDESIGN**
>
> Deny the system as proposed. Offer the client a redesigned retention programme built on objective engagement signals. Notify the vendor immediately — deploying in the EU after February 2, 2025 exposes both parties to fines of up to EUR 35m or 7% of global turnover.

### Lawful redesign option

Remove all emotion inference, facial movement analysis, and speech sentiment processing. Replace with objective, consent-based signals:

- **Attendance and punctuality records** — already available in HR systems, no new data collection needed.
- **Task completion rates and response times** — objective, system-generated signals with no inference required.
- **Voluntary pulse surveys** — short anonymised weekly check-ins asking employees directly about workload and support. Opt-in only.
- **Structured manager one-to-ones** using a conversation guide — logged by the manager, not the AI.
- **Training completion and participation rates** from the LMS.

Meetings may still be recorded for quality assurance if employees give proper informed consent, but facial movement and sentiment analysis must be disabled. This redesign achieves the same business goal — identifying disengaging employees early — without any prohibited inference.

---

## Summary

| Case | Classification | Decision |
|---|---|---|
| 1 — Bank credit scoring | HIGH-RISK (Annex III, area 5) | ⚠️ Approve with controls |
| 2 — Fintech support chatbot | LIMITED RISK / TRANSPARENCY (Art. 50) + possible HIGH-RISK (eligibility feature) | ⚠️ Approve with controls |
| 3 — Retail inventory forecasting | MINIMAL RISK | ✅ Approve |
| 4 — Workforce engagement analytics | PROHIBITED (Art. 5(1)(f)) | ❌ Deny and redesign |

---

## Case 1 — Mini Implementation Roadmap

**Bank credit scoring AI | High-risk, Annex III area 5 | Deadline: December 2, 2027**

| Before market placement (provider obligations) | Before first use (deployer obligations) | Evidence to request from vendor |
|---|---|---|
| **Risk management system (Art. 9)** — documented, iterative, covering known and foreseeable risks for credit-scoring use. | **FRIA completed** — specific to the bank's deployment context, applicant population, and loan types. | **Conformity assessment report** — full Annex IV technical documentation package. |
| **Data governance (Art. 10)** — bias testing across demographic groups (age, gender, nationality, income bracket). Results documented. | **Human oversight policy redesigned** — written internal guidance must make clear that loan officers are free and expected to override the AI. Remove any language penalising deviation without justification. | **Bias and fairness test results** — breakdown of model performance across protected demographic groups. |
| **Technical documentation (Art. 11)** — Annex IV package: model architecture, training data sources, performance metrics, drift monitoring plan. | **Staff training** — loan officers must understand what the score means, what it does not capture, and how to document an override. | **Data governance documentation** — training data sources, labelling methodology, data quality checks, and how historical lending bias was assessed and mitigated. |
| **Automatic logging (Art. 12)** — all inputs, outputs, and anomalies retained for at least 6 months. | **Applicant disclosure** — inform applicants in writing that an AI system was used (Art. 13 / GDPR Art. 22). Provide right to request human review. | **Drift monitoring plan** — how the vendor detects and responds to model degradation post-deployment. |
| **Instructions for use (Art. 13)** — covering intended use, limitations, override procedures, and maintenance schedule. | **Log retention policy** — bank retains system-generated logs for a minimum of 6 months, longer if sector regulation requires. | **Audit log specification** — exact fields captured per transaction, retention format, and access controls. |
| **Human oversight built in (Art. 14)** — system must allow loan officers to interpret outputs and override without penalty. | **Incident reporting procedure** — defined escalation path if system produces anomalous outputs or serious incidents (15-day reporting obligation). | **SLA for serious incident reporting** — vendor's defined response time if a model failure is identified post-deployment. |
| **Accuracy and robustness testing (Art. 15)** — performance across applicant sub-groups; adversarial/edge case testing. | | **Previous deployment references** — evidence the model has been validated in comparable EU lending contexts. |
| **Conformity assessment (Art. 43)** — self-assessment for this category; results on file. | | |
| **EU Declaration of Conformity (Art. 47)** signed and **CE marking (Art. 48)** affixed. | | |
| **Registration (Art. 49)** in EU public database before go-live. | | |

---

*EU AI Act lab — Daria Bystrova | Ironhack AI Consulting & Integration | May 2026 | Not legal advice.*
