# Idea Pitch: <name>

Status: Draft | Proposed | Shortlisted | Rejected | Picked
Updated: YYYY-MM-DD
Owner: <name or @handle>

## One-liner

<one sentence: who is helped, how, using what GCP AI capability>

## Problem

- Who feels the pain?
- What does it cost them today (time / money / frustration)?
- Why hasn't it been solved already?

## Solution

- What does the user see / do?
- What's the AI core? (e.g. Gemini long-context, Imagen, Vertex Vector Search,
  Document AI, Speech-to-Text, Live API, Grounding with Google Search,
  function-calling agents…)
- What's the minimal end-to-end flow?

## Why GCP

- Which GCP services make this easier / cheaper / better than alternatives?
- Any preview / GA features we'd lean on?

## Demo plan

- The one screen / one command that will make judges go "oh".
- What we'll fake vs. what's real.

## Scope (hackathon-sized)

- Must-have:
- Nice-to-have:
- Out of scope:

## Risks / unknowns

- Technical:
- Data:
- Time:

## Stack (provisional — Java-first)

- Runtime: Java 21 + Spring Boot 3.x
- Build: Maven
- LLM orchestration: LangChain4j on Vertex AI
- GCP Java client libs needed:
- Frontend (only if the demo needs one):
- Data store:
- Deviation from Java? (only if a GCP SDK gap forces it — record in
  `decisions/`):
