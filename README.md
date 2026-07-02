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

## Repository structure

SafeScreen/
├── code/
│   ├── 01_deduplication.py
│   ├── 02_title_abstract_screening.py
│   ├── 03_full_text_ai_screening.py
|
├── LICENSE
└── README.md
