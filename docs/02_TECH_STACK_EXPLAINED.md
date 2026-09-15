# 02. Tech Stack Explained (What, Why, and How)

Every technology used in this project was selected for a specific engineering reason. Below is a complete breakdown of every tool, library, and framework with its **What**, **Why**, and **How**.

---

## 1. Core Framework & Language

### 🌐 Next.js (App Router)
* **WHAT is it?** A modern React framework for building fast, full-stack web applications with server-side rendering and built-in API routing.
* **WHY do we use it?** Next.js eliminates the need for a separate backend server (like Express). It allows us to run secure server-side operations (like PDF parsing and AI API keys) directly in Server Actions and Route Handlers.
* **HOW it is used here:**
  * Uses the **App Router** (`app/dashboard/page.tsx`, `app/(marketing)/page.tsx`).
  * Server Actions (`actions/ats-actions.ts`, `actions/resume-actions.ts`) process resume uploads without exposing API secrets to the browser.

### ⚛️ React 19
* **WHAT is it?** The leading JavaScript UI library for building interactive user interfaces using reusable components and declarative state.
* **WHY do we use it?** Enables dynamic, reactive UI updates (e.g., live score recalculation, tab switching, and streaming interview feedback) without reloading the page.
* **HOW it is used here:**
  * Manages interactive states (e.g., active question index, recording status, audio feedback toggle).

### 📘 TypeScript
* **WHAT is it?** A strongly typed superset of JavaScript that adds type checking at compile time.
* **WHY do we use it?** Prevents common runtime bugs (like `undefined is not a function`), ensures strict data contracts between AI responses and UI components, and provides autocomplete in code editors.
* **HOW it is used here:**
  * Defines interfaces for `AtsResult`, `InterviewSession`, `KeywordTaxonomy`, and API responses (`lib/types.ts`).

---

## 2. Artificial Intelligence & Document Ingestion

### 🧠 NVIDIA Nemotron 3 Super (120B Model)
* **WHAT is it?** A state-of-the-art Large Language Model (LLM) developed by NVIDIA for deep reasoning, structured extraction, and complex analytical tasks.
* **WHY do we use it?** Generic small models fail to give realistic ATS scores and often hallucinate. Nemotron provides senior principal-engineer level evaluations and consistently outputs clean, structured JSON.
* **HOW it is used here:**
  * Connected via NVIDIA's API endpoint (`https://integrate.api.nvidia.com/v1`) using the OpenAI-compatible SDK (`lib/ai/nemotron.ts`).
  * Evaluates resume vs. job description and generates targeted interview questions with rubrics.

### 📄 pdf-parse
* **WHAT is it?** A lightweight server-side Node.js library for extracting text streams from raw PDF binary buffers.
* **WHY do we use it?** Resume PDFs contain complex layouts. `pdf-parse` extracts the clean UTF-8 text representation of the resume on the server in milliseconds.
* **HOW it is used here:**
  * In `actions/resume-actions.ts` & `lib/storage/pdf.ts`, incoming PDF uploads are converted into plain text strings before being fed into the AI prompt.

### 🛡️ Zod
* **WHAT is it?** A TypeScript-first schema declaration and validation library.
* **WHY do we use it?** LLM outputs can sometimes return invalid or unexpected formats. Zod validates the AI's JSON output before it reaches the frontend, guaranteeing zero UI crashes.
* **HOW it is used here:**
  * `lib/validation/ats-schema.ts` defines the exact shape of `atsResponseSchema`. If the AI produces malformed data, Zod catches it instantly.

---

## 3. User Authentication & Database

### 🔐 Clerk (`@clerk/nextjs`)
* **WHAT is it?** A complete user management and authentication platform providing pre-built login, sign-up, user profiles, and session security.
* **WHY do we use it?** Building custom authentication (passwords, cookies, OAuth, 2FA) is complex and prone to security vulnerabilities. Clerk handles this in minutes with top-tier security.
* **HOW it is used here:**
  * `middleware.ts` protects dashboard routes.
  * `<UserButton />`, `<SignIn />`, and `<SignedIn>` components manage user session state throughout the header and navigation.

### 💾 Prisma ORM & LocalDB Engine
* **WHAT is it?** 
  * **Prisma**: A modern Database Object-Relational Mapper (ORM) that lets you query databases using TypeScript objects.
  * **LocalDB**: A zero-cloud disk-persisted database engine (`.data/db.json`).
* **WHY do we use it?** It allows the project to run 100% locally out-of-the-box without needing to configure remote cloud databases or Docker containers, while maintaining full ORM query compatibility.
* **HOW it is used here:**
  * `prisma/schema.prisma` defines models: `User`, `Resume`, and `InterviewSession`.
  * `lib/db/` manages reading and writing user analyses and interview logs.

---

## 4. UI, Styling & Animation

### 🎨 Tailwind CSS (v4)
* **WHAT is it?** A utility-first CSS framework for rapid UI styling directly in HTML/JSX classes.
* **WHY do we use it?** Eliminates huge CSS files, ensures design consistency, and makes responsive layouts (mobile, tablet, desktop) effortless.
* **HOW it is used here:**
  * Styles modern dark-mode cards, glassmorphic effects (`backdrop-blur-md`), badges, gradients, and grids across all components.

### 🧩 Radix UI Primitives (`@radix-ui/*`)
* **WHAT is it?** An unstyled, accessible component library for complex UI elements like Dialogs, Tabs, Tooltips, Accordions, and Dropdowns.
* **WHY do we use it?** Radix handles all WAI-ARIA accessibility rules, keyboard navigation, and focus traps, leaving visual styling to Tailwind.
* **HOW it is used here:**
  * Powers the tabs (`components/ui/tabs.tsx`), tooltips, and modal dialogs.

### 🎬 Framer Motion & GSAP
* **WHAT is it?** Industry-standard animation libraries for React.
* **WHY do we use it?** Smooth animations make the dashboard feel like a high-end engineering workbench rather than a static form.
* **HOW it is used here:**
  * Animated score counters, smooth tab transitions, pulsating recording indicators, and card entrances.

### 💎 Lucide React (`lucide-react`) & React Icons
* **WHAT is it?** A clean, consistent SVG icon set for modern web applications.
* **WHY do we use it?** Provides lightweight, scalable visual cues (microphones, upload arrows, checkmarks, warning badges).
* **HOW it is used here:**
  * Used inside all buttons, badges, and diagnostic cards.

---

## 5. Browser Audio & Speech APIs

### 🎙️ Web Speech API (`webkitSpeechRecognition` / `SpeechRecognition`)
* **WHAT is it?** A native browser API that converts spoken audio from your microphone into real-time text without needing external paid speech-to-text APIs.
* **WHY do we use it?** Enables zero-latency voice rehearsal in the Interview Terminal for free directly in Chrome, Edge, and Safari.
* **HOW it is used here:**
  * Inside `components/InterviewTerminal.tsx`, transcribes candidate spoken answers in real-time and computes speaking speed (Words Per Minute).

### 🔊 Web Audio API (`lib/sound.ts`)
* **WHAT is it?** A native browser API for generating and synthesizing sound effects directly using code (oscillators and gain nodes).
* **WHY do we use it?** Provides tactile audio feedback (clicks, success chimes, terminal beeps) without loading external heavy `.mp3` audio files.
* **HOW it is used here:**
  * `lib/sound.ts` plays subtle micro-interaction sounds on button clicks, tab switches, and analysis completions.
