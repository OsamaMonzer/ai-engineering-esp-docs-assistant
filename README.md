# ESP Docs Assistant — AI-Powered ESP8266 Documentation Assistant

> Answers ESP8266 development questions for students and hobbyist developers, grounded in official Espressif documents and cited by document and section.

## Problem Statement

Students and hobbyist developers building IoT projects on the ESP8266 must find answers (pin functions, voltage levels, SDK setup, AT commands) across many separate manufacturer documents, some of which apply only to specific software versions. General-purpose AI assistants may answer without grounding in the official specifications. AI engineering makes it possible to build a document-grounded assistant that retrieves the relevant passages and shows where each answer came from.

## Evidence of the Problem

- Espressif publishes ESP8266 documentation as many separate documents, and its index notes that some older software variants are no longer updated, so users must work out which document applies to their setup. ([Espressif Technical Documents](https://www.espressif.com/en/support/documents/technical-documents))
- Research on embedded system development reports that the volume and interrelated structure of technical documents make retrieval difficult, and that general LLMs often lack the specialised knowledge for domain-specific queries. (Lu et al., ACM TODAES, 2025 — https://doi.org/10.1145/3731680)

## Initial Related Work / Existing Approaches

- **HSG-RAG** (Lu et al., 2025, https://doi.org/10.1145/3731680) — knowledge-graph-based retrieval for embedded technical documentation. Limitation: heavy knowledge-graph construction; not a lightweight beginner assistant.
- **RAG chatbot for firmware manuals** (Argnani, Politecnico di Torino MSc thesis, 2025, https://webthesis.biblio.polito.it/38774/1/tesi.pdf) — Limitation: thesis-level prototype; coverage of ESP8266-class beginner questions and version handling still to be verified.
- **Datasheet-aware compatibility verification** (Qiao et al., arXiv:2608.25217, https://arxiv.org/pdf/2608.25217) — Limitation: targets design-time compatibility checks, not conversational Q&A with citations.

## Engineering Gap / Limitation

In this initial scan, existing work relies on heavy knowledge-graph construction, targets design-time verification, or is a thesis-level prototype. A lightweight, citation-grounded, version-aware assistant for beginner ESP8266 questions built only on public Espressif documents was not found. Developers still search several documents manually, or trust ungrounded answers where a wrong pin or voltage can damage hardware. (To be refined in Topic 2.)

## Project Objectives

- O1: To design a document-grounded AI assistant that answers ESP8266 development questions using official Espressif documentation.
- O2: To develop the assistant with multi-document retrieval, source citations, and guardrails for unsafe or out-of-scope questions.
- O3: To evaluate answer correctness, groundedness, latency, and safety.

## Target Users

Undergraduate students and beginner-to-intermediate hobbyist developers building IoT projects on ESP8266 boards.

## Data Sources

- ESP8266 Technical Reference Manual — https://documentation.espressif.com/esp8266-technical_reference_en.pdf
- ESP-WROOM-02 Datasheet — https://www.espressif.com/sites/default/files/documentation/0c-esp-wroom-02_datasheet_en.pdf
- ESP8266 RTOS SDK Programming Guide (Get Started) — https://docs.espressif.com/projects/esp8266-rtos-sdk/en/latest/get-started/index.html

> Source PDFs are not committed to this repository (see `.gitignore`). Download them locally from the links above.

## Weekly Progress

| Week | Focus | Deliverable | Status |
|------|-------|-------------|--------|
| W1 | System map + project scope | README + docs | ✅ |
