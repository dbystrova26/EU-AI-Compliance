# GDPR & Privacy Stack — Discussion Prep

**Daria Bystrova · Week 6 · Day 3**

---

## Part 1 — Scope and Personal Data

**Q1. A US company with no EU offices collects data from EU users. Does GDPR apply?**

Yes. GDPR applies under the extraterritorial scope of Article 3(2) — the "targeting criterion." If a company offers goods or services to EU residents, or monitors the behaviour of EU residents (e.g. through tracking cookies, analytics, or personalisation), GDPR applies regardless of where the company is based. Having no EU office is irrelevant. The company would need to appoint an EU representative under Article 27.

---

**Q2. Personal data classification**

| Data | Personal data? | Why |
|---|---|---|
| A name and email address | Yes | Direct identifiers — unambiguously link to a specific natural person |
| An IP address | Yes (usually) | The CJEU has confirmed dynamic IPs can be personal data if the controller can reasonably identify the individual; static IPs almost certainly are |
| A pseudonymised user ID (key held separately) | Yes | Pseudonymised data is still personal data under GDPR — the key exists and re-identification is possible; it is not anonymised |
| Machine sensor data from a factory robot | No (usually) | No natural person is identified or identifiable from temperature/pressure/speed data; unless linked to operator shifts or biometric patterns, it falls outside GDPR |
| An AI model trained on customer chat logs | Yes (likely) | The model may have memorised personal data from training; if the original logs were personal data, the model derived from them requires careful analysis — see Recital 26 and recent regulatory guidance |

---

**Q3. Pseudonymisation vs anonymisation — why it matters for AI**

**Pseudonymisation** replaces direct identifiers (name, email) with a key, but the original data can be re-identified if the key is accessible. The data remains personal data under GDPR — all obligations continue to apply.

**Anonymisation** is the irreversible removal of all identifying information such that re-identification is not reasonably possible by anyone. Only truly anonymised data falls outside GDPR's scope entirely. The bar is extremely high — the EDPB requires that re-identification be impossible even with all available means.

**Why it matters for AI:** Many AI projects call data "anonymised" after removing names and emails, but retain quasi-identifiers (age + postcode + job title) that can be re-identified when combined. This is pseudonymisation at best. Teams building training datasets or RAG pipelines must pass the full anonymisation test — not just a field-removal check — before claiming GDPR does not apply.

---

## Part 2 — Roles and Responsibilities

**Q4. Controller vs processor**

A **controller** determines the purposes and means of processing — it decides why and how personal data is used. A **processor** processes personal data on behalf of the controller, following the controller's instructions. The distinction matters because controllers bear the primary compliance burden (lawful basis, privacy notices, DPIAs, data subject rights responses). Processors have narrower obligations but must act only on documented controller instructions and cannot use the data for their own purposes.

---

**Q5. Retailer + US-hosted LLM API chatbot**

- **Controller:** The retailer — it determines the purpose (customer service) and decides to use the chatbot
- **Processor:** The US LLM API provider — it processes customer data on the retailer's behalf following the retailer's configuration
- **Required document:** A Data Processing Agreement (DPA) under Article 28, including Standard Contractual Clauses (SCCs) or confirmation of DPF certification as the international transfer mechanism for the EU-to-US data flow

---

**Q6. The consultant's role in an AI project**

The lesson suggests consultants are typically **processors** — they process personal data on behalf of the client (the controller) to deliver a defined service. This means the consultant must: act only on the client's documented instructions; not use client data for their own purposes; ensure sub-processors (e.g. the LLM API used during development) are bound by equivalent obligations; and have a DPA in place with the client before any personal data is processed. In some cases — if the consultant designs the system and sets the processing purposes independently — they could be a joint controller, which carries heavier obligations.

---

## Part 3 — The Seven Principles and Lawful Bases

**Q7. The seven GDPR principles (Article 5)**

1. Lawfulness, fairness, and transparency
2. Purpose limitation
3. Data minimisation
4. Accuracy
5. Storage limitation
6. Integrity and confidentiality (security)
7. Accountability

---

**Q8. Most commonly violated principle in AI projects**

**Purpose limitation** — Article 5(1)(b). The classic trap: data collected for one purpose (e.g. resolving customer support tickets) is later repurposed for a different purpose (e.g. training an AI model) without a new lawful basis or compatibility assessment. Teams assume that because they already have the data, they can use it for anything.

---

**Q9. The six lawful bases (Article 6)**

1. Consent
2. Contract
3. Legal obligation
4. Vital interests
5. Public task
6. Legitimate interests

---

**Q10. Using support chat logs to train an AI model — lawful basis challenge**

The challenge is **purpose limitation** — the logs were collected to resolve complaints, not to train AI models. Using them for training is a new, different purpose. The controller must either obtain fresh consent for the new purpose, or conduct a **compatibility assessment** to determine whether training is compatible with the original purpose.

If relying on **legitimate interests (LIA)**, the three-part test is:

1. **Purpose test:** Is the interest legitimate and specific? (e.g. improving the accuracy of internal customer service tooling)
2. **Necessity test:** Is the processing necessary to achieve that purpose? Is there a less intrusive alternative?
3. **Balancing test:** Do the controller's interests override the data subjects' reasonable expectations and privacy interests? (Key question: did customers expect their complaints to be used for AI training? Almost certainly not.)

---

## Part 4 — Rights, DPIA, and Transfers

**Q11. The eight data subject rights**

1. Right to be informed (Articles 13–14)
2. Right of access (Article 15)
3. Right to rectification (Article 16)
4. Right to erasure / right to be forgotten (Article 17)
5. Right to restrict processing (Article 18)
6. Right to data portability (Article 20)
7. Right to object (Article 21)
8. Rights related to automated decision-making and profiling (Article 22)

---

**Q12. Bank AI loan rejection — most relevant right**

**Right:** Article 22 — Rights related to automated decision-making with legal or similarly significant effects.

**Three things the bank must provide to a rejected applicant:**

1. **Human intervention** — the right to request that a human reviews the automated decision
2. **Right to contest** — the right to challenge the decision
3. **Explanation of the logic** — meaningful information about the logic involved in the automated decision (not a full technical explanation, but enough to understand why the decision was reached)

---

**Q13. DPIA — when mandatory**

**Three situations where a DPIA is mandatory (Article 35 + EDPB guidelines):**

1. Large-scale processing of special-category data (health, biometric, etc.)
2. Systematic and large-scale evaluation or scoring of individuals (including profiling)
3. Systematic monitoring of a publicly accessible area on a large scale

**Does a typical AI project require one?** Very often yes. AI projects frequently involve scoring or evaluating individuals (EDPB criterion 1), combining datasets from multiple sources (criterion 6), innovative technology (criterion 8), and cross-border transfers (criterion 9). If two or more EDPB criteria apply, a DPIA is generally required.

---

**Q14. EU-to-US transfer mechanism and risk**

**Mechanism:** Standard Contractual Clauses (SCCs) — the 2021 version issued by the European Commission. Alternatively, vendors certified under the EU-US Data Privacy Framework (DPF, July 2023) can rely on the adequacy decision for DPF-certified entities.

**Risk:** Both mechanisms are legally fragile. Privacy Shield (the predecessor to DPF) was invalidated by the CJEU in *Schrems II* (2020) because US surveillance law (FISA 702, EO 12333) allows access to data in ways incompatible with EU fundamental rights. The DPF faces a likely *Schrems III* challenge. A future CJEU ruling could invalidate the DPF, requiring rapid remediation of all EU-US transfer arrangements.

---

## Part 5 — Data Act, ePrivacy, Digital Omnibus

**Q15. What problem does the Data Act solve?**

The Data Act (effective September 2025) solves the problem of **data lock-in and unequal access to data generated by connected products and related services** (IoT devices, smart machines, cloud services). Data generated by connected products (e.g. a smart factory machine, a connected car) often flows only to the manufacturer, even when the user or a third-party service provider could benefit from it. The Data Act gives users the right to access their own data and share it with third parties, and creates portability rights for cloud services. It mainly applies to manufacturers of connected products, cloud service providers, and data holders in B2B and B2C contexts.

---

**Q16. ePrivacy as lex specialis**

**Lex specialis** means "more specific law." Where ePrivacy covers the same activity as GDPR but in greater detail or with stricter requirements, ePrivacy's rules take precedence — it carves out a specific regime that overrides GDPR's more general framework for that activity.

**Example:** GDPR allows processing based on legitimate interests without prior consent for many activities. But ePrivacy requires **prior consent** for placing non-essential cookies on a user's device — regardless of whether legitimate interests might otherwise apply under GDPR. You cannot use GDPR's legitimate interests basis to skip the cookie consent requirement that ePrivacy mandates.

---

**Q17. ePrivacy consent requirements**

| Activity | Consent required? |
|---|---|
| Session login cookie | No — strictly necessary for the service the user requested |
| Third-party analytics cookie | Yes — not strictly necessary; tracks behaviour for a purpose beyond delivering the service |
| A/B testing cookie | Yes — tracks user behaviour for optimisation; not strictly necessary |
| Shopping cart cookie | No — strictly necessary to deliver the e-commerce service |
| AI-driven retargeting pixel | Yes — tracks behaviour across sites to serve targeted advertising; requires prior consent |

---

**Q18. The Digital Omnibus is a legislative proposal — what that means for consultants**

A legislative proposal is a draft that has been put forward but not yet adopted as law. It may change significantly before final adoption, or may not be adopted at all. Consultants must advise clients based on **the law as it currently stands**, not on proposals. The Digital Omnibus can be referenced as forward-looking context — "this is likely to change in this direction" — but clients cannot build compliance programmes around it, and consultants cannot advise clients to implement it as if it were already in force. Doing so creates risk if the final text differs from the proposal.

---

## Part 6 — Quick Scenarios

**Q19. AI tool screening job applications — violations**

**Two GDPR obligations likely violated:**
1. **Article 22** — the system makes automated decisions with legal effects (candidate rejection) without a human review mechanism, right to contest, or explanation of the decision logic
2. **Article 13/14 (transparency)** — candidates are not informed that an AI system is being used to evaluate their application or how it works

**One AI Act obligation likely violated:**
The system falls under **Annex III Area 4** (employment and workers management) — a high-risk AI system. As a high-risk system, it requires human oversight (Article 14), technical documentation (Article 11), and a conformity assessment (Article 43) before deployment. None of these appear to be in place.

---

**Q20. E-commerce personalisation with behavioural tracking — check ePrivacy first**

Before GDPR applies, **ePrivacy** must be checked. Behavioural tracking via cookies or tracking pixels requires **prior consent** under the ePrivacy Directive — legitimate interests under GDPR cannot substitute for this. The company's lawyer's advice about GDPR legitimate interests is correct for the data processing layer, but irrelevant if the tracking mechanism itself (the cookie or pixel) has not obtained prior consent. The company must implement a compliant consent mechanism (cookie banner with genuine choice) before the behavioural data can lawfully be collected at all.

---

**Q21. "Anonymised" customer emails — is removing names and phone numbers sufficient?**

No — this is almost certainly **pseudonymisation, not anonymisation**. Removing direct identifiers (name, phone) still leaves quasi-identifiers in email content: writing style, specific complaint details, account references, dates, locations, and behavioural patterns that could allow re-identification — especially when combined with other data the company holds. GDPR's anonymisation standard requires that re-identification be impossible by any reasonably available means, not just unlikely.

**What they should do instead:**
- Conduct a formal **anonymisation impact assessment** using recognised techniques (k-anonymity, differential privacy, or generalisation) and verify that re-identification risk is genuinely eliminated
- If true anonymisation is not achievable, treat the data as personal data and conduct a **purpose limitation compatibility assessment** and **DPIA** before using it for model training
- Consider whether **synthetic data generation** from the original logs could substitute, avoiding use of real customer content entirely

---

## Part 7 — Build Your Artefacts

**Q22 — AI Project GDPR Audit Checklist**

**ROLES**
1. Have we documented whether each entity in the system is a controller, processor, or joint controller?
2. Is there a signed Data Processing Agreement (DPA) with every vendor or third party that processes personal data on our behalf?
3. For any international transfers, is a valid transfer mechanism (SCCs or DPF certification) documented for each data flow?

**LAWFUL BASIS**
4. Is a lawful basis identified and documented for every distinct processing purpose?
5. If relying on legitimate interests, has a written three-part LIA been completed and retained?
6. If the data was collected for a different original purpose, has a compatibility assessment been conducted before using it for the new purpose (e.g. AI training)?

**RIGHTS**
7. Does the system support a response to a Subject Access Request within 30 days — including any AI-generated outputs about the data subject?
8. Is there a documented process for handling erasure and objection requests?
9. If the system makes automated decisions with significant effects, is there a human review mechanism, right to contest, and explanation of the decision logic in place?

**TRANSFERS**
10. Has every international data transfer been mapped, and is the transfer mechanism verified and documented for each?

**DOCUMENTATION**
11. Has a DPIA been conducted if two or more EDPB trigger criteria apply?
12. Is there a Record of Processing Activities (Article 30 register) covering this system?
13. Are data retention periods defined and technically enforced for all personal data categories?

---

**Q23 — EU Data Law Stack Map**

```
┌─────────────────────────────────────────────────────────────────┐
│  LAYER 1 — GDPR (General Data Protection Regulation)           │
│  When it applies: any processing of personal data about EU      │
│  residents, anywhere in the world                               │
│  Key obligation: lawful basis required for every processing     │
│  purpose; data subject rights must be operationalised           │
└─────────────────────────────────────────────────────────────────┘
         ↓ ePrivacy overrides GDPR where it is stricter
┌─────────────────────────────────────────────────────────────────┐
│  LAYER 2 — ePRIVACY DIRECTIVE (lex specialis over GDPR)        │
│  When it applies: cookies, tracking pixels, electronic          │
│  communications, device-level data collection                   │
│  Key obligation: prior consent required for non-essential        │
│  cookies and tracking — legitimate interests cannot substitute  │
└─────────────────────────────────────────────────────────────────┘
         ↓ applies in parallel with GDPR
┌─────────────────────────────────────────────────────────────────┐
│  LAYER 3 — DATA ACT (in force September 2025)                  │
│  When it applies: connected products (IoT), cloud switching,    │
│  data generated by smart devices and related services           │
│  Key obligation: users have the right to access and share data  │
│  generated by their connected devices with third parties        │
└─────────────────────────────────────────────────────────────────┘
         ↓ applies in parallel with GDPR for AI systems
┌─────────────────────────────────────────────────────────────────┐
│  LAYER 4 — EU AI ACT (phased from 2024–2027)                   │
│  When it applies: AI systems placed on the EU market or used    │
│  in the EU — risk-tiered (prohibited / high-risk / limited /   │
│  minimal)                                                       │
│  Key obligation: high-risk systems require conformity           │
│  assessment, human oversight, technical documentation, and      │
│  DPIA coordination with GDPR                                    │
└─────────────────────────────────────────────────────────────────┘
         ↓ NOT YET IN FORCE — do not advise clients to implement
┌─────────────────────────────────────────────────────────────────┐
│  PENDING — DIGITAL OMNIBUS (legislative proposal only)         │
│  Status: proposed amendments to GDPR, AI Act, and other laws   │
│  Consultant guidance: monitor for updates; advise clients on    │
│  current law only; flag as forward-looking context, not         │
│  compliance requirement                                         │
└─────────────────────────────────────────────────────────────────┘
```

**How the layers interact in a typical AI project:**
- A personalisation AI using cookies → ePrivacy (consent for tracking) + GDPR (lawful basis for processing) + AI Act (if risk tier triggered)
- An AI tool scoring job applicants → GDPR (Article 22 automated decisions) + AI Act (Annex III high-risk) — both must be satisfied simultaneously
- An IoT device feeding an AI system → Data Act (data access rights) + GDPR (personal data in sensor outputs) + AI Act (if system makes decisions about people)
