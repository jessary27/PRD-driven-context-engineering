# PRD Led Context Engineering: Memory as Infrastructure

> **Purpose**: PRD Led Context Engineering enables product teams to build with AI by turning **shared memory (humans + AI)** into maintained infrastructure. This ensures teams move fast without losing alignment.
>
> This repository is one expression of that system. The principles apply at any stage because documentation becomes a **Knowledge Graph** that humans and AI can query.

---

<!-- SECTION: evolution -->
## The Evolution

The progression of memory in software teams:

- **Start-up / Waterfall** relied on **Static Memory**. We wrote everything down upfront. It created certainty, but made change slow and expensive.
- **Agile** moved faster but created **Fragmented Memory**. We scattered knowledge across tickets, wikis, and chats and lost shared understanding.
- **PRD Led Context Engineering** builds **Shared Memory**. It treats **AI as a team member**, not just a tool, and keeps documentation synchronized with code so humans and AI can navigate the same truth.
<!-- /SECTION: evolution -->

---

<!-- SECTION: cognitive-shift -->
## The Cognitive Shift

We are changing how we measure work, not just tools.

| Traditional Agile      | PRD Led Context Engineering        | The Shift (Automation & Infrastructure)                                                                |
| :--------------------- | :--------------------------------- | :----------------------------------------------------------------------------------------------------- |
| **Sprints**            | **Context Windows**                | We don't time-box based on dates; we _scope-box_ based on cognitive capacity.                          |
| **User Stories**       | **Prompts**                        | We don't write descriptions; we engineer _prompts_ that deterministically load context.                |
| **Tribal Knowledge**   | **Source of Truth**                | If it isn't in the Knowledge Graph (`SoT/`), it doesn't exist.                                         |
| **Standups**           | **Documentation Hooks (event-driven)** | We don't have status meetings. `.claude/hooks` handles context loading, gate checks, SoT reminders, and subagent memory handoffs. |
| **Project Management** | **Context Governance**             | We don't task-manage people. The system gates execution until context is verified valid.               |
<!-- /SECTION: cognitive-shift -->

---

<!-- SECTION: manifesto -->
## The Manifesto

We are building complex products together.

As humans, we have limits; our cognitive load is finite. Push it too far, and we start forgetting edge cases or introducing bugs.
Our AI partners have limits, too. Their context windows are vast but finite; flooding them leads to hallucination and drift.

When we collaborate—human to human or human to AI—these limits compound. We miscommunicate. We overwrite assumptions. Shared memory drifts.
So we treat team memory as a shared asset across humans and AI, not an individual burden.

### Memory as Infrastructure

This philosophy comes from two experiences.

**First: Leading Human Teams**
Before AI, I led software teams where alignment followed a clear pattern: rally around a single **Source of Truth Artifact**—a mission document—and the team moved as one. Shared memory anchored there. Without it, even great talent drifted.

**Second: Partnering with AI**
When I began coding with AI, I noticed a similar pattern. Sometimes the AI was brilliant; other times it was dense. I realized the variable wasn't the model's intelligence—it was the **Context Density** I provided. When the context was rich and structured, the AI performed at a senior level. When it was vague, it hallucinated.

**PRD Led Context Engineering is the convergence of these truths.**

Here, documentation is not an afterthought. **Documentation is the infrastructure of our shared memory.** The Source of Truth Artifact anchors both humans and AI.

> **The Golden Rule**: If it isn't part of the memory infrastructure, it isn't true.

So how do we introduce team and AI memory into the repo? We do it with **Source of Truth Artifacts (SoT)**—durable documents that turn decisions into shared, queryable memory.
Each SoT entry gets a **Unique ID**. That ID is referenceable and functions as a memory node with **weight and value** because it points to a validated decision, not a suggestion.

When we define a User Journey and give it an ID (`UJ-101`), we create a node in our shared **Knowledge Graph** and offload that complexity from individual brains into the file system.
When the AI references `BR-004`, it is not guessing; it is retrieving a specific, immutable memory we encoded.

**Key Terms**

- **Source of Truth Artifacts**: The clear, anchoring documents that hold a team's shared memory for a product.
- **Context Density**: How much relevant, structured context we deliver per prompt or handoff.
- **Knowledge Graph**: The linked network of IDs (`BR-xxx`, `UJ-xxx`, `API-xxx`) across files that makes memory retrievable for humans and AI.

We treat the `SoT/` files (`SoT.*.md`) as the long-term memory store of the product.

- The **AI** reads the SoT files to understand constraints without needing infinite context.
- **Humans** read the SoT files to remember what we decided last week.
- **We** meet in the code, confident we are building the same thing.

### The 4 Pillars of Context Engineering

To make shared memory practical, we rely on four core concepts:

1.  **Just-in-Time Context**
    _IDs allow loading only what's needed._
    We don't dump the entire repository into the context window. By using **Unique IDs** (`UJ-101`, `API-002`), we reference and load _only_ the specific context needed for a task. This eliminates noise and reduces hallucination.

2.  **The Documentation Ecosystem**
    _A living, connected system for shared memory._
    Documentation is not a static PDF; it is a live graph. We connect `PRD` to `Epics` to `SoT` via links, skills, and hooks. When the logic changes in the Source of Truth, the ecosystem ensures both human and AI know.

3.  **Context Validation**
    _Tools to measure context density._
    We treat context like code: we measure it. If context is **too sparse**, the AI drifts. If it is **too dense**, the AI gets confused. We validate that we are providing the optimal amount of information for performance.

4.  **Progressive Documentation**
    _Update in place; never create copies._
    We never write `PRD_v2.md`. We update the single Source of Truth in place. As the product evolves from v0.1 to v1.0, the documentation evolves with it, maintaining a single, current reality for the team.

This structure preserves alignment and momentum. By engineering context, we build software no single person could hold in their head at once.
<!-- /SECTION: manifesto -->

---

<!-- SECTION: doc-ecosystem -->
## The Documentation Ecosystem: 3+1+SoT+Temp

To make memory infrastructure practical, we use an **intentional architecture** designed to **manage Context Density**. This keeps human cognitive load and AI context windows within limits.

### 1. Executive Functions: Building AI's instincts

This layer orients attention and sets priorities.

- `README.md`: The Dashboard. The status, TOC, and "instincts" of the project (where am I? what is active?).
- `PRD.md`: The Strategy. The "Why" and "What" of the product.
- `CLAUDE.md`: The Physics. The rules of how the AI must behave.

### 2. Focus Memory: The Execution Layer

- `epics/`: The work in progress. This is the only "variable" state. An Epic frames a specific problem (Context Window) so we can solve it without distraction.

### 3. Long-Term Memory: Source of Truth Artifacts

- `SoT/SoT.*.md`: The immutable facts.

  - **Business Rules (`BR-xxx`)**: Hard constraints.
  - **User Journeys (`UJ-xxx`)**: Critical paths.
  - **Data Contracts (`API-xxx`)**: Interfaces.

    This is the shared memory store. We duplicate nothing here. We reference everything via **Unique IDs**.

    > **Just-in-Time Context**: Unique IDs allow us to pull _only_ what is needed for an active task. Instead of dumping the entire documentation into the context window, we reference specific IDs (`UJ-101`, `API-002`). This reduces input tokens while maintaining deep, specific understanding.

### 4. Short-Term Memory: Scratch Pad

- `temp/`: The workspace for **Audits, Explorations, Tech Debt Analysis, and Concepting**.
  - **Naming Convention**: Files must be associated with the Active Epic (e.g., `temp/EPIC-05_audit_log.md` or `temp/EPIC-05_tech_debt.md`).
  - **Rule**: We **Archive** these files when the associated Epic is marked complete. This preserves the context and logic that led to the final implementation.
  - **v0 Integration**: v0.dev component output is staged here as `temp/EPIC-XX_ComponentName.tsx` before Claude Code integrates it into the product repo.
<!-- /SECTION: doc-ecosystem -->

---

<!-- SECTION: lifecycle -->
## The Progressive PRD

A common mistake in AI-assisted development is the "One-Shot"—asking the AI to build the entire app at once. This leads to generic code, hallucinations, and rapid context drift.

Instead, we use a **Progressive PRD**.

`PRD.md` is a **Gated Workflow**, not just a document. We force the AI to focus on one section at a time (e.g., "Strategy", then "User Journeys", then "Data Model").

1.  **Constrained Focus**: By limiting the context window to a single phase, we prevent the AI from "guessing" the architecture before it understands the user needs.
2.  **ID Rigor**: Deep focus allows us to generate meaningful IDs (`UJ-xxx`, `BR-xxx`) without overwhelming the system. These IDs become the anchors for all future code.
3.  **Outcome Quality**: The result is not just a working product, but a _desirable_ one, built with care and speed.

### The PRD Lifecycle (v0.1 to v1.0)

We do not proceed to the next stage until the **Definition of Done (DoD)** is met.

| Version  | Name                     | Focus                 | Definition of Done (DoD)                                           |
| :------- | :----------------------- | :-------------------- | :----------------------------------------------------------------- |
| **v0.1** | **Spark**                | Problem & Outcomes    | Problem defined, Outcomes measurable, Open Questions list.         |
| **v0.2** | **Market Definition**    | Segments & ICP        | Segments sized, "Not For" defined, Business Rules (`BR-`) created. |
| **v0.3** | **Commercial Model**     | Value & Pricing       | Competitors profiled, Pricing model, Monetization rules.           |
| **v0.4** | **User Journeys**        | Personas & Flows      | Core journeys mapped (`UJ-`), Dependencies (`API-`) noted.         |
| **v0.5** | **Red Team Review**      | Risks & Feasibility   | Risks (Market/Tech) identified, Mitigations linked to tests.       |
| **v0.6** | **Architecture**         | Technical Strategy    | Stack selected, API contracts (`API-`) drafted, Cost guardrails.   |
| **v0.7** | **Build Execution**      | Implementation Loop   | Code tested (`TEST-`), SoT updated, Epic loop execution.           |
| **v0.8** | **Release & Deployment** | Operational Readiness | Runbooks (`RUN-`), Monitoring (`MON-`), Rollback plan.             |
| **v0.9** | **Launch**               | Go-to-Market          | Launch metrics (`KPI-`), Fetch channels (`CFD-`) active.        |
| **v1.0** | **Growth**               | Market Adoption       | Paying customers, Retention analysis, Optimization loop.           |

### The Iterative Ecosystem

While the **PRD Lifecycle** is gated for discipline, the **Documentation Ecosystem** allows flexibility.

> **The Paradox**: Gates provide focus; the ecosystem provides agility.

Because our documentation is modular and interlocked via hooks, we can revisit any section just-in-time. If customer feedback changes the **Strategy** during the **Build** phase, we don't restart the plan. We simply:

1.  Open a context window for `PRD.md` (Strategy Section).
2.  Update the `BR-xxx` rules.
3.  Let `.claude/settings.json` hooks handle context reload + gate checks + SoT reminders; if hooks are disabled, update the active Epic manually.

This allows the product to evolve without losing the structure that keeps humans and AI aligned.
<!-- /SECTION: lifecycle -->

---

<!-- SECTION: repo-structure -->
## Repository Structure

This template contains **two logical layers**: the **Methodology Layer** (this repo) and the **Product Layer** (a separate repo created at v0.6). They are distinct by design.

```text
~/dev/
├── PRD-driven-context-engineering/   ← THIS REPO (methodology + planning brain)
│   ├── README.md                        # Dashboard, structure, and status
│   ├── PRD.md                           # Product definition (Progressive PRD)
│   ├── CLAUDE.md                        # Agent operating instructions + tech conventions
│   ├── SoT/                             # Long-term memory (BR-, UJ-, API-, DES-, etc.)
│   ├── epics/                           # Active context windows (execution tasks)
│   ├── temp/                            # Scratchpad; v0.dev output stages here
│   ├── scripts/                         # Utility scripts
│   ├── docs/                            # Extended methodology documentation
│   └── .claude/                         # Methodology runtime
│       ├── agents/                      # Role agents: HORIZON, STUDIO, WERK, METRO
│       ├── hooks/                       # Event-driven session/context hooks
│       ├── skills/                      # prd-v* lifecycle skills + ghm-* core skills
│       ├── domain-profile.yaml          # ID registry, skill taxonomy, tech stack config
│       └── settings.json                # Hook wiring and execution config
│
└── my-product-name/                   ← PRODUCT REPO (created at v0.6 Architecture gate)
    ├── src/
    │   ├── app/                         # Next.js App Router — each route = SCR- entry
    │   │   ├── layout.tsx               # Root layout (DES-001 design tokens)
    │   │   ├── page.tsx                 # Home / landing (SCR-001)
    │   │   ├── (auth)/                  # Route group: login, signup
    │   │   ├── (app)/                   # Route group: authenticated app shell
    │   │   └── api/                     # Next.js API routes = API- entries
    │   ├── components/
    │   │   ├── ui/                      # shadcn/ui base components (DES-00X)
    │   │   ├── layout/                  # Structural components: Navbar, Sidebar (DES-01X)
    │   │   └── features/                # Feature components per UJ-/SCR- (DES-02X+)
    │   ├── lib/
    │   │   ├── api/                     # API client functions, maps to API- entries
    │   │   ├── auth/                    # Auth helpers
    │   │   └── utils/                   # General utilities
    │   ├── hooks/                       # React hooks (custom, per feature)
    │   └── types/                       # TypeScript types from DBT- entries
    ├── __tests__/
    │   ├── unit/                        # TEST- unit tests
    │   ├── integration/                 # TEST- integration tests
    │   └── e2e/                         # TEST- end-to-end tests
    ├── public/images/                   # Static assets
    ├── .claude/                         # Copied from methodology repo at v0.6
    ├── SoT/                             # Copied from methodology repo at v0.6
    ├── epics/                           # Product-scoped epics
    ├── temp/                            # v0.dev output staging
    └── CLAUDE.md                        # Updated with product-specific stack (Section 4)
```

> **Note**: The `src/`, `__tests__/`, and `public/` folders in this methodology repo are structural **scaffolding templates** only. They contain `.gitkeep` placeholder files so the folder layout is preserved in Git and immediately visible when the template is used. Real code lives in the product repo, not here.
<!-- /SECTION: repo-structure -->

---

<!-- SECTION: using-this-template -->
## Using This Template

This repo is a **methodology template**, not a deployable application. The workflow below shows how to go from fork to shipping product.

### The Two-Repo Model

The most important structural rule: **the methodology repo and the product repo are always separate.**

| Repo | Purpose | Created When |
|------|---------|-------------|
| `PRD-driven-context-engineering/` | Planning brain, SoT, PRD lifecycle, agent configuration | Now (you're here) |
| `my-product-name/` | Actual Next.js app, real code | At v0.6 Architecture gate |

You run Claude Code sessions in the **product repo**, not the methodology repo. The methodology repo is where you plan; the product repo is where you build.

### Step 1 — Start Here (v0.1–v0.5: Planning)

Work in this methodology repo through the PRD Lifecycle gates:

1. **Fork** this repo for your product.
2. **Rename** using the `.claude/rename_templates.py` script to replace placeholder names.
3. **Work the PRD gates** (v0.1–v0.5) using HORIZON and STUDIO agents:
   - Define the problem, ICP, and outcomes (`PRD.md` v0.1–0.2)
   - Map commercial model and competitors (`PRD.md` v0.3)
   - Build User Journeys (`UJ-`), Personas (`PER-`), and Screens (`SCR-`) in `SoT/` (v0.4)
   - Run Red Team risk review (`PRD.md` v0.5)
4. **Prototype** in Figma Make using SCR- and DES- specs from `SoT/`.
5. **Do not write code** until the v0.6 gate is passed.

### Step 2 — Create the Product Repo (v0.6: Architecture)

When the v0.6 Architecture DoD is met:

1. Create a new empty GitHub repo: `my-product-name`.
2. Copy `.claude/`, `SoT/`, `epics/`, and `CLAUDE.md` from this methodology repo into it.
3. Update `CLAUDE.md` Section 4 with your specific database and auth choices.
4. Run the scaffold prompt in Claude Code (Desktop) pointed at the new repo:

```
We are at v0.6 Architecture gate. The PRD is validated.
Tech stack confirmed: Next.js 15 App Router, TypeScript, Tailwind, shadcn/ui.
Deployment: Vercel. Database: [your choice].

Please:
1. Initialize: npx create-next-app@latest . --typescript --tailwind --app --src-dir
2. Install shadcn/ui: npx shadcn@latest init
3. Create folder structure per CLAUDE.md Section 4 conventions
4. Create placeholder files with @implements stubs for: [list SCR- and DES- entries]
5. Commit as: "v0.6: project scaffold complete, structure matches SoT IDs"
```

After this runs, every folder in the product repo is pre-mapped to a SoT ID.

### Step 3 — Build with the Epic Loop (v0.7: Build Execution)

All code work happens in the **product repo** using the WERK agent and Epic loop:

1. Open an Epic in `epics/EPIC-XX_feature-name.md`.
2. WERK reads the Epic, resolves the SoT IDs, and generates code in the correct `src/` paths.
3. v0.dev component output is staged in `temp/EPIC-XX_ComponentName.tsx` first, then Claude Code integrates it with `@implements` tags.
4. SoT entries are updated (`Implemented: ✅`) before the Epic closes.
5. Vercel auto-deploys the `dev` branch preview after each commit.

### The ID → File Map (How Claude Code Knows Where to Write)

Every SoT ID maps to an exact file path. This is declared in both `CLAUDE.md` Section 4 and `.claude/domain-profile.yaml` so agents never guess.

| ID | SoT File | Code Location |
|----|----------|---------------|
| `SCR-XXX` | `SoT/SoT.USER_JOURNEYS.md` | `src/app/[route]/page.tsx` |
| `DES-XXX` | `SoT/SoT.DESIGN_COMPONENTS.md` | `src/components/[category]/ComponentName.tsx` |
| `API-XXX` | `SoT/SoT.API_CONTRACTS.md` | `src/app/api/[resource]/route.ts` |
| `DBT-XXX` | `SoT/SoT.DATA_MODEL.md` | `src/types/models.ts` + migration files |
| `TEST-XXX` | `SoT/SoT.TESTING.md` | `__tests__/[unit\|integration\|e2e]/` |
| `UJ-XXX` | `SoT/SoT.USER_JOURNEYS.md` | `@implements` tag across `src/` |
| `BR-XXX` | `SoT/SoT.BUSINESS_RULES.md` | `@implements` tag + validation logic |

### v0.dev Component Integration

v0 is a **component fabricator**, not a code committer. All v0 output passes through Claude Code before it touches the codebase:

```
STUDIO creates DES-XXX spec in SoT/SoT.DESIGN_COMPONENTS.md
       ↓
YOU generate component at v0.dev using the DES-XXX spec
       ↓
Save output to  temp/EPIC-XX_ComponentName.tsx
       ↓
CLAUDE CODE integrates: moves file, adds @implements tags, wires to parent page
       ↓
SoT.DESIGN_COMPONENTS.md entry updated: "Implemented: ✅"
       ↓
Vercel deploys dev branch preview
```

> **Rule**: v0 output is never committed directly to `src/`. Always integrate through Claude Code to preserve `@implements` tag integrity and the ID graph.
<!-- /SECTION: using-this-template -->

---

<!-- SECTION: repo-structure-claude -->
## `.claude` Methodology Layer

- **Skills are split by intent**: `prd-v*` skills map to lifecycle stages; `ghm-*` skills handle operational work like gate checks, ID hygiene, SoT building, and status synchronization.
- **Hooks are event-driven**: `SessionStart` injects read order, `UserPromptSubmit` checks context density for epic/gate prompts, and `Stop` reminds on SoT cascade updates.
- **Subagent memory is automated**: `SubagentStart` loads agent memory, while `SubagentStop` prompts memory updates and runs a post-delegation drift check.
- **Hook behavior is standardized**: `.claude/hooks/HOOK_CONTRACT.md` keeps the interface consistent even when scripts are swapped or extended.
- **Stack awareness is declared**: `.claude/domain-profile.yaml` contains the `tech_stack` block that maps SoT IDs to Next.js file paths, so agents resolve locations without being told in every prompt.

> **Agent Note**: `.claude/` can be replaced with `.gemini/`, `.codex/`, or any other agent structure, but the skills, hooks, custom commands, and agent model here were built with Anthropic’s documentation model in mind.

> **Fork Note**: This `README.md` explains the methodology. When you fork this repo for a product, copy `README_template.md` to `README.md` and customize it for that product.
<!-- /SECTION: repo-structure-claude -->

---

<!-- SECTION: contributing -->
## Contributing

Thank you for helping us refine the **PRD Led Context Engineering** methodology. This repository is not just a codebase; it is a living system of **Memory as Infrastructure**.

### Core Philosophy

Before contributing, please read:

1.  **[`README.md`](README.md)**: The “Executive Functions” layer and Project Dashboard.
2.  **[`CLAUDE.md`](CLAUDE.md)**: The Agent Operating Instructions.

Our goal is to optimize **Context Density**: providing the AI (and humans) with exactly the right information at the right time.

### Ways to Contribute

#### 1. Refine the Methodology

- **Templates**: Improve `SoT/` templates or `epics/EPIC_TEMPLATE.md`.
- **Workflows**: Suggest automation hooks or better ways to manage the “Source of Truth”.
- **Documentation**: Clarify the “Rules of the Road” in `README.md`.

#### 2. Report Friction

- If you find a “Gate” in the PRD Lifecycle that slows you down without adding value, let us know.
- If the AI struggles to find context, report it as a “Context Leak.”

### Getting Started

1.  **Fork & Branch**: Create a branch for your feature or fix.
2.  **Follow the Lifecycle**: Even for meta-changes, we respect the spirit of the **Gated Workflow**.
3.  **Traceability**: If you add a new concept, give it an ID (e.g., `BR-XXX` or `UJ-XXX`) if it’s durable.

### Contribution Standards

- **Terminology**: Use “PRD Led Context Engineering”, “Source of Truth”, and “Epics” consistent with `README.md`.
- **Links**: Always use relative links to files (e.g., `[Link](README.md)`), not absolute paths.
- **Tone**: Professional, prescriptive, and rigorous.

### Questions?

- Open a GitHub Issue for discussion.
- Check `README.md` for the current status of the methodology.
<!-- /SECTION: contributing -->
