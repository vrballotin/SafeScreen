# SafeScreen

An auditable AI-assisted workflow for systematic review deduplication, title/abstract screening, and full-text prioritization.

## Overview

SafeScreen is a reproducible workflow developed to reduce manual workload in systematic reviews while keeping all decisions traceable and auditable.

The workflow was built for a methodological validation study using a completed systematic review as the reference standard. It is intended for research, validation, and audit purposes. It should not be used as a replacement for human reviewers.

## What SafeScreen does

SafeScreen includes four main components:

1. **Deduplication**
   - Detects duplicate bibliographic records using DOI, PMID, PMCID, title similarity, abstract similarity, year, and author safeguards.
   - Keeps the most complete record within each duplicate cluster.
   - Exports removed duplicates and duplicate-pair audit files.

2. **Title and abstract screening**
   - Applies protocol-derived rules to classify records as `include` or `exclude`.
   - Uses a conservative machine-learning rescue step for borderline records.
   - Exports all screening decisions, included records, excluded records, and PRISMA-style flow counts.

3. **Full-text prioritization**
   - Extracts text from PDF files.
   - Uses a structured large language model prompt to classify full texts against predefined eligibility criteria.
   - Exports included, excluded, and article-level decision files.

## Editable parts of the workflow

SafeScreen was designed to be adapted to different systematic review topics. The main logic of the workflow is reproducible, but several parts of the code are intentionally editable so that researchers can align the screening process with their own protocol.

The most important editable sections are the eligibility criteria used for title/abstract screening and full-text prioritization. These include the inclusion and exclusion rules, population terms, intervention or exposure terms, comparator terms, outcome terms, and study-design filters. For a new review, these rules should be updated according to the research question and eligibility criteria defined in the protocol.

The title/abstract screening module contains protocol-derived keyword groups and rule-based gates. These can be modified to reflect different topics, synonyms, acronyms, clinical terms, and exclusion concepts. The optional machine-learning rescue step can also be adjusted by changing the training labels, similarity thresholds, and rescue probability cutoff.

The deduplication module can be adapted by changing the matching thresholds for title similarity, abstract similarity, year tolerance, and author safeguards. These parameters may need adjustment depending on the quality of the bibliographic records, the databases used, and the amount of missing metadata.

The full-text prioritization module uses a structured prompt with predefined eligibility criteria. This prompt should be edited for each review before running the workflow. Researchers should clearly define what counts as eligible, what should be excluded, and what information the model must extract or justify. The model output should always be reviewed by humans before any final decision is made.

Paths, file names, database exports, and output folders are also editable. Users should update these sections to match their local directory structure and the format of their bibliographic files.

Because eligibility criteria vary across reviews, SafeScreen should not be used as a fixed screening tool. It is a customizable workflow. Any adaptation should be documented, and all thresholds, prompts, and rule changes should be reported to keep the review process transparent and auditable.

## Repository structure
SafeScreen/
├── CODE/
│   ├── 01_deduplication.py
│   ├── 02_title_abstract_screening.py
│   └── 03_full_text_ai_screening.py
├── LICENSE
└── README.md

**Installation**
Option 1: Google Colab

**Download or clone the repository:**

git clone https://github.com/vrballotin/SafeScreen.git

Upload the notebooks to Google Drive or open them directly in Google Colab.

**Run the notebooks sequentially**:

01_deduplication.ipynb
02_title_abstract_screening.ipynb
03_full_text_ai_screening.ipynb

Each notebook contains its own installation and configuration section.

Option 2: Local installation

**Create and activate a virtual environment:**

python -m venv .venv

Linux or macOS:

source .venv/bin/activate

Windows:

.venv\Scripts\activate

Install the dependencies:

python -m pip install --upgrade pip
pip install -r requirements.txt

Local execution may require removing or replacing the Google Colab and Google Drive mounting commands.

**Recommended working-directory structure**

Create the following directory in Google Drive:

SafeScreen_data/
├── input/
│   └── records.csv
├── pdfs/
└── output/

**Set the base directory in each notebook:**

BASE_DIR = "/content/drive/MyDrive/SafeScreen_data"

INPUT_CSV = f"{BASE_DIR}/input/records.csv"
PDF_DIR = f"{BASE_DIR}/pdfs"
OUTPUT_DIR = f"{BASE_DIR}/output"

Do not leave [PATH_REMOVED] or other placeholder paths in the notebooks.

Input data
Bibliographic CSV

The deduplication module accepts a CSV file containing bibliographic records exported from databases or reference-management software.

The only mandatory field is a title column.

**Recommended fields are:**

Information	Accepted examples of column names	Requirement
Title	title, Title	Required
Abstract	abstract, summary, abstract_note	Strongly recommended
DOI	doi	Recommended
PubMed ID	pmid, pubmed_id	Optional
PubMed Central ID	pmcid, pmc_id	Optional
Year	year, publication_year, pubyear	Recommended
Authors	authors, author	Recommended
Journal	journal, source, publication	Optional
Volume	volume	Optional
Issue	issue, number	Optional
Pages	pages, page	Optional
Keywords	keywords, keyword	Optional
Notes	notes, note	Optional

SafeScreen attempts to identify common variations in column names automatically. Users should inspect the detected columns printed at the beginning of each run.

The CSV should preferably use UTF-8 encoding. The deduplication module also attempts to read common alternative encodings.

Full-text PDFs

Place the PDF files to be processed inside:

SafeScreen_data/pdfs/

**Requirements:**

one article per PDF;
filenames should be unique;
PDFs must contain extractable text;
scanned image-only PDFs require OCR before processing;
users must have permission to process the documents.

The current workflow uses the PDF filename as the article identifier.

Configuring the eligibility criteria

SafeScreen is a customizable workflow rather than a universal screening classifier.

Before using it for another systematic review, edit the protocol-specific sections in the title/abstract and full-text notebooks.

These sections include:

population criteria;
intervention or exposure terms;
comparator criteria;
eligible outcomes;
study designs;
exclusion criteria;
synonyms and abbreviations;
title and abstract matching thresholds;
machine-learning rescue threshold;
full-text prompt.

All adaptations should be documented in the systematic-review protocol and reported in the final publication.

**Module 1: Deduplication**

Open:

notebooks/01_deduplication.ipynb

Configure:

INPUT_CSV = f"{BASE_DIR}/input/records.csv"
OUTPUT_DIR = f"{BASE_DIR}/output"

The main configurable thresholds are:

TITLE_THR = 97
ABS_THR = 95
ABS_MIN_LEN = 150
ABS_PREFIX_LEN = 80
YEAR_TOLERANCE = 1
AUTHOR_GUARD = True

Run all notebook cells.

Expected outputs:

deduplicated.csv
duplicates_removed.csv
duplicate_pairs.csv
deduplicated.csv: records retained after deduplication.
duplicates_removed.csv: records removed as duplicates, including the primary removal reason.
duplicate_pairs.csv: pairs identified by deterministic or fuzzy matching for audit.

Researchers should manually inspect a sample of duplicate pairs before accepting the final deduplicated dataset.

**Module 2: Title and abstract screening**

Open:

notebooks/02_title_abstract_screening.ipynb

The default input is:

output/deduplicated.csv

Review and adapt all protocol-derived term lists before execution.

The principal configurable parameters are:

MIN_OUTCOME_DOMAINS = 1
ML_RESCUE_THR = 0.90

Run all notebook cells.

Expected outputs:

AI_screening_ALL.csv
AI_screening_INCLUDED.csv
AI_screening_AUTO_EXCLUDED.csv
PRISMA_FLOW.txt
AI_screening_ALL.csv: all records and their automated decisions.
AI_screening_INCLUDED.csv: records retained for human assessment.
AI_screening_AUTO_EXCLUDED.csv: records classified as excluded.
PRISMA_FLOW.txt: summary counts for workflow reporting.

Automated exclusions must be reviewed according to the review protocol. The module should not be treated as an independent human reviewer without prospective validation.

**Module 3: Full-text prioritization**

Open:

notebooks/03_full_text_ai_screening.ipynb
Anthropic API key

An Anthropic API key is required for this module.

The recommended approach in Google Colab is to enter the key only for the current runtime:

import getpass
import os

os.environ["ANTHROPIC_API_KEY"] = getpass.getpass(
    "Enter your Anthropic API key: "
)

Do not:

paste the key directly into a notebook cell;
commit it to GitHub;
include it in a shared Google Drive folder;
save it in an unencrypted text file.

The notebook should retrieve it with:

api_key = os.environ.get("ANTHROPIC_API_KEY")

if not api_key:
    raise RuntimeError("ANTHROPIC_API_KEY was not configured.")
Model configuration

The archived validation release used:

MODEL = "claude-sonnet-4-6"
TEMPERATURE = 0
MAX_TOKENS = 1200
MAX_INPUT_CHARACTERS = 35000

Model availability, API behavior, and pricing may change. Record the model name, execution date, parameters, and API version used in each study.

**Running the module**

Place the eligible PDFs in the configured PDF directory and review the structured eligibility prompt.

Set:

FORCE_FRESH = True

to delete earlier results and screen all PDFs again.

Set:

FORCE_FRESH = False

to resume an interrupted run.

**Expected outputs:**

full_text_decisions_claude.csv
full_text_INCLUDED.csv
full_text_EXCLUDED.csv
full_text_summary.txt

The terms “included” and “excluded” in these output filenames refer to automated prioritization categories. Final eligibility decisions must be made by human reviewers.

**Example dataset**

A small synthetic dataset should be provided in:

examples/sample_records.csv

It should contain:

at least one exact DOI duplicate;
at least one fuzzy title duplicate;
at least one unique eligible record;
at least one clearly irrelevant record;
missing metadata examples.

Users can copy the example file to the input directory to verify that the workflow runs correctly before processing their own review data.

**Reproducibility and audit trail**

For every execution, retain:

the original database exports;
the deduplicated dataset;
all duplicate-pair files;
all automated screening decisions;
the exact eligibility-term lists;
the full-text prompt;
package versions;
model name and configuration;
execution date;
manually reviewed corrections;
the SafeScreen release or commit identifier.

For studies using a modified version, archive the exact code release used for analysis in a permanent repository such as Zenodo.

**Known limitations**
The workflow was internally validated in a single systematic-review topic.
Eligibility terms must be adapted for each review.
Performance may change with different databases, terminology, languages, or incomplete metadata.
The title and abstract module does not reproduce all human full-text retrieval decisions.
PDF text extraction may fail for scanned or poorly formatted documents.
The full-text module depends on a commercial external API.
Large-language-model outputs may change across model versions.
API-generated decisions require human verification.
Workload reduction does not establish that autonomous exclusion is safe.

External and prospective validation is recommended before operational use in other topics.

**Data protection**

Do not submit confidential, identifiable, unpublished, or restricted information to an external API unless this is permitted by the applicable research protocol, institutional policies, data-use agreements, and ethics requirements.

Systematic-review articles are generally public documents, but access rights and copyright restrictions still apply.

**Troubleshooting**
Title column not found

Confirm that the CSV includes a column containing title in its name.

**Input file not found**

Check BASE_DIR, INPUT_CSV, and the Google Drive mounting status.

**Empty abstract fields**

The title and abstract module can run with missing abstracts, but classification performance may be reduced.

**PDF text is empty**

The document may be scanned. Apply OCR before processing or assess the article manually.

**API authentication error**

Confirm that ANTHROPIC_API_KEY is configured in the current runtime and that the account has API access.

**Citation**

When using SafeScreen, cite the archived software release:

Ballotin VR, et al. SafeScreen: an auditable AI-assisted workflow for systematic-review deduplication, title and abstract screening, and full-text prioritization. Version 1.0.1. Zenodo. https://doi.org/10.5281/zenodo.21412862

Also cite the methodological validation article once published.

**License**

SafeScreen is distributed under the MIT License. See LICENSE.

The license applies to the software code. It does not grant permission to redistribute copyrighted articles or database records.

**Contributing**

Issues, corrections, and reproducibility reports are welcome through the GitHub Issues page.

**When reporting a problem, provide:**

the SafeScreen version or commit;
Python and package versions;
operating environment;
input-column names;
the complete error message;
a minimal non-confidential example, when possible.
Contact

**Vinicius Remus Ballotin, MD
Department of Health Sciences
University of Caxias do Sul
Caxias do Sul, Rio Grande do Sul, Brazil**
