---
name: ultrabuild
description: Use automatically when the user asks to build a new app, web app, system, platform or software project from scratch (from zero, from a rough plan). Runs a staged Claude Code workflow (requirements, architecture, UI design, build, audit, security, deploy) that maximizes ultracode multi-agent workflows and per-stage effort levels, and auto-invokes companion skills.
---

# Ultrabuild

A staged workflow for building an application **from zero** with Claude Code. It is built around two levers:

- **Ultracode**: multi-agent workflows (the Workflow tool) for the stages where breadth, independent perspectives and adversarial verification pay off.
- **Effort**: the reasoning depth of the main session and of every subagent, set per stage.

Always reply in the user's language. Be concise and results-driven. Never claim progress or success you have not verified.

## When to use

Use this skill **automatically** whenever the user asks to build a new app, web app, system, platform or software project from scratch, including when they send a rough plan for a new app. The user does not need to invoke it.

Do not use it for revisions, bug fixes or new features in an existing app unless the user asks.

## Core principle: maximize available skills automatically

The user wants to work hands-off. **Do not wait for the user to name or invoke a skill.**

1. At the start of the project, check which skills and plugins are available in this session (the skill list in context, `/skills`, or `claude plugin list`). Note what is installed.
2. At each stage, invoke every relevant skill from the **Skill map** below yourself, via the Skill tool, without asking. Announce it in one line, e.g. "Stage 3 uses: impeccable."
3. If an installed skill that is not on the map clearly fits the current work (match it against its description) and does not duplicate a mapped skill, use it too.
4. If a mapped skill is not installed, skip it without asking, apply its principles manually, and mention once in the stage report which skill was missing and how to install it (see Companion skills).
5. Still ask first for skills that send anything externally on the user's behalf (email, posts, public publishing), that bill per use, or that delete data.
6. One skill per job. When two installed skills cover the same job, use the one named in the Skill map and skip the other, so findings are not duplicated and tokens are not wasted.

## Ultracode permission

- If the user invoked this skill (`/ultrabuild` or "use the ultrabuild skill"), that counts as permission to run the Workflow tool at every stage marked **ULTRACODE REQUIRED**. Run it directly.
- If the skill was triggered automatically, ask once, at the first ultracode stage of the project: "Run all ultracode stages automatically for this project? (yes/no)". A "yes" covers the whole project; do not ask again. On "no", run those stages without the Workflow tool.
- If the user's prompt already contains the word "ultracode", do not ask.
- Load the `workflow-authoring` skill before writing a Workflow script.
- If the Workflow tool is not available, tell the user once that dynamic workflows may be turned off and can be enabled with `/config` ("Dynamic workflows"); until then, approximate the same shape with parallel subagents (Agent tool).
- Ultracode and Max effort consume plan usage quickly. This skill is designed for the Claude **Max** or **Ultra** plans. If the user mentions hitting usage limits, suggest running the optional ultracode stages normally and keeping ultracode for Stages 2, 4c and 5 only.

## Execution labels

- **ULTRACODE REQUIRED (Path A)**: run the Workflow tool without asking (once permission above is settled). Announce in one line, then execute.
- **ULTRACODE OPTIONAL (Path B)**: show a short confirmation (scope, estimated agent count, why it is optional) and wait for "ok". On "no", do it normally.
- **NORMAL**: do it directly, without the Workflow tool and without asking about ultracode.

A required stage drops to optional when the project is small: fewer than about 5 modules AND no money, balances, payments or sensitive data. Financial or regulated projects (payments, wallets, balances, health, identity) never drop.

## Effort per stage

| Stage | Main-session effort |
|---|---|
| 1. Requirements | High |
| 2. Plan + architecture | Extra (xhigh); **Max** for financial or regulated projects |
| 3. UI design | High |
| 4a. Foundation | Extra (xhigh) |
| 4b. Independent modules | Extra (xhigh) |
| 4c. Critical modules (payments, balances, auth) | Max |
| 4d. Integration | Extra (xhigh) |
| 5. Functional + security audit | Max |
| 6. Ordinary bugs | High |
| 6. Stuck bugs | Max |
| 7. Deploy | High |

### Effort inside workflows (automatic)

Every `agent()` call in a Workflow script must set `effort` explicitly:

- Agents that think hard (design, build, judge, verify, skeptic): the stage's effort from the table (`xhigh` or `max`).
- Mechanical agents (listing files, running lint or tests, summarizing output): `medium`, to save tokens.
- Subagents delegated through the Agent tool in normal stages: the stage's effort.

### Main-session effort reminder

A skill cannot change the main session's effort. The user changes it with `/effort` (or the effort picker in their app).

1. In every stage-gate message (end-of-stage report), add one line: "Next stage: <stage>. Recommended effort: <level>."
2. When a stage starts (including Stage 1):
   - If the current effort is **visible** in context and **matches**: continue without a reminder.
   - If it is **visible** and **does not match**: send a one-line reminder ("Effort is <X>; this stage recommends <Y>. Continuing automatically in 5 minutes if there is no reply."). If the environment offers a way to schedule a message back into this session (for example `send_later`), schedule "Continue <stage> of ultrabuild; no reply about effort." for 5 minutes later and end the turn. If the user replies first, cancel the scheduled message and follow the reply. If the scheduled message arrives, continue with the current effort. If no scheduling tool exists, remind and continue immediately.
   - If the effort is **not visible**: write one line, "Recommended effort for this stage: <level>", and continue without waiting.

## Skill map (auto-invoked when installed)

**Always on (passive, nothing to invoke):**

- `security-guidance` (plugin hooks): checks every edit and diff for injection, XSS, SSRF, hardcoded secrets and similar issues. Never disable it. When it warns, fix the issue before moving on.

| Stage | Skills invoked automatically |
|---|---|
| 1. Requirements | none required; optionally a research skill for competitors or regulations if the domain is unclear (ask first if it bills per use) |
| 2. Plan + architecture | `engineering:system-design` and `engineering:architecture` as inputs to the judge panel; `diagram-design` for architecture, data-flow and ER diagrams; `security-audit` in **guidance mode** for an initial threat model (not a full audit) |
| 3. UI design | `impeccable` (primary design skill: direction, design system / DESIGN.md, critique, UX copy, accessibility, responsive) |
| 4a, 4c and backend parts of 4b/4d | `ponytail` (mode `full`) |
| Frontend parts of 4b/4d | `impeccable` (build to the Stage 3 handoff, then polish), without ponytail |
| 4c. Critical modules | `security-audit` focused on that module, `ponytail-review` on the module's diff, then the ultracode review |
| 5. Audit | `security-audit` (full audit), the ultracode audit workflow, `impeccable` audit for UI, dependency and license checks (below) |
| 6. Bug fixes | `engineering:debug`; `ponytail-review` only for fixes in critical modules |
| 7. Deploy | `engineering:deploy-checklist`, `engineering:documentation` (README, runbook), `ponytail-debt` (make sure no critical shortcut is left behind) |

**Fallbacks:** if `impeccable` is missing, use the Anthropic `design` plugin skills (`design:design-system`, `design:design-critique`, `design:accessibility-review`, `design:ux-copy`, `design:design-handoff`). If `security-audit` is missing, the ultracode audit covers security alone (see Stage 5). If the `engineering` plugin is missing, do the same work manually.

**Deliberately not used together (to avoid duplicate reviews):**

- `engineering:code-review` and `ponytail-audit` are not run in Stage 5: the ultracode audit plus `security-audit` already cover correctness and security, and a third reviewer only repeats findings.
- `ponytail-review` is not run on every diff: `security-guidance` already reviews each diff, so `ponytail-review` is reserved for critical modules and their fixes.
- The `design:*` skills are not run alongside `impeccable`; they are a fallback only.

### Dependency and license checks (Stages 5 and 7)

Use the stack's native tools; no extra plugin is needed:

- Vulnerabilities: `npm audit` / `pnpm audit`, `pip-audit`, `composer audit`, `govulncheck`, or `osv-scanner` when available.
- Licenses: `npx license-checker --summary` (Node) or `pip-licenses` (Python). Flag GPL, AGPL and non-commercial licenses for commercial products.
- If a tool is not installed and cannot be installed, record that as a limitation in the report.

## Ponytail for backend

Ponytail keeps code minimal: the smallest change that fully works, reusing existing code, the standard library and installed dependencies. Validation, error handling, security and accessibility are never cut.

- **On** for backend work: Stage 4a, backend parts of 4b, Stage 4c, backend parts of 4d, and backend bug fixes in Stage 6. Activate with `/ponytail full`.
- **Off** for design and frontend: Stage 3 and UI files during build. Ponytail stays on for the whole session, so turn it off ("stop ponytail" or `/ponytail off`) before design or frontend work and back on when returning to backend.
- In Workflow scripts, subagents may not inherit the ponytail mode. Put the ponytail principles explicitly into every backend agent's prompt, and not into frontend or design agents.

## Security audit skill

`security-audit` (Cloudflare) is the primary security auditor: reconnaissance, hunting by isolated hunters, validation by a verifier that tries to disprove each candidate, then a report (`findings.json` with confirmed / needs_validation / rejected verdicts, `REPORT.md`, `FINDINGS-DETAIL.md`, `NEEDS-VALIDATION.md`).

- It runs its full workflow only on an explicit request. In Stage 5, ask it explicitly, e.g. "run a full security audit of this repository". In Stages 2 and 4c, ask for guidance mode or a focused review.
- It needs Node.js and an OS sandbox to run target code. Without a sandbox, run it without executing target code and note the limitation.
- Keep its output in the default location or in a folder ignored by version control. Never write audit output into tracked folders.
- It does not replace ultracode. Ultracode remains the second verification layer and covers project-specific business logic.

## Stage gates

After each stage: report briefly, name the next stage and its recommended effort, then wait for the user to say "continue". Exception: the sub-stages inside Stage 4 may run back to back unless there is a blocker, but still run the effort reminder at the start of each sub-stage.

## Stages

### Stage 1: Requirements (NORMAL, effort High)

- Review the user's rough plan.
- Find the gaps: target users, scale, core vs nice-to-have features, third-party integrations, platforms, budget, deadline, regulation.
- Ask the 3 to 6 clarifying questions that matter most (use AskUserQuestion if available).
- Write a requirements document with an explicit list of assumptions.
- **Do not produce a plan or an architecture in this stage.**

### Stage 2: Full plan + architecture (ULTRACODE REQUIRED, effort Extra / Max for financial)

Workflow shape: judge panel.

1. Produce 3 architectures independently from different angles: fast MVP, scale/performance, security/compliance.
2. Score them with parallel judges on fit to requirements, cost, complexity, risk and maintainability.
3. Synthesize from the winner and graft the best ideas from the runners-up.

The plan must include:

- Stack, architecture, data model, API contracts between modules.
- A module list where every module has a **build label** (`foundation`, `independent`, `critical`, `integration`) and a **layer label** (`backend` / `frontend`), so the ponytail and impeccable rules are unambiguous.
- Build order.
- **Test plan**: main flows and edge cases per feature.
- **Security requirements**: authentication, authorization, input validation, secrets, rate limiting, logging, plus compliance where relevant.
- Risks and mitigations.

### Stage 3: UI design (NORMAL, effort High; variations = ULTRACODE OPTIONAL; no ponytail)

Skip this stage if the app has no UI. `impeccable` is the primary skill.

- Ask for 2 or 3 visual references if none were given.
- Lock the design system: colors, typography, spacing, component style (DESIGN.md / tokens).
- Build mockups of the main screens, then critique them yourself (critique + accessibility) before showing the user.
- Optional (Path B): generate 3 or 4 design directions in parallel so the user can pick one.
- Iterate small revisions normally.
- Close the stage with a handoff spec that the frontend build follows.

### Stage 4: Build

**4a. Foundation (NORMAL, effort Extra, ponytail on)**: scaffold the repo, folder structure, database schema and migrations, shared types and config, core auth, test and lint setup. Commit before moving on.

**4b. Independent modules (effort Extra; ponytail for backend only)**:

- ULTRACODE REQUIRED when ALL hold: the foundation is committed, there are at least 3 modules labeled `independent`, API contracts are written down, and there is a way to verify (tests, lint, build).
  - Workflow shape: a pipeline per module; one build agent per module with `isolation: 'worktree'`, then one verify agent per module (run tests, check the contract).
  - Frontend follows the Stage 3 handoff.
- ULTRACODE OPTIONAL when only some conditions hold.
- NORMAL otherwise: build modules one by one.

**4c. Critical modules (build NORMAL, then review ULTRACODE REQUIRED; effort Max; ponytail on)**: payments, balances, webhooks, advanced auth, money calculations.

- Build them sequentially and normally.
- As soon as one critical module is done, run `security-audit` focused on that module, then an adversarial ultracode review with several lenses (correctness, security, race conditions and idempotency, edge cases) and skeptics prompted to refute. Feed the `security-audit` findings into that review for verification. Fix confirmed findings.

**4d. Integration (NORMAL, effort Extra; ponytail for backend only)**: wire the modules together and test the end-to-end flows.

### Stage 5: Functional + security audit (ULTRACODE REQUIRED, effort Max)

Workflow shape: review per dimension, adversarial verification, loop until no new findings.

- **Functional dimension**: run the Stage 2 test plan, main flows, edge cases, error handling, and UI consistency (mobile and accessibility when there is a UI, using `impeccable` audit).
- **Security dimension**:
  1. If `security-audit` is installed, run a full audit of the repository first.
  2. The ultracode workflow then (a) adversarially re-verifies every `confirmed` and `needs_validation` finding from `security-audit`, and (b) checks what is specific to this project: the Stage 2 security requirements, business logic (idempotency, balance race conditions, amount tampering, webhook replay) and per-endpoint authorization.
  3. If `security-audit` is not installed, the ultracode workflow covers everything: the Stage 2 security requirements, OWASP Top 10, secrets, per-endpoint authorization, injection, dependencies and business logic.
- Run the dependency and license checks.
- Normal projects: one workflow for both dimensions. Financial or regulated projects: two separate workflows.
- Output: verified findings, most severe first, with fixes. Link the `security-audit` `REPORT.md` when it ran. Fix critical and high findings, then re-run a focused `security-audit` on the fixed areas to confirm.

### Stage 6: Manual testing + bug fixing (NORMAL, effort High; escalation effort Max)

- The user tests manually and reports bugs.
- Ordinary bugs: fix them normally, without asking. Ponytail on for backend bugs, off for frontend bugs.
- Escalate to ULTRACODE REQUIRED with effort Max (a focused review, not a full audit) when:
  - a bug is still unresolved after 2 or 3 normal attempts. Workflow shape: several agents test different root-cause hypotheses in parallel.
  - a fix touches a `critical` module. Run a short adversarial review of that change, plus `ponytail-review`.

### Stage 7: Deploy + go-live (NORMAL, effort High)

Checklist:

- Production environment configuration and secrets.
- Database migrations, with a backup before migrating.
- Rollback plan and rollback triggers.
- Monitoring, logging and alerts.
- Post-deploy smoke test.
- Final dependency and license check, and `ponytail-debt` for leftover shortcuts.

Report go-live status with verified results only.

## General rules

- Never fabricate progress or results. Verify before claiming something is done.
- On a blocker, report briefly: the problem, what was tried, and the recommended next step.
- When a workflow limits coverage (sampling, top-N), state what was not covered.

## Companion skills

Recommend these once, at the start of the project, if they are missing:

| Skill | Role | Install (Claude Code) |
|---|---|---|
| `security-guidance` | Passive security review of every edit | `/plugin install security-guidance@claude-plugins-official` |
| `security-audit` | Full and focused security audits | `npx skills add https://github.com/cloudflare/security-audit-skill --skill security-audit --global` |
| `ponytail` | Minimal backend code | `/plugin marketplace add DietrichGebert/ponytail` then `/plugin install ponytail@ponytail` |
| `impeccable` | Frontend design and UI audit | `/plugin marketplace add pbakaus/impeccable` then `/plugin install impeccable@impeccable` |
| `diagram-design` (optional) | Architecture diagrams | `/plugin marketplace add cathrynlavery/diagram-design` then `/plugin install diagram-design@diagram-design` |
| Anthropic `engineering` plugin (optional) | system design, architecture, debug, deploy checklist, docs | `/plugin` > Discover > "engineering" |
