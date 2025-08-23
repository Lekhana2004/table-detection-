# table-detection-
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






















