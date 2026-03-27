---
title: "Claude Code in Desktop: Setup & Operational Guide"
version: "1.0.0"
updated: "2026-03-26"
authority: "PRD Led Context Engineering"
relates_to: "docs/OPERATIONAL_FLOW.md"
---

> **Purpose**: This is the hands-on operating guide for running Claude Code inside the
> Claude Desktop app — the recommended primary environment for executing this methodology.
> It covers everything from first-time setup through a repeatable session pattern for
> both new product development and ongoing Epic-by-Epic iteration on an existing product.
>
> Use this document when:
> - Setting up Claude Desktop's Code tab for the first time on a new or existing repo
> - Starting any new project (v0.1 → v1.0 green-field flow)
> - Launching the next Epic cycle on an existing product (the repeatable loop)
> - Handing off Perplexity research exports into the pipeline
> - Running background agent sessions while working in Cowork or Perplexity in parallel
>
> **The core distinction from the VS Code terminal guide**: Claude Desktop's Code tab
> runs the same underlying Claude Code engine as the CLI — hooks fire correctly, shell
> scripts execute, and sessions are portable (`/desktop` ↔ terminal). It adds visual
> diff review, in-window app preview, and background PR monitoring. For this methodology,
> Desktop is the preferred primary interface.
>
> **What this document does not cover**: Which tool does what at each PRD stage, or why.
> For that, see `docs/OPERATIONAL_FLOW.md`. For the hook and skill specifications
> themselves, see `.claude/hooks/HOOK_CONTRACT.md` and `.claude/skills/README.md`.

---

## Legend

| Symbol | Who / What |
|--------|------------|
| 🔵 **YOU** | Human decision, input, or manual action required |
| 🟣 **PERPLEXITY** | External research in the Perplexity browser tab |
| 🟡 **CLAUDE DESKTOP — Code Tab** | Active Claude Code session with hooks firing |
| 🟤 **COWORK** | Claude Cowork / Chat — strategic prompting, doc review |
| 🔴 **HOOK** | Automated event fired by `settings.json` — no action needed |
| 🟢 **SKILL** | Invoked by you or Claude to run a structured task |
| 🟠 **SUB-AGENT** | Spawned by Claude for a parallel delegated task |
| 🟦 **v0** | UI code generation from UJ- specs |
| ⬛ **VERCEL** | Deployment and live preview — fires on git push |

---

# PART 1: FIRST-TIME SETUP

## Step 0 — Prerequisites Checklist

Before starting, confirm each item:

- [ ] **Claude Desktop** is installed — download at [claude.ai/download](https://claude.ai/download)
- [ ] **Claude Code CLI** is installed — open your Mac Terminal and run:
  ```bash
  claude --version
  ```
  If this returns "command not found," install via [claude.ai/code](https://claude.ai/code)
- [ ] **Your repo is cloned locally** — you have a local folder on your Mac containing
  `README.md`, `PRD.md`, `.claude/`, `SoT/`, `temp/`, and `epics/`
- [ ] **Git is configured** — in Terminal, run:
  ```bash
  git config --global user.name "Your Name"
  git config --global user.email "you@email.com"
  ```
- [ ] **Claude Max 5x or higher** subscription is active — hooks and background agents
  require a plan that supports sub-agents and extended sessions

---

## Step 1 — Open Your Project in Claude Desktop Code Tab

1. Open **Claude Desktop** on your Mac
2. Look for the **Code** tab in the top navigation (distinct from the Chat/Cowork tab)
3. Click **Open Project** or **Open Folder**
4. Navigate to your local repo folder — select the **root folder**
   (the one containing `README.md` and `.claude/`) — not a subfolder
5. Click **Open**

> **Why the root folder matters**: Claude Code sets `$CLAUDE_PROJECT_DIR` to whatever
> folder you open. Your hooks reference this variable to find their scripts. If you open
> a subfolder, the hooks cannot locate their files and will silently fail.

---

## Step 2 — Verify Hooks Are Wired

Do this once after opening a new project for the first time.

In the Claude Desktop Code tab, look for the **Terminal** panel (usually at the bottom).
If it's not visible: `View → Terminal`.

Run this test command:

```bash
echo '{}' | bash "$CLAUDE_PROJECT_DIR"/.claude/hooks/context-validation.sh
```

**Expected result**: JSON output with no errors. If you see an error, check that:
- You opened the correct root folder (Step 1)
- The hook files exist at `.claude/hooks/context-validation.sh`
- The files have execute permission — if not, run:
  ```bash
  chmod +x .claude/hooks/*.sh
  ```

---

## Step 3 — Confirm Your Agents Are Loaded

Your repo has five named agents in `.claude/agents/`:

| Agent | Role | Lifecycle Stage |
|-------|------|-----------------|
| **HORIZON** | Strategy, market, risk | v0.1–v0.5 |
| **STUDIO** | Design, UX, journeys | v0.4–v0.6 |
| **WERK** | Architecture, build | v0.6–v0.8 |
| **DEVLAB** | Testing, QA | v0.7–v0.8 |
| **METRO** | GTM, launch, feedback | v0.9 |

Each agent has an `AGENT.md` (role definition) and `MEMORY.md` (persistent session state).
No action needed — Claude loads these automatically when sub-agents are spawned. This
confirmation step is just for your awareness.

---

# PART 2: NEW PROJECT FLOW (v0.1 → v1.0)

This section covers starting a brand new product from scratch. If you are continuing
an existing product into a new Epic cycle, skip to **Part 3**.

---

## SESSION 1: Project Initialization

### EVENT 1 — 🔵 YOU: Create the project file structure

If your repo does not yet have a `PRD.md` or Active Epic:

1. In the Claude Desktop Code tab file tree, confirm these folders exist:
   - `epics/`
   - `SoT/`
   - `temp/`
   - `.claude/`
2. If `PRD.md` does not exist yet, you will create it in Event 3

### EVENT 2 — 🔴 HOOK: `SessionStart` fires `context-validation.sh`

**Automatic — no action needed.**

The moment you type your first message in the Code tab session, this hook fires and:
- Checks for `README.md`, `PRD.md`, `CLAUDE.md` accessibility
- Injects the 3+1 file reading order into Claude's context
- Outputs a pass/fail status

If it fails (PRD.md missing, wrong folder open), it tells you what's missing before
Claude responds. Fix before proceeding.

### EVENT 3 — 🔵 YOU + 🟡 CLAUDE DESKTOP: Initialize the project

Type your session-opening prompt:

```
@README.md @CLAUDE.md

I'm starting a new project. PRD.md does not exist yet.

Product concept: [describe your product idea in 2-3 sentences]

Please:
1. Create PRD.md using the standard template for this repo
2. Set version to v0.1
3. Confirm you have loaded 3+1 context and are ready to begin
   the v0.1 Problem Framing phase
```

Claude creates `PRD.md`, writes the template structure, and confirms readiness.

### EVENT 4 — 🔴 HOOK: `UserPromptSubmit` fires `context-density-gate.sh`

**Automatic — fires on every prompt.**

This hook evaluates your prompt for sufficient context density. At v0.1, there are no
IDs yet — so the gate is lenient. As you progress to v0.7+, it will require `BR-`, `UJ-`,
and `API-` references. You do not need to do anything — just be aware it is running.

---

## SESSION 1 (continued): v0.1 — Problem Framing

### EVENT 5 — 🟡 CLAUDE DESKTOP + 🟢 SKILL: Problem framing interview

Prompt:

```
Invoke prd-v01-problem-framing.
Interview me to establish a precise problem statement.
Do not accept a solution — only a problem.
```

Claude runs the skill as a structured interview. Answer its questions. This is internal
clarity work — no external research needed yet.

### EVENT 6 — 🟡 CLAUDE DESKTOP + 🟢 SKILL: User value articulation

After the problem interview completes:

```
Invoke prd-v01-user-value-articulation.
Structure the value proposition from our conversation and
identify how outcomes will be measured.
```

Claude writes the v0.1 section of `PRD.md` and creates initial stub IDs.

### EVENT 7 — 🟡 CLAUDE DESKTOP + 🟢 SKILL: ID registration

```
Invoke ghm-id-register.
Register all IDs created in this session into the appropriate SoT files.
```

Claude writes stub entries to `SoT/`.

### EVENT 8 — 🔴 HOOK: `Stop` fires `sot-update-trigger.sh`

**Automatic — fires after every Claude response.**

Reminds Claude to cascade any `temp/` findings to `SoT/` and update the Epic Session
State before you finish the session. You will see a system message in the Code tab.

### EVENT 9 — 🟡 CLAUDE DESKTOP + 🟢 SKILL: Gate check

```
Invoke ghm-gate-check for v0.1.
Confirm DoD: problem defined, outcomes measurable, open questions listed.
```

If gate passes: move to v0.2.
If gate fails: Claude tells you exactly what is missing. Address it before continuing.

### EVENT 10 — 🔵 YOU: Commit and push

In the Claude Desktop Code tab Source Control panel (or via the terminal):

```bash
git add -A
git commit -m "PRD v0.1: problem framing complete"
git push
```

---

## SESSION 2: v0.2 — Market Definition

### EVENT 11 — 🔴 HOOK: `SessionStart` fires (new session)

Opens cleanly. Claude re-loads 3+1 context. Confirm it identifies v0.1 as complete
and v0.2 as the active stage.

Opening prompt:

```
@README.md @PRD.md @CLAUDE.md

Resuming from v0.1 complete. We are beginning v0.2 Market Definition.
Confirm active stage and what you need from me to proceed.
```

### EVENT 12 — 🟣 PERPLEXITY: Market sizing research (you run this separately)

**Open Perplexity in your browser — separate from Desktop.**

Run Deep Research queries:
- TAM/SAM/SOM for your target market
- ICP behavioral and demographic profiles
- "Not For" exclusion criteria validated against real examples

When complete:
1. Copy the full Perplexity output
2. In Claude Desktop Code tab file tree: right-click `temp/` → New File
3. Name it: `EPIC-01_market_research.md`
4. Paste content → Save

### EVENT 13 — 🟡 CLAUDE DESKTOP: Ingest and structure research

```
@temp/EPIC-01_market_research.md

This is Perplexity Deep Research output for v0.2 market segmentation.
Invoke prd-v02-product-type-classification then
prd-v02-competitive-landscape-mapping using this research.
Structure all findings into PRD.md v0.2 section.
Do not invent data — only use what is in the file.
```

### EVENT 14 — 🟠 SUB-AGENT spawned by Claude (may be automatic)

If segments are complex, Claude may spawn a HORIZON sub-agent to cross-reference
segments against existing BR- rules.

**Automatic — no action needed.**

### EVENT 15 — 🔴 HOOK: `SubagentStart` fires `subagent-memory-load.sh`

**Automatic.** HORIZON's `MEMORY.md` is injected into the sub-agent's context.
This prevents the agent from re-deriving context you have already established.

### EVENT 16 — 🔴 HOOK: `SubagentStop` fires `subagent-memory-save.sh`

**Automatic.** Sub-agent findings are saved back to `MEMORY.md`. Any new patterns
or decisions discovered are logged for the next session.

### EVENT 17 — 🟡 CLAUDE DESKTOP: Generate BR- rules

```
Generate BR- business rules for each validated market segment.
Write them to SoT/SoT.BusinessRules.md.
Then invoke ghm-id-register to register all new IDs.
```

### EVENT 18 — 🔴 HOOK: `Stop` fires — SoT cascade reminder

**Automatic.** Claude is reminded to cascade temp/ → SoT/ and update Epic Session State.

### EVENT 19 — 🟡 + 🟢 Gate check and commit

```
Invoke ghm-gate-check for v0.2.
DoD: segments sized, ICP defined, "Not For" criteria established, BR- rules in SoT.
```

If gate passes:

```bash
git add -A
git commit -m "PRD v0.2: market segments + BR-001 through BR-00X complete"
git push
```

---

## SESSIONS 3–8: v0.3 through v0.6

Follow the same pattern for each stage. Reference `docs/OPERATIONAL_FLOW.md` for
the exact skills to invoke and where Perplexity research is required:

| Stage | Perplexity Research Needed? | Primary Agent | Key Skills |
|-------|-----------------------------|---------------|------------|
| v0.3 Commercial Model | ✅ Competitor pricing, monetization benchmarks | HORIZON | `prd-v03-features-value-planning`, `prd-v03-pricing-model`, `prd-v03-moat-definition` |
| v0.4 User Journeys | ✅ UX best practices (targeted, not deep research) | STUDIO | `prd-v04-persona-definition`, `prd-v04-user-journey-mapping`, `prd-v04-screen-flow-definition` |
| v0.5 Red Team | ✅ Adversarial research — failure modes, legal risks | HORIZON | `prd-v05-risk-discovery-interview`, `prd-v05-technical-stack-selection` |
| v0.6 Architecture | ✅ Stack benchmarks, cost models (targeted) | WERK + STUDIO | `prd-v06-architecture-design`, `prd-v06-technical-specification`, `prd-v06-environment-setup` |

**After v0.6: the pre-build gate.** Before moving to v0.7, run:

```
Invoke ghm-sot-builder.
Consolidate all IDs from v0.1–v0.6 into a coherent, cross-linked SoT.
Verify no orphaned IDs, no duplicates.
Then invoke ghm-status-sync to update README.md dashboard.
```

This is the most important gate in the entire process. Do not proceed to build until
this passes cleanly.

---

## SESSIONS 9+: v0.7 — Build Execution

**From here, Perplexity exits. Claude Code is the primary tool.**

### EVENT 20 — 🔴 HOOK: `SessionStart` — SoT integrity check

Opening prompt for every build session:

```
@README.md @PRD.md @CLAUDE.md @epics/[ACTIVE-EPIC].md

Starting a v0.7 build session.
Active Epic: [EPIC-XX name]
Confirm: SoT is loaded, active Epic is identified,
and context-validation passed.
```

### EVENT 21 — 🟡 CLAUDE DESKTOP + 🟢 SKILL: Epic scoping

```
Invoke prd-v07-epic-scoping.
Scope the next unit of work against UJ-XXX and API-XXX IDs.
Break into Context Windows of manageable size.
```

### EVENT 22 — 🟡 + 🟢 Test planning BEFORE build

```
Invoke prd-v07-test-planning.
Create TEST-XXX entries for each deliverable before writing any code.
```

> **Rule**: No code gets written until TEST-XXX entries exist. This is non-negotiable
> in the methodology and an anti-pattern for WERK to violate.

### EVENT 23 — 🟡 CLAUDE DESKTOP + 🟢 SKILL: Implementation loop

```
Invoke prd-v07-implementation-loop.
Begin Context Window 1: [focus area from Epic].
Reference UJ-XXX, BR-XXX, and API-XXX as required.
Tag all code with // @implements [ID] comments.
```

### EVENT 24 — 🔴 HOOK: `UserPromptSubmit` — context density gate (strict at v0.7)

At this stage, every prompt must reference at least one active ID. If you send a
vague prompt, the hook will warn you. Add the relevant `BR-`, `UJ-`, or `API-`
reference before Claude proceeds.

### EVENT 25 — 🟦 v0: UI component generation (when UJ- screen flows are ready)

For any screen defined in a `UJ-` journey, take the spec to v0:

1. Open [v0.dev](https://v0.dev) in your browser
2. Paste the relevant `UJ-XXX` screen flow definition from your SoT
3. v0 generates the React/Tailwind component
4. Download or copy the code
5. Save to `temp/EPIC-XX_component_name.tsx`
6. Back in Claude Desktop Code tab:

```
@temp/EPIC-XX_component_name.tsx
Integrate this v0-generated component into the project.
Ensure it implements UJ-XXX and references BR-XXX constraints.
Tag with // @implements UJ-XXX.
```

### EVENT 26 — 🔴 HOOKS: Sub-agent memory load/save (if parallel agents spawn)

**Automatic.** WERK may spawn DEVLAB for parallel test writing while it builds.
Both SubagentStart and SubagentStop hooks fire automatically.

### EVENT 27 — ⬛ VERCEL: Auto-deploy on push

When you commit and push to your `dev` branch:

```bash
git add -A
git commit -m "EPIC-01 CW1: [feature] implementing UJ-001, BR-003"
git push origin dev
```

Vercel detects the push and auto-deploys a preview URL. Use this URL to manually
validate the UJ- flow against your acceptance criteria.

### EVENT 28 — 🔴 HOOK: `Stop` — SoT cascade + Epic Session State update

**Automatic after every response.** At the explicit end of a build session:

```
We are ending this build session.
1. Confirm all temp/ findings cascaded to SoT/
2. Update EPIC-XX Session State: Last Action, Stopping Point, Next Steps
3. Verify all @implements tags are present in new code
4. Run ghm-status-sync
```

---

# PART 3: REPEATABLE EPIC LOOP (Existing Product)

Once your product is past v1.0 and you are iterating — adding features, refining
existing ones, fixing issues — every Epic follows this condensed repeating pattern.
This is the loop you will run most often.

---

## THE EPIC LOOP: Start to Finish

### LOOP EVENT 1 — 🔵 YOU: Create the new Epic file

1. In Claude Desktop Code tab file tree: navigate to `epics/`
2. Copy `EPIC_TEMPLATE.md`
3. Rename to `EPIC-XX_[short-name].md` (increment the number from the last Epic)
4. Open the file and fill in:
   - Epic number and name
   - State: `Planned`
   - Lifecycle stage (usually `v0.7` for new feature work)
   - Initial Objective & Scope
   - Context IDs: list the `BR-`, `UJ-`, `API-` IDs this Epic touches

### LOOP EVENT 2 — 🔵 YOU (optional): Perplexity research if new territory

Only needed if this Epic introduces a new market dimension, new competitor capability,
or new technical approach not already covered in SoT.

If needed: run targeted Perplexity research → save to `temp/EPIC-XX_[topic].md`

If not needed (most Epics on existing products): skip to Loop Event 3.

### LOOP EVENT 3 — 🔴 HOOK: `SessionStart` fires on Desktop Code session open

Opening prompt for every Epic loop session:

```
@README.md @PRD.md @CLAUDE.md @epics/EPIC-XX_[name].md

Starting Epic EPIC-XX: [name].
This Epic is augmenting an existing product — not starting from scratch.
Confirm: 3+1 context loaded, active Epic identified, SoT integrity intact.
Tell me what IDs this Epic affects and flag any conflicts with existing entries.
```

### LOOP EVENT 4 — 🔴 HOOK: `UserPromptSubmit` gate — every prompt checked

**Automatic.** Every prompt in an Epic loop session must reference at least one
active ID. Build this into every message you send.

### LOOP EVENT 5 — 🟡 CLAUDE DESKTOP: Phase A — Plan

```
Execute EPIC-XX Phase A: Plan.
Load all referenced BR-XXX, UJ-XXX, and API-XXX entries from SoT.
Define the implementation strategy.
Identify which Context Windows we need.
```

### LOOP EVENT 6 — 🟡 CLAUDE DESKTOP: Phase B — Design

```
Execute EPIC-XX Phase B: Design.
Create or update any SoT entries affected by this Epic.
Document schema changes or component structure as needed.
```

If this Epic introduces new screen flows, generate them for v0 now (see Event 25).

### LOOP EVENT 7 — 🟡 CLAUDE DESKTOP + 🟢: Phase C — Build (Context Windows)

```
Invoke prd-v07-test-planning for EPIC-XX.
Then invoke prd-v07-implementation-loop.
Begin Context Window 1: [focus area].
```

Work through each Context Window sequentially. Commit after each one:

```bash
git add -A
git commit -m "EPIC-XX CW1: [feature] implements [ID list]"
git push origin dev
```

### LOOP EVENT 8 — 🟠 SUB-AGENTS: Parallel work if Epic is large

For Epics spanning multiple systems, Claude may spawn:
- **WERK** for backend implementation
- **STUDIO** for UI/component work
- **DEVLAB** for test writing

**Automatic spawn and memory management via hooks.**

If you want to explicitly direct parallelization:

```
Spawn a DEVLAB sub-agent to write TEST-XXX entries for Context Window 1
while we continue to Context Window 2.
```

### LOOP EVENT 9 — 🟡 CLAUDE DESKTOP: Phase D — Validate

```
Execute EPIC-XX Phase D: Validate.
Run automated test suite and report results.
Manually check flows against UJ-XXX acceptance criteria.
Verify all @implements tags are present.
```

If running tests requires the terminal:

```bash
npm test
```

### LOOP EVENT 10 — 🟡 CLAUDE DESKTOP: Phase E — Harvest

```
Execute EPIC-XX Phase E: Finish (Harvest).
1. Move any useful temp/ notes to SoT/
2. Delete temp files used in this Epic
3. Finalize all SoT entries to match implemented code
4. Review Agent Observations table — triage each to accept/discard
5. Invoke ghm-sot-builder to verify SoT integrity
6. Invoke ghm-status-sync to update README.md dashboard
```

### LOOP EVENT 11 — 🔵 YOU: Mark Epic complete and push

In the Epic file, update:
- `State: Complete`
- Session State: fill in Last Action and leave Next Steps blank

Then commit:

```bash
git add -A
git commit -m "EPIC-XX complete: [name] — all phases A-E done"
git push origin dev
```

### LOOP EVENT 12 — ⬛ VERCEL: Preview deploy triggers automatically

Vercel deploys the `dev` branch. Visit the preview URL and do a final manual
verification of the Epic's deliverables in the live environment.

### LOOP EVENT 13 — 🔵 YOU (when ready): Merge to main

When the Epic's preview is validated:

```bash
git checkout main
git merge dev
git push origin main
```

Vercel auto-deploys the production build.

---

## RUNNING PARALLEL SESSIONS

For situations where you want Claude Code working autonomously while you use
Cowork or Perplexity:

### Starting a background agent session

In Claude Desktop Code tab:

1. Click the **session type dropdown** next to the chat input
2. Select **"Background"**
3. Give Claude a complete, self-contained task:

```
Background task: Using @temp/EPIC-XX_research.md and the existing
SoT entries, complete EPIC-XX Phase B (Design).
Create all necessary SoT entries, register all IDs,
and update the Epic file with Phase B completion status.
Do not start Phase C — stop after Phase B.
```

4. Claude works autonomously. An **Agent Status indicator** appears in the Desktop header.

### Monitoring without interrupting

- Watch the **Agent Sessions view** for live progress
- Do NOT interrupt mid-step — wait for a natural pause
- To steer: click **"Steer with Message"** → Claude completes current step then incorporates your guidance
- To stop immediately: click **"Stop and Send"**

### The parallel workflow pattern

```
WHAT YOU DO                          WHAT RUNS IN BACKGROUND
─────────────────────────────────   ──────────────────────────────────
Perplexity: research for EPIC-02    Claude Code: executing EPIC-01 Phase C
                ↓                                      ↓
Save to temp/EPIC-02_research.md    Commits files, updates SoT, fires hooks
                ↓                                      ↓
Cowork: review PRD language         Background session completes, awaits steering
                ↓                                      ↓
Return to Desktop Code tab:         @mention EPIC-02 file → start next Epic
```

---

## END-OF-SESSION RITUAL (Every Session)

Run this closing prompt before you stop working for the day:

```
We are ending this session.
1. Confirm all temp/ findings cascaded to SoT/
2. Update the active Epic's Session State section completely:
   - Last Action: [exact last thing completed]
   - Stopping Point: [exact file/function/test where we stopped]
   - Next Steps: [exact instructions for the next session to pick up]
   - Context: [any key decisions or blockers]
3. Run ghm-status-sync to update README.md dashboard
4. Confirm no orphaned IDs in SoT
```

Then commit:

```bash
git add -A
git commit -m "Session close: [date] — [brief description of what was done]"
git push
```

---

## QUICK REFERENCE: HOOK EVENTS

| Hook | Fires When | What It Does | Action Needed? |
|------|-----------|--------------|----------------|
| `context-validation.sh` | Session opens | Checks 3+1 files accessible, injects reading order | None — auto |
| `context-density-gate.sh` | Every prompt submitted | Evaluates ID density in your prompt | Add IDs if warned |
| `sot-update-trigger.sh` | After every Claude response | Reminds Claude to cascade temp → SoT, update Epic state | None — auto |
| `subagent-memory-load.sh` | Sub-agent spawns | Injects agent MEMORY.md into sub-agent context | None — auto |
| `subagent-memory-save.sh` | Sub-agent finishes | Saves agent findings back to MEMORY.md | None — auto |

---

## QUICK REFERENCE: AGENT ROLES

| Agent | Lifecycle | When to Explicitly Invoke |
|-------|-----------|---------------------------|
| **HORIZON** | v0.1–v0.5 | Strategy, market analysis, risk review |
| **STUDIO** | v0.4–v0.6 | Persona work, journey mapping, design system |
| **WERK** | v0.6–v0.8 | Architecture, implementation, deployment |
| **DEVLAB** | v0.7–v0.8 | Test planning, QA validation |
| **METRO** | v0.9 | GTM strategy, launch metrics, feedback loops |

To explicitly invoke an agent:

```
Activate HORIZON agent for this conversation.
Load HORIZON's MEMORY.md and AGENT.md context.
We are working on [task].
```

---

## TROUBLESHOOTING

**Hooks are not firing**
→ Confirm you opened the repo root folder (not a subfolder)
→ Run: `echo '{}' | bash "$CLAUDE_PROJECT_DIR"/.claude/hooks/context-validation.sh`
→ If permission error: `chmod +x .claude/hooks/*.sh`

**Claude is drifting from SoT / inventing IDs**
→ Your prompt lacks ID references — the density gate is warning you for a reason
→ Add explicit `@SoT/[filename].md` reference to re-anchor Claude

**Sub-agent spawned without permission**
→ This is expected behavior for complex tasks — agents auto-spawn when needed
→ If token budget is a concern, add to your prompt: "Do not spawn sub-agents — work sequentially"

**Session state was lost between sessions**
→ You did not run the end-of-session ritual
→ Open the active Epic file → Session State section should have been updated
→ If blank: reconstruct from your last commit message and git log

**Vercel not deploying on push**
→ Confirm your repo is connected to Vercel: [vercel.com/dashboard](https://vercel.com/dashboard)
→ Check the branch — Vercel must be watching `dev` (or `main`) specifically

---

*For the full tool responsibility map across all PRD stages, see `docs/OPERATIONAL_FLOW.md`.*
*For hook specifications, see `.claude/hooks/HOOK_CONTRACT.md`.*
*For skill specifications, see `.claude/skills/skills-inventory.md`.*
