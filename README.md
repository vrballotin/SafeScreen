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
