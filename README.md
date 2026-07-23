# 📚 AI Study Assistant

A full-stack, production-ready AI-powered study companion. Students upload lecture
notes (PDF, DOCX, or TXT) and the app turns them into summaries, grounded Q&A,
quizzes, flashcards, mock exam papers, and personalized study plans — so less time
is spent re-reading dense notes and more time is spent actually learning.

---

## 1. Project Overview

**App Name:** AI Study Assistant
**Problem Statement:** Students spend a disproportionate amount of time reading and
re-reading long lecture notes to extract the handful of ideas that actually matter
for exams. This app automates that extraction using an LLM (Groq's LLaMA models),
grounding every answer strictly in the student's own uploaded material.

**Live Deliverables:**
- Frontend deployed on **Vercel**
- Backend deployed on **HuggingFace Spaces** (Docker + FastAPI)

---

## 2. Features

| Feature | Description |
|---|---|
| **Authentication** | Register, login, logout, JWT-protected routes |
| **Dashboard** | Total subjects, total notes, recent uploads, quick actions |
| **Subjects** | Create / edit / delete subjects, each containing multiple notes |
| **Notes** | Upload PDF / TXT / DOCX, automatic text extraction & storage |
| **AI Summary** | Summary, key points, definitions, formulas, exam tips |
| **Ask AI** | Q&A strictly grounded in the uploaded note — replies "This topic is not covered in the uploaded notes." when the answer isn't present |
| **Quiz Generator** | 5 / 10 / 15 MCQs, each with 4 options, correct answer, and explanation — fully interactive with scoring |
| **Flashcards** | Auto-generated Q/A flashcards with a flip animation |
| **Explain Simply** | Select any text in a note and get a "explain it like I'm 12" rewrite |
| **Study Planner** | Input exam date + daily study hours → get a day-by-day revision schedule |
| **Search** | Full-text search across all uploaded notes |
| **History** | Every summary, quiz, flashcard set, question, mock paper, and study plan is logged and browsable from the Profile page |
| **Mock Paper** | Exam-style short and long questions generated from a note |
| **Light / Dark Mode** | System-aware theme with manual toggle |

---

## 3. Tech Stack

**Frontend:** React 18, Vite, React Router, Axios, Tailwind CSS, React Icons, react-hot-toast
**Backend:** FastAPI, Pydantic v2, SQLAlchemy 2.0, SQLite, PyMuPDF, python-docx, Groq API, Uvicorn
**Auth:** JWT (python-jose) + bcrypt password hashing (passlib)

---

## 4. Architecture

```
                        ┌─────────────────────────┐
                        │        Frontend          │
                        │  React + Vite + Tailwind │
                        │       (Vercel)            │
                        └────────────┬─────────────┘
                                     │ REST (JWT Bearer)
                                     ▼
                        ┌─────────────────────────┐
                        │        Backend            │
                        │  FastAPI (HF Spaces/Docker)│
                        │  ┌─────────────────────┐  │
                        │  │ routers/             │  │
                        │  │  auth, subjects,      │  │
                        │  │  notes, ai, planner,  │  │
                        │  │  history, dashboard   │  │
                        │  └─────────┬─────────────┘  │
                        │            │                │
                        │  ┌─────────▼───────────┐    │
                        │  │ services/             │   │
                        │  │  file_extraction.py   │   │
                        │  │  ai_service.py (Groq)  │   │
                        │  └─────────┬───────────┘    │
                        │            │                │
                        │  ┌─────────▼───────────┐    │
                        │  │ SQLAlchemy models →   │   │
                        │  │  SQLite (study_assistant.db) │
                        │  └───────────────────────┘   │
                        └────────────┬─────────────┘
                                     │
                                     ▼
                        ┌─────────────────────────┐
                        │      Groq LLM API         │
                        │   (LLaMA 3.3 70B Versatile)│
                        └─────────────────────────┘
```

**Folder Structure**

```
ai-study-assistant/
├── backend/
│   ├── app/
│   │   ├── core/          # config, database, security, deps
│   │   ├── models/        # SQLAlchemy models
│   │   ├── schemas/       # Pydantic request/response schemas
│   │   ├── services/      # file extraction + Groq AI integration
│   │   ├── routers/       # auth, subjects, notes, ai, planner, history, dashboard
│   │   └── main.py        # FastAPI app entrypoint
│   ├── uploads/           # (runtime) uploaded file storage
│   ├── requirements.txt
│   ├── Dockerfile         # HuggingFace Spaces deployment
│   ├── .env.example
│   └── README.md          # HF Spaces metadata
│
└── frontend/
    ├── src/
    │   ├── components/    # Sidebar, Topbar, ProtectedRoute, Common (Modal/Spinner/EmptyState)
    │   ├── context/        # AuthContext, ThemeContext
    │   ├── layouts/        # AppLayout
    │   ├── pages/           # Landing, Login, Register, Dashboard, Subjects,
    │   │                    # UploadNote, NoteDetail, AIChat, Quiz, Flashcards,
    │   │                    # StudyPlanner, Profile, NotFound
    │   └── services/        # api.js + one service module per domain
    ├── index.html
    ├── vite.config.js
    ├── tailwind.config.js
    ├── vercel.json          # SPA rewrite rule
    └── .env.example
```

---

## 5. Installation (Local Development)

### Prerequisites
- Python 3.11+
- Node.js 18+
- A [Groq API key](https://console.groq.com) (free tier available)

### Backend

```bash
cd backend
python -m venv venv
source venv/bin/activate        # Windows: venv\Scripts\activate
pip install -r requirements.txt

cp .env.example .env
# edit .env: set SECRET_KEY and GROQ_API_KEY

uvicorn app.main:app --reload --port 8000
```

Backend runs at `http://localhost:8000`, interactive docs at `http://localhost:8000/docs`.

### Frontend

```bash
cd frontend
npm install

cp .env.example .env
# edit .env: set VITE_API_URL=http://localhost:8000

npm run dev
```

Frontend runs at `http://localhost:5173`.

---

## 6. Environment Variables

**Backend (`backend/.env`)**

| Variable | Description |
|---|---|
| `SECRET_KEY` | Long random string used to sign JWTs |
| `ALGORITHM` | JWT algorithm (default `HS256`) |
| `ACCESS_TOKEN_EXPIRE_MINUTES` | Token lifetime in minutes |
| `DATABASE_URL` | SQLAlchemy DB URL (default local SQLite file) |
| `GROQ_API_KEY` | Your Groq API key |
| `GROQ_MODEL` | Groq model name (default `llama-3.3-70b-versatile`) |
| `CORS_ORIGINS` | Comma-separated list of allowed frontend origins |
| `MAX_UPLOAD_SIZE_MB` | Max upload file size in MB |
| `UPLOAD_DIR` | Directory for stored uploads |

**Frontend (`frontend/.env`)**

| Variable | Description |
|---|---|
| `VITE_API_URL` | Base URL of the deployed/running backend |

---

## 7. Deployment

### Backend → HuggingFace Spaces
1. Create a new Space → SDK: **Docker**.
2. Push the contents of `backend/` to the Space repo (the `Dockerfile` and the
   YAML metadata block at the top of `backend/README.md` are already configured
   for port `7860`).
3. In the Space's **Settings → Repository secrets**, add `SECRET_KEY` and
   `GROQ_API_KEY` (and any other values from `.env.example` you want to override).
4. The Space will build and expose the API at
   `https://<your-username>-<space-name>.hf.space`.

### Frontend → Vercel
1. Import the `frontend/` folder as a new Vercel project (framework preset: Vite).
2. Set the environment variable `VITE_API_URL` to your deployed HF Spaces URL.
3. Deploy. `vercel.json` already includes the SPA rewrite rule so client-side
   routing works on refresh/direct links.
4. Once deployed, update `CORS_ORIGINS` in the backend's `.env`/Space secrets to
   include your Vercel URL, then restart the Space.

---

## 8. AI Prompts (Design Notes)

Every AI feature calls Groq's chat completions endpoint with
`response_format: {"type": "json_object"}` and a strict system prompt so
responses are always machine-parseable. Key prompting decisions:

- **Ask AI** is explicitly instructed to answer *only* from the note text and to
  return a `grounded: boolean` flag; the backend enforces the exact fallback
  string `"This topic is not covered in the uploaded notes."` whenever
  `grounded` is false, regardless of what the model returns.
- **Quiz / Flashcards / Mock Paper** prompts request an exact item count and the
  backend truncates/validates the response so the UI never renders malformed data.
- **Explain Simply** prompts for a 12-year-old reading level with an analogy.
- **Study Planner** is given a condensed digest of all notes in the subject (not
  raw text) plus the number of days until the exam, and is asked to sequence
  foundational topics earlier and revision/practice closer to the exam date.
- Note text sent to the model is capped (~12,000 characters) to stay within
  context limits on very long documents; the backend truncates and flags this
  in the prompt.

---

## 9. Security Notes

- Passwords are hashed with bcrypt before storage — never stored or logged in plaintext.
- All non-auth endpoints require a valid JWT (`Authorization: Bearer <token>`), verified via `app/core/deps.py`.
- File uploads are restricted to `.pdf`, `.txt`, `.docx` and capped by `MAX_UPLOAD_SIZE_MB`.
- `GROQ_API_KEY` and `SECRET_KEY` are read only from environment variables and are never exposed to the frontend.
- CORS is restricted to the origins listed in `CORS_ORIGINS`.

---

## 10. Screenshots

_Add screenshots of the Dashboard, Note Detail (Summary tab), Quiz, and Flashcards
views here after your first local run — e.g. `docs/screenshots/dashboard.png`._

---

## 11. Future Improvements

- Real-time collaborative note sharing between classmates
- Spaced-repetition scheduling for flashcards (SM-2 algorithm)
- Export quizzes / mock papers to PDF
- OCR support for scanned/handwritten PDF notes
- Streaming AI responses (token-by-token) in the Ask AI chat
- Multi-note context for Ask AI (query across an entire subject, not just one note)
- Usage analytics per subject (time spent, quiz accuracy trends over time)

---

## License
This project was built as a university submission. All rights reserved by the author.
