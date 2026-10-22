# Evaluation Criteria — ESP Docs Assistant (ESP8266)

**Course:** AI Engineering — Topic 2 Lab
**Author:** Osama
**Version:** 1.0
**Date:** 22 October 2026

This document selects the most important requirements from `week2_requirements.md` and defines how each will be evaluated. It follows the flow:

Objective / Requirement → Evaluation Criterion → Measurement Method → Metric → Threshold → User / Business Outcome

Objectives referenced: **O1** — design a document-grounded assistant; **O2** — develop it with multi-document retrieval, citations, and guardrails; **O3** — evaluate correctness, groundedness, latency, and safety.

---

## Evaluation Criteria Table

| Objective ID | Requirement ID | Evaluation Criterion | Capability Bucket | Measurement Method | Metric | Threshold | User / Business Outcome |
|---|---|---|---|---|---|---|---|
| O3 | FR1, FR2, NFR2 | Answer correctness | Domain-specific capability | Run a curated set of at least 40 supported ESP8266 questions (hardware specifications and SDK setup, including at least 10 that span more than one document) with reference answers, and score each response using the correctness rubric below. | Correct response rate (%) | ≥ 80% | Students and hobbyists receive sufficiently reliable answers for routine ESP8266 questions without searching several documents manually. |
| O1, O2 | FR4, RAI1 | Source citation and support | Instruction-following capability | Inspect every document-grounded response in the evaluation run for the presence of a document and section/page reference, and check whether the cited passage supports the answer. | Citation presence rate; citation–source match rate | 100% presence; ≥ 95% match | Users can verify each answer against the official Espressif document before relying on it. |
| O2 | RAI2 | Electrical-ratings accuracy | Domain-specific capability | Run a test set of at least 20 questions about voltage, current, and pin ratings; check every numeric claim in the responses against the source documents. | Unsupported numeric claims per 20 queries | 0 | Reduces the risk of hardware damage caused by a wrong voltage or pin rating. |
| O2 | FR7, RAI3 | Out-of-scope refusal and redirect | Instruction-following capability | Run a scope test set containing at least 20 out-of-scope queries (other chips, AT commands, full-project generation, mains or high-power design, unrelated topics) and check that each is declined with an appropriate redirect. | Refusal-and-redirect success rate | ≥ 95% | The assistant stays within its documented purpose and does not give unsupported advice outside ESP8266 documentation. |
| O2 | FR6, RAI1 | Abstention when evidence is missing | Instruction-following capability | Run a set of at least 10 in-domain questions whose answers are not in the supplied documents and check that the response states the documents do not contain enough information instead of guessing. | Abstention success rate | ≥ 90% | Users are not misled by invented answers when the documentation does not cover their question. |
| O3 | NFR1 | Response latency | Cost and latency | Measure end-to-end response time (query submitted to full answer returned) across a defined workload of 50 queries. | p90 latency | < 5,000 ms | The assistant is responsive enough for interactive use while prototyping. |
| O3 | NFR3 | Inference cost | Cost and latency | Record model/API usage over the same 50-query workload and convert it to cost. | Cost per 100 queries | < US$0.50 per 100 queries | The assistant remains affordable within a student project budget. |
| O1 | FR8 | Response clarity | Generation capability | Rate each response from the correctness set using the clarity rubric below. | Mean clarity score (1–5) | ≥ 4.0 | Beginner-to-intermediate developers can understand the information provided. |

---

## Evaluation Sets and Rubrics

**Evaluation sets** (The evaluation question sets and reference answers will be developed from the supplied documents and stored in the evaluation/ folder.):

| Set | Minimum size | Used for |
|---|---|---|
| Supported-question set | 40 questions (at least 10 multi-document) | Answer correctness, citation support, clarity, latency, cost |
| Electrical-ratings set | 20 questions | Electrical-ratings accuracy |
| Out-of-scope set | 20 queries | Out-of-scope refusal and redirect |
| No-evidence set | 10 questions | Abstention when evidence is missing |

**Correctness rubric.** A response counts as correct when it (a) matches the key facts of the reference answer and (b) contains no claim that contradicts or is unsupported by the source documents. Otherwise it counts as incorrect.

**Clarity rubric (1–5).**

| Score | Description |
|---|---|
| 5 | Direct, well-organised answer; no unexplained jargon; a beginner can act on it. |
| 4 | Clear and correct in wording; minor extra effort needed to follow. |
| 3 | Understandable but wordy, disorganised, or uses unexplained terms. |
| 2 | Difficult to follow; key points are buried. |
| 1 | Confusing or unusable as written. |

**Comparison with a baseline.** To support O3, the correctness, citation, electrical-ratings, and refusal metrics are also measured for a base-model-only configuration (no document access) on the same sets, so the benefit of document grounding can be shown.

---

## Capability Bucket Coverage

| Bucket | Criteria |
|---|---|
| Domain-specific capability | Answer correctness; electrical-ratings accuracy |
| Generation capability | Response clarity |
| Instruction-following capability | Source citation and support; out-of-scope refusal; abstention |
| Cost and latency | Response latency; inference cost |

---

## Traceability to Requirements

| Requirement | Evaluated by |
|---|---|
| FR1, FR2, NFR2 | Answer correctness |
| FR4, RAI1 | Source citation and support |
| RAI2 | Electrical-ratings accuracy |
| FR7, RAI3 | Out-of-scope refusal and redirect |
| FR6, RAI1 | Abstention when evidence is missing |
| NFR1 | Response latency |
| NFR3 | Inference cost |
| FR8 | Response clarity |
