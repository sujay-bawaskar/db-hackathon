# Ship-It Agent — Development Setup

Status: Proposed
Updated: 2026-07-22

## Purpose

Ship-It Agent is a polyglot hackathon demo:

- **Java** owns the fixture-backed MCP façades and the historical-release
  retrieval service.
- **Python + Google ADK** owns agent orchestration, Gemini reasoning, and the
  lightweight demo surface.

This keeps integration and retrieval components in the team's primary language
while using ADK where its Python-first agent workflow is the reason for the
stack exception. See `decisions/2026-07-18-stack.md`.

## Local prerequisites

Install the following before starting:

- JDK 21 and Maven 3.9+ for the Java services.
- Python 3.11+ and `uv` for the ADK agent environment.
- Google Cloud CLI authenticated to the hackathon GCP project only when using
  real Vertex AI, Document AI, or BigQuery calls.

No GitHub, Jira, ServiceNow, or Confluence credentials are required. The demo
uses synthetic fixtures and does not call bank systems.

## Intended workspace layout

```text
ship-it-agent/
├── services/
│   ├── mcp-fixtures/             # Java Spring Boot MCP façade service
│   └── release-retrieval/        # Java Spring Boot historical-release API
├── agent/                        # Python ADK orchestration and UI
├── fixtures/
│   ├── github/
│   ├── jira/
│   ├── servicenow/
│   ├── confluence/
│   └── releases/
└── contracts/                    # versioned JSON schemas and tool examples
```

Keep each component independently runnable. Shared data travels through
versioned HTTP/MCP contracts rather than cross-language imports.

## Java services

### MCP fixture façade

Build one Spring Boot service that presents narrow MCP tools backed by local
fixture files. Its only responsibility is translating a validated tool request
into a deterministic fixture response.

Initial read-only tools:

- `github.get_pull_request`
- `github.get_workflow_run`
- `jira.get_issue`
- `servicenow.get_change`
- `confluence.search_policy`

Simulated mutation tools must be explicit dry runs:

- `github.create_draft_pull_request`
- `jira.create_subtask`
- `servicenow.create_change_note`

Every simulated mutation returns `dryRun: true`, a synthetic identifier, and
its proposed payload. It must not write to an external system.

Use Spring Boot 3.x, Maven, JUnit 5, AssertJ, and Spring's supported MCP server
integration. Tests should load fixtures and assert the exact tool response,
including not-found and invalid-input cases.

### Release retrieval service

Build a separate Spring Boot API over a vector database containing sanitized
historical release records. It accepts a release context—change type, affected
service, risk signals, and optional query text—and returns the most comparable
past releases.

A result includes:

- a stable synthetic release ID;
- similarity/relevance score;
- concise outcome and remediation summary;
- source citations identifying the fixture and relevant release fields;
- a `synthetic: true` marker.

Use Vertex AI Vector Search for the GCP-backed retrieval path, with the Java
service responsible for embedding/query translation and response citations. For
local-only development, a deterministic lexical/metadata fixture implementation
may stand in behind the same service interface. Do not represent historic
similarity as an approval or a replacement for a current policy check.

## Python ADK agent

Create an isolated Python environment and install the agent dependencies:

```sh
cd agent
uv venv --python 3.11
source .venv/bin/activate
uv pip install google-adk google-cloud-aiplatform google-cloud-documentai google-cloud-bigquery
```

The agent coordinates the following sequence:

1. Validate a ticket or PR input.
2. Call MCP façade tools to collect synthetic current-state evidence.
3. Call the Java retrieval API for comparable synthetic releases.
4. Send the evidence, release precedents, and policy clauses to Gemini through
   ADK.
5. Produce a readiness report and, only when allowed, request a dry-run
   remediation action through an MCP façade.

The agent is an orchestrator: it must not read fixture files directly or embed
vendor-specific API behavior. Tool descriptions must state that all fixture
responses and write-like actions are synthetic/dry-run.

## Local configuration

Use environment variables rather than committed credentials:

```sh
export MCP_FIXTURES_BASE_URL=http://localhost:8081
export RELEASE_RETRIEVAL_BASE_URL=http://localhost:8082
export GOOGLE_CLOUD_PROJECT=<hackathon-project-id>
export GOOGLE_CLOUD_LOCATION=<vertex-ai-region>
```

Use a local-only agent mode when GCP credentials are unavailable. In that mode,
Gemini, Document AI, and BigQuery interactions are replaced by clearly labeled
synthetic responses; MCP and retrieval behavior remains real against local
fixtures.

Do not commit `.env` files, service-account JSON, API keys, production URLs, or
non-synthetic release data.

## Run order

1. Start the MCP fixture façade on port 8081.
2. Start the release retrieval service on port 8082.
3. Verify each service health endpoint and one representative contract request.
4. Activate the Python environment, set the local URLs, and start the ADK demo
   surface.
5. Submit a synthetic Jira ticket or GitHub PR fixture and verify that the final
   report displays MCP evidence, retrieved release citations, and a clearly
   labelled dry-run remediation.

## Contract and safety rules

- Store JSON request/response examples in `contracts/` and change producer and
  consumer together when a contract changes.
- Give fixture records stable IDs so agent output can cite evidence precisely.
- Treat all MCP tool input as untrusted: validate identifiers, constrain
  fixture paths, and never expose arbitrary file reads.
- Keep MCP permissions capability-specific; read tools are separate from
  dry-run tools.
- The agent may recommend and draft safe remediations, but it may not bypass
  required human approval, change secrets, or perform external writes.

## Demo acceptance check

The setup is ready when a developer can run the three local processes and the
agent can, using only synthetic data:

1. fetch a ticket and PR through MCP;
2. retrieve and cite comparable release outcomes;
3. generate a policy-backed readiness report; and
4. return a `dryRun: true` remediation proposal without contacting an external
   service.
