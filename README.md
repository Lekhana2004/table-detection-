### Table and Title Extraction from Financial PDFs (Gemini + OCR)

Detect and extract tables and their titles from financial statement PDFs (both digital and scanned) using Google Gemini, pdf2image, and optional OCR. Designed for Google Colab, the pipeline converts PDFs to images, calls Gemini for structured JSON, and exports tidy CSVs for downstream analysis.

#### Why this exists
Financial PDFs are notoriously inconsistent. This project automates: locating tables, pairing them with nearby titles, and returning clean, analyzable data.

---

### Features
- **Multi-PDF batch support**: Process an entire folder of PDFs at once.
- **Digital + scanned PDFs**: Uses `pdf2image` and optional `pytesseract` OCR fallback.
- **Gemini-powered extraction**: Structured JSON responses (schema-first prompting).
- **CSV/JSON export**: Clean tables exported to `output/` for immediate use.
- **Robust error handling**: Invalid PDFs, empty pages, missing titles, and API timeouts.
- **Colab-first UX**: Simple, copy-paste cells to get results.

---

### Architecture / Workflow

```
PDFs  →  pdf2image (per-page PNGs)  →  [optional] OCR (pytesseract)
       →  Gemini (google-generativeai) prompt  →  Structured JSON
       →  pandas normalization  →  CSV + JSON written to output/
```

High level:
1) Convert PDF pages to images with `pdf2image` (Poppler).
2) For scanned/low-quality pages, run OCR via `pytesseract` as a fallback signal.
3) Send page image(s) + instructions to Gemini for table detection + title association.
4) Normalize Gemini responses into a consistent JSON schema.
5) Save JSON and flattened CSV to `output/` per PDF/page/table.

---

### Tools & Libraries
- **google-generativeai**: Calls Gemini (e.g., `gemini-1.5-pro` or `gemini-1.5-flash`) for structure-aware extraction.
- **pdf2image**: Converts PDFs to images. Requires Poppler binaries.
- **pillow (PIL)**: Image handling (resize, format conversions).
- **pandas**: Normalizes JSON into tidy CSV.
- **pytesseract (optional)**: OCR for scanned PDFs; requires Tesseract binary.

Rationale: Gemini is strong at layout understanding from images; `pdf2image` offers reliable, deterministic rasterization; `pandas` standardizes output for analytics.

---

### Installation (Google Colab)

1) Open a new Colab notebook (`https://colab.research.google.com`).
2) System dependencies (Poppler for `pdf2image`, Tesseract for OCR fallback):

```bash
!apt-get update -qq
!apt-get install -y -qq poppler-utils tesseract-ocr | cat
```

3) Python dependencies:

```bash
!pip install -q -r https://raw.githubusercontent.com/your-org/your-repo/main/requirements.txt
```

Or, if running directly from a cloned repo in Colab/VM:

```bash
!pip install -q -r requirements.txt
```

4) Set your Gemini API key securely (do NOT hardcode in notebooks):

```python
import os, getpass
os.environ["GEMINI_API_KEY"] = getpass.getpass("Enter GEMINI_API_KEY: ")
# Optionally choose a model name your pipeline supports
MODEL_NAME = os.environ.get("GEMINI_MODEL", "gemini-1.5-pro")
```

Tip: In Colab, you can also use the "secrets" UI or `google.colab.userdata.get('GEMINI_API_KEY')` if configured.

---

### Usage

Upload one or more PDFs and run your pipeline cells. Example helpers below are generic; adapt to your notebook/script entrypoints.

1) Upload PDFs into Colab:

```python
from google.colab import files
import os

os.makedirs("/content/pdfs", exist_ok=True)
uploaded = files.upload()  # Pick one or more local PDF files
for name, data in uploaded.items():
    with open(f"/content/pdfs/{name}", "wb") as f:
        f.write(data)
```

2) Run the extraction pipeline (replace the call with your project’s function or notebook cell):

```python
INPUT_DIR = "/content/pdfs"
OUTPUT_DIR = "/content/output"
os.makedirs(OUTPUT_DIR, exist_ok=True)

# Example: call your pipeline function here
# process_pdf_directory(input_dir=INPUT_DIR, output_dir=OUTPUT_DIR, model_name=MODEL_NAME, use_ocr_fallback=True)

print("Done. Check:", OUTPUT_DIR)
``` 

3) Browse results:

```python
import glob
sorted(glob.glob("/content/output/**/*", recursive=True))[:25]
```

---

### Sample Output

JSON (per page/table):

```json
{
  "pdf": "Q1_2024_FS.pdf",
  "page": 7,
  "title": "Consolidated Statements of Operations (Unaudited)",
  "tables": [
    {
      "id": "table_1",
      "bbox": [72, 180, 540, 690],
      "columns": ["Revenue", "Q1 2024", "Q1 2023"],
      "rows": [
        ["Product", "12,345", "11,210"],
        ["Service", "8,765", "7,980"],
        ["Total revenue", "21,110", "19,190"]
      ]
    }
  ]
}
```

CSV (flattened):

```csv
pdf,page,title,table_id,row_index,column,cell
Q1_2024_FS.pdf,7,"Consolidated Statements of Operations (Unaudited)",table_1,0,Revenue,Product
Q1_2024_FS.pdf,7,"Consolidated Statements of Operations (Unaudited)",table_1,0,Q1 2024,12,345
Q1_2024_FS.pdf,7,"Consolidated Statements of Operations (Unaudited)",table_1,0,Q1 2023,11,210
...
```

Note: Exact schema/fields depend on your prompt and post-processing code; keep both consistent and documented in your repo.

---

### Edge Cases Handled
- **Multi-page tables**: Heuristics to stitch continuation pages (e.g., carry-over headers).
- **Missing or ambiguous titles**: Use nearest heading or default to page-level context.
- **Scanned/low-resolution PDFs**: OCR fallback via `pytesseract`.
- **Rotated or skewed pages**: Normalize orientation before sending to Gemini.
- **Invalid/corrupt PDFs**: Try-except blocks and per-file error logs.
- **API variability**: Retry with backoff and max tokens/page size controls.

---

### Tips & Best Practices
- Prefer `gemini-1.5-pro` for high accuracy; use `gemini-1.5-flash` for speed/cost.
- Prompt with a strict JSON schema and `strict: true` (if supported) to reduce parsing errors.
- Limit per-request pages/images to control context size and rate limits.
- Log raw Gemini responses alongside parsed JSON for auditability.

---

### Security & Privacy
- Never commit API keys. Use `GEMINI_API_KEY` in Colab environment only.
- Be cautious with sensitive PDFs—avoid uploading confidential material to third-party services.

---

### License
Licensed under the **Apache License 2.0**. See the `LICENSE` file if provided, or read the license text at `https://www.apache.org/licenses/LICENSE-2.0`.

---

### Citation
If this helps your research/product, please star the repo and cite in your docs.

# Table Detection and Title Extraction

A Python-based tool to detect tables in PDF documents, extract their titles, and export the results in structured formats using Google Gemini Vision API.

---

## Features
- Convert PDF pages into high-resolution images  
- Detect tables on each page with Gemini Vision API  
- Extract table titles (or mark as `Unknown` if not found)  
- Export results in JSON and CSV  
- Handles invalid or corrupted PDFs with error logging  

---

## Tech Stack
- PDF Processing: `pdf2image`, `Pillow`  
- AI/ML: `google-generativeai` (Gemini Vision API)  
- Data Handling: `pandas`, `json`  
- Configuration: `python-dotenv` (API key and settings)  

---

## High-Level Workflow
```mermaid
graph TD
    A[PDF Document] --> B[PDF to Images]
    B --> C[Gemini Vision API]
    C --> D[Extract Table Titles]
    D --> E[Save Results]
    E --> F1[JSON]
    E --> F2[CSV]






















