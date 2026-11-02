# data

This directory contains public, non-text provenance artefacts and instructions for locally generated data.

## Local-only source files and prepared text

The source PDFs, downloaded SDK page text, and prepared full-text JSONL exports are local-only. They are excluded from this public repository because no clear redistribution licence for the extracted document text was identified.

The ESP-WROOM-02 datasheet contains a copyright notice stating that all rights are reserved and that no express or implied intellectual-property licence is granted. Espressif's [Terms of Service](https://www.espressif.com/en/content/terms-use) also reserve rights in website content and restrict reproduction/distribution. This project therefore takes the conservative approach of publishing provenance metadata, not extracted document text. This is a project-handling decision, not a legal determination.

## Reproducing the data

1. Open `notebooks/week3_pipeline.ipynb` in Google Colab.
2. Run all cells. The notebook downloads the two PDF sources and retrieves the SDK Get Started page for local text conversion.
3. The Part B JSONL files are generated under `data/processed/`; Part C outputs are generated under `data/pipeline_v1/<kind>/`. These files remain local unless redistribution permission is confirmed.

The committed `week3_manifest.json` and the three Part C `pipeline_manifest.json` files preserve hashes, counts, processing configuration and source-to-output lineage. A hash is evidence of file identity, not evidence of permission to redistribute.

## Current public contents

- `week3_manifest.json`: Part B source/output provenance.
- `pipeline_v1/documents_datasheet/pipeline_manifest.json`
- `pipeline_v1/documents_trm/pipeline_manifest.json`
- `pipeline_v1/documents_sdk/pipeline_manifest.json`

The prepared JSONL files are deliberately not included.
