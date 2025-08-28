### Contributing Guide

Thanks for your interest in improving this project! We welcome issues, discussions, and pull requests.

---

### How to Contribute
1. Fork the repository and create your feature branch from `main`:
   - Branch naming: `feature/<short-name>` or `fix/<short-name>`
2. If working in Colab, open a new notebook or the project notebook and install deps:

```bash
!apt-get update -qq && apt-get install -y -qq poppler-utils tesseract-ocr | cat
!pip install -q -r requirements.txt
```

3. Set `GEMINI_API_KEY` via environment variable (do not hardcode):

```python
import os, getpass
os.environ["GEMINI_API_KEY"] = getpass.getpass("Enter GEMINI_API_KEY: ")
```

4. Make focused edits with clear commit messages.
5. Add or update documentation (README examples, edge cases, rationale) for visible changes.
6. Ensure notebooks/scripts run end-to-end on sample PDFs before submitting.

---

### Pull Request Checklist
- Descriptive title and summary of changes
- Scope limited to one logical change set
- Updated docs/examples if behavior or options changed
- No secrets or private data committed
- CI/lint (if available) passes

---

### Reporting Issues
Please include:
- What you tried and expected vs. what happened
- Minimal reproducible steps (PDF type, single-page sample if possible)
- Environment (Colab, local Python version) and dependency versions
- Any relevant logs or error messages

---

### Code Style & Principles
- Favor readable, explicit code with clear variable names
- Handle errors gracefully and log actionable messages
- Add small, focused helpers rather than large monolith functions
- Keep prompts and JSON schemas versioned and documented

---

### License
By contributing, you agree that your contributions will be licensed under the Apache 2.0 License.

