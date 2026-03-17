# StudyAI – Advanced AI Study Assistant

## Architecture Overview

A full-stack web app that helps students generate AI-powered study materials from PDFs or text. Students select their level, upload content, and receive structured study guides with quizzes.

## Tech Stack

- **Frontend**: React + TypeScript, Vite, TailwindCSS, shadcn/ui, Framer Motion, Wouter
- **Backend**: Express.js (TypeScript), Drizzle ORM
- **Database**: PostgreSQL (Replit managed)
- **AI**: Replit AI Integrations (OpenAI, `gpt-5.2` model, no user API key needed)
- **PDF Parsing**: `pdfjs-dist` (client-side)

## User Flow

1. **Welcome** (`/`) — Level selection: School (grades 6-8, 9-10, 11-12), College, Competitive Exam
2. **Dashboard** (`/dashboard`) — Library of saved study materials, stats overview
3. **Upload** (`/upload`) — Drag-and-drop PDF (max 1MB) or type/paste text; configure preferences
4. **Results** (`/results/:id`) — Tabbed study guide: Summary, Key Points, Concepts, Formulas, Definitions, Concept Simplifier, Q&A, Quiz Mode

## Key Files

- `shared/schema.ts` — Drizzle ORM schema (`documents`, `studyMaterials`) + Zod validators
- `shared/routes.ts` — API contract types
- `server/routes.ts` — Express API handlers; OpenAI integration with level-aware prompts
- `server/storage.ts` — `DatabaseStorage` class with CRUD operations
- `client/src/App.tsx` — Routing (Wouter): Welcome → Dashboard → Upload → Results
- `client/src/pages/Welcome.tsx` — Onboarding with level + grade selection
- `client/src/pages/Dashboard.tsx` — Study library with stats and topic cards
- `client/src/pages/Upload.tsx` — PDF upload, preferences form, generation trigger
- `client/src/pages/Results.tsx` — Fetches and renders study material by ID
- `client/src/components/workspace/ResultViewer.tsx` — Tab-based content display
- `client/src/components/workspace/QuizMode.tsx` — Interactive quiz (MCQ, Short Answer, True/False, Fill in blanks)
- `client/src/hooks/use-study.ts` — TanStack Query hooks for API interaction
- `client/src/index.css` — CSS variables (Inter + Lora fonts, blue/violet academic palette)

## Database Schema

```
documents: id, title, content, createdAt
studyMaterials: id, documentId, title, userLevel, preferences (jsonb), result (jsonb), createdAt
```

## API Endpoints

- `POST /api/documents` — Create a document
- `GET /api/documents/:id` — Get a document
- `POST /api/study-materials` — Generate AI study materials
- `GET /api/study-materials` — List all study materials
- `GET /api/study-materials/:id` — Get a specific study material
- `DELETE /api/study-materials/:id` — Delete a study material

## AI Prompt Design

The system prompt adapts to the user's level (e.g., school 6-8 uses simple language, competitive exam focuses on exam-relevant facts). Output is strict JSON with: `summary`, `key_concepts`, `important_points`, `definitions`, `formulas`, `concept_simplified`, `questions`.

## User Preferences

- **Summary length**: short / medium / detailed
- **Language**: English / Hindi / Hinglish
- **Question type**: MCQ / Short Answer / Long Answer / Fill in the blanks / True-False / Mixed
- **Number of questions**: 5 / 10 / 20 / 30
- **Difficulty**: easy / medium / hard
- **Toggles**: quiz_mode, concept_simplifier, extract_formulas, extract_definitions

## User Level Storage

Stored in `localStorage` as `studyLevel` (e.g., `"school_9_10"`, `"college"`, `"competitive"`)
