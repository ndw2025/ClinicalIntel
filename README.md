# ClinicalIntel: Health Misinformation Detection and Evidence-Aware Verification System

ClinicalIntel is a full-stack research prototype for detecting and explaining health misinformation in medical claims and medical article URLs. The system combines a React chat workspace, a FastAPI backend, a fine-tuned transformer classifier, deterministic medical-scope validation, trusted evidence retrieval, evidence-aware verdict calibration, MongoDB-backed user sessions, and report export features.

The project is designed for academic research and final-year project demonstration. It provides informational credibility assessment only. It does not provide medical diagnosis, treatment advice, dosage recommendations, or emergency health guidance.

## Project Overview

Online health misinformation can influence how people understand vaccines, diseases, symptoms, treatments, supplements, and public-health guidance. During events such as the COVID-19 pandemic, unsupported medical claims spread quickly across websites and social platforms. Many users do not have the time or technical knowledge to manually compare those claims against medical databases or public-health institutions.

ClinicalIntel addresses this problem by allowing a user to submit either a direct health claim or a specific medical article URL. The backend validates that the input is medical and suitable for analysis, runs a transformer-based credibility classifier, retrieves evidence from trusted health sources, calibrates the final verdict according to evidence quality, and returns a structured explanation through a chat-style interface.

## Research Gap

Many misinformation detection projects focus mainly on binary classification accuracy. This creates several limitations in a health domain:

- General fake-news detection models are not always suitable for medical claims because health misinformation uses domain-specific terminology.
- A raw machine-learning confidence score can be misleading when the model is overconfident or when the claim is outside the training distribution.
- Many classifier-based systems do not show users which evidence was consulted.
- URL-based health misinformation detection is harder than text-only classification because web pages require extraction, cleanup, and article suitability checks.
- User-facing systems need clear explanations, cautious verdicts, and strong medical-scope validation rather than behaving like a general chatbot.

ClinicalIntel responds to this gap by combining model inference with evidence retrieval, explainability, source inspection, and evidence-aware calibration. The final verdict is not treated as the raw model output alone. Instead, the system separates the raw classifier signal from the calibrated user-facing verdict.

## Main Objectives

- Build a health-focused misinformation detection web application for English medical claims and medical article URLs.
- Integrate a fine-tuned transformer classifier into a FastAPI backend.
- Validate whether user input is medical and verifiable before analysis.
- Extract readable text from specific public medical article URLs.
- Retrieve supporting, contradicting, neutral, or unknown evidence from trusted health sources.
- Calibrate final verdict confidence using both model output and evidence quality.
- Present results in a chat-based user interface with an evidence inspector.
- Store authenticated user sessions, chat messages, reports, and cached analysis data.
- Support report review and export for project demonstration and documentation.

## Key Features

### 1. Backend-Authenticated User Flow

- Signup and login through backend APIs.
- Authenticated user hydration through `/api/auth/me`.
- Session cookie support for authenticated app usage.
- Explicit logout through the profile menu.
- Unauthenticated users are redirected to the login page.

### 2. Chat-Based Medical Verification Workspace

- Chat-style analysis page at `/app/analysis`.
- `+ New chat` creates a new verification thread.
- Saved sessions list for returning to previous analyses.
- Separate claim mode and URL mode inputs.
- Assistant response cards tied to the selected user message.
- Voice-to-text input support in claim mode on supported browsers.

### 3. Medical-Scope Validation

- Deterministic validation before inference.
- Accepts medical claims involving diseases, treatments, drugs, symptoms, vaccines, diagnostics, anatomy, and public-health topics.
- Rejects non-medical questions such as sports, programming, finance, entertainment, and generic personal questions.
- Rejects broad non-verifiable educational prompts when they do not form a claim suitable for credibility assessment.

### 4. URL Analysis

- Accepts specific medical article URLs, guidance pages, and PubMed records.
- Rejects generic homepages, search pages, weak extraction pages, localhost/private network URLs, and unsafe URLs.
- Extracts page title, canonical URL, source domain, paragraph count, content length, article-likeness diagnostics, and normalized article text.
- Uses the extracted text for medical-scope validation and analysis.

### 5. Transformer-Based Classifier

- Loads an exported fine-tuned transformer model from `backend/final_best_model`.
- Supports CPU inference by default and CUDA if available.
- Returns raw predicted class, raw confidence, and probability values.
- Keeps raw classifier output visible for transparency.

### 6. Evidence Retrieval

- Retrieves evidence from trusted public-health and biomedical sources.
- Uses PubMed, WHO, and CDC where enabled.
- Deduplicates repeated evidence records.
- Tracks evidence stance as supporting, contradicting, neutral, or unknown.
- Displays source cards in the frontend evidence inspector.

### 7. Evidence-Aware Verdict Calibration

ClinicalIntel does not blindly show the raw classifier confidence as the final result. The verdict service considers model prediction, evidence strength, source quality, and stance counts.

Final verdict labels include:

- `Likely Verifiable`
- `Likely Misleading`
- `Uncertain`
- `Needs More Evidence`

This design reduces the risk of showing an overconfident model result when trusted evidence suggests a different interpretation.

### 8. Evidence Inspector

- Right-side evidence panel for the selected assistant response.
- Shows current verdict, calibrated confidence, raw classifier signal, and source cards.
- Source detail drawer for reviewing evidence snippets and provider links.
- Desktop inspector and smaller-screen drawer behavior.

### 9. Reports and Export

- Reports page for reviewing previous analyses.
- Report detail view.
- Text summary export.
- Structured browser-generated PDF export.
- Supports backend report data and local saved report fallback where available.

### 10. Research Feed, Verified Sources, and Settings

- Research Feed page for browsing curated or backend-derived findings.
- Verified Sources page for reviewing evidence providers.
- Settings page for local prototype preferences.
- Sidebar navigation for product sections.

## System Architecture

```text
User
  -> React + Vite frontend
  -> FastAPI backend
  -> Medical-scope validation
  -> URL extraction and preprocessing
  -> Transformer credibility classifier
  -> Evidence retrieval from PubMed, WHO, CDC
  -> Evidence-aware verdict calibration
  -> Chat response, evidence inspector, report export
  -> MongoDB storage for users, sessions, messages, reports, and cache
```

## Technology Stack

### Frontend

- React 19
- TypeScript
- Vite
- React Router
- Tailwind CSS
- Material UI
- Framer Motion
- Lucide React icons
- jsPDF for report export
- Vitest and React Testing Library

### Backend

- Python
- FastAPI
- Uvicorn
- Pydantic and Pydantic Settings
- Transformers
- PyTorch
- HTTPX
- BeautifulSoup
- readability-lxml
- Motor for MongoDB
- Tenacity for retry behavior
- Structlog for structured logging
- Pytest and pytest-asyncio

### Machine Learning and Evidence

- Fine-tuned transformer sequence classification model
- Local model artifact loaded from `backend/final_best_model`
- PubMed evidence retrieval
- WHO and CDC public-health source integration
- Optional Ollama cloud rewrite layer for presentation wording

### Database and Persistence

- MongoDB Atlas or compatible MongoDB instance
- User accounts
- Auth sessions
- Chat sessions
- Chat messages
- Reports
- Extraction, inference, evidence, and rewrite cache records

## Repository Structure

```text
.
|-- backend/
|   |-- app/
|   |   |-- api/
|   |   |-- core/
|   |   |-- models/
|   |   |-- services/
|   |   `-- utils/
|   |-- tests/
|   |-- .env.example
|   |-- requirements.txt
|   `-- README.md
|-- frontend/
|   |-- public/
|   |-- src/
|   |   |-- components/
|   |   |-- context/
|   |   |-- data/
|   |   |-- hooks/
|   |   |-- layouts/
|   |   |-- pages/
|   |   |-- routes/
|   |   |-- services/
|   |   `-- types/
|   |-- .env.example
|   |-- package.json
|   `-- README.md
|-- design/
|-- setup.md
|-- test.md
`-- README.md
```

## Model Artifact Notice

The trained model files are not committed to GitHub because model weight files are large and can exceed GitHub file limits. After cloning the repository, copy the exported model into:

```text
backend/final_best_model
```

Required runtime files:

```text
backend/final_best_model/config.json
backend/final_best_model/model.safetensors
backend/final_best_model/tokenizer.json
backend/final_best_model/tokenizer_config.json
backend/final_best_model/training_args.bin
```

See `setup.md` for exact PowerShell and Git Bash copy commands.

## Environment Variables

Backend environment file:

```text
backend/.env
```

Frontend environment file:

```text
frontend/.env
```

Important backend values:

```env
APP_ENV=development
APP_HOST=127.0.0.1
APP_PORT=8000
FRONTEND_ORIGINS=http://localhost:5173,http://127.0.0.1:5173
MODEL_DIR=./final_best_model
MONGODB_URL=mongodb+srv://username:password@cluster.mongodb.net/?retryWrites=true&w=majority
MONGODB_REQUIRED=false
MONGODB_DB_NAME=clinicalintel
PUBMED_ENABLED=true
WHO_ENABLED=true
CDC_ENABLED=true
OLLAMA_ENABLED=false
OLLAMA_API_KEY=
OLLAMA_MODEL=gpt-oss:20b-cloud
```

Important frontend value:

```env
VITE_API_BASE_URL=http://127.0.0.1:8000
```

Do not commit real `.env` files, API keys, or database passwords.

## Quick Start

For complete setup instructions for both PowerShell and Git Bash, read:

```text
setup.md
```

Short local run summary:

### Backend

```bash
cd backend
python -m venv .venv
source .venv/Scripts/activate
python -m pip install -r requirements.txt
python -m uvicorn app.main:app --host 127.0.0.1 --port 8000 --reload --reload-dir app
```

### Frontend

```bash
cd frontend
npm install
npm run dev -- --host 127.0.0.1 --port 5173
```

Open:

```text
http://127.0.0.1:5173
```

## Main API Endpoints

```text
GET    /health
GET    /api/model/info
GET    /api/sources/status
POST   /api/auth/signup
POST   /api/auth/login
GET    /api/auth/me
POST   /api/auth/logout
GET    /api/chat/sessions
POST   /api/chat/sessions
GET    /api/chat/sessions/{session_id}
POST   /api/predict
POST   /api/extract
POST   /api/evidence
POST   /api/analyze
GET    /api/feed
GET    /api/reports
GET    /api/reports/{request_id}
```

## Manual Test Examples

Accepted medical claims:

```text
Do COVID-19 vaccines reduce severe disease?
Does insulin help manage diabetes?
Can sugar cure cancer?
Does drinking bleach cure COVID-19?
Can vitamin C prevent colds?
```

Rejected non-medical examples:

```text
Who won the cricket match yesterday?
Can you help me with JavaScript loops?
```

Accepted URL examples:

```text
https://www.who.int/news-room/questions-and-answers/item/coronavirus-disease-(covid-19)-vaccines
https://www.cdc.gov/diabetes/treatment/index.html
https://pubmed.ncbi.nlm.nih.gov/33301246/
```

Rejected URL example:

```text
https://www.news-medical.net/
```

The rejected URL is a homepage, not a specific medical article or guidance page.

## Testing

Backend:

```bash
cd backend
source .venv/Scripts/activate
python -m pytest
```

Frontend:

```bash
cd frontend
npm run lint
npm run test
npm run build
```

## Safety and Limitations

- ClinicalIntel is a prototype research system.
- It does not provide medical advice, diagnosis, treatment decisions, or emergency support.
- Results depend on model quality, evidence retrieval quality, and source availability.
- The raw classifier can be wrong; therefore the system uses evidence-aware calibration.
- URL extraction can fail when external websites block requests or use complex page structures.
- MongoDB is required for reliable authenticated persistence in production.
- The system currently focuses on English text and URLs, not images, video, audio, or multilingual misinformation.

## Future Improvements

- Retrain and evaluate the classifier on a larger balanced medical misinformation dataset.
- Add stronger evidence ranking and stance detection.
- Add source-specific extraction rules for WHO, CDC, PubMed, and major health publishers.
- Improve multilingual support.
- Add deployment monitoring and stronger security hardening.
- Expand automated browser testing for full user workflows.
- Add expert review for clinical validation.

## Project Status

ClinicalIntel is a working academic prototype. It is suitable for local demonstration, research reporting, and further development. For real deployment, it requires production-grade database configuration, security review, model evaluation, and medical expert validation.
