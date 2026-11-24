# PackCheck AI – Local LLM Edition

Packaged-commodity label checking prototype using:
- FastAPI backend
- Browser frontend (HTML/CSS/JavaScript; no Node/npm required)
- Ollama local LLM (Qwen3 4B recommended starting model)
- OCR via Tesseract (optional; manual text entry works without OCR)
- Deterministic rule checks + LLM explanations

> Important: This is a development prototype, not an official Legal Metrology determination.
> Legal requirements can vary by commodity, package type, exemptions and amendments. Always
> verify the current rules and obtain qualified/legal review before operational use.
> The LLM only explains results; it does not decide compliance.

## 1. Requirements
- Windows 10/11, Python 3.10+
- Ollama installed
- Recommended: 16 GB+ RAM. Your 24 GB RAM / RTX 4050 laptop is a suitable starting point.
- Optional OCR: Tesseract OCR for Windows.

## 2. Start Ollama
Open PowerShell:
```powershell
ollama pull qwen3:4b
ollama run qwen3:4b
```
Wait for the model prompt, test it, then type `/bye`.
Ollama normally serves its local API at `http://localhost:11434`.

Check:
```powershell
ollama list
ollama ps
```

## 3. Start the backend
Open a NEW PowerShell window and go to the extracted project folder:
```powershell
cd path\to\PackCheck_AI_Local_LLM\backend
py -m venv .venv
.\.venv\Scripts\Activate.ps1
python -m pip install --upgrade pip
pip install -r requirements.txt
Copy-Item .env.example .env
uvicorn app.main:app --reload --host 127.0.0.1 --port 8000
```
If PowerShell blocks activation, run this once in that terminal:
```powershell
Set-ExecutionPolicy -Scope Process -ExecutionPolicy Bypass
.\.venv\Scripts\Activate.ps1
```
Backend test: open http://127.0.0.1:8000/api/health
API docs: http://127.0.0.1:8000/docs

## 4. Start the frontend
Open `frontend/index.html` in Chrome/Edge, OR from the project folder run:
```powershell
cd path\to\PackCheck_AI_Local_LLM\frontend
py -m http.server 5500
```
Then open http://127.0.0.1:5500

For camera access, use `http://localhost` (the local server URL), allow camera permission, and close other apps using the camera.

## 5. Use the app
1. Upload a clear package-label photo, or choose Start camera and Capture.
2. Click "Extract text (OCR)" if Tesseract is installed. If not, paste/type the package text in the OCR text box.
3. Click "Analyze label".
4. Review each extracted field. Correct OCR mistakes before relying on the report.
5. Read the rule-engine findings. "Needs review" means evidence is missing/unclear, not automatically illegal.
6. Choose a language and click "Explain with local AI" to get an Ollama-generated explanation.
7. Download the JSON evidence report.

## 6. Optional OCR installation
Install Tesseract for Windows from its official UB Mannheim distribution:
https://github.com/UB-Mannheim/tesseract/wiki

During installation, include English language data. Add the Tesseract install folder to PATH if needed, commonly:
`C:\Program Files\Tesseract-OCR`

Restart PowerShell and test:
```powershell
tesseract --version
```
The app still works without Tesseract: paste OCR/label text manually.

## 7. Ollama model settings
Default model is `qwen3:4b`. Change `OLLAMA_MODEL` in `backend/.env` if you have pulled another Ollama model.
Example:
```env
OLLAMA_BASE_URL=http://localhost:11434
OLLAMA_MODEL=qwen3:4b
```
Do not expose Ollama's local API directly to the public internet.

## 8. What is included
- Upload and webcam capture UI
- OCR endpoint (optional Tesseract)
- Editable label text and extracted declaration fields
- Rule-based preliminary checks for MRP, net quantity, responsible business name/address, consumer-care details, date marking and country of origin where applicable
- Product vs online-listing field comparison
- Local LLM explanations in English, Tamil, Bengali, Telugu and Malayalam (quality depends on model)
- Officer-style dashboard summary
- Downloadable JSON evidence report
- Knowledge notes and official-source starting points

## 9. Troubleshooting
**`ollama` is not recognized**: restart terminal after installing Ollama; verify installation.
**Connection refused at port 11434**: open Ollama from Start menu or run `ollama serve`.
**Model not found**: run `ollama pull qwen3:4b`.
**OCR unavailable**: install Tesseract and restart terminal; or paste text manually.
**CORS / frontend cannot connect**: backend must be running at `http://127.0.0.1:8000`; use the provided local frontend server.
**Slow responses**: close heavy apps; use a smaller model such as `qwen3:1.7b` if available.

## 10. Project structure
```text
PackCheck_AI_Local_LLM/
  backend/
    app/main.py
    app/rules.py
    app/ocr.py
    app/llm.py
    app/rag.py
    knowledge/
    requirements.txt
    .env.example
  frontend/
    index.html
    styles.css
    app.js
  reports/
  README_TANGLISH.md
```

## 11. Before real deployment
- Verify every rule against the latest official Act, Rules and amendments.
- Add category-specific exemptions and commodity-specific standards.
- Add authentication, role-based access, audit logs and secure storage.
- Add image quality checks, barcode/QR decoding and robust multi-view OCR.
- Use a production database and encrypted evidence storage.
- Do not use the development server or expose local Ollama publicly.
