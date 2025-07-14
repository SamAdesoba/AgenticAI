# AgenticAI

# Credit Underwriting Document Processing System

## 1. Introduction

This system automates the extraction, processing, and standardization of credit underwriting documents. It streamlines credit assessment by extracting key financial and administrative data, applying business rules, and saving processed info to a structured SQL Server database.

---

## 2. Architecture Overview

The system is modular and supports parallel processing. Key steps:

- Extract records from SQL DB
- Download documents in parallel
- Process each document using extractor modules
- Combine and standardize extracted data
- Apply hard & soft business rules
- Save structured data back to DB
- Clean up temporary files

---

## 3. Core Components

### `main.py`

- Orchestrates entire flow
- Manages logging, config, scheduling
- Controls document download & processing
- Applies final transformations
- Uses `joblib` for parallelism

### `utils/standardized_dataframe.py`

- `transform_dataframe(df)`: Standardizes column names and injects default values
- `hard_soft_rule(df)`: Applies rule-based commenting logic based on facility type

### `db/db.py`

- Handles DB connectivity via `pyodbc` and `SQLAlchemy`
- Supports cross-platform ODBC setup
- Key functions:
  - `get_last_runtime()`
  - `extract_data_from_db(table)`
  - `get_loan_details_from_db(account_number)`

### `extractors/`

- Each extractor specializes in a specific document type
- Uses Google Gemini LLM (e.g., `gemini-2.5-flash-preview-04-17`)
- Extracted fields include:
  - `is_present`
  - `date`
  - `amount`
  - `tenor`
  - `signature`
  - `stamp`
  - `missing_features`
  - `extraction_issues`

---

## 4. Data Flow

1. `extract()` triggers `main_extraction()`
2. Records pulled via `db.extract_data_from_db()`
3. Each record is processed in parallel:
   - Downloads documents
   - Sends to respective extractors
   - Standardizes dataframe
   - Applies business rules
4. Final result saved via `save_to_db()`
5. Temporary files cleaned using `TempFileManager`

---

## 5. Key Features

- ✅ Automated extraction from unstructured documents
- ✅ Modular extractors for different doc types
- ✅ Parallel processing with `joblib`
- ✅ Seamless DB integration
- ✅ Rule-based commenting (hard/soft)
- ✅ Efficient temp file cleanup
- ✅ Supports incremental data loads
- ✅ Dashboard and reporting support

---

## 6. Setup and Dependencies

- **Core libraries**: `pandas`, `numpy`, `requests`, `pyodbc`, `SQLAlchemy`, `joblib`
- **LLM integration**: Google Gemini API
- **Database**: SQL Server (cross-platform support)
- Optional: OCR/NLP dependencies for extractors

---

## 7. Next Steps

- [ ] Implement logic for loans > or < 100M
- [ ] Add audited account and turnover validations for missing data
- [ ] Investigate checklist for additional missing conditions

---
