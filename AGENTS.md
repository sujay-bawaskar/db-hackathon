# AGENTS.md — rules for AI assistants working in this repo

This repo is the brainstorming + planning workspace for the July 2026
hackathon. It is **GCP-focused, general-AI, Java-first**.

## Context

- Hackathon window: July 2026.
- Platform bias: Google Cloud AI (Gemini, Imagen, Veo, Vertex AI Vector
  Search, Document AI, Speech-to-Text, Live API, Grounding, BigQuery,
  function-calling agents, Workspace extensions).
- Language: **Java**. The team is Java engineers. Default stack:
  - Spring Boot 3.x for HTTP services / REST APIs
  - Maven (preferred) or Gradle for builds
  - JUnit 5 + AssertJ for tests
  - Google Cloud Java client libraries
    (`google-cloud-vertexai`, `google-cloud-storage`, `google-cloud-bigquery`,
    `google-cloud-speech`, `google-cloud-documentai`, etc.)
  - For LLM calls: the Vertex AI Java SDK / `google-cloud-vertexai` generative
    API, or LangChain4j on top of it.
  - Frontend: only if a demo needs one — keep it minimal (a single HTML/Thymeleaf
    page, or a small Vaadin/HTMX surface). Don't spin up a JS SPA unless the
    idea demands it.
  - Reach for another language only when a GCP SDK is Java-missing or a demo
    surface makes Java impractical — and record that exception in
    `decisions/<date>-stack.md`.

## Workflow (follow this order)

1. **Diverge:** read `ideas/SEED-LIST.md`. To expand an idea, copy
   `templates/idea-pitch.md` to `ideas/<short-name>.md` and fill it in.
2. **Converge:** score pitched ideas using `templates/scoring-rubric.md`.
3. **Decide:** write `decisions/<YYYY-MM-DD>-pick.md` recording the chosen
   idea, runner-up, and one-paragraph rationale.
4. **Build:** start `team-log.md` from `templates/team-log.md`; update it
   every working session.

## File conventions

- Filenames: lowercase, hyphenated. Decision records are date-prefixed
  (`2026-07-18-pick.md`).
- One idea per file in `ideas/`.
- Never delete a rejected idea — set `Status: Rejected` at the top and add a
  one-line "why" so the reasoning survives.
- `notes/` is freeform; everything else follows a template.

## When asked to "add an idea"

- Append to `ideas/SEED-LIST.md` as a numbered entry with: title, 2-3 line
  description, a "Wow demo" line, and a "GCP fit" line. Match the existing
  format.
- Only spin out a full `ideas/<name>.md` pitch if explicitly asked.

## When asked to "pick an idea"

- Do NOT just announce a winner. Run the rubric: produce a filled
  scorecard, then write the decision record in `decisions/`.
- If two ideas are within 2 weighted points, break the tie on judge appeal
  and say so in the decision record.

## When asked to "start building"

- Confirm a decision record exists in `decisions/`. If not, stop and ask.
- Scaffold Java-first: Spring Boot 3.x parent POM, Maven, JUnit 5. Add the
  specific `google-cloud-*` deps the picked idea needs. Only deviate from
  Java if a GCP SDK gap forces it, and record that in
  `decisions/<date>-stack.md`.

## Style

- Keep pitches short — target a 5-minute read.
- Prefer concrete demo moments over abstract value claims.
- Cite the specific GCP capability that makes each idea work; "use AI" is
  not a citation.

## Don't

- Don't commit secrets, API keys, or service account JSON. If a build step
  needs credentials, document the env var name only.
- Don't push to any remote unless explicitly asked.
- Don't reformat or "clean up" existing files unprompted — match the
  surrounding style.
