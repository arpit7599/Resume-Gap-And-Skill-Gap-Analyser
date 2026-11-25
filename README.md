<!-- Project Badges -->

# ResumePilot - AI Resume Intelligence Platform

![React](https://img.shields.io/badge/React-19-087ea4?logo=react&logoColor=white)
![Vite](https://img.shields.io/badge/Vite-7-646cff?logo=vite&logoColor=white)
![Node.js](https://img.shields.io/badge/Node.js-20-339933?logo=nodedotjs&logoColor=white)
![Express](https://img.shields.io/badge/Express-5-000000?logo=express&logoColor=white)
![MongoDB](https://img.shields.io/badge/MongoDB-Mongoose-47a248?logo=mongodb&logoColor=white)
![OpenAI](https://img.shields.io/badge/OpenAI-GPT--Powered-412991)
![License](https://img.shieldsields.io/badge/License-MIT-blue)

ResumePilot is an end-to-end, full-stack career-readiness platform that helps candidates transform a resume into an application strategy. It combines PDF parsing, AI-powered information extraction, role-based skill matching, ATS-style scoring, claim verification, interview simulation, and personalized project roadmaps.

Unlike a standalone resume checker, ResumePilot follows the complete candidate workflow:

```text
Resume Upload -> Text Extraction -> Skill Analysis -> Gap Detection
     -> Application Readiness -> Claim Verification -> Interview Prep
     -> Project Roadmap -> Resume Improvement
```

---

## Table of Contents

- [Product Highlights](#core-features)
- [System Architecture](#system-architecture)
- [Frontend Architecture](#frontend-explanation)
- [Backend Architecture](#backend-explanation)
- [API Documentation](#main-api-routes)
- [Local Development](#local-setup)
- [Environment Variables](#environment-variables)
- [Testing](#testing-and-quality)
- [Deployment](#deployment)
- [Screenshots](#product-screenshots)

> **Demo mode:** ResumePilot runs with deterministic fallback analysis when OpenAI or MongoDB is not configured. This makes core skill matching and scoring available even in a local or restricted environment.

## Product Screenshots

### Landing Page

![ResumePilot landing page](frontend/docs/screenshots/landing-page.png)

The landing page presents ResumePilot as an interview-focused resume optimization tool. It highlights the core promise: more interviews through a better resume. The hero section communicates the main value clearly: real-time ATS scores, skill matching, and AI recommendations. The workflow strip below the hero previews the major product modules, including Role Match, Missing Skills, Interview Simulation, and Project Plan.

### Analyzer Dashboard

![ResumePilot analyzer dashboard](frontend/docs/screenshots/analyzer-dashboard.png)

The analyzer dashboard is the command center for the product. Users select a target domain such as Frontend Developer, Backend Developer, Data Analyst, or Full Stack Developer, then upload a PDF resume for analysis. The left sidebar shows the major analysis modules: Missing Skills, ATS Checker, ICM Score, Role Match, Application Readiness, Interview Loop, and Roadmap. The top metrics show resume score, top missing skill, and analysis time after a scan is completed.

## Core Features

### Resume Upload and AI Analysis

Users upload a PDF resume and select a target role/domain. The backend extracts text from the PDF, sends the resume content to the AI analysis service when an OpenAI key is available, and falls back to deterministic skill extraction when AI is not configured. The response includes extracted skills, matched skills, missing skills, a resume score, and improvement suggestions.

### Missing Skills Detection

The Missing Skills module compares the candidate's extracted resume skills with the selected role requirements. For example, a Backend Developer profile is checked against skills such as Node, Express, MongoDB, SQL, and System Design. The result tells users exactly which important skills are already present and which ones should be added through projects, experience bullets, or certifications.

### ATS Checker

The ATS Checker helps estimate how cleanly the resume can be understood by applicant tracking systems. It focuses on keyword alignment, section clarity, role relevance, and resume structure. The dashboard turns this into a practical checklist so users can improve the resume before submitting applications.

### ICM Weighted Score

ICM scoring combines multiple hiring signals into one weighted score:

- Skill match: 40%
- Experience strength: 25%
- Project depth: 20%
- ATS keyword alignment: 15%

This gives users a more balanced readiness score than a simple keyword count.

### Role Match

Role Match compares the resume against a selected job role or job description context. It helps users understand whether their resume is positioned for Frontend Developer, Backend Developer, Data Analyst, or Full Stack Developer paths. It also highlights role-specific missing skills and alignment gaps.

### Resume Claim Verification

ResumePilot can generate a skill-claim test from the uploaded resume. The system creates questions based on claimed skills, evaluates the user's answers, and produces an authenticity score. This helps check whether resume claims are backed by practical knowledge.

### Interview Loop

The Interview Loop creates company and role-specific interview simulations. It includes technical basics, applied scenarios, and debugging/decision questions. After the user answers, the system returns round-level scores and a readiness recommendation.

### Application Readiness

Application Readiness brings together resume score, missing skills, ATS health, role alignment, and interview signal. It gives users a final view of whether they are ready to apply or should improve specific weak areas first.

### Roadmap Builder

The Roadmap Builder turns missing skills into an action plan. It can generate a project blueprint with milestones, deliverables, and resume bullet ideas so the user knows how to close gaps in a practical, portfolio-friendly way.

## System Architecture

```mermaid
flowchart LR
  User["User"] --> Browser["React + Vite Frontend"]
  Browser --> API["Express API"]
  API --> Upload["Multer PDF Upload"]
  Upload --> Parser["PDF Text Parser"]
  Parser --> AI["OpenAI Analysis Service"]
  Parser --> Fallback["Fallback Skill Extractor"]
  AI --> SkillMatch["Skill Matcher + Scoring"]
  Fallback --> SkillMatch
  SkillMatch --> MongoDB["MongoDB Models"]
  SkillMatch --> Browser
```

## Product Flow

```mermaid
sequenceDiagram
  participant U as User
  participant F as Frontend
  participant B as Backend API
  participant P as PDF Parser
  participant A as AI/Fallback Analyzer
  participant S as Scoring Engine

  U->>F: Select role and upload resume PDF
  F->>B: POST /api/analyze-resume
  B->>P: Extract resume text
  P->>A: Analyze skills and suggestions
  A->>S: Compare against role requirements
  S->>B: Score, matched skills, missing skills
  B->>F: Return analysis result
  F->>U: Show ATS, missing skills, role match, readiness
```

## Tech Stack

| Layer | Technology |
| --- | --- |
| Frontend | React 19, Vite, React Router, Tailwind CSS, Framer Motion |
| UI/Visuals | Lucide React, GSAP, Lenis, Three.js / React Three Fiber |
| Backend | Node.js, Express 5 |
| Uploads | Multer |
| PDF Parsing | pdf-parse |
| AI | OpenAI Responses API with fallback extraction |
| Database | MongoDB with Mongoose |
| Deployment | Vercel frontend, Render backend |

## Frontend Explanation

The frontend lives in `frontend/` and is responsible for the complete user experience: landing page, resume upload, dashboard navigation, analysis views, theme switching, export actions, and the protected workspace flow after a resume is analyzed.

### Frontend Responsibilities

- Render the public landing page with product positioning and calls to action.
- Let users choose a target domain before uploading a resume.
- Upload PDF resumes to the backend through `FormData`.
- Store analysis results in browser `localStorage` so workspace pages can reuse the latest scan.
- Show ATS, missing skills, ICM score, role match, readiness, interview loop, and roadmap pages.
- Call backend APIs for resume analysis, claim verification, interview simulation, and project-plan generation.
- Provide a responsive dashboard layout with sidebar navigation and light/dark theme support.

### Important Frontend Files

| File | Purpose |
| --- | --- |
| `frontend/src/App.jsx` | Main routed application. Contains landing page, analyzer page, protected workspace pages, theme state, local analysis state, and route definitions. |
| `frontend/src/services/api.js` | API client for backend calls. Handles API base URL selection, local fallback, request timeouts, and JSON error handling. |
| `frontend/src/lib/scoring.js` | Frontend scoring helpers for weighted ICM scoring and analysis-duration formatting. |
| `frontend/src/components/PremiumLanding.jsx` | Alternative animated landing component using GSAP, Lenis, Three.js, and React Three Fiber. |
| `frontend/src/components/ui/button.jsx` | Shared button primitive used across the interface. |
| `frontend/src/index.css` | Global styles and Tailwind-driven visual system. |
| `frontend/docs/screenshots/` | Product screenshots used by this README. |

### Frontend Pages and Views

| View | What It Does |
| --- | --- |
| Landing Page | Explains the product promise and routes users into the analyzer flow. |
| Analyze Page | Lets users select a domain, upload a PDF, run analysis, and view top-level metrics. |
| Missing Skills | Shows role-specific skill gaps and suggested next steps. |
| ATS Checker | Displays ATS-readiness checks around keywords, structure, and readability. |
| ICM Score | Combines skill match, experience, project depth, and ATS keyword alignment into a weighted score. |
| Role Match | Compares the resume against role expectations or job-description context. |
| Application Readiness | Summarizes whether the candidate is ready to apply based on resume and interview signals. |
| Interview Loop | Uses backend interview simulation APIs to test role readiness. |
| Roadmap Builder | Builds a practical improvement roadmap from missing skills and selected target role. |

### Frontend State Flow

```mermaid
flowchart TD
  Start["Landing Page"] --> Analyze["Analyze Page"]
  Analyze --> Domain["Select Target Domain"]
  Domain --> Upload["Upload Resume PDF"]
  Upload --> ApiClient["frontend/src/services/api.js"]
  ApiClient --> Backend["Express Backend"]
  Backend --> Result["Analysis Result"]
  Result --> LocalStorage["localStorage"]
  LocalStorage --> Workspace["Protected Workspace Routes"]
  Workspace --> Missing["Missing Skills"]
  Workspace --> ATS["ATS Checker"]
  Workspace --> ICM["ICM Score"]
  Workspace --> Role["Role Match"]
  Workspace --> Ready["Application Readiness"]
  Workspace --> Interview["Interview Loop"]
  Workspace --> Roadmap["Roadmap Builder"]
```

## Backend Explanation

The backend lives in `backend/` and provides the resume analysis API. It accepts PDF uploads, extracts resume text, performs AI or fallback skill extraction, computes skill matches and scores, optionally stores results in MongoDB, and powers advanced workflows such as claim verification, interview simulation, and project roadmap generation.

### Backend Responsibilities

- Start an Express server and expose `/health` plus `/api/*` routes.
- Accept PDF uploads through Multer memory storage.
- Extract text from uploaded resumes with `pdf-parse`.
- Analyze resume text with OpenAI when `OPENAI_API_KEY` is available.
- Use fallback keyword extraction when AI is unavailable.
- Compare extracted skills with role-required skills.
- Calculate resume score from matched vs required skills.
- Persist resume and analysis results when MongoDB is connected.
- Generate skill-claim tests, evaluate answers, and create company shortlists.
- Generate interview simulations and evaluate interview readiness.
- Generate project blueprints that help users close missing-skill gaps.

### Important Backend Files

| File | Purpose |
| --- | --- |
| `backend/server.js` | Express app entry point. Loads environment variables, connects MongoDB, mounts routes, and starts the server. |
| `backend/routes/resumeRoutes.js` | Defines all resume, assessment, interview, and project-plan API routes. |
| `backend/controllers/resumeController.js` | Main request handlers for analysis, LinkedIn-like parsing, claim tests, interview simulations, and project plans. |
| `backend/middleware/uploadMiddleware.js` | Multer configuration for accepting uploaded resume PDFs. |
| `backend/services/resumeParser.js` | Extracts text from uploaded PDF buffers. |
| `backend/services/openaiService.js` | Runs AI resume analysis and project blueprint generation, with fallback behavior. |
| `backend/services/skillMatcher.js` | Compares extracted resume skills against required role skills. |
| `backend/services/assessmentService.js` | Creates claim verification tests, company shortlists, and interview simulations. |
| `backend/services/linkedinParser.js` | Parses LinkedIn-like profile text for profile analysis. |
| `backend/utils/scoringUtils.js` | Calculates score from matched and required skills. |
| `backend/config/db.js` | Connects to MongoDB using `MONGO_URI`. |
| `backend/config/openai.js` | Configures the OpenAI client. |

### Backend Models

| Model | Stored Data |
| --- | --- |
| `Resume` | Original extracted resume text, extracted skills, and upload timestamp. |
| `AnalysisResult` | Resume score, matched skills, missing skills, suggestions, and linked resume ID. |
| `CandidateAssessment` | Claim verification test ID, claimed skills, authenticity score, claim status, and shortlist results. |

### Backend Analysis Pipeline

```mermaid
flowchart TD
  Request["POST /api/analyze-resume"] --> Multer["Multer receives PDF"]
  Multer --> Parser["resumeParser extracts text"]
  Parser --> OpenAI{"OpenAI configured?"}
  OpenAI -->|Yes| AI["AI extracts skills and suggestions"]
  OpenAI -->|No| Fallback["Fallback keyword extractor"]
  AI --> Match["skillMatcher compares required skills"]
  Fallback --> Match
  Match --> Score["scoringUtils calculates score"]
  Score --> Persist{"MongoDB connected?"}
  Persist -->|Yes| Save["Save Resume + AnalysisResult"]
  Persist -->|No| Skip["Skip persistence"]
  Save --> Response["Return score, skills, gaps, suggestions"]
  Skip --> Response
```

### Frontend and Backend Contract

The frontend communicates with the backend through `frontend/src/services/api.js`. `VITE_API_BASE_URL` is used when provided, and the frontend falls back to `http://localhost:5000/api` for local development. Resume uploads use `multipart/form-data`, while interview, claim-test submission, and roadmap generation use JSON requests.

```mermaid
flowchart LR
  Frontend["React Frontend"] --> ApiClient["API Client"]
  ApiClient --> Analyze["POST /api/analyze-resume"]
  ApiClient --> ClaimStart["POST /api/generate-claim-test"]
  ApiClient --> ClaimSubmit["POST /api/submit-claim-test"]
  ApiClient --> InterviewStart["POST /api/start-interview-sim"]
  ApiClient --> InterviewSubmit["POST /api/submit-interview-sim"]
  ApiClient --> ProjectPlan["POST /api/generate-project-plan"]
  Analyze --> Backend["Express Controllers + Services"]
  ClaimStart --> Backend
  ClaimSubmit --> Backend
  InterviewStart --> Backend
  InterviewSubmit --> Backend
  ProjectPlan --> Backend
```

## Main API Routes

| Method | Route | Purpose |
| --- | --- | --- |
| `GET` | `/health` | Backend health check |
| `POST` | `/api/analyze-resume` | Upload PDF and return score, skills, gaps, and suggestions |
| `POST` | `/api/parse-linkedin-profile` | Analyze LinkedIn-like profile text |
| `POST` | `/api/generate-claim-test` | Generate a skill verification test from resume claims |
| `POST` | `/api/submit-claim-test` | Evaluate skill verification answers |
| `POST` | `/api/start-interview-sim` | Start a role/company interview simulation |
| `POST` | `/api/submit-interview-sim` | Evaluate interview simulation answers |
| `POST` | `/api/generate-project-plan` | Generate a gap-closing project roadmap |

## Project Structure

```text
ai-resume-analyzer/
  backend/
    config/              # OpenAI and MongoDB configuration
    controllers/         # API request handlers
    middleware/          # File upload middleware
    models/              # MongoDB schemas
    routes/              # Express routes
    services/            # AI analysis, parsing, skill matching, assessments
    utils/               # Scoring helpers
  frontend/
    docs/screenshots/    # README screenshots
    src/
      components/        # UI components
      lib/               # Frontend scoring helpers
      services/          # API client
      App.jsx            # Main routed application
  DEPLOY_RENDER.md       # Render + Vercel deployment guide
  render.yaml            # Render backend blueprint
```

## Local Setup

### Backend

```bash
cd backend
npm install
```

Create `backend/.env`:

```env
PORT=5000
MONGO_URI=your_mongodb_connection_string
OPENAI_API_KEY=your_openai_api_key
```

Start the backend:

```bash
npm run dev
```

If `MONGO_URI` is missing, the backend starts without database persistence. If `OPENAI_API_KEY` is missing or the AI request fails, ResumePilot uses fallback skill extraction.

### Frontend

```bash
cd frontend
npm install
```

Create `frontend/.env` if the backend is not running on the default local URL:

```env
VITE_API_BASE_URL=http://localhost:5000/api
```

Start the frontend:

```bash
npm run dev
```

## Environment Variables

| Variable | Scope | Required | Description |
| --- | --- | --- | --- |
| `PORT` | Backend | No | Express server port. Defaults to `5000`. |
| `MONGO_URI` | Backend | Recommended | MongoDB connection string for resume and assessment persistence. |
| `OPENAI_API_KEY` | Backend | Optional | Enables AI-powered resume analysis and project-plan generation. |
| `VITE_API_BASE_URL` | Frontend | Optional | Backend API URL, ending in `/api`. Defaults to `/api`. |

Example production values:

```env
# Backend
PORT=5000
MONGO_URI=mongodb+srv://<user>:<password>@<cluster>/<database>
OPENAI_API_KEY=sk-your-key

# Frontend
VITE_API_BASE_URL=https://your-api.onrender.com/api
```

Never commit `.env` files or API keys to source control.

## API Examples

### Analyze a resume

```bash
curl -X POST "http://localhost:5000/api/analyze-resume" \
  -F "resume=@/path/to/resume.pdf" \
  -F "requiredSkills=JavaScript,React,Node.js,MongoDB"
```

Example response shape:

```json
{
  "success": true,
  "resumeScore": 78,
  "extractedSkills": ["JavaScript", "React", "Node.js"],
  "matchedSkills": ["JavaScript", "React", "Node.js"],
  "missingSkills": ["MongoDB"],
  "suggestions": ["Add quantified MongoDB project outcomes."],
  "analysisTime": 1.42
}
```

### Generate a project plan

```bash
curl -X POST "http://localhost:5000/api/generate-project-plan" \
  -H "Content-Type: application/json" \
  -d '{
    "role": "Backend Developer",
    "missingSkills": ["MongoDB", "System Design"],
    "extractedSkills": ["Node.js", "Express"]
  }'
```

## Testing and Quality

Frontend tests use Node's built-in test runner:

```bash
cd frontend
npm test
npm run lint
npm run build
```

Backend validation:

```bash
cd backend
npm start
curl http://localhost:5000/health
```

For a complete verification pass:

1. Start both services.
2. Upload a valid PDF resume from the dashboard.
3. Confirm the role skills and extracted skills appear in the response.
4. Run claim verification and interview simulation workflows.
5. Verify `MONGO_URI` persistence or the fallback mode behavior.
6. Run a production frontend build before deployment.

## Deployment

The intended deployment setup is:

- Backend on Render using `render.yaml`
- Frontend on Vercel with root directory set to `frontend`
- `VITE_API_BASE_URL` pointing to the Render API URL

Detailed deployment steps are available in `DEPLOY_RENDER.md`.

## Why ResumePilot Matters

Most resume tools only give a generic score. ResumePilot is built around the actual job search workflow: identify missing skills, improve ATS alignment, verify claimed skills, prepare for interviews, and create a roadmap that turns weak areas into portfolio proof.
