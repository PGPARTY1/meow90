# 05. Beginner's Setup, Troubleshooting & Viva / Interview Q&A

This guide covers how to run the project from scratch, understand all environment variables, fix common issues, and prepare for project presentations or technical viva questions.

---

## 💻 Step-by-Step Local Setup

### Prerequisites
- **Node.js**: Version 18.x or higher installed on your computer.
- **Git**: Installed for version control.
- A modern web browser (Google Chrome, Microsoft Edge, or Brave recommended for Web Speech API).

### 1. Clone & Install Dependencies
```bash
# 1. Clone the repository
git clone https://github.com/Ishikajain2006/Resume-analyzer.git

# 2. Enter project folder
cd Resume-analyzer

# 3. Install all required packages
npm install
```

### 2. Configure Environment Variables
Create a file named `.env.local` in the root folder and add the following keys:

```env
# Clerk Authentication Keys (From https://clerk.com dashboard)
NEXT_PUBLIC_CLERK_PUBLISHABLE_KEY=pk_test_...
CLERK_SECRET_KEY=sk_test_...

# NVIDIA AI Nemotron Inference Keys (From https://build.nvidia.com)
NVIDIA_API_KEY=nvapi-...
NVIDIA_BASE_URL=https://integrate.api.nvidia.com/v1
NVIDIA_MODEL=nvidia/nemotron-3-super-120b-a12b
```

### 3. Run Development Server
```bash
npm run dev
```
Open your browser and visit: **[http://localhost:3000](http://localhost:3000)**

---

## ❓ Frequently Asked Questions & Troubleshooting

### Q1: The microphone isn't recording my voice in the Interview Terminal.
* **Fix**: Ensure you have granted microphone permissions in your browser. Also note that the Web Speech API works natively in Chromium-based browsers (Chrome, Edge, Brave, Opera) and Safari.

### Q2: I get a "Port 3000 is already in use" message.
* **Fix**: Next.js will automatically try port 3001 ([http://localhost:3001](http://localhost:3001)), or you can close the previous process running on port 3000.

### Q3: How do I test the application without uploading my real resume?
* **Fix**: On the dashboard, click the **"⚡ Load Alex Mercer (Senior Full-Stack)"** button. It instantly loads a pre-configured sample candidate profile for testing.

---

## 🎓 College Project Viva / Presentation Cheat Sheet

If an interviewer, professor, or evaluator asks you about this project, here are the exact answers to give:

### Q1: What makes your ATS analyzer different from standard keyword matchers?
> *"Standard ATS checkers only do dumb regex or keyword counting. Our project uses the 120-billion parameter **NVIDIA Nemotron 3 Super** model to evaluate semantic depth, architectural scope, and practical experience. It even categorizes skills into clear engineering taxonomies and creates custom scenario-based interview questions."*

### Q2: Why did you choose Next.js with Server Actions instead of a separate Express backend?
> *"Next.js App Router and Server Actions allow us to build a secure, full-stack application within a single unified codebase. Sensitive operations—like reading PDF byte buffers and calling the NVIDIA AI API—run securely on the server without exposing API keys to the client browser."*

### Q3: How does the PDF extraction work without privacy risks?
> *"We use server-side byte buffer extraction using `pdf-parse`. The resume PDF is processed securely in memory on the server and converted to clean text streams. No candidate data is sent to untrusted third-party document scrapers."*

### Q4: How does the live interview voice rehearsal work?
> *"We utilize the native browser **Web Speech API** for zero-latency speech-to-text conversion. As the candidate speaks, our telemetry engine tracks their elapsed time, total word count, and calculates their speaking pace in **Words Per Minute (WPM)** to ensure they communicate at a confident, professional pace."*

### Q5: How do you guarantee the AI doesn't return broken data?
> *"We use **Zod** schema validation on the server. The AI is strictly instructed to return JSON matching our schema, and Zod validates every single property (scores, keywords, questions) before passing the data to the React UI. If anything is malformed, our fallback handler ensures the application never crashes."*
