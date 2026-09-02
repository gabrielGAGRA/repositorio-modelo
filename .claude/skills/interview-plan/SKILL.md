---
name: interview-plan
description: Grill the user while planning. Interview before drafting anything — one question at a time, each with a recommended answer. Settle decisions, write the plan (Goal, Approach, Risks, Migration, Rollout), and require user approval on the plan before writing code.
---

# Interview and Plan (Grill Me)

Most build requests fail in specification, not execution. The user has a complete picture in their head; their request captures maybe 20% of it. Building from that 20% produces code that gets thrown away and redone. Grilling the user before drafting anything costs minutes and saves hours.

## Core rule

**Approve the plan, not the diff.** The plan is the design doc. It is where human taste does the most work, because the plan is what produces the code.

Where taste decides:
- **Goal** — Is this the right feature to build at all?
- **Risks** — Did it surface the data leak before any code?
- **Rollout** — Is the blast radius small enough to ship?

Before producing any code, file, design, or deliverable, conduct an interview. Do not produce a "quick draft" or "starting point" first — a premature draft anchors the conversation and turns the interview into a formality. The interview comes first, always.

## How to run the interview

1. **One question at a time, each with a recommended answer.**
   - Never barrage the user with large batches or lists of questions.
   - Ask exactly **one question at a time**, pairing it with your clear **recommended answer** (e.g., `(Recommended) Option A ... because ...`).
   - This keeps friction minimal: the user can simply reply "yes", select the recommendation, or steer in a different direction.

2. **If an interactive elicitation tool is available**, use it with the recommended option listed first. Otherwise, format in plain text with the recommendation prominently highlighted.

3. **Never ask what you already know.** Before asking, scan the conversation, uploaded files, and repository context. Asking something already answered wastes time and damages trust.

4. **Every question must be able to change the build.** Know what you would do differently for each plausible answer. If every answer leads to the same outcome, cut the question.

5. **Adapt depth to stakes.** Stop asking once the critical unknowns, risks, and boundaries are resolved. When needed questions are answered, settle — do NOT write any code yet.

## Question coverage

Draw questions from these areas to shape the plan:
- **Goal**: What triggered this request; what problem it solves; whether this is the right feature to build at all.
- **Approach**: Architecture, data flow, tech stack, constraints, and contracts.
- **Risks**: Security vulnerabilities, data leak vectors, failure modes, race conditions, regressions surfaced before writing code.
- **Migration**: Breaking changes, legacy code retention, data migrations, backward compatibility.
- **Rollout**: Blast radius containment, staging, feature flags, fallback strategy.
- **Scope & Taste**: Must-haves vs. out of scope, user experience standards, rejection criteria.

## Handling pushback

If the user says "just build it," respect that — the interview serves them, not a bureaucracy. Compress to the single most critical decision with your recommendation and proceed on stated assumptions directly in the plan for approval.

## After the interview: The Plan

After the needed questions, settle. **Do NOT write any code yet.** Put those decisions on the plan.

Format the plan using this exact structure:

```markdown
# Plan: [Feature or Task Name]

## Goal
[Is this the right feature to build at all? Clear problem statement and desired outcome.]

## Approach
- [Key architecture / technical decisions]
- [Acceptance criteria, types, schemas, and vertical slices]

## Risks
- [Surfaced data leaks, security concerns, regressions, or failure modes before any code]

## Migration
1. [Step-by-step migration, schema updates, or backward compatibility strategy]

## Rollout
- [Blast radius mitigation, phased rollout, feature flags, verification plan]
```

**Get explicit approval on the plan before writing code or tests.** The approved plan is the contract.

## What counts as non-trivial

- **Grill for**: Features, architecture changes, tools, scripts over ~20 lines, schema changes, integrations, automations.
- **Skip for**: One-liner code fixes, explanations, pure debugging of existing errors, syntax corrections, format conversions.

## Example Flow

**User**: "Add user export to CSV."

**Agent**:
> **Question 1**: Should the CSV export be generated synchronously on request, or asynchronously as a background job with a download link?
>
> **(Recommended)**: Asynchronously via background task, because large user tables can block the request worker and cause HTTP 504 timeouts.

**User**: "Agreed, background job."

**Agent**:
> **Question 2**: For data privacy and leak prevention, should sensitive fields like hashed passwords, internal tenant IDs, or audit tokens be stripped by default?
>
> **(Recommended)**: Yes, enforce an explicit allowlist of exportable fields to prevent accidental PII/sensitive data leaks.

**User**: "Yes, allowlist only."

*(Decisions settled. Agent drafts the plan — NO CODE YET)*

**Agent**:
> # Plan: Asynchronous User CSV Export
>
> ## Goal
> Allow administrators to export user data without impacting API response times or exposing sensitive data.
>
> ## Approach
> - Trigger an async export task via existing task queue.
> - Use a strict allowlist schema for exported columns (id, name, email, created_at).
> - Generate temporary signed download URLs.
>
> ## Risks
> - Data leak risk: Solved via strict field allowlist before any export code runs.
> - Disk fill risk on worker: Solved by streaming directly to cloud storage.
>
> ## Migration
> 1. Add background worker task definition.
> 2. No database migration needed.
>
> ## Rollout
> - Roll out behind admin-only permission flag.
> - Blast radius limited to background queue.
>
> Please review and approve this plan before implementation begins.