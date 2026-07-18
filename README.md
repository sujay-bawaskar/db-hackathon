# DB Hackathons — 2026-07

Workspace for the July 2026 hackathon. General AI, GCP-focused, Java stack.

## Goals

- Capture ideas, refine them, and pick one to build.
- Keep a lightweight paper trail so the team (and future Devin sessions) can pick up where we left off.
- Java-first: Spring Boot for services, Maven/Gradle for builds, JUnit for
  tests. Reach for other languages only when a GCP SDK or a demo surface
  (e.g. a web UI) makes Java impractical — and record that choice in
  `decisions/`.

## Repo layout

```
.
├── README.md          # this file
├── AGENTS.md          # rules for Devin / AI assistants working in this repo
├── ideas/             # one markdown file per candidate idea
│   └── SEED-LIST.md   # starter ideas to react to / refine
├── templates/         # copy-and-fill templates
│   ├── idea-pitch.md
│   ├── scoring-rubric.md
│   └── team-log.md
├── notes/             # freeform research / scratch notes
└── decisions/         # dated decision records (ADR-style)
```

## Workflow

1. **Diverge** — skim `ideas/SEED-LIST.md`, copy `templates/idea-pitch.md` into
   `ideas/<short-name>.md` for anything worth fleshing out.
2. **Converge** — score each pitched idea with `templates/scoring-rubric.md`.
3. **Commit** — record the pick in `decisions/<date>-pick.md` explaining why.
4. **Build** — start a `team-log.md` from `templates/team-log.md` and track
   daily progress there.

## Conventions

- Filenames: lowercase, hyphenated, date-prefixed for decisions
  (`2026-07-18-pick.md`).
- One idea per file. Keep pitches short — aim for a 5-minute read.
- Don't delete rejected ideas; mark them `Status: Rejected` at the top so the
  reasoning survives.
