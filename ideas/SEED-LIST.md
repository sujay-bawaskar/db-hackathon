# Idea Seed List — GCP AI Hackathon (2026-07)

Starter ideas to react to, combine, or reject. Each is intentionally short —
copy `templates/idea-pitch.md` into `ideas/<short-name>.md` to flesh one out.

**Stack assumption: Java.** Spring Boot 3.x services, Maven, JUnit 5, and the
GCP Java client libs (`google-cloud-vertexai`, `google-cloud-bigquery`,
`google-cloud-speech`, `google-cloud-documentai`, …). LangChain4j is the
default for LLM orchestration on top of Vertex AI. Deviate only when a GCP
SDK is missing for Java.

GCP AI capabilities in play: Gemini (long-context, multimodal, Live API),
Imagen / Veo, Vertex AI Vector Search, Document AI, Speech-to-Text / Text-to-
Speech, Grounding with Google Search, function-calling agents, BigQuery + ML,
NotebookLM-style RAG, Gemini for Google Workspace.

---

## 1. "Replay" — meeting video → searchable, askable archive
Drop a Meet recording in; Gemini long-context + Speech-to-Text produce a
transcript, per-speaker summary, action items, and a Vector Search index so
you can ask "what did we decide about X?" across all past meetings.
**Wow demo:** ask a question, jump to the exact video timestamp.
**GCP fit:** Gemini 2M-token context, Vertex Vector Search, STT — all via
`google-cloud-vertexai` + `google-cloud-speech`; Spring Boot REST + pgvector
or Vertex Vector Search for the index.

## 2. "Paper Trail" — research literature agent
Give it a topic; agent uses Grounding with Google Search + Gemini to pull
recent papers, summarize, cross-reference, and produce a literature map with
citations you can verify.
**Wow demo:** "what's new in <niche> since 2025?" → annotated reading list.
**GCP fit:** Grounding, function-calling, long-context synthesis — LangChain4j
agent loop in a Spring Boot service.

## 3. "Triage" — multimodal support ticket router
Tickets come in as text + screenshots + logs. Gemini multimodal classifies,
extracts structured fields, drafts a reply, and routes to the right queue.
**Wow demo:** drop a blurry screenshot, watch it extract the error code.
**GCP fit:** Gemini multimodal, Document AI, function-calling —
`google-cloud-documentai` for OCR, LangChain4j tools for routing.

## 4. "Field Notes" — voice-first site inspection reporter
Inspector talks; Live API transcribes, asks clarifying questions in real
time, and generates a structured report with photos annotated by Imagen /
Gemini vision.
**Wow demo:** live conversation that pulls missing fields out of you.
**GCP fit:** Live API, Gemini vision, structured output — Spring Boot WebSocket
endpoint bridging the Live API to a mobile/web mic.

## 5. "Spec to Story" — PRD → runnable prototype
Paste a PRD; agent generates UI mockups (Imagen), a data model, BigQuery
DDL, and a stub backend. Human reviews, agent iterates.
**Wow demo:** PRD in, clickable mockup + schema out in 5 minutes.
**GCP fit:** Imagen, Gemini code-gen, BigQuery — `google-cloud-bigquery` for
DDL execution; Spring Boot serves the generated mockup.

## 6. "Compliance Copilot" — policy → control gap finder
Upload a cloud architecture diagram + a policy PDF; Gemini reasons over
both, flags likely violations, cites the policy clause and the offending
resource.
**Wow demo:** red marks on a diagram with cited policy text.
**GCP fit:** Gemini multimodal + long-context, Document AI —
`google-cloud-documentai` parses the policy PDF; LangChain4j RAG over both.

## 7. "Replay Coach" — sports tape analysis
Upload game footage; Gemini video understanding tags key moments, generates
a highlight reel (Veo), and answers "show me every defensive lapse."
**Wow demo:** natural-language video search over raw footage.
**GCP fit:** Gemini video, Veo — Spring Boot async pipeline via
`google-cloud-vertexai` video understanding.

## 8. "Doc Detective" — contract risk heatmap
Long PDF contract → Gemini extracts obligations, compares against a playbook,
heatmaps risky clauses with suggested redlines.
**Wow demo:** red/green clause heatmap + one-click redline.
**GCP fit:** Gemini long-context, Document AI, structured output —
`google-cloud-documentai` + LangChain4j structured `record` output.

## 9. "Schema Whisperer" — natural-language BigQuery explorer
Ask "which customers churned last quarter and why?" → agent introspects
BigQuery schema, writes + runs SQL, charts the result, explains it.
**Wow demo:** non-technical user gets a chart + plain-English answer.
**GCP fit:** BigQuery, Code Interpreter API, Gemini, function-calling —
`google-cloud-bigquery` JDBC, LangChain4j tool-calling to run + chart SQL.

## 10. "Onboarding Twin" — personalized new-hire agent
New hire chats; agent pulls from their role, team docs, and Calendar, builds
a 30-60-90 plan, schedules intros, drafts first-week emails.
**Wow demo:** "I'm a new PM on team X" → full first-week plan.
**GCP fit:** Gemini + Workspace extension, grounding, agents — Spring Boot
backend calling the Workspace + Calendar APIs via Google Java client libs.

## 11. "Bug Bounty Bot" — repro-from-report agent
Takes a bug report (text + screenshot), spins up a repro script, runs it in
a sandbox, and replies with a minimal repro + suggested fix.
**Wow demo:** paste a flaky bug, get a runnable repro.
**GCP fit:** Gemini code-gen, function-calling, Cloud Run sandbox — Spring
Boot orchestrator spawns a Cloud Run job per repro attempt.

## 12. "Lecture Loop" — NotebookLM-style study buddy for one course
Upload syllabus + lectures; student asks questions, gets answers grounded
only in course material, with flashcards + spaced-repetition quizzes
generated on the fly.
**Wow demo:** "quiz me on week 3" → adaptive quiz.
**GCP fit:** Vertex AI Search / RAG, Gemini, structured output —
LangChain4j RAG over Vertex Vector Search, Spring Boot serves the quiz API.

---

## How to use this list

- Don't pick blindly. Pick the one that's hardest for *you* to dismiss.
- Combine: e.g. #1 + #6 = "compliance review of recorded design reviews."
- Reject loudly: write a one-line "why not" next to any idea you cross off —
  it stops the team from re-litigating.
