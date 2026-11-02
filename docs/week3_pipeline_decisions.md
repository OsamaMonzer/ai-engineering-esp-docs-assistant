# Week 3 — Pipeline Decisions (ESP Docs Assistant)

**Course:** AI Engineering — Topic 3 Lab | **Author:** Osama | **Date:** 2 November 2026
**Notebook:** `notebooks/week3_pipeline.ipynb` | **Inventory:** `docs/week3_data_inventory.md` | **Diagram:** `docs/week3_pipeline_diagram.png`

## 1. Requirements and sources

| Requirement | What the data must provide | Source(s) |
|---|---|---|
| FR1 | Hardware specifications | S01 datasheet, S02 TRM |
| FR4, RAI1 | Page or section on every chunk | S01, S02 (page); S03 (section) |
| FR5 | Version and date of each document | Cover, footer, inventory |
| RAI2 | Electrical values with units kept intact | S01 Tables 1-1, 4-1, 4-3 |
| FR2, FR3 | SDK setup content | S03 |
| NFR4 | Adding a document is cheap | `DOCS` dictionary (Cell 8A) |

Pathway: **A (PDF/TXT)** only. Other pathways do not apply (no tabular, image, audio, JSON or time-series data). S03 is HTML, so it is saved as text first and then processed through the TXT branch.

## 2. Part B comparison (datasheet S01, 500-character target)

| Approach | Chunks | Avg length | Chunks starting lowercase (rough proxy for mid-word starts) | Chunks with page number |
|---|---|---|---|---|
| Fixed-size | 51 | 494 | 26 | 0% |
| Paragraph-aware | 55 | 458 | 29 | 0% |
| Page-aware paragraph | 66 | 381 | 22 | 100% |
| **Page-aware, line-packed (selected)** | **63** | **395** | **10** | **100%** |

PDF extraction separates lines with single newlines, so the paragraph splitter rarely finds paragraph breaks and falls back to blind cuts. The line-packed method packs whole lines and hard-cuts only at a space.

**Selected configuration:** page/section-aware, line-packed, 500 characters, with ligature repair (`fi`, `fl`, `ff`, `ffi`, `ffl`), repair of split "T" words ("T able", "T yp"), and whitespace normalisation.

- **Benefit:** every chunk keeps its page (PDF) or section (web), which FR4 and RAI1 require; the fewest mid-word starts.
- **Accepted limitation:** a 500-character window can split tables from their headers. On page 13, Table 4-1 is split, and the header row is only in the first chunk. On page 8, the "Note:" label is separated from the text of the 500 mA supply recommendation. Table-aware chunking or repeating headers is a later improvement.
- **Scope of the evidence:** the four-way comparison was run on the datasheet only. The same configuration was applied to the TRM and the SDK page without a separate comparison.
- **Cleaning check:** after the final run, the three exported files contain no ligature characters, no "T able" and no "T yp".

## 3. Pipeline design

- **ETL, not ELT:** text is cleaned and chunked before storage; the prepared JSONL is the stored artefact. There is no warehouse stage.
- **Batch, not streaming:** the sources are static documents updated rarely; the pipeline is rerun on demand.
- **Citation fields:** PDF chunks carry `page` (`section` is null); web chunks carry `section` (`page` is null).
- **NFR4:** adding a document means adding one entry to `DOCS` and running Cell 8A. The 15-minute threshold has not been measured yet.
- **Output locations:** Part B in `data/processed/` with `data/week3_manifest.json`; Part C in `data/pipeline_v1/<kind>/` with `pipeline_manifest.json`, for `documents_datasheet`, `documents_trm` and `documents_sdk`.

## 4. Stage contracts

| Stage | Contract / evidence | Failure response |
|---|---|---|
| Ingest | Chosen file exists (`select_source` / `resolve_source`); download uses `raise_for_status` | Stop with a clear error (missing file or failed download) |
| Inspect | Text is extractable (`inspect_documents`); pages or sections counted, empty ones listed | Stop if no extractable text (scanned PDFs need OCR) |
| Prepare | Chunks are produced from the selected method | Stop if no chunks |
| Validate | Output exists, is non-empty, reads back with the same count; every PDF chunk has an integer page, every web chunk a section; SHA-256 recorded | Stop on missing, empty or mismatched output |
| Record lineage | Source linked to exact output with checksums (`week3_manifest.json`, `pipeline_manifest.json`) | Stop or report a missing mapping |

**Two failure points shown in the diagram:**
1. Source download fails or the URL moves (Espressif links have changed before) — the pipeline stops at ingest with the HTTP error; fix the URL and rerun.
2. A PDF page or file yields no extractable text (scanned or image-only) — the pipeline stops at inspect instead of exporting empty chunks; OCR is outside this lab.

## 5. Evidence

- Part B: 63 chunks (S01), 460 (S02), 30 (S03); all pages covered (23 of 23, 111 of 111); lineage manifest check **PASS** for 3 records.
- Part C: Part C chunk text is identical to Part B for all three sources. Repeatability check (Cell 9): **[CONFIRM: three PASS lines after the final rerun]**.
- Differences between Part B and Part C: Part C records omit the `processing` field (it is in the manifest as `processing_method`); Part C inspection reports `units` rather than `pages`.

## 6. Risks carried to Topic 4

- No "Absolute Maximum Ratings" text found in the datasheet; the assistant should abstain rather than guess (FR6, RAI2).
- The datasheet is marked NRND and points to ESP8684-WROOM-02C; its cover and last page disagree on copyright year (2025 and 2023).
- Chunks contain table-of-contents dot leaders and footer boilerplate; the pinout figure is lost; headers can be split from table rows.
- Values such as 80 mA (average), 170 mA (transmit) and 500 mA (supply) are different quantities and must be cited with their row labels.
- S03 omits per-OS toolchain steps (FR2 only partly covered); one S03 row mentions ESP32, which is out of scope (FR7).
- Provenance caveat: SHA-256 shows the same bytes were processed, not that redistribution is permitted. Raw PDFs are gitignored; check Espressif's terms before committing prepared chunks. `source_version` and `access_rights` in the manifests are default placeholders; the inventory is the authority.
