---
title: "CLAUDE Agent Operating Guide"
updated: "2026-03-27"
authority: "PRD Led Context Engineering"
template_version: "3.1.0"
---

# CLAUDE.md — Agent Operating Guide

> **Mission**: Build software in lockstep with the PRD Version Lifecycle.
> **Authority**: Load `README.md` → `PRD.md` → `CLAUDE.md` → Active EPIC.
> **Core Rule**: If it's not in the ID Graph (Specs), it doesn't exist.

---

<!-- SECTION: session-protocols -->
## 1. Session Protocols (MANDATORY)

### Start of Session

1. **Load Context**: `README.md`, `PRD.md`, and the Active EPIC.
2. **Read Session State**: Check the **Session State** section of the Active EPIC for "Where we left off".
3. **Check Git Status**: Confirm you are on the right branch/commit.

### End of Session

1. **Update the EPIC Session State section**:
   - **Progress**: What specifically was done? (Link IDs)
   - **Stop Point**: File/Line where work ceased.
   - **Next**: Exact instructions for the next agent.
2. **Commit**: `session: [EPIC-XX] summary of work`.
<!-- /SECTION: session-protocols -->

---

<!-- SECTION: execution-rules -->
## 2. Execution Rules

### Document Ecosystem

The methodology uses a layered document structure:

| Layer | Files | Purpose |
|-------|-------|---------|
| **Navigation** | README.md | Entry point, dashboard, current status |
| **Strategy** | PRD.md | Requirements evolving v0.1→v1.0 |
| **Execution** | epics/EPIC-XX.md | Active work, session handoffs |
| **Knowledge** | SoT/*.md | Durable specs with unique IDs |
| **Scratchpad** | temp/*.md | Ephemeral notes, harvested to SoT |

**ID Ownership** (see [`.claude/domain-profile.yaml`](.claude/domain-profile.yaml) for full registry):
- SoT files own: BR, UJ, PER, SCR, API, DBT, TEST, DEP, RUN, MON, CFD, DES, TECH, ARC, INT
- PRD.md owns: FEA (v0.3), RISK (v0.5), GTM (v0.9)
- README.md owns: KPI metrics

**Cross-Reference Rule**: Every ID should link to related IDs. This creates a knowledge graph that agents can traverse.

### Documentation Discipline

- **IDs**: Reference `BR-`, `UJ-`, `API-` IDs in code comments and commits.
- **SoT Updates**: Update `SoT/` files _before_ or _during_ code changes, never "later".
- **Temp Files**: Use `temp/` for scratchpad, but harvest to SoT before closing the EPIC.

### Progressive Documentation Protocol

> **Rule**: One Document, Many Versions > Many Documents.

- **No Fragmentation**: Never create `PRD-v2.md`. Update `PRD.md` and increment the version header.
- **Change Tracking**: Use inline diffs for minor changes and append logs for major ones.
- **Handoffs**: If hitting context limits, leave a `<!-- HANDOFF -->` marker with summary of state.

### Context Efficiency

- **Batching**: Ask for comprehensive plans (Opus-style) before executing piecemeal (Sonnet-style).
- **Consolidation**: Validation and Verification steps should happen in bulk to save tokens.
- **Pruning**: if context is full, suggest a `session_handoff` where you summarize state and clear history.

### Coding Standards

#### Traceability Protocol (MANDATORY)

Every major code unit must declare which ID it implements.

```typescript
// @implements BR-101 (Free Limit)
// @see API-045
export class RateLimiter { ... }
```

- **Tests First**: Create/Update tests (`TEST-`) for every feature.
- **No Secrets**: Never commit credentials.
- **Small Commits**: Group changes by ID/Feature.

### Lifecycle Gates

- **Do Not Skip**: Verify the Gate Checklist in `PRD.md` or `README.md` (PRD Lifecycle) before advancing.
- **Blockers**: If a gate cannot be passed, update the EPIC and STOP.
<!-- /SECTION: execution-rules -->

---

<!-- SECTION: quick-reference -->
## 3. Quick Reference

- **Lifecycle Guide**: [`README.md`](README.md)
- **ID System**: [`SoT/SoT.UNIQUE_ID_SYSTEM.md`](SoT/SoT.UNIQUE_ID_SYSTEM.md)
- **SoT Index**: [`SoT/SoT.README.md`](SoT/SoT.README.md)
- **EPIC Template**: [`epics/EPIC_TEMPLATE.md`](epics/EPIC_TEMPLATE.md)
- **Active Work**: [`epics/`](epics/)
- **Domain Profile**: [`.claude/domain-profile.yaml`](.claude/domain-profile.yaml)
- **Hook Contract**: [`.claude/hooks/HOOK_CONTRACT.md`](.claude/hooks/HOOK_CONTRACT.md)

**When in doubt, follow the Source of Truth.**
<!-- /SECTION: quick-reference -->

---

<!-- SECTION: tech-stack-conventions -->
## 4. Tech Stack & File Conventions

> **Scope**: This section applies to the **product repo** (created at v0.6 Architecture gate).
> When you copy this CLAUDE.md into a product repo, update the Stack and ID → File Conventions
> blocks to match your specific project choices. The methodology repo itself does not contain
> framework code — only the product repo does.

### Stack

- **Framework**: Next.js 15 (App Router)
- **UI**: React 19, Tailwind CSS, shadcn/ui
- **Language**: TypeScript (strict mode)
- **Backend**: Next.js API Routes (no separate BE server)
- **Database**: [your choice — e.g., Supabase / Prisma / PlanetScale]
- **Auth**: [your choice — e.g., Clerk / NextAuth / Supabase Auth]
- **Deployment**: Vercel (preview on `dev`, production on `main`)

### ID → File Conventions

Every SoT ID maps directly to a file location in the product repo. When Claude Code sees an ID in a prompt, it resolves the ID to this path — no guessing required.

| SoT ID | SoT File | Maps to Code Location |
|--------|----------|----------------------|
| `SCR-XXX` | `SoT/SoT.USER_JOURNEYS.md` | `src/app/[route-name]/page.tsx` |
| `DES-XXX` | `SoT/SoT.DESIGN_COMPONENTS.md` | `src/components/[category]/ComponentName.tsx` |
| `API-XXX` | `SoT/SoT.API_CONTRACTS.md` | `src/app/api/[resource]/route.ts` |
| `DBT-XXX` | `SoT/SoT.DATA_MODEL.md` | `src/types/models.ts` + migration files |
| `TEST-XXX` | `SoT/SoT.TESTING.md` | `__tests__/[unit\|integration\|e2e]/` |
| `UJ-XXX` | `SoT/SoT.USER_JOURNEYS.md` | Referenced via `@implements` tags across `src/` |
| `BR-XXX` | `SoT/SoT.BUSINESS_RULES.md` | Referenced via `@implements` tags and validation logic |

### v0 Component Integration Rule

v0 is a **component fabricator** that executes DES- specs into React code. Its output is never committed directly to the codebase — it always passes through Claude Code for traceability wiring.

```
STUDIO creates DES-XXX spec in SoT/SoT.DESIGN_COMPONENTS.md
           ↓
YOU take DES-XXX spec text to v0.dev
           ↓
v0 generates React / Tailwind component
           ↓
YOU save output to temp/EPIC-XX_ComponentName.tsx
           ↓
CLAUDE CODE (Desktop) integrates:
  - Moves file to src/components/[category]/ComponentName.tsx
  - Adds @implements DES-XXX, UJ-XXX tags
  - Wires to parent page.tsx
  - Updates SoT.DESIGN_COMPONENTS.md entry: "Implemented: ✅"
           ↓
VERCEL auto-deploys dev branch preview
```

### Traceability Format (Next.js specific)

All components, routes, and API handlers must include traceability comments at the top of the file:

```typescript
// @implements DES-021 (Feature Card)
// @implements UJ-003 (User views feature list)
// @see API-007 (Feature list endpoint)
export function FeatureCard({ ... }) { ... }
```

### Product Repo Scaffold (One-time, at v0.6 Gate)

When the v0.6 Architecture gate is reached, run the following prompt in Claude Code (Desktop), pointed at the new empty product repo:

```
We are at v0.6 Architecture gate. The PRD is validated.

Tech stack confirmed: Next.js 15 App Router, TypeScript, Tailwind, shadcn/ui.
Deployment: Vercel. Database: [your choice].

Please:
1. Initialize the Next.js project with:
   npx create-next-app@latest . --typescript --tailwind --app --src-dir
2. Install shadcn/ui:
   npx shadcn@latest init
3. Create the full folder structure per CLAUDE.md conventions:
   src/app/, src/components/ui/, src/components/layout/,
   src/components/features/, src/lib/, src/hooks/, src/types/,
   __tests__/unit/, __tests__/integration/, __tests__/e2e/, temp/
4. Create placeholder files with @implements stubs for:
   [list your SCR- and DES- entries from SoT]
5. Commit as: "v0.6: project scaffold complete, structure matches SoT IDs"
```

After this runs, the entire codebase is pre-mapped to the SoT. Every subsequent Epic fills in pre-existing stubs.
<!-- /SECTION: tech-stack-conventions -->
