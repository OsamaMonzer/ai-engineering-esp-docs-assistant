# Requirements Specification — ESP Docs Assistant (ESP8266)

**Course:** AI Engineering — Topic 2 Lab
**Author:** Osama
**Version:** 1.0
**Date:** 22 October 2026

This document refines the Week 1 project framing, defines the application scope, and specifies the functional, non-functional, and Responsible AI requirements for the ESP Docs Assistant. It feeds into the evaluation criteria (`week2_evaluation_criteria.md`) and the V1 architecture (`week2_architecture_decisions.md`).

---

## Section 0 — Project Framing & Application Scope Checkpoint

| Framing element | Content |
|---|---|
| **Problem Evidence** | (1) Espressif publishes ESP8266 documentation as many separate documents, and its technical-documents index notes that some older software variants are no longer updated, so users must work out which document applies to their setup ([Espressif Technical Documents](https://www.espressif.com/en/support/documents/technical-documents)). (2) Research on embedded system development reports that the volume and interrelated structure of technical documents make retrieval difficult, and that general LLMs often lack the specialised knowledge for domain-specific queries (Lu et al., ACM TODAES, 2025 — https://doi.org/10.1145/3731680). |
| **Existing Approaches** | (1) **HSG-RAG** (Lu et al., 2025) — knowledge-graph-based retrieval for embedded technical documentation; requires heavy knowledge-graph construction and is not a lightweight beginner assistant. (2) **RAG chatbot for firmware manuals** (Argnani, Politecnico di Torino MSc thesis, 2025) — a thesis-level prototype; its coverage of ESP8266-class beginner questions and version handling is not established. (3) **Datasheet-aware compatibility verification** (Qiao et al., arXiv:2608.25217) — targets design-time compatibility checks rather than conversational question answering with citations. In practice, developers also rely on manual search across Espressif documents and on general-purpose LLM chatbots that answer without grounding in the official documents; these serve as the practical baselines. |
| **Project / Engineering Gap** | In the initial scan, a lightweight, citation-grounded, version-aware assistant for beginner ESP8266 questions, built only on public Espressif documents, was not found. Developers still search several documents manually, or trust ungrounded answers where a wrong pin or voltage can damage hardware. This is a practical engineering gap (grounding, traceability, safety controls) and not a claim of scientific novelty. |
| **Objective O1** | To design a document-grounded AI assistant that answers ESP8266 development questions using official Espressif documentation. |
| **Objective O2** | To develop the assistant with multi-document retrieval, source citations, and guardrails for unsafe or out-of-scope questions. |
| **Objective O3** | To evaluate answer correctness, groundedness, latency, and safety. |
| **Intended Users** | Undergraduate students and beginner-to-intermediate hobbyist developers building IoT projects on ESP8266 boards. |
| **Supported Tasks** | Answering questions on pin functions, voltage levels, and other hardware specifications; answering SDK setup questions; answering questions that span more than one document; pointing the user to the relevant document and section. |
| **Information / Knowledge Domain** | Official Espressif ESP8266 documents: the ESP8266 Technical Reference Manual, the ESP-WROOM-02 Datasheet, and the ESP8266 RTOS SDK Programming Guide (Get Started). English only. |
| **Operating Context** | Used through a chat-style web interface by one user at a time while prototyping; internet-connected; advisory use only (the system does not control or flash devices). |
| **Important Constraints** | Student project with a limited (free-tier or low-cost) model/API budget; semester timeline; public documents only; source PDFs are not committed to the repository and are downloaded locally; PDF tables and pinout figures are difficult to parse; the SDK guide is a web page (HTML) rather than a PDF. |
| **Explicit Out-of-Scope Tasks** | Generating complete firmware or projects; certifying that a circuit or design is safe; mains-voltage or high-power electrical design; chips other than the ESP8266 (e.g. ESP32); AT command questions (no AT command document is included in the V1 data sources); controlling or flashing real devices; medical, legal, or financial advice. |
| **Evaluation Focus** | Does the assistant give correct, source-supported answers to routine ESP8266 documentation questions, indicate which document and version applies, refuse or abstain when a question is outside its scope or evidence, and respond fast enough for interactive use? |

**Traceability check:**

| Objective | Supporting requirements |
|---|---|
| O1 — Design a document-grounded assistant | FR1, FR2, FR3, FR4, NFR2, RAI1 |
| O2 — Develop with retrieval, citations, and guardrails | FR3, FR4, FR5, FR6, FR7, NFR4, RAI2, RAI3, RAI4 |
| O3 — Evaluate correctness, groundedness, latency, safety | NFR1, NFR2, NFR3, NFR5, RAI1, RAI2, RAI3 |

---

## Section 1 — Functional Requirements

| # | Requirement — The system SHALL | Scope Boundary — SHALL NOT (where relevant) |
|---|---|---|
| FR1 | Answer questions about ESP8266 hardware specifications (pin functions, voltage levels, memory, interfaces) using the supplied official documents. | State a numeric specification (voltage, current, pin number, register value) that is not supported by the source documents. |
| FR2 | Answer SDK setup questions that are covered by the supplied SDK guide. | Present setup steps for a software variant or version that is not covered by the supplied documents. |
| FR3 | Combine information from more than one supplied document when a question spans documents (e.g. a pin function in the datasheet and its configuration in the SDK guide). | Merge conflicting statements from different documents without flagging the conflict. |
| FR4 | Provide a source reference (document name and section or page) for every answer based on the documents. | Present unsupported information as if it came from an official document. |
| FR5 | State which document and software variant or version an answer applies to, and note when the user's setup is not specified and the answer may differ. | Assume a software version when the user has not specified one and the sources differ. |
| FR6 | Indicate clearly when the supplied documents do not contain enough information to answer. | Fill gaps with unverified general knowledge presented as fact. |
| FR7 | Decline queries outside the defined scope (other chips, AT command questions, full-project generation, mains or high-power design, unrelated topics) and redirect the user appropriately. | Provide medical, legal, or financial advice, or approve a circuit as "safe". |
| FR8 | Present answers in clear, concise language suitable for beginner-to-intermediate developers. | — |

---

## Section 2 — Non-Functional Requirements

| # | Quality Attribute | Requirement | Measurement Method | Metric | Threshold |
|---|---|---|---|---|---|
| NFR1 | Performance | The system shall keep interactive responses within an acceptable latency. | Measure end-to-end latency on a defined test workload of 50 queries. | p90 latency | < 5,000 ms |
| NFR2 | Correctness | Answers shall be factually consistent with the source documents. | Score a curated evaluation set (at least 40 questions with reference answers) using a defined rubric. | Correct response rate (%) | ≥ 80% |
| NFR3 | Cost | Inference cost shall stay within the project budget. | Record model/API usage over the evaluation workload. | Cost per 100 queries | < US$0.50 per 100 queries |
| NFR4 | Maintainability | Adding or updating a source document (PDF or web page) shall not require rebuilding unrelated components. | Run a controlled document-add test with a new document. | Time to incorporate a new document | < 15 min |
| NFR5 | Reliability | The system shall return a valid response (answer, abstention, or refusal) for well-formed queries. | Run the evaluation workload and count failed, empty, or error responses. | Successful response rate | ≥ 98% |

---

## Section 3 — Responsible AI Requirements

| # | Responsible AI Attribute / Risk | Requirement | Measurement Method | Metric | Threshold |
|---|---|---|---|---|---|
| RAI1 | Transparency / Grounding (hallucination) | The system shall provide a verifiable source reference for document-grounded answers and indicate when supporting evidence cannot be found. | Inspect document-grounded responses for citation presence and check whether the cited passage supports the answer. | Citation presence rate; citation–source match rate | 100% presence; ≥ 95% match |
| RAI2 | Safety (hardware damage) | The system shall only state electrical limits (voltage, current, ratings) that are supported by the documents, and shall remind the user to verify ratings against the datasheet. | Run a test set of at least 20 electrical-ratings questions and check numeric claims against the source documents. | Unsupported numeric claims per 20 queries | 0 |
| RAI3 | Safety / Scope | The system shall refuse out-of-scope queries and redirect users appropriately. | Run a scope test set containing at least 20 out-of-scope queries. | Refusal-and-redirect success rate | ≥ 95% |
| RAI4 | Privacy | The system shall not transmit user secrets (Wi-Fi passwords, API keys, tokens) pasted into queries to external model APIs. | Run privacy test cases containing dummy credentials and inspect outgoing API payloads. | Number of detected credential transmissions | 0 |
