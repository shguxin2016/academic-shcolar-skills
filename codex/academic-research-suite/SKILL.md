---
name: academic-research-suite
description: >
  Codex-native Academic Research Skills router for deep research, literature
  review, systematic review, academic paper writing, manuscript review,
  citation checks, disclosure, full research-to-paper pipelines, and experiment
  planning or validation. Supports aliases such as ars-plan, ars-outline,
  ars-abstract, ars-lit-review, ars-citation-check, ars-disclosure,
  ars-format-convert, ars-revision-coach, ars-revision, and ars-full.
metadata:
  version: "0.1.0"
  upstream_suite: "academic-research-skills"
  codex_adapter: true
---

# Academic Research Suite for Codex

This is the Codex router for the Academic Research Skills suite. It expects the
suite content to live under `ars/`. Install with:

```powershell
.\scripts\install-codex-skill.ps1 -Force
```

or:

```bash
./scripts/install-codex-skill.sh --force
```

After installation, restart Codex or open a new Codex conversation and invoke:

```text
Use $academic-research-suite to plan a literature review on ...
```

## First Rule

Do not load the whole suite by default. Select one workflow, read that
workflow's `SKILL.md`, then load only the agent, reference, template, or shared
files needed for the user's current stage.

## Workflow Router

Choose the workflow by intent:

| User intent | Read first |
|---|---|
| Deep research, literature review, systematic review, meta-analysis, fact-checking, research-question refinement | `ars/deep-research/SKILL.md` |
| Academic paper writing, paper outline, abstract, revision, citation formatting, AI disclosure, LaTeX/DOCX/PDF formatting guidance | `ars/academic-paper/SKILL.md` |
| Paper review, peer review simulation, editorial decision, reviewer calibration, re-review after revision | `ars/academic-paper-reviewer/SKILL.md` |
| End-to-end research-to-paper pipeline, integrity gates, staged review/revision/finalization workflow | `ars/academic-pipeline/SKILL.md` |

If a request spans multiple workflows, start with
`ars/academic-pipeline/SKILL.md` unless the user clearly asks for a single
phase.

## Paper Topic Scoping Override

If the user wants to write a paper, thesis, proposal, article, or manuscript,
but only provides a broad topic, tentative title, or research direction without
a clear answerable research question, route to `ars/deep-research/SKILL.md` in
Socratic mode first.

First response in this path:

1. State that the request is being routed to deep-research Socratic mode because
   the research question is not yet precise.
2. Ask 3-5 narrowing questions.
3. Do not produce an outline, draft, literature review, or full pipeline
   dashboard until at least one candidate research question is clear.

## Alias Router

Codex does not install Claude slash commands, but this package emulates their
intent through plain aliases:

| Alias | Route |
|---|---|
| `ars-plan` | `ars/academic-paper/SKILL.md` in plan mode |
| `ars-outline` | `ars/academic-paper/SKILL.md` in outline-only mode |
| `ars-abstract` | `ars/academic-paper/SKILL.md` in abstract-only mode |
| `ars-lit-review` | `ars/academic-paper/SKILL.md` lit-review mode, or `ars/deep-research/SKILL.md` lit-review mode when source discovery is needed |
| `ars-citation-check` | `ars/academic-paper/SKILL.md` citation-check mode |
| `ars-disclosure` | `ars/academic-paper/SKILL.md` disclosure mode |
| `ars-format-convert` | `ars/academic-paper/SKILL.md` format-convert mode |
| `ars-revision-coach` | `ars/academic-paper/SKILL.md` revision-coach mode |
| `ars-revision` | `ars/academic-paper/SKILL.md` revision mode |
| `ars-full` | `ars/academic-pipeline/SKILL.md` full workflow |

If a slash-prefixed alias is intercepted by the client, tell the user to use the
plain alias form, for example `ars-plan my topic`.

## Codex Runtime Mapping

- Treat upstream "agent" files as scoped role prompts to perform inline.
- Do not spawn Codex subagents automatically. Use subagents only when the user
  explicitly asks for parallel delegation.
- Use Codex web browsing for current facts, source verification, citation
  checks, and external evidence, and provide source links.
- Treat Claude-specific hook and plugin metadata as upstream traceability only.
- Preserve independence in multi-review phases: write each reviewer section
  before synthesizing.

## Shared Resources

Resolve upstream relative paths under `ars/`:

- `shared/...` -> `ars/shared/...`
- `scripts/...` -> `ars/scripts/...`
- `examples/...` -> `ars/examples/...`
- `docs/...` -> `ars/docs/...`

If `ars/` is missing, stop and ask the user to run the Codex installer script
from the repository root.
