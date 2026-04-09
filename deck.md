---
marp: true
theme: default
paginate: true
size: 16:9
footer: "DDEV — AI harness proposal (discussion draft)"
style: |
  section {
    justify-content: flex-start;
  }
  section.lead h1 {
    font-size: 2.2em;
  }
  table {
    font-size: 0.72em;
  }
  th, td {
    padding: 0.35em 0.5em;
    vertical-align: top;
  }
---

<!-- _class: lead -->

# AI harness for **ddev**

Observable, measurable AI-assisted contributions — without reinventing the wheel

---

## Agenda

- Problem & goal — mixed tools, no provenance, drifting models.
- **Harness-first** — plan → generate → verify → telemetry → feedback.
- Options, building blocks, environments, **metrics**.
- CI gates, variability, governance, **first deliverables**.
- References & **maintainer CTA**.

---

## Why now

- ddev is moving toward **AI-assisted development** alongside human review.
- Contributors already use **Claude Code, Codex, Cursor**, and other tools — **no shared provenance**.
- **Black-box models** drift day to day; identical prompts can yield different results.
- We need **project standards, verification, and telemetry** that work for **any contributor**.

---

## Goal

Make AI-assisted contributions:

- **Consistent** — same gates and artifacts for every PR.
- **Observable** — unified telemetry across local, CI, staging, and shadow runs.
- **Measurable** — KPIs and time series, not vibes.

Automation and monitoring so model-driven changes stay **safe, reproducible, and auditable**.

---

## Harness-first (high level)

**Constrain and instrument** the workflow so agents can explore, while the **harness proves correctness**.

Tight feedback loops: fast local checks, strict CI, shadow/canary signal, then feed results back into prompts and rules.

![w:900](./assets/harness-loop.svg)

---

## Candidate approaches (overview)

| Approach | Scope | Strengths | Weaknesses | Best for |
|----------|-------|-----------|------------|----------|
| **Harness-first** | Repo + CI + runtime | Fast automated verification; tight loops | Upfront engineering + telemetry | Large-scale agent-driven change |
| **Impact map + gated plan** | Planning only | Catches structural mistakes early; human checkpoint | Needs repo scanning + map tooling | Feature planning / scoping |
| **Reference / community harnesses** | Reusable parts | Best practices; faster adoption | Adaptation to ddev | Bootstrapping primitives |

**Stance:** combine — **plan gates** + **verification harness** + **reuse** where it fits.

---

## Core components (1/2) — observable & measurable

**Agent registry & fingerprinting** — provider, model id, prompt/toolchain snapshot → machine-readable PR header; trend by model.

**Structured task spec** — short machine-readable **repository impact map** before generation (files, APIs, tests).

**Generation harness** — isolated runs: repo snapshot, LSP/MCP where allowed, **strict write scopes** → track out-of-scope edits and “hallucination” paths.

---

## Core components (2/2)

**Automated verification suite** — static analysis, units, **structural/layering** rules, security linters, behavioral/golden tests, property checks.

**Shadow / canary + observability** — integration + canary with logs, metrics, traces; compare to baseline.

**Feedback loop & harness evolution** — verification + telemetry **updates prompts, constraints, tests**.

**Audit trail & provenance** — fingerprint → plan → diff → verification → deploy telemetry (**immutable** story per change).

---

## Test environments (recommended stack)

| Layer | Role |
|-------|------|
| **Local dev harness** | Fast, deterministic — pre-commit: generation checks + linters |
| **CI harness** | Gated PRs: impact map validation, static + unit/integration + structural |
| **Staging shadow** | Representative workloads + telemetry |
| **Canary / shadow production** | Feature flags / shadow traffic — non-user-impact validation |
| **Replay sandbox** | Deterministic replay of failing traces |

**Requirement:** standardized events/metrics to a **central observability store** so runs compare across machines and models.

---

## Metrics to track (examples)

- **Agent provenance coverage** — % of PRs with agent metadata.
- **Plan–change fidelity** — impact map vs final diff.
- **Verification pass rate** — first-run green for agent PRs.
- **Median human review time** — per AI-generated PR.
- **Hallucination incidents** — invented APIs/paths (count / PR).
- **Regression delta** — error rate & latency vs baseline in shadow.
- **Model variance index** — day-over-day spread for same prompt + snapshot.
- **Harness tightness** — rule/prompt updates vs pass-rate movement.

Time series + **alert thresholds** on regressions.

---

## CI: pre-merge vs post-merge

**Required before merge**

- Valid **agent metadata** (see RFC / `AGENTS.md`).
- **Repository impact map** validated (scanner + diff matcher).
- Formatters + static linters.
- Unit tests + structural layering tests.
- Optional **harness score** (0–100) from checks — policy sets minimum later.

**After merge**

- Integration in **staging shadow**.
- **Canary** with telemetry gates; **auto-rollback** if thresholds fail.

---

## Model & product variability

- **Pin context** — commit hash, prompt template id, toolchain in metadata → reproducibility.
- **Seeded experiments** — N parallel generations; pick best by harness score when nondeterministic.
- **Shadow evaluation** — run multiple agents/models in parallel; surface diffs to reviewers.
- **Drift monitoring** — model variance index; alerts tie to stricter rules or rollback.

We **don’t** need one vendor — we need **one harness** that works across them.

---

## Governance & contributor policy

- **PR template** — agent metadata, plan artifact, harness score (when enabled).
- **Allowlist** — approved tool/model versions; CI can **block** unknown agents (policy TBD with maintainers).
- **Reviewer checklist** — provenance, plan fidelity, shadow results.
- **Docs** — `AGENTS.md` + short **harness playbook** (local checks, reading telemetry).
- **Humans without agents** — same verification path; metadata marks **manual** contribution.

---

## Low-effort starting steps

1. **Issue** in ddev/ddev — “AI harness: proposal + checklist” — collect constraints & owners.
2. **`AGENTS.md`** — minimal metadata schema + PR expectations.
3. **Prototype CI job** — validate metadata + static checks + simple harness score.
4. **Repo scanner** — emit impact map artifact per ticket/PR.
5. **Two-week experiment** — noncritical area; measure the metrics above.

Reuse **community patterns** (references slide) instead of bespoke everything.

---

## Deliverables (priority order)

1. **Issue + RFC** — harness policy, metrics, rollout (this deck as starting body).
2. **PR template + `AGENTS.md`** — metadata + planning artifacts enforced in review.
3. **Minimal CI harness** — metadata validation, linters, harness score v0.
4. **Repo scanner prototype** — impact maps for humans + tooling.
5. **Shadow-run pipeline** — staging + telemetry for generated changes.

---

## References

- [Observability-driven harnesses — Datadog](https://www.datadoghq.com/blog/ai/harness-first-agents/)
- [Harness engineering (Codex) — OpenAI](https://openai.com/index/harness-engineering/)
- [Structured workflows — Red Hat Developer](https://developers.redhat.com/articles/2026/04/07/harness-engineering-structured-workflows-ai-assisted-development)
- [awesome-harness-engineering — walkinglabs](https://github.com/walkinglabs/awesome-harness-engineering)

---

<!-- _class: lead -->

## Call to action

**Open a maintainer thread:** RFC + tracking issue — align on scope (“metadata only” vs full shadow stack), owners, and **pilot** boundaries.

Offer optional follow-ups: pasted **RFC body**, **PR template**, and **AGENT_METADATA** schema for GitHub.

---

## Summary

- **Harness-first** = provenance + structured plan + scoped generation + automated verification + telemetry + feedback into rules.
- **Phase thoughtfully:** RFC + `AGENTS.md` + CI prototype + impact-map scanner → then shadow/canary when maintainers are ready.
- **Presenter hint:** OSS can’t mandate one IDE — **CI and artifacts** are the shared contract.
