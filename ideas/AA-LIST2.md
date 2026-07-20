# 🚀 ReleaseGate AI — Autonomous Release Control Resolution Agent

---

## The Problem

Every engineering team has a **release checklist** — a set of gates that must pass before code ships: version bumps, changelog entries, security scans, infrastructure drift checks, compliance sign-offs, dependency audits, environment config validation, and more. In practice, these controls are:

- **Discovered late** — engineers find blocking issues hours before a release window.
- **Resolved manually** — each fix is a context switch: find the issue, understand the requirement, write the fix, open a PR, wait for CI, get it reviewed.
- **Done out of order** — PRs are raised ad hoc, creating merge conflicts and sequencing nightmares.
- **Opaque to code owners** — the person responsible for approving the release has no single view of what's pending, in what order, and why.

**ReleaseGate AI** eliminates this entirely. It scans all release controls automatically, resolves every resolvable issue by generating targeted PRs (code fixes, version bumps, infra changes, config patches), sequences them in the correct merge order, and hands the code owner a structured release readiness report with a single prioritised PR queue to approve.

---

## Vision Statement

> *"One command. Every release control resolved. A sequenced PR queue in your inbox — before you've finished your coffee."*

ReleaseGate AI acts as a release engineer who never sleeps. It knows your release standards, understands your codebase and infrastructure, resolves what it can autonomously, escalates what it can't, and always hands off a clear, ordered, human-reviewable trail.

---

## System Architecture Overview

```
┌─────────────────────────────────────────────────────────────────────┐
│                          TRIGGER                                    │
│     Git tag push  │  Release branch creation  │  Manual /rgate cmd  │
└──────────┬────────┴──────────────┬────────────┴──────────┬──────────┘
           └──────────────────────▼────────────────────────┘
                                  │
                    ┌─────────────▼──────────────┐
                    │      RELEASE MANIFEST       │
                    │       BUILDER               │  ← Reads: repo config,
                    │                             │    release policy file,
                    └─────────────┬───────────────┘    infra state, env vars
                                  │
                    ┌─────────────▼──────────────┐
                    │    CONTROL SCANNER          │  ← Runs all registered
                    │    (Parallel Checks)        │    release control checks
                    │                             │    concurrently
                    └─────────────┬───────────────┘
                                  │
                         ┌────────▼────────┐
                         │  CONTROL ITEMS  │
                         │   (Pass/Fail/   │
                         │    Resolvable)  │
                         └────────┬────────┘
                   ┌──────────────┼──────────────┐
                   ▼              ▼               ▼
             ✅ PASS         🔧 AUTO-         🚨 ESCALATE
             (no action)     RESOLVABLE       TO HUMAN
                             │
                    ┌────────▼────────────┐
                    │  RESOLVER AGENTS    │  ← Specialist agents per
                    │  (per control type) │    control category
                    └────────┬────────────┘
                             │
                    ┌────────▼────────────┐
                    │  PR FACTORY         │  ← Generates, validates,
                    │                     │    and opens PRs per fix
                    └────────┬────────────┘
                             │
                    ┌────────▼────────────┐
                    │  DEPENDENCY &       │  ← Resolves merge order,
                    │  SEQUENCER ENGINE   │    detects PR interdependencies,
                    │                     │    builds the merge DAG
                    └────────┬────────────┘
                             │
                    ┌────────▼────────────┐
                    │  RELEASE READINESS  │  ← Delivers structured report
                    │  REPORT GENERATOR   │    + ordered PR queue to
                    │                     │    code owner(s)
                    └─────────────────────┘
```

---

## Release Control Categories

ReleaseGate AI understands and resolves controls across six domains:

### 1. 📦 Versioning Controls

| Control | Detection | Auto-Resolution |
|---|---|---|
| `package.json` / `pyproject.toml` version not bumped | Diff against last release tag | Bump semver based on commit log (major/minor/patch) |
| Version inconsistency across multi-module monorepo | Cross-file version scan | Synchronise all module versions to target |
| Missing Git tag for release | Tag absence check | Create annotated tag and push |
| CHANGELOG.md not updated | File diff check | Generate changelog from commit messages using Conventional Commits spec |
| Version in docs/README out of sync | Regex scan on docs/ | Update all doc references to new version |

### 2. 🏗️ Infrastructure Controls

| Control | Detection | Auto-Resolution |
|---|---|---|
| Terraform plan shows drift | `terraform plan` output diff | Generate infra PR with corrected `.tf` resource blocks |
| Missing environment variable in target environment | Compare `.env.example` vs secrets store | Raise infra PR to add secret placeholder + notify owner |
| Kubernetes manifest version mismatch | Image tag scan in manifests | PR to update image tags to release version |
| Missing Helm chart value for new config key | Diff `values.yaml` vs code config | PR to add missing Helm value with sensible default |
| Database migration not applied | Pending migration check | Flag to DBA; generate migration PR if schema is additive |

### 3. 🔐 Security & Compliance Controls

| Control | Detection | Auto-Resolution |
|---|---|---|
| CVE in direct dependencies | `npm audit` / `pip-audit` / `trivy` | PR to bump affected package to patched version |
| Licence incompatibility introduced | Licence diff vs allowlist | PR to swap package or add licence exception with justification |
| Secrets committed in diff | `git-secrets` / `trufflehog` scan | Block release; escalate immediately — no auto-fix |
| SAST finding above severity threshold | Semgrep / Snyk results | Generate targeted code fix PR for auto-patchable patterns |
| Missing security headers in API config | Static config scan | PR to add headers to framework config |

### 4. ✅ Quality & Test Controls

| Control | Detection | Auto-Resolution |
|---|---|---|
| Code coverage dropped below threshold | Coverage report diff | Flag; generate stub test PR if threshold is minor (< 2%) |
| Failing test in release branch | CI report | Invoke AutoPatch AI (see companion project) |
| Integration test environment not provisioned | Environment health check | Raise infra PR to provision ephemeral test environment |
| Performance regression detected | Benchmark comparison | Flag to owner with detailed regression report |

### 5. 📋 Process & Governance Controls

| Control | Detection | Auto-Resolution |
|---|---|---|
| Release notes not drafted | GitHub Release / Confluence check | Generate release notes draft from commits + JIRA tickets |
| Required reviewer not assigned to release PR | PR review settings | Auto-assign based on CODEOWNERS + release policy |
| JIRA tickets in release not in `Done` state | JIRA API query | Comment on PR with list of blocking tickets; assign to PO |
| Sign-off from required approver missing | Approval audit | Send structured approval request to designated approvers |
| Runbook not updated | Doc freshness check | Generate PR to update runbook with new deployment steps |

### 6. 🌐 Environment & Config Controls

| Control | Detection | Auto-Resolution |
|---|---|---|
| Feature flags not set for target environment | Feature flag platform API | PR / config update to set flags to release state |
| Config schema has new required field with no default | Schema diff | PR to add default value + migration note |
| DNS / CDN config not pointing to new service | DNS record check | Raise infra PR; flag for network team if zone is restricted |
| Third-party API keys approaching expiry | Secret expiry metadata | Notify owner with rotation runbook link |

---

## Resolver Agent Architecture

Each control category has a **specialist Resolver Agent** — a focused LLM agent with domain-specific tools and prompts:

```
┌────────────────────────────────────────────────────────────┐
│                   RESOLVER AGENT (base)                    │
│  Input: Control item + codebase context + policy config    │
│  Output: Patch diff(s) + PR metadata + confidence score    │
│  Tools: read_file, write_file, run_command, call_api       │
└────────────────────────────────────────────────────────────┘
          │               │                │               │
    ┌─────▼──────┐ ┌──────▼─────┐ ┌───────▼────┐ ┌──────▼──────┐
    │ Version    │ │   Infra    │ │  Security  │ │  Process   │
    │  Agent     │ │   Agent    │ │   Agent    │ │   Agent    │
    │            │ │            │ │            │ │            │
    │ semver lib │ │ terraform  │ │ pip-audit  │ │ JIRA API   │
    │ git tags   │ │ k8s API    │ │ semgrep    │ │ Confluence │
    │ changelog  │ │ helm       │ │ trivy      │ │ GitHub API │
    └────────────┘ └────────────┘ └────────────┘ └────────────┘
```

Each agent operates independently and in parallel. Agents communicate their output — patches and PR metadata — to the **PR Factory**, which assembles, validates, and opens each PR.

---

## The Sequencer Engine — Ordering the PR Queue

This is the hardest and most architecturally interesting part of the system.

PRs cannot be merged in arbitrary order. A version bump PR must merge before the Helm chart image tag PR. A database migration PR must merge before the service deployment PR. The sequencer builds a **Directed Acyclic Graph (DAG)** of PR dependencies and outputs a topologically sorted merge sequence.

### Dependency types the engine understands:

```
DEPENDENCY TYPES
────────────────
hard_before     → PR-A must merge before PR-B can even be opened
                  (e.g. shared lib version bump before consumers)

soft_before     → PR-A should merge before PR-B for correctness
                  (e.g. config default before service deployment)

parallel_safe   → PR-A and PR-B have no shared files; can merge in any order

mutually_exclusive → PR-A and PR-B cannot coexist; human must choose
                     (e.g. two competing dependency resolution strategies)

blocks_release  → PR must be merged before release tag is created
                  (version bump, security fix)

post_release    → PR is informational only; can merge after release
                  (runbook update, non-blocking doc change)
```

### Example DAG output for a typical release:

```
RELEASE v2.4.0 — PR MERGE SEQUENCE
════════════════════════════════════

WAVE 1  (parallel — no dependencies between these)
  ├── PR #201  fix: bump CVE in lodash to 4.17.21          [security]
  ├── PR #202  fix: add missing DB_TIMEOUT env to .env.example [config]
  └── PR #203  fix: patch null check in PaymentService       [code]

WAVE 2  (depends on Wave 1 merging)
  ├── PR #204  chore: bump version to 2.4.0 across all modules [version]
  └── PR #205  docs: update CHANGELOG for v2.4.0             [docs]

WAVE 3  (depends on PR #204)
  ├── PR #206  infra: update k8s image tags to v2.4.0        [infra]
  └── PR #207  infra: add DB_TIMEOUT to production Helm values [infra]

WAVE 4  (depends on Wave 3 — deploy-time)
  └── PR #208  infra: update feature flag release-payment-v2 to ON [config]

POST-RELEASE (merge anytime after tag is cut)
  └── PR #209  docs: update runbook with v2.4.0 deployment steps [docs]

ESCALATED (requires human action — cannot be auto-resolved)
  └── ⚠️  JIRA-4421: "Checkout flow redesign" ticket not in Done state
       → Owner: @sarah.chen | Blocking: release tag creation
```

---

## Release Readiness Report (Code Owner Deliverable)

The final output delivered to the code owner — via PR comment, Slack, email, or all three — is a structured **Release Readiness Report**:

```
╔══════════════════════════════════════════════════════════════════╗
║          RELEASE READINESS REPORT — v2.4.0                      ║
║          Generated: 2026-07-20 09:14 UTC  |  by ReleaseGate AI  ║
╠══════════════════════════════════════════════════════════════════╣
║  CONTROLS SCANNED:    24                                         ║
║  ✅ PASSED:           16  (no action needed)                     ║
║  🔧 AUTO-RESOLVED:     7  (PRs raised — see queue below)         ║
║  🚨 NEEDS HUMAN:       1  (see escalations)                      ║
╠══════════════════════════════════════════════════════════════════╣
║  ESTIMATED REVIEW TIME:  ~25 minutes (7 PRs, 4 merge waves)      ║
║  RELEASE BLOCKER COUNT:  1 (human escalation)                    ║
╠══════════════════════════════════════════════════════════════════╣
║  PR MERGE QUEUE (approve in order):                              ║
║                                                                  ║
║  WAVE 1  [merge in any order within wave]                        ║
║  → PR #201  CVE fix: lodash          ★★★★★ confidence           ║
║  → PR #202  config: DB_TIMEOUT env   ★★★★★ confidence           ║
║  → PR #203  code: null check fix     ★★★★☆ confidence           ║
║                                                                  ║
║  WAVE 2  [after Wave 1]                                          ║
║  → PR #204  version bump: 2.4.0      ★★★★★ confidence           ║
║  → PR #205  changelog update         ★★★★★ confidence           ║
║                                                                  ║
║  WAVE 3  [after Wave 2]                                          ║
║  → PR #206  k8s image tags: v2.4.0   ★★★★★ confidence           ║
║  → PR #207  helm: DB_TIMEOUT value   ★★★★★ confidence           ║
║                                                                  ║
║  WAVE 4  [after Wave 3 deployed]                                 ║
║  → PR #208  feature flag: payment-v2 ★★★★☆ confidence           ║
║                                                                  ║
║  ESCALATIONS REQUIRING YOUR ACTION:                              ║
║  ⚠️  JIRA-4421 not in Done — assigned to @sarah.chen            ║
║      Action: Resolve or defer this ticket to unblock release     ║
╚══════════════════════════════════════════════════════════════════╝
```

---

## Release Policy File (`releasegate.yml`)

Teams configure ReleaseGate via a declarative policy file committed to the repo root. This is the single source of truth for what controls are enforced and which are auto-resolvable.

```yaml
# releasegate.yml
version: "1.0"

release:
  target_branch: "main"
  version_strategy: conventional_commits   # major | minor | patch | conventional_commits
  version_files:
    - package.json
    - pyproject.toml
    - helm/Chart.yaml

controls:
  versioning:
    enabled: true
    auto_resolve: true
    changelog: true

  security:
    enabled: true
    auto_resolve:
      cve_severity: ["LOW", "MEDIUM"]   # HIGH and CRITICAL → escalate
      sast_patterns: true
    block_on: ["HIGH", "CRITICAL"]

  infrastructure:
    enabled: true
    auto_resolve: true
    providers:
      - terraform
      - kubernetes
      - helm

  quality:
    enabled: true
    coverage_threshold: 80
    auto_resolve_coverage_delta: 2      # auto-fix if drop is ≤ 2%

  process:
    enabled: true
    required_approvers:
      - "@platform-team"
      - "@security-team"
    jira_project: "PROJ"
    release_notes: true

notifications:
  code_owner_report: true
  channels:
    - slack: "#releases"
    - email: "release-leads@company.com"
  pr_labels:
    ai_generated: "ai-generated"
    release_control: "release-control"
```

---

## Technology Stack

| Layer | Technology |
|---|---|
| LLM backbone | Claude claude-sonnet-4-6 (tool use / function calling) |
| Agent orchestration | LangGraph (stateful agent DAG) |
| Code & config analysis | Tree-sitter AST, custom YAML/TOML parsers |
| Infra analysis | Terraform SDK, Kubernetes Python client, Helm SDK |
| Security scanning | `pip-audit`, `npm audit`, `trivy`, Semgrep |
| VCS integration | PyGithub / Octokit (PR creation, CODEOWNERS) |
| Ticket integration | JIRA REST API, Linear API |
| Sequencer / DAG | NetworkX (Python graph library) |
| Notification layer | Slack Bolt, SendGrid, GitHub PR comments |
| Queue / async | Celery + Redis |
| Policy config | YAML (`releasegate.yml`) parsed with Pydantic |
| Observability | OpenTelemetry + Langfuse for LLM tracing |
| Deployment | Docker + Kubernetes CronJob / GitHub App |

---

## Hackathon Scope (24-Hour MVP)

The 24-hour build is a razor-sharp, single-path demo. No parallelism, no full DAG engine, no pluggable policy file — just a clean, end-to-end story that proves the core value proposition is real and working.

### North Star for 24 hours

> *Trigger one command on a broken repo. Watch three PRs appear on GitHub in the right order. Read the Release Readiness Report in the PR comment.*

### What you build

**One linear pipeline — no branching logic:**

```
CLI trigger → scan 3 hardcoded controls → generate 3 PRs → post ordered report
```

**Hardcoded control set (pick these three — they cover code, infra, and versioning):**

| # | Control | What the agent does |
|---|---|---|
| 1 | Outdated `package.json` version | Reads current version, inspects commit log, bumps to correct semver, updates CHANGELOG |
| 2 | Vulnerable dependency (`lodash < 4.17.21`) | Detects CVE via `npm audit`, rewrites version in `package.json` + `package-lock.json` |
| 3 | Stale image tag in `k8s/deployment.yaml` | Reads current tag, compares to version from step 1, writes corrected manifest |

**Hardcoded merge sequence** (no DAG engine — just a sorted array):

```python
PR_SEQUENCE = [
    {"id": 1, "type": "security",   "title": "fix: patch lodash CVE-2021-23337"},
    {"id": 2, "type": "versioning", "title": "chore: bump version to 1.4.2 + changelog"},
    {"id": 3, "type": "infra",      "title": "infra: update k8s image tag to v1.4.2"},
]
# Rule: security always first, version before infra. Hardcoded. Ship it.
```

**Release Readiness Report** posted as a GitHub PR comment — plain markdown, no fancy rendering needed:

```markdown
## 🚀 ReleaseGate AI — Release Readiness Report

**Scanned:** 3 controls | ✅ 0 passed | 🔧 3 auto-resolved | 🚨 0 escalated

### PR Merge Queue

**Wave 1** — merge first
- [ ] #47  `fix: patch lodash CVE-2021-23337` → [View PR](#)

**Wave 2** — merge after Wave 1
- [ ] #48  `chore: bump version to 1.4.2 + changelog` → [View PR](#)

**Wave 3** — merge after Wave 2
- [ ] #49  `infra: update k8s image tag to v1.4.2` → [View PR](#)

_All PRs generated by ReleaseGate AI. Review diffs before merging._
```

### Tech choices for 24 hours — keep it minimal

| Need | 24-hour choice | Why |
|---|---|---|
| LLM | Claude API (direct HTTP, no framework) | No LangGraph setup time; just `fetch` calls |
| PR creation | PyGithub | 10 lines to open a PR |
| Repo manipulation | `gitpython` + temp branch per fix | Simple, reliable |
| CVE detection | `npm audit --json` piped to stdout | Zero setup, built into npm |
| CLI trigger | `python releasegate.py` | No click/argparse needed for a demo |
| Report delivery | GitHub PR comment via API | One POST request |

**No Redis. No Celery. No vector store. No AST parser. No YAML policy file.** All of that is 48-hour territory.

### Hour-by-hour build plan (2-person team)

```
HOURS 0–2   Setup
  Person A:  Create demo GitHub repo, plant the 3 bugs, set up GitHub token + env
  Person B:  Scaffold Python project, wire Claude API call, test a basic diff generation

HOURS 2–6   Build the three resolver functions
  Person A:  version_bump_resolver() — semver logic + CHANGELOG write
  Person B:  cve_fix_resolver() — npm audit parse + package.json patch

HOURS 6–9   Infra resolver + PR factory
  Person A:  k8s_image_tag_resolver() — YAML read/write for deployment.yaml
  Person B:  pr_factory() — branch creation, commit, push, open PR via PyGithub

HOURS 9–11  Sequencer + Report
  Person A:  sequence_prs() — apply hardcoded sort, return ordered list
  Person B:  post_report() — format markdown report, post as PR comment

HOURS 11–13 Integration + happy path end-to-end
  Both:      Wire all steps together in releasegate.py main(), run full demo flow,
             fix integration bugs, verify 3 PRs appear on GitHub in correct order

HOURS 13–16 Polish + rehearse demo
  Person A:  Clean up PR descriptions, add root-cause explanation to each PR body
  Person B:  Rehearse live demo, prep fallback screen recording, write pitch notes

HOURS 16–24 Buffer / sleep / pitch prep
```

### Demo script (5 minutes)

1. **Show the broken repo** — open GitHub, point to the three planted issues (30 sec).
2. **Run the command** — `python releasegate.py --repo owner/repo --release v1.4.2` (10 sec).
3. **Watch the output scroll** — scanning, generating, pushing. Three PRs appear live in the GitHub UI (90 sec).
4. **Open the release PR** — show the Release Readiness Report comment with the ordered merge queue (60 sec).
5. **Merge Wave 1 live** — approve and merge PR #47, show CI goes green (60 sec).
6. **Close** — *"In 24 hours we built a system that replaces 45 minutes of manual release prep. Here's where it goes next."* (30 sec).

### What the 24-hour MVP deliberately defers

| Deferred | Picked up in 48-hour build |
|---|---|
| Policy file (`releasegate.yml`) | Hardcode all config in 24h |
| Real DAG / topological sort | Hardcoded array sort |
| Parallel control scanning | Sequential is fine for 3 controls |
| Multiple language support | Python + npm only |
| Slack / email notifications | GitHub comment only |
| Confidence scoring on PRs | All PRs shown as ★★★★★ |
| Human escalation path | Out of scope for 24h demo |

---

## Hackathon Scope (48-Hour MVP)

Demonstrate a compelling end-to-end slice across **three control types** on a real GitHub repo:

**MVP control set:**
1. **Version bump** — detect missing semver bump; generate CHANGELOG + `package.json` PR.
2. **CVE fix** — detect vulnerable npm/pip dependency; generate patched version PR.
3. **Kubernetes image tag** — detect stale image tag in manifests; generate infra PR.

**MVP sequencer:** hardcode the 3-PR DAG (version → CVE → k8s) as a demo of the merge wave concept; prove the architecture, don't over-engineer for day one.

**Demo scenario:** A pre-seeded monorepo on GitHub with all three issues planted. Trigger `releasegate run` from the CLI. Watch the console as each control is scanned, each PR opens in real time on GitHub, and the Release Readiness Report is posted as a comment on the release PR.

**Team split (4–5 people):**

| Role | Responsibility |
|---|---|
| **Orchestration lead** | LangGraph agent DAG, control runner, parallel execution |
| **Resolver engineers (×2)** | One per control domain (versioning + security; infra) |
| **Sequencer engineer** | DAG builder, topological sort, wave grouping |
| **DX / demo** | CLI, report formatter, GitHub PR comment renderer, live demo setup |

---

## Stretch Goals (Post-Hackathon Roadmap)

- **Self-learning policy engine** — ReleaseGate learns which controls your team enforces most strictly and auto-tunes confidence thresholds based on PR acceptance history.
- **Release simulation mode** — dry-run the entire release without opening PRs; output a what-would-happen report for planning.
- **Cross-repo release coordination** — for microservice platforms, coordinate release gates across multiple repos with inter-service version dependency tracking.
- **Rollback intelligence** — post-release, monitor for anomalies and automatically prepare a rollback PR queue if key SLOs degrade.
- **Audit & compliance export** — generate a signed PDF audit trail of every control checked, every resolution made, and every human approval received — for SOC 2 / ISO 27001 evidence packages.
- **GitHub App distribution** — package as a GitHub App so any team can install it with one click, no infrastructure required.

---

## Why This Wins a Hackathon

- **Relatable pain** — every engineer in the room has been burned by a release-day scramble. The problem lands instantly.
- **Breadth of impact** — this isn't a single-tool hack; it touches code, infra, security, process, and governance. Architecturally ambitious.
- **The sequenced PR queue is the killer demo moment** — judges see a GitHub repo go from "blocked release" to "7 PRs open, ordered, ready to approve" in under 60 seconds.
- **Clear enterprise path** — `releasegate.yml` as a policy file is immediately recognisable as something a platform team would adopt. It's a product, not a script.
- **Composable with the AutoPatch AI companion** — the code-fix resolver can delegate to AutoPatch for complex bug resolution, showing a coherent AI-native engineering platform vision.

---

