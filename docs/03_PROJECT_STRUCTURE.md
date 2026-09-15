# 03. Project Structure & Directory Guide

This document gives you a complete tour of the repository's file system, explaining the purpose of every directory and key file.

---

## 🗂️ High-Level Directory Tree

```
meowmeow90/
├── actions/             # Next.js Server Actions (Backend functions callable from UI)
│   ├── ats-actions.ts        # Handles triggering ATS score calculations
│   ├── interview-actions.ts  # Handles creating & saving interview sessions
│   └── resume-actions.ts     # Handles resume PDF upload & text parsing
├── app/                 # Next.js App Router (Pages, Routes, Layouts)
│   ├── (marketing)/          # Landing / homepage route
│   │   └── page.tsx          # Public marketing page
│   ├── dashboard/            # Protected engineering workbench
│   │   └── page.tsx          # Main dashboard (ATS, Keywords, Gaps, Terminal)
│   ├── api/                  # REST API Route Handlers (Upload, Analyze, Interview)
│   ├── globals.css           # Global Tailwind CSS styles and theme variables
│   ├── layout.tsx            # Root HTML layout with Clerk Provider & header
│   └── error.tsx / not-found # Friendly fallback UI for errors and 404s
├── components/          # Reusable React UI Components
│   ├── AtsScoreCard.tsx      # Main ATS Score radial meter & sub-score bars
│   ├── DiagnosticSummary.tsx # Executive summary briefing & core strengths
│   ├── InterviewTerminal.tsx # Voice rehearsal terminal with WPM & speech-to-text
│   ├── InterviewQuestions.tsx# Question cards with rubric accordions
│   ├── JobDescriptionInput.tsx# Target job description textarea & presets
│   ├── KeywordTaxonomy.tsx   # Categorized matched/missing skills (Cloud, DBs, etc.)
│   ├── KeywordList.tsx       # Searchable keyword pill badges
│   ├── ResumeUploader.tsx    # Drag-and-drop PDF dropzone + 1-Click Sample Resume
│   ├── SkillGapList.tsx      # High-priority missing technical proficiencies
│   ├── TerminalLoader.tsx    # Animated hacker/terminal style loading screen
│   ├── react-bits/           # Special visual effects (Bento grids, particle text)
│   └── ui/                   # Base atomic UI elements (Buttons, Tabs, Dialogs)
├── docs/                # 📚 Beginner-Friendly Documentation Guides
├── lib/                 # Backend Utilities, AI Engine & Helpers
│   ├── ai/                   # NVIDIA Nemotron LLM prompts & client setup
│   ├── auth/                 # Clerk user authentication helpers
│   ├── db/                   # LocalDB disk persistence & Prisma bridge
│   ├── storage/              # PDF extraction & file storage handlers
│   ├── validation/           # Zod schemas for deterministic JSON validation
│   ├── sound.ts              # Web Audio API sound generator
│   ├── types.ts              # Core TypeScript interface definitions
│   └── utils.ts              # Utility helpers (cn class merger)
├── prisma/              # Database Schema & Migration files
│   └── schema.prisma         # Data models (User, Resume, InterviewSession)
├── public/              # Static files (Images, sample PDFs, icons)
│   └── uploads/resumes/      # Local directory where uploaded PDFs are saved
├── .env.local           # Environment variables (Clerk keys, NVIDIA API key)
├── middleware.ts        # Clerk route protection middleware
├── package.json         # Project dependencies and script commands
├── tailwind.config.ts   # Tailwind CSS theme customization
└── tsconfig.json        # TypeScript compiler configuration
```

---

## 🔍 Detailed Breakdown of Key Folders

### 1. `actions/` (Server Actions)
Next.js Server Actions are asynchronous functions that execute exclusively on the server. They can be imported and called directly from React components without needing to manually write `fetch('/api/...')` requests.
* **`resume-actions.ts`**: Receives the uploaded resume `FormData`, validates size and MIME type, saves the file, extracts plain text via `pdf-parse`, and returns the extracted content.
* **`ats-actions.ts`**: Takes the resume text and job description, calls the NVIDIA Nemotron AI engine, validates the output with Zod, and saves the diagnostic result to the database.
* **`interview-actions.ts`**: Generates or retrieves 5 tailored technical interview questions with scoring rubrics and saves candidate practice responses.

---

### 2. `app/` (Application Routes & Pages)
This project uses the modern **Next.js App Router**:
* **`app/layout.tsx`**: The master wrapper for all pages. It includes the `<ClerkProvider>` for authentication, global fonts, the top navigation header, and audio feedback controls.
* **`app/(marketing)/page.tsx`**: The public landing page with product features, hero animations, and "Get Started" buttons.
* **`app/dashboard/page.tsx`**: The core application workbench. It coordinates the 4 main tabs:
  1. *ATS Diagnostic Summary*
  2. *Keyword Taxonomy & Match Coverage*
  3. *Skill Gaps & Weakness Analysis*
  4. *Technical Interview Rehearsal Terminal*

---

### 3. `components/` (Interactive UI Layer)
* **`ResumeUploader.tsx`**: Features a drag-and-drop zone with instant PDF validation and a 1-click **Sample Resume** button (pre-loaded with a Senior Full-Stack Engineer profile for quick testing).
* **`AtsScoreCard.tsx`**: Renders an animated circular score gauge (0–100) and displays sub-score bars for Keyword Match Coverage, Experience Scope, and Technical Depth.
* **`KeywordTaxonomy.tsx`**: Organizes skills into 5 distinct engineering categories: Languages & Runtimes, Frameworks, Cloud & DevOps, Databases & Distributed Systems, and Architecture.
* **`InterviewTerminal.tsx`**: A full-featured interview simulator that connects to your browser's microphone via the **Web Speech API**, records your answers, calculates your speaking pace (Words Per Minute), and compares your answer against senior engineering rubrics.

---

### 4. `lib/` (Core Logic & Infrastructure)
* **`lib/ai/nemotron.ts`**: Configures the connection to NVIDIA's AI endpoints with temperature, token limits, and strict system prompts.
* **`lib/db/`**: Handles database reads/writes to `.data/db.json` so the app works 100% offline or locally without needing cloud databases.
* **`lib/sound.ts`**: Implements browser-native sound effects using the Web Audio API without needing external sound files.
* **`lib/validation/ats-schema.ts`**: Declares Zod schemas to guarantee that the AI's response always contains every required score, keyword, and question format.
