# Week 3 — Data Inventory (ESP Docs Assistant)

**Course:** AI Engineering — Topic 3 Lab | **Author:** Osama | **Date:** 2 November 2026
**Pipeline version:** week3-v1 | **Pathway:** A (PDF/TXT) | **Notebook:** `notebooks/week3_pipeline.ipynb`
Source IDs follow the order in `data/week3_manifest.json`.

## 1. Requirement-to-data mapping (from `docs/week2_requirements.md`)

| Requirement | Needed information | Source | Why usable / constraint |
|---|---|---|---|
| FR1 — hardware specs (pins, voltage, memory, interfaces) | Pin tables, electrical characteristics, memory and interface descriptions | S01 datasheet, S02 TRM | Official Espressif documents, public. Tables extract well; figures (pinout) do not. |
| FR4 / RAI1 — citation by document and page or section | Document identity plus page (PDF) or section (web) on every chunk | S01, S02 (page); S03 (section) | Basic chunker would give `page: None`; replaced with page/section-aware chunking. |
| FR5 — document and version | Version and date of each source | Cover page, footer and revision history | Recorded manually below; manifest `source_version` is only a default placeholder. |
| RAI2 — only document-supported electrical limits | Numeric ratings with units kept next to their labels | S01 Tables 1-1, 4-1, 4-3 | Values and units survive extraction; no "Absolute Maximum Ratings" text found (see section 4). |
| FR2 / FR3 — SDK setup and cross-document questions | SDK setup flow | S03 SDK Get Started page | HTML source, converted to text; per-OS toolchain steps are on other pages (section 4). |
| NFR4 — add a document without rebuilding other parts | Config-driven ingestion | `DOCS` dictionary in notebook Cell 8A | Adding a source = one dictionary entry and one run (time not yet measured). |

## 2. Source inventory

| Source ID | Filename / URL | Type and purpose | Version / date / owner | Access / rights | Inspection evidence |
|---|---|---|---|---|---|
| S01 | `0c-esp-wroom-02_datasheet_en.pdf` — https://www.espressif.com/sites/default/files/documentation/0c-esp-wroom-02_datasheet_en.pdf | PDF, 23 pages. FR1, FR4, FR5, RAI2 (pins, electrical ratings) | Version 3.7; footer date 2025.11; cover "Copyright 2025" but last page "Copyright 2023" (inconsistency inside the document). Marked NRND ("Not Recommended For New Designs"); cover points to ESP8684-WROOM-02C. Owner: Espressif. Downloaded 2 Nov 2026. | Public download. Redistribution terms **not yet verified**; raw PDF is gitignored. | 23 of 23 pages have text, 0 empty pages; 25,227 raw characters. Page 8 pin table rows intact; page 7 Table 1-1 and page 13 Table 4-1 keep values, units and Min/Typ/Max positions. Figure 2-1 pinout extracts as garbage characters. 9 pypdf "wrong pointing object" warnings (harmless). SHA-256 `092893b04e97d5a8e5c224f03d02062ca5e9523ff86bdc8b689b0c6ba0668750` |
| S02 | `esp8266-technical_reference_en.pdf` — https://documentation.espressif.com/esp8266-technical_reference_en.pdf | PDF, 111 pages. FR1, FR4 (peripherals, registers, SPI/I2C detail) | Version 1.7 (cover); footer date 2020.10. Owner: Espressif. Downloaded 2 Nov 2026. | Public download. Redistribution terms **not yet verified**; raw PDF is gitignored. | 111 of 111 pages have text, 0 empty pages; 199,417 raw characters; code samples extract as readable text. SHA-256 `25f08aa786724a9c82ad503fda6b4339b154d59efedc5c9dfff9544aca756211` |
| S03 | `esp8266_rtos_sdk_get_started.txt` — https://docs.espressif.com/projects/esp8266-rtos-sdk/en/latest/get-started/index.html | HTML page converted to text (BeautifulSoup, container `div role=main`). FR2, FR3 (SDK setup) | "latest" branch, no fixed version label. Retrieved 2026-11-02T01:02:27 UTC. Owner: Espressif. | Public web page. Reuse terms **not yet verified**. | 16 sections, 173 lines, 9,947 characters; headings recovered (Introduction, Setup Toolchain, Get ESP8266_RTOS_SDK, Configure, Build and Flash, Monitor, Environment Variables, Related Documents). Header lines in the file record URL and retrieval time. SHA-256 `c494bc164c771f3ccdf55fbf68c7b28ffddb8e72a4267df934a85b601fabca5e` |

Printed page numbers differ from PDF page numbers. Chunks store the **PDF page index**. S01: printed = PDF − 5 (17 of 17 footers checked). S02: printed = PDF − 8 (94 of 94 footers checked).

## 3. Prepared outputs

| Source | Part B output (`data/processed/`) | Chunks | Part C output (`data/pipeline_v1/`) |
|---|---|---|---|
| S01 | `prepared_text_0c-esp-wroom-02_datasheet_en.jsonl` | 63 | `documents_datasheet/` |
| S02 | `prepared_text_esp8266-technical_reference_en.jsonl` | 460 | `documents_trm/` |
| S03 | `prepared_text_esp8266_rtos_sdk_get_started.jsonl` | 30 | `documents_sdk/` |

Part B and Part C chunk text is identical for all three sources.

## 4. Known limitations and caveats

- **No "Absolute Maximum Ratings" text found in S01.** Searches for "absolute maximum" and "ratings" found no pages; Table 4-1 gives operating conditions only. The assistant should abstain on per-pin maximum-current questions (FR6, RAI2). *Confirm by searching the PDF manually before final submission.*
- **Numbers that need their row labels:** average operating current 80 mA (Table 1-1), transmit current 170 mA (Table 4-3), and the 500 mA supply recommendation (page 8 note) are different quantities.
- **Table headers can be separated from rows** by the 500-character window (Table 4-1 on page 13 is split; header row is only in the first chunk).
- **Pinout figure** (Figure 2-1) is lost; the pin definition table carries the same information as text.
- **Table of contents and footer boilerplate** are in the chunks: dot-leader TOC chunks (S01: 7 on page 5; S02: 43 on pages 4-8) and footer text such as "Submit Documentation Feedback" (S01: 17 of 63 chunks; S02: 98 of 460).
- **S03 covers one page only.** Windows / Linux / Mac OS toolchain steps are linked, not included; hyperlinks are dropped; table cells are separated by `|` lines; one row mentions ESP32 (`ESPBAUD`).
- **Source typos kept as-is:** "temperatue", "certifaction", "Reliablity".
- **Provenance:** SHA-256 proves the same bytes were processed, not that redistribution is permitted. Manifest fields `source_version` and `access_rights` are tool defaults ("Not verified"); this inventory is the authority until they are filled in.
