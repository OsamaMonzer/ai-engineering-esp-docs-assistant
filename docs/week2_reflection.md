# Week 2 Reflection — ESP Docs Assistant (ESP8266)

**Course:** AI Engineering — Topic 2 Lab
**Author:** Osama
**Date:** 22 October 2026

---

## Part A — Which Responsible AI requirement will be the most difficult to measure or enforce technically? Why?

RAI2 (Safety — hardware damage) will be the most difficult. It requires every electrical limit the assistant states (voltage, current, ratings) to be supported by the source documents. These values mostly sit in tables and pinout figures, which PDF parsing often mangles, so a wrong value can appear next to a citation that looks valid. Enforcing the requirement means checking each numeric claim against the exact source passage, which is hard to automate and slow to verify by hand. For this reason the evaluation uses a small, manually audited set of at least 20 electrical-ratings questions, and I expect the zero-unsupported-claims threshold to be the hardest one to meet.

---

## Part B — Which evaluation criterion is most important for determining whether the application will be useful to its intended users? Why?

Answer correctness (≥ 80% on the supported-question set). Users come to the assistant to avoid searching several documents manually, so they only benefit if the answers are right, and a wrong pin or voltage can damage hardware. Source citation matters because it lets users check an answer, but it only helps once most answers are correct. Comparing correctness with a base-model-only baseline also shows whether document grounding actually improves the results.

---

## Part C — Which architecture decision involved the most important trade-off? Which requirement drove it, and what trade-off was accepted?

ADR1, including knowledge access. It was driven by FR1–FR5, NFR2, and RAI1, which together require answers to be grounded in and cited from the official ESP8266 documents; a base model alone cannot provide that. The trade-offs accepted are more system complexity, extra retrieval time against the p90 < 5 s latency target (NFR1), and answer quality that now depends on retrieval quality. I judged this worthwhile because grounding and traceability are the core of the project's objectives (O1 and O2).
