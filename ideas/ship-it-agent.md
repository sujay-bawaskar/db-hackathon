# Idea Pitch: Ship-It Agent

Status: Draft
Updated: 2026-07-18
Owner:

## One-liner

An AI release assistant that reads a bank's change request, diff, test logs, and policy docs, then drafts the release package and routes it through the right SDLC gates.

## Problem

- **Release managers and engineering leads** spend days assembling release evidence for every production deployment.
- **What it costs today:** manually writing release notes, mapping test coverage, summarizing risk, chasing approvals, and re-checking compliance PDFs. A "simple" production deployment can take 1-2 weeks.
- **Why it persists:** each step lives in a different system (Jira, GitHub, Jenkins, test reports, policy wikis, email). No one tool understands the full change-to-deploy narrative, and humans end up stitching it together.

## Solution

- **What the user sees:** a lightweight Python web UI where a release manager pastes a ticket or PR URL. The ADK agent returns a draft release note, risk heatmap, required approvers, and an email ready to send.
- **AI core:**
  - **ADK (Agent Development Kit)** orchestrates the multi-step workflow: ingest, reason, check policy, draft, and route.
  - **Gemini long-context** ingests the PR diff, test logs, and commit history to summarize what changed and why.
  - **Document AI** parses compliance/policy PDFs to extract control requirements.
  - **Function-calling tools** create Jira sub-tasks, draft the approval email, and trigger Cloud Build/Cloud Deploy jobs.
  - **BigQuery** stores deployment metrics for DORA-style lead-time and change-failure tracking.
- **Minimal end-to-end flow:**
  1. User pastes a change ticket URL.
  2. ADK agent fetches the linked PR, diff, and test report.
  3. Agent checks the change type against policy requirements and flags missing evidence.
  4. Agent drafts release note, risk statement, and approval request.
  5. Human reviews, edits, and clicks "Send for approval."

## Why GCP

- **ADK** is Google's agent framework and integrates natively with Vertex AI, BigQuery, and Document AI.
- **Gemini on Vertex AI** handles the long-context synthesis of diffs + logs + tickets in one pass, which is the core value.
- **Document AI** turns static policy PDFs into structured controls the agent can reason over.
- **Function-calling agents** let us define tools for Jira, email, and Cloud Build/Deploy without building a fragile rules engine.
- **BigQuery** is a natural fit for the deployment analytics and DORA metrics the bank already reports.

## Demo plan

- **The "oh" moment:** paste a real-looking Jira ticket URL → in under 60 seconds the screen shows a generated release note, a green/yellow/red risk heatmap, and a draft approval email addressed to the right signatories.
- **What we'll fake:** the Jira/GitHub integrations can be mocked with JSON fixtures so the demo doesn't depend on live bank systems.
- **What's real:** the ADK agent, Vertex AI/Gemini summarization, Document AI policy parsing, and BigQuery write.

## Scope (hackathon-sized)

- **Must-have:**
  - Paste ticket/PR and generate a release package (summary + risk + evidence checklist).
  - Parse one sample policy PDF with Document AI and map clauses to change type.
  - Draft an approval email via ADK function-calling.
- **Nice-to-have:**
  - BigQuery sink for deployment lead-time metric.
  - Simple web UI showing the generated package and approval button.
  - Multi-language diff summarization for polyglot bank repos.
- **Out of scope:**
  - Real approvals or deployments in a production bank environment.
  - Full bi-directional sync with Jira/ServiceNow beyond mocked calls.
  - Multi-region DR or production-grade CI/CD wiring.

## Risks / unknowns

- **Technical:** ADK is still early; Python team fluency and local setup time may be higher than Java.
- **AI:** Gemini context may need chunking for very large PRs or test logs.
- **Data:** Sample policy/test data must be synthetic; no real bank documents.
- **Time:** Integrating mocked Jira + GitHub + email may consume more wiring time than AI time.

## Stack (provisional — Python + ADK)

- **Runtime:** Python 3.11+
- **Agent framework:** Google ADK (Agent Development Kit), Python package `google-adk`
- **LLM:** Gemini via Vertex AI
- **GCP client libs needed:**
  - `google-adk`
  - `google-cloud-vertexai`
  - `google-cloud-documentai`
  - `google-cloud-bigquery`
- **Frontend (only if the demo needs one):** a single FastAPI or Streamlit page with a form and result cards.
- **Data store:** in-memory or SQLite for the demo; BigQuery for the optional metrics sink.
- **Deviation from Java?** Yes — see `decisions/2026-07-18-stack.md`.
