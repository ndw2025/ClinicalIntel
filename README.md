```

```

# ClinicalIntel

ClinicalIntel is a prototype health misinformation detection web application with:

- a React + Vite frontend in `frontend/`
- a FastAPI + transformer backend in `backend/`
- a saved fine-tuned classifier in `backend/final_best_model/`

The product is analysis-first: users submit a health claim or article URL, the backend runs model inference, retrieves evidence, and returns a structured response for the frontend workspace.

## Current local run status

The project is already running on this machine:

- Frontend: `http://127.0.0.1:5173`
- Backend: `http://127.0.0.1:8000`

Current dev server processes:

- Backend PID: `4840`
- Frontend PID: `7464`

To stop them from PowerShell:

```powershell
Stop-Process -Id 4840,7464
```

To stop them from Git Bash:

```bash
taskkill //PID 4840 //F
taskkill //PID 7464 //F
```

## Run from Git Bash

Use these exact local origins during development:

- Frontend: `http://127.0.0.1:5173`
- Backend: `http://127.0.0.1:8000`

`localhost` and `127.0.0.1` are different browser origins for CORS. If the frontend runs on `127.0.0.1` but the backend only allows `localhost`, browser preflight requests to `/api/analyze` will fail and the UI will fall back to offline demo mode.
If you change `.env`, the frontend host, or backend CORS settings while Vite is running, hard-refresh the browser tab after the servers restart so the client does not keep stale local dev state.

### 1. Start the backend

```bash
cd "/e/PROJECTS/Out Source Project/Health Misinformation Detection Web Application/backend"
source .venv/Scripts/activate
uvicorn app.main:app --host 127.0.0.1 --port 8000 --reload --reload-dir app
```

Backend health check:

```bash
curl http://127.0.0.1:8000/health
```

### 2. Start the frontend

Open a second Git Bash terminal:

```bash
cd "/e/PROJECTS/Out Source Project/Health Misinformation Detection Web Application/frontend"
npm run dev -- --host 127.0.0.1 --port 5173
```

Open:

```text
http://127.0.0.1:5173
```

## Project setup

### Backend setup

```bash
cd "/e/PROJECTS/Out Source Project/Health Misinformation Detection Web Application/backend"
python -m venv .venv
source .venv/Scripts/activate
python -m pip install --upgrade pip
python -m pip install -r requirements.txt
```

The backend reads environment variables from `backend/.env`.

Important values:

- `MONGODB_URL`
- `MONGODB_REQUIRED=false` for local development, `true` for production fail-fast behavior
- `MODEL_DIR=./final_best_model`
- `FRONTEND_ORIGINS=http://localhost:5173,http://127.0.0.1:5173`
- `OLLAMA_ENABLED=true|false`
- `OLLAMA_API_KEY`
- `OLLAMA_MODEL=gpt-oss:20b-cloud`

### Frontend setup

```bash
cd "/e/PROJECTS/Out Source Project/Health Misinformation Detection Web Application/frontend"
npm install
```

Create `frontend/.env` for local development:

```env
VITE_API_BASE_URL=http://127.0.0.1:8000
```

## Notes

- Manual text examples for the Analysis page:
  - `Can sugar reduce COVID-19 severity?`
  - `Do COVID-19 vaccines reduce severe disease?`
  - `Does insulin help manage diabetes?`
  - `Who won the cricket match yesterday?` should be rejected as non-medical.
- Manual URL examples:
  - `https://www.who.int/news-room/questions-and-answers/item/coronavirus-disease-(covid-19)-vaccines`
  - `https://www.cdc.gov/diabetes/treatment/index.html`
  - `https://pubmed.ncbi.nlm.nih.gov/33301246/`
  - `https://www.news-medical.net/news/20250527/Scientists-discover-unique-molecular-fingerprints-of-insulin-resistance.aspx`
  - `https://www.news-medical.net/` should be rejected because it is a homepage, not a specific article.
- Reports can be exported as text summaries or structured browser-generated PDF files from the Reports page.
- The backend `.venv` was created and dependencies were installed successfully.
- The backend test suite currently passes:

```bash
cd "/e/PROJECTS/Out Source Project/Health Misinformation Detection Web Application/backend"
source .venv/Scripts/activate
pytest
```

- The frontend production build also completes successfully:

```bash
cd "/e/PROJECTS/Out Source Project/Health Misinformation Detection Web Application/frontend"
npm run build
```
