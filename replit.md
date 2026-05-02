# Resume Analyzer

AI-powered resume analysis system that scores resumes, matches job roles, and generates actionable feedback.

## Architecture

### Services

| Service | Port | Description |
|---------|------|-------------|
| Java Spring Boot Engine | 8090 (`JAVA_ENGINE_PORT`) | Resume parsing, scoring (0-100), job matching, suggestions |
| Python FastAPI PDF Service | 8091 (`PYTHON_PDF_PORT`) | Extracts text from uploaded PDF files using pdfplumber |
| Node.js API Server | 8080 → `/api` | REST API, DB persistence, AI integration, file upload |
| React Frontend | dynamic | Vite + Tailwind + Shadcn UI |

### Key Paths
- Frontend: `artifacts/resume-analyzer/`
- API Server: `artifacts/api-server/`
- Java Engine: `artifacts/java-resume-engine/`
- Python PDF Service: `artifacts/python-pdf-service/`
- Shared DB lib: `lib/db/`
- OpenAPI spec: `lib/api-spec/openapi.yaml`
- Generated React hooks: `lib/api-client-react/src/generated/`
- Generated Zod schemas: `lib/api-zod/src/generated/`

## API Endpoints

### Core
- `POST /api/resume/analyze` — Analyze resume text (JSON body)
- `POST /api/resume/upload` — Upload PDF/TXT file (multipart form)
- `GET /api/resume/history` — List all analyses
- `GET /api/resume/history/:id` — Get single analysis
- `GET /api/jobs` — List all 18 job roles
- `GET /api/stats/overview` — Dashboard statistics

### AI (OpenAI GPT via Replit integration, model: `gpt-5-nano` for ~17-21s response)
- `GET /api/ai/insights/:id` — ATS score, strengths/weaknesses, actionable insights, LinkedIn optimization
- `POST /api/ai/cover-letter/:id` — Generate personalized cover letter
- `POST /api/ai/interview-prep/:id` — Generate interview questions and tips
- `GET /api/ai/salary/:id` — Estimate salary ranges across regions (US/EU/India/Remote) with negotiation tips
- `GET /api/ai/roadmap/:id` — Generate a personalized 90-day learning roadmap with phased plan and resources

## Frontend Pages
- `/` Dashboard — stats overview with gradient hero, recent analyses
- `/analyze` Upload + analyze form
- `/results/:id` Detailed analysis with AI panels (insights, salary, roadmap, cover letter, interview prep) + JSON export
- `/compare` Side-by-side resume comparison with bar chart
- `/jobs` Job role library
- `/history` All past analyses

## Theme
- App is dark-mode by default (set in `main.tsx` via `document.documentElement.classList.add('dark')`)
- Premium dark palette with violet/blue gradients, glow effects, and glassmorphism
- Helper classes: `.gradient-text`, `.gradient-border`, `.card-hover`, `.glow-primary`, `.animate-glow` (defined in `index.css`)

## Database
PostgreSQL via Drizzle ORM. Schema: `lib/db/src/schema.ts`
- `resume_analyses` table — stores all analysis results

## Java Engine
- 18 predefined job roles with skill requirements
- Keyword-based resume parsing (no NLP)
- Scoring: Completeness (30) + Skills Richness (25) + Experience Depth (25) + Structure (20) = 100
- Pre-built JAR: `artifacts/java-resume-engine/target/resume-engine-1.0.0.jar`
- To rebuild: `cd artifacts/java-resume-engine && mvn package -DskipTests`

## Codegen
Run after modifying `lib/api-spec/openapi.yaml`:
```
pnpm --filter @workspace/api-spec run codegen
```

## Environment Variables / Secrets
- `DATABASE_URL` — PostgreSQL connection
- `SESSION_SECRET` — Express session secret
- `AI_INTEGRATIONS_OPENAI_BASE_URL` — OpenAI proxy base URL (Replit integration)
- `AI_INTEGRATIONS_OPENAI_API_KEY` — OpenAI API key (Replit integration)
- `JAVA_ENGINE_PORT` — Java engine port (set to 8090 in workflow)
- `PYTHON_PDF_PORT` — Python PDF service port (set to 8091 in workflow)
