# Idea Pitch: Ship-It Agent

Status: Draft
Updated: 2026-07-22
Owner:

## One-liner

An AI release engineer that turns a change request into a policy-backed release package, resolves safe release-control gaps with sequenced PRs, and gives the code owner one human-reviewable path to ship.

## Problem

- **Release managers, engineering leads, and code owners** spend days assembling evidence and clearing release gates across Jira, GitHub, CI, infrastructure, policy wikis, and email.
- **What it costs today:** release blockers are discovered late; version, changelog, dependency, configuration, and approval fixes create context switches and ad-hoc PRs that conflict or merge in the wrong order. A simple production deployment can take 1–2 weeks.
- **Why it persists:** existing tools check individual controls but do not understand the full change-to-deploy narrative, decide which gaps are safe to resolve, or produce an ordered, auditable handoff to the release owner.

## Solution

- **What the user sees:** a lightweight web UI where a release manager pastes a ticket or PR URL. Ship-It Agent returns a release-readiness report: release note, risk heatmap, policy evidence checklist, unresolved blockers, and a prioritised PR merge queue.
- **AI core:**
  - **ADK (Agent Development Kit)** orchestrates manifest building, policy checks, resolution, validation, and handoff.
  - **Gemini long-context** synthesizes the PR diff, test logs, commit history, and infrastructure/configuration changes into a release narrative and risk assessment.
  - **Document AI** extracts control requirements from compliance and policy PDFs.
  - **Specialist resolver agents** classify failed controls as pass, safe-to-auto-resolve, or human escalation; they draft targeted fixes for approved control types.
  - **Function-calling tools** create draft PRs, Jira sub-tasks, approval emails, and optionally trigger Cloud Build or Cloud Deploy.
  - **BigQuery** stores release outcomes and deployment metrics for DORA-style lead-time and change-failure tracking.
- **Minimal end-to-end flow:**
  1. User pastes a change ticket or PR URL.
  2. The agent fetches mocked linked evidence, builds a release manifest, and checks selected controls: test evidence, policy clauses, changelog/version consistency, dependency vulnerability, and environment configuration.
  3. The agent generates one safe remediation PR, such as a changelog or configuration update, while clearly escalating controls it must not change, such as secrets or missing human sign-off.
  4. A sequencer assigns remediation PRs to dependency-aware merge waves.
  5. The code owner reviews the release package, ordered queue, and approval email, then sends it for approval.

## Why GCP

- **ADK** provides the agent workflow and tool-calling primitives needed to coordinate evidence gathering, control checks, and bounded remediation.
- **Gemini on Vertex AI** can reason over the ticket, diff, logs, and policy context together—the core change-to-release synthesis task.
- **Document AI** turns static policy PDFs into structured controls that the agent can cite in its findings.
- **Function-calling agents** provide controlled integrations for mocked Jira, GitHub, email, and Cloud Build/Deploy actions without a brittle custom rules engine.
- **BigQuery** is a natural fit for release audit data and deployment analytics the bank already reports.

## Demo plan

- **The “oh” moment:** paste a realistic Jira ticket URL and watch Ship-It Agent produce a green/yellow/red readiness report, cite the relevant policy clauses, create a safe fix PR, and show exactly which merge wave it belongs to before drafting the approval email.
- **What we'll fake:** Jira, GitHub, CI, infrastructure, and email integrations use JSON fixtures; PR creation is displayed as a draft rather than calling a live bank system.
- **What's real:** the ADK workflow, Vertex AI/Gemini synthesis, Document AI policy parsing, control classification, PR dependency sequencing, and BigQuery write.

## Scope (hackathon-sized)

- **Must-have:**
  - Paste a ticket/PR and generate a release package with summary, risk, and evidence checklist.
  - Parse one sample policy PDF with Document AI and map clauses to a change type.
  - Check a small, deterministic control set and classify each as pass, auto-resolvable, or human escalation.
  - Generate one safe remediation PR draft and show its position in an ordered merge queue.
  - Draft an approval email via ADK function-calling.
- **Nice-to-have:**
  - BigQuery sink for deployment lead-time metrics.
  - Multiple remediation PRs displayed in parallel merge waves.
  - Cloud Build/Cloud Deploy trigger in dry-run mode.
- **Out of scope:**
  - Real approvals, production deployments, or writes to bank systems.
  - Autonomous remediation for secrets, high-severity security findings, destructive infrastructure changes, or required human sign-offs.
  - Full bidirectional sync with Jira or ServiceNow, multi-region DR, and production-grade CI/CD wiring.

## Risks / unknowns

- **Technical:** ADK is still early; Python team fluency and local setup time may be higher than Java. PR dependency inference must be constrained to deterministic demo rules rather than presented as fully autonomous reasoning.
- **AI:** Gemini may need chunking for very large diffs or test logs; generated remediation must remain bounded and reviewed by a human.
- **Data:** All policy, ticket, and test data must be synthetic; no real bank documents or credentials.
- **Time:** Mock integration wiring, Document AI parsing, and one credible remediation flow must take priority over broad control coverage.

## Stack (provisional — Python + ADK)

- **Runtime:** Python 3.11+
- **Agent framework:** Google ADK (Agent Development Kit), Python package `google-adk`
- **LLM:** Gemini via Vertex AI
- **GCP client libs needed:**
  - `google-adk`
  - `google-cloud-vertexai`
  - `google-cloud-documentai`
  - `google-cloud-bigquery`
- **Frontend (only if the demo needs one):** a single FastAPI or Streamlit page with a form, readiness report, and merge-queue cards.
- **Data store:** in-memory or SQLite for demo fixtures; BigQuery for the optional metrics sink.
- **Deviation from Java?** Yes — see `decisions/2026-07-18-stack.md`.
