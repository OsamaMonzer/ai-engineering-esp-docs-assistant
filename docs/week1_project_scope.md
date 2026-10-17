# Week 1 Project Scope — ESP Docs Assistant

**Project Name:** ESP Docs Assistant (working title)

**Domain:** Embedded Systems / IoT Development Support (ESP8266 documentation)

## Problem Statement

Students and hobbyist developers building IoT projects on the ESP8266 must find answers (pin functions, voltage levels, SDK setup, AT commands) scattered across many separate manufacturer documents, some of which apply only to specific software versions. A single question often needs several documents, and general-purpose AI assistants may answer from memory without grounding in the official specifications. AI engineering makes it possible to build a document-grounded question-answering assistant that retrieves the relevant passages, answers in natural language, and shows which document and section the answer came from.

## Evidence of the Problem

- **Evidence 1:** Espressif publishes ESP8266 documentation as many separate documents (datasheets, hardware design guidelines, SDK guides, AT instruction sets, a technical reference), so one practical question can require several of them. Its documentation index also notes that some older ESP8266 software variants (e.g. NonOS AT) are no longer updated and recommends newer IDF-based AT firmware for new designs, so users must work out which document applies to which software version.
  - Source / citation: Espressif Systems, "Technical Documents" (ESP8266 series). https://www.espressif.com/en/support/documents/technical-documents (accessed 17 Oct 2026). Version note: Espressif documentation index (ESP8266 search listing), https://espressif.com/en/search/node/ESP8266?page=11 (accessed 17 Oct 2026).
- **Evidence 2:** Published research on embedded system development reports that the sheer volume of technical documents and the intricate relationships between their contents make it hard to retrieve the needed information efficiently, and that general LLMs often lack the specialised knowledge needed for domain-specific embedded queries.
  - Source / citation: Z. Lu, H. Xu, A. Chen, S. Tang, J. Zhang, Y. Feng, W. Pan, J. Huang, "HSG-RAG: Hierarchical Knowledge Base Construction for Embedded System Development," ACM Transactions on Design Automation of Electronic Systems, vol. 30, no. 6, pp. 1–21, 2025. https://doi.org/10.1145/3731680
- **Evidence 3 (supporting):** A 2026 conference paper makes a similar point: models pretrained on general-purpose data may struggle to give reliable answers in niche, highly specialised domains such as embedded systems, motivating domain-specific RAG evaluation.
  - Source / citation: K. Sevinç, B. Şimşek, H. Parlar, O. D. Altıntaş, "A Dataset and Benchmark for Embedded Systems Information Retrieval and Generation," 34th Signal Processing and Communications Applications Conference (SIU), Istanbul, 2026. https://doi.org/10.1109/siu71813.2026.11636984

## Initial Related Work

- **Study / solution 1:** HSG-RAG — a knowledge-graph-based retrieval method for embedded system technical documentation, with a benchmark for multi-hop questions.
  - Source / citation: Lu et al., ACM TODAES 30(6), 2025. https://doi.org/10.1145/3731680
  - Key limitation: Requires building and maintaining a hierarchical knowledge graph from the documents, which is heavy for a one-semester student project; it is not packaged as a lightweight assistant for beginners.
- **Study / solution 2:** Domain-specific RAG chatbot for firmware manuals (MSc thesis).
  - Source / citation: T. Argnani, "Retrieval-Augmented Generation for Technical Documentation: A Domain-Specific Chatbot for Firmware Manuals," MSc thesis, Politecnico di Torino, Dec 2025. https://webthesis.biblio.polito.it/38774/1/tesi.pdf
  - Key limitation: Thesis-level prototype aimed at firmware manuals; my initial scan has not confirmed whether it covers ESP8266-class beginner questions or handling of conflicting software versions.
- **Study / solution 3:** LLM-driven, datasheet-aware hardware compatibility verification for early-stage embedded system design.
  - Source / citation: H. Qiao et al., "LLM-Driven, Datasheet-Aware Automated Hardware Compatibility Verification for Early-Stage, Pre-Schematic Embedded System Design," arXiv:2608.25217. https://arxiv.org/pdf/2608.25217 
  - Key limitation: Targets design-time compatibility checking between components rather than open-ended, conversational question answering with citations for developers.

(Existing approaches also include: Espressif's static documentation pages — authoritative but fragmented; and general-purpose AI assistants — flexible but not necessarily grounded in the official specifications.)

## Engineering Gap / Limitation

In this initial scan, the related work either relies on heavy knowledge-graph construction, targets design-time compatibility verification, or is a thesis-level prototype. I did not find a lightweight, citation-grounded, version-aware assistant that answers beginner ESP8266 development questions using only public Espressif documents. The practical limitation is that developers still have to search several separate documents manually, or trust ungrounded answers from general-purpose assistants, when a wrong pin or voltage answer can damage hardware. *(To be refined in Topic 2 once related work is read in more detail.)*

## Initial Project Objectives

- **O1:** To design a document-grounded AI assistant that answers ESP8266 development questions using official Espressif documentation.
- **O2:** To develop the assistant with retrieval over multiple documents, source citations (document and section), and guardrails for unsafe or out-of-scope questions.
- **O3:** To evaluate the assistant in terms of answer correctness against the official documents, groundedness (citation accuracy), latency, and safety.

## Target Users

Undergraduate students and beginner-to-intermediate hobbyist developers building IoT projects on ESP8266 boards (for example in an IoT or embedded systems course).

## Data Sources

- **Source 1:** ESP8266 Technical Reference Manual (Espressif) — URL: https://documentation.espressif.com/esp8266-technical_reference_en.pdf
- **Source 2:** ESP-WROOM-02 Datasheet (Espressif) — URL: https://www.espressif.com/sites/default/files/documentation/0c-esp-wroom-02_datasheet_en.pdf
- **Source 3:** ESP8266 RTOS SDK Programming Guide, Get Started section (Espressif) — URL: https://docs.espressif.com/projects/esp8266-rtos-sdk/en/latest/get-started/index.html

*Note: older Espressif PDF links (e.g. the SDK Getting Started Guide and Quick Start Guide) no longer resolve, because Espressif moved its documentation to a new platform. This is a real example of the outdated-document risk (Risk 2) and is why the pipeline should record the URL and access date for every source.*

*Licensing note: documents are used for local/course purposes only. PDFs are not committed to the public repository (only URLs and derived, non-redistributable metadata).*

## Similar to EduAdvisor

- Document-grounded question answering (RAG) over official PDF documents.
- Needs citations, guardrails, and evaluation of correctness, groundedness, latency, and safety.
- Hallucination and fairness-style coverage risks apply in the same way.

## Different from EduAdvisor

- Domain is technical documentation (datasheets, SDK guides) rather than university policies and course catalogues.
- Documents contain tables, pin diagrams, AT commands, and code, which makes parsing and chunking harder than plain prose.
- Answers depend on hardware and software version, so the system must handle version mismatches between documents.
- Users are developers whose mistakes can damage hardware, so correctness of pins, voltages, and commands is critical.

## Responsible AI Risks

- **Risk 1 — Hallucination:** The model invents or misstates a pin function, voltage tolerance, or AT command → the user wires or configures the board incorrectly, wasting time or damaging hardware.
- **Risk 2 — Outdated or mismatched documents:** The system retrieves a passage written for a different SDK or firmware version → the user follows instructions that do not work with their setup.
- **Risk 3 — Safety / over-reliance:** A user asks about mains voltage, battery charging, or other hazardous wiring and the assistant gives confident advice → risk of injury or fire. The assistant should decline unsafe topics and point to official guidance.
- **Additional note — Licensing:** Official documents may not be redistributable, so the repository must store URLs and not copies of the PDFs.
