# 00_DOCS_GOVERNANCE.md

stage: M1_MOCK_WEB_MVP
last_reviewed: 2026-06-29
owner: Documentation governance and source-of-truth rules

---

## 0. Purpose

This document defines how SyncMate documentation should be organized, maintained, and interpreted.

The goal is to prevent the documentation system from becoming inconsistent, duplicated, stale, or confusing for AI coding agents and human reviewers.

This file answers:

* Which document owns which type of information?
* What should happen when documents conflict?
* When should rules be referenced instead of copied?
* How should examples and golden cases be maintained?
* How should documentation evolve when the project moves from M1 to later milestones?

This document is not a product specification, engineering specification, or task document.

---

## 1. Core Principle

Each important fact should have one source-of-truth owner.

Other documents may reference that owner, but should not copy the same rule in full unless repetition is intentionally needed for safety or task execution.

The documentation system should be:

* Clear
* Low-duplication
* Easy to maintain
* Easy for AI agents to follow
* Easy for humans to review
* Resistant to contradiction
* Grounded in examples, not only prohibitions

---

## 2. Canonical File Naming

The canonical AI-agent instruction file is:

```txt
AGENTS.md
```

Use the plural form.

Do not use both `AGENT.md` and `AGENTS.md`.

If the repository currently contains `AGENT.md`, rename it to:

```txt
AGENTS.md
```

All future documentation should refer to `AGENTS.md`.

This avoids broken references and prevents AI agents from reading the wrong instruction file.

---

## 3. Relationship with START_HERE

`docs/00_START_HERE.md` is the entry map.

It should tell readers:

* What SyncMate is
* What the current stage is
* What to read first
* Where to find product, engineering, data, API, AI, task, and governance documents

`docs/00_DOCS_GOVERNANCE.md` is the documentation rulebook.

It should tell readers:

* How documents relate to each other
* Which document owns which facts
* How to handle conflicts
* How to avoid duplication
* How to maintain examples and version markers

Do not turn `00_START_HERE.md` into a long governance document.

Do not turn `00_DOCS_GOVERNANCE.md` into a product or engineering specification.

---

## 4. Source-of-Truth Ownership

Use the following ownership rules.

### 4.1 Project Context

Owner document:

```txt
docs/01_PROJECT_CONTEXT.md
```

Owns:

* Product background
* Target users
* Core user pain points
* Product motivation
* Long-term product direction
* What SyncMate is and is not at a high level

Other documents may summarize this briefly, but should not redefine the product motivation.

---

### 4.2 Current Product Scope and Flow

Owner document:

```txt
docs/product/01_MVP_SCOPE.md
```

Owns:

* Current milestone product scope
* M1 product flow
* What M1 includes
* What M1 excludes from a product perspective
* Product-level acceptance criteria
* Core product rules, such as question confirmation before diagnosis

Other documents should reference the MVP scope instead of copying the full product flow.

Allowed short reference:

```txt
Follow the M1 product flow defined in docs/product/01_MVP_SCOPE.md.
```

Avoid copying the entire flow into every document.

---

### 4.3 Technical Stack and Engineering Boundaries

Owner document:

```txt
docs/engineering/00_TECH_STACK.md
```

Owns:

* Framework choice
* TypeScript / React / Tailwind / Vitest choices
* Client-only M1 architecture
* In-memory state rule
* Dependency policy
* Offline technical constraints
* No backend / no API routes / no server actions rule
* Engineering boundaries for M1

Other documents may reference it, but should not restate all technical constraints.

---

### 4.4 Folder Structure

Owner document:

```txt
docs/engineering/02_FOLDER_STRUCTURE.md
```

Owns:

* Expected folders
* File responsibilities
* Layer boundaries
* Where types, mock services, AI validators, components, and routes should live

Other documents may mention allowed files for a task, but should not redefine the entire folder architecture.

---

### 4.5 Data Model

Owner document:

```txt
docs/data/00_DATA_MODEL.md
```

Owns:

* Domain entities
* Field definitions
* Data relationships
* Mistake, Diagnosis, CorrectionNote, ReviewTask, and related models
* Domain-level naming

Other documents should not invent alternative field names or structures.

---

### 4.6 API / Service Contract

Owner document:

```txt
docs/api/01_API_CONTRACT.md
```

Owns:

* Mock service function contracts
* Function inputs and outputs
* Service-level behavior
* Error behavior where applicable
* Future replaceability of service contracts

Other documents may list task-relevant functions, but should not redefine their exact contracts.

---

### 4.7 AI Output Schema

Owner document:

```txt
docs/ai/07_AI_OUTPUT_SCHEMA.md
```

Owns:

* AI-like output shape
* DiagnosisOutput schema
* Correction block types
* AI-output field names
* Required and optional AI-output fields

Other documents should not define competing AI output schemas.

---

### 4.8 Runtime Validation

Owner document:

```txt
docs/ai/10_RUNTIME_VALIDATION.md
```

Owns:

* Runtime validation requirements
* Required rejection paths
* Validator behavior
* How raw AI-like output should be treated
* What invalid output means

Other documents may mention that validator tests are required, but should not duplicate the full validation rules.

---

### 4.9 Task Queue and Milestone Execution

Owner document:

```txt
docs/tasks/01_MILESTONE_M1.md
```

Owns:

* M1 task order
* Task breakdown
* Task-level acceptance criteria
* GitHub Issue templates
* Definition of done for M1 execution

This document may summarize constraints from product and engineering docs for execution clarity, but it should avoid becoming a second source of truth.

If a task document repeats a rule, it should name the owner document.

Example:

```txt
This task follows the no-backend rule defined in docs/engineering/00_TECH_STACK.md.
```

---

### 4.10 AI Agent Behavior

Owner document:

```txt
AGENTS.md
```

Owns:

* How AI coding agents should behave
* Required reading behavior
* Task completion format
* Stop-and-ask rules
* Safety and scope reminders for AI execution
* Human-review expectations

`AGENTS.md` may contain short reminders of important constraints, but should avoid duplicating full product, data, API, AI, or engineering specifications.

---

## 5. Conflict Resolution

### 5.1 First Rule: Follow the Owner Document

When two documents conflict, first identify which document owns the affected topic according to Section 4.

The owner document wins for its owned topic.

Examples:

* Product flow conflict → follow `docs/product/01_MVP_SCOPE.md`.
* Technical stack conflict → follow `docs/engineering/00_TECH_STACK.md`.
* Folder responsibility conflict → follow `docs/engineering/02_FOLDER_STRUCTURE.md`.
* Domain field conflict → follow `docs/data/00_DATA_MODEL.md`.
* Mock service contract conflict → follow `docs/api/01_API_CONTRACT.md`.
* AI output shape conflict → follow `docs/ai/07_AI_OUTPUT_SCHEMA.md`.
* Runtime validation behavior conflict → follow `docs/ai/10_RUNTIME_VALIDATION.md`.
* Task order conflict → follow `docs/tasks/01_MILESTONE_M1.md`.
* AI agent behavior conflict → follow `AGENTS.md`.

---

### 5.2 Global Fallback Order

If ownership is unclear, use this single global fallback order.

```txt
1. Current explicit instruction from the project owner
2. Hard data, privacy, and safety constraints
3. AGENTS.md
4. docs/00_DOCS_GOVERNANCE.md
5. docs/01_PROJECT_CONTEXT.md
6. docs/product/01_MVP_SCOPE.md
7. docs/engineering/00_TECH_STACK.md
8. docs/engineering/02_FOLDER_STRUCTURE.md
9. docs/data/00_DATA_MODEL.md
10. docs/api/01_API_CONTRACT.md
11. docs/ai/07_AI_OUTPUT_SCHEMA.md
12. docs/ai/10_RUNTIME_VALIDATION.md
13. docs/tasks/01_MILESTONE_M1.md
```

Important:

A current explicit instruction from the project owner overrides older documents unless it violates hard data, privacy, safety, or milestone-scope constraints.

If a current instruction appears to conflict with hard constraints, stop and ask before coding.

---

### 5.3 What to Do When a Conflict Is Found

When a conflict is found:

1. Do not silently choose the easier rule.
2. Identify the conflicting documents.
3. Identify the owner document if possible.
4. If ownership is unclear, use the global fallback order.
5. Report the conflict in the task completion notes.
6. If the conflict affects implementation safety, data boundary, or product scope, stop and ask before coding.
7. If the conflict is minor and the owner document is clear, follow the owner document and mention the inconsistency.

---

## 6. Duplication Policy

### 6.1 Default Rule

Do not copy full rules across multiple documents.

Prefer:

```txt
This follows the M1 offline requirement defined in docs/engineering/00_TECH_STACK.md.
```

Instead of repeating the full offline rule everywhere.

---

### 6.2 Allowed Repetition

Some repetition is allowed when it improves safety or execution clarity.

Allowed short reminders:

* M1 uses mock data only.
* M1 has no real AI.
* M1 has no real OCR.
* M1 has no backend.
* M1 must run offline.
* Student data must not be sent overseas.

However, detailed implementation rules should live only in their owner documents.

Example:

Allowed in a task document:

```txt
No real AI calls are allowed in this task.
```

Not preferred in a task document:

```txt
A full duplicated list of every disallowed AI provider, every data-boundary rule, every future provider design, and every validation rule.
```

---

### 6.3 When Repetition Is Required

Repetition is required only when:

* The rule is safety-critical.
* The rule prevents irreversible data exposure.
* The rule prevents hidden external calls.
* The rule is part of a GitHub Issue acceptance criterion.
* The rule is needed so an AI coding agent does not miss it during a narrow task.

Even then, keep the repeated text short and reference the owner document.

---

## 7. Reference Policy

### 7.1 Use Stable References

Cross-document references should be used carefully.

Prefer referencing stable owner documents instead of many deep file paths.

Good:

```txt
Follow the AI output schema owner document.
```

Acceptable when needed:

```txt
Follow docs/ai/07_AI_OUTPUT_SCHEMA.md.
```

Avoid unnecessary long lists of file paths in every document.

---

### 7.2 Risk of Broken Paths

Hard-coded paths can become stale if files are renamed or moved.

When adding or changing file paths:

* Check that the referenced file exists.
* Prefer stable document names.
* Avoid repeating the same path in many places.
* Update references when a file is renamed.
* Mention path changes in the task completion report.

---

### 7.3 Required Reading Lists

Required reading lists should be short and task-specific.

Do not require every AI agent to read every document for every task.

Better:

```txt
For validator work, read:
- AI output schema owner document
- Runtime validation owner document
- Data model owner document
```

Worse:

```txt
Always read every document in the repository before doing any task.
```

`docs/00_DOCS_GOVERNANCE.md` should not be part of every coding task's required reading list.

Read this document when:

* Creating or editing documentation
* Resolving document conflicts
* Deciding source-of-truth ownership
* Reviewing cross-document consistency
* Moving to a new milestone

---

## 8. Version and Stage Markers

Every major documentation file should start with lightweight metadata.

Recommended header:

```txt
stage: M1_MOCK_WEB_MVP
last_reviewed: YYYY-MM-DD
owner: <document responsibility>
```

Example:

```txt
stage: M1_MOCK_WEB_MVP
last_reviewed: 2026-06-29
owner: Engineering stack and technical boundaries
```

This is not for decoration.

It helps humans and AI agents understand whether a document is still valid for the current milestone.

---

## 9. When Moving to a New Milestone

When SyncMate moves from M1 to M2 or later, do not simply keep using all M1 rules.

Before starting a new milestone:

1. Review `00_START_HERE.md`.
2. Review `00_DOCS_GOVERNANCE.md`.
3. Review the milestone scope document.
4. Mark which M1 constraints still apply.
5. Mark which M1 constraints are retired or relaxed.
6. Create a new milestone task document.
7. Update `last_reviewed` dates.
8. Avoid editing old milestone documents as if they were current truth.

Example:

```txt
M1 forbids real AI.
M2 may allow real AI through a provider adapter.
Therefore, M2 should not simply reuse M1's no-real-AI rule without qualification.
```

---

## 10. Positive Examples and Golden Examples

### 10.1 Why Examples Are Required

Rules like “do not become an answer generator” are not enough.

AI coding agents and writing agents need examples of what good output looks like.

A good documentation system should include:

* Positive examples
* Negative examples
* Golden end-to-end examples
* Clear acceptance criteria
* Short explanations of why the rule exists

---

### 10.2 Golden Example Owner

Golden examples should live in a dedicated examples document, not be scattered across all docs.

Required future file:

```txt
docs/examples/01_GOLDEN_MISTAKE_FLOW.md
```

This document should include:

```txt
question
→ student wrong solution
→ confirmed question
→ mock diagnosis output
→ mapped domain data
→ final correction note
→ review prompt
→ A4 preview expectation
```

This is an explicit documentation backlog item, not an optional nice-to-have.

It should be created before or alongside the first UI-heavy implementation task, because UI work needs a concrete product-quality target.

Other documents may reference this golden example but should not copy it fully.

---

### 10.3 Product Boundary Examples

Critical product rules should include examples.

For example, the rule:

```txt
SyncMate should not become a generic answer generator.
```

Should include:

Good behavior:

```txt
Shows where the student's reasoning broke, identifies the mistake cause, connects it to weak knowledge points, and leaves space for student correction.
```

Bad behavior:

```txt
Only provides a complete solution with no reference to the student's wrong step, mistake cause, or review plan.
```

The detailed version should live in the golden example document.

---

## 11. Writing Rules for Future Docs

When creating or editing a document, follow this structure where appropriate:

```txt
What this document owns
What this document does not own
Why the rule exists
Positive example
Negative example
Acceptance criteria
References to owner documents
```

Avoid writing only:

```txt
Do not X.
Must Y.
Never Z.
```

Pure prohibition documents are brittle.

They stop mistakes, but they do not teach the system what good output should look like.

---

## 12. Documentation Review Checklist

Before accepting a new or edited documentation file, check:

* Does this document have a clear owner?
* Does it duplicate rules from another owner document?
* Does it define a new source of truth accidentally?
* Does it include stage and last_reviewed metadata?
* Does it include why the rule exists where useful?
* Does it include examples for ambiguous rules?
* Does it avoid unnecessary hard-coded paths?
* Does it state what to do when it conflicts with other documents?
* Does it help AI agents produce better work, not just avoid mistakes?
* Does it make future maintenance easier?

---

## 13. Special Rule for AI-Facing Docs

AI-facing docs should be written for execution, not just reading.

They should be:

* Short enough to follow
* Specific enough to constrain behavior
* Clear about source-of-truth ownership
* Clear about when to stop
* Clear about what success looks like
* Grounded in examples

AI-facing docs should not become giant duplicated manuals.

For AI coding agents:

```txt
AGENTS.md should tell the agent how to behave.
Owner docs should tell the agent what is true.
Task docs should tell the agent what to do now.
```

`docs/00_DOCS_GOVERNANCE.md` is not a default execution prompt.

It is a governance reference used mainly when editing documentation, resolving conflicts, or reviewing source-of-truth ownership.

---

## 14. Summary

The documentation system should follow this principle:

```txt
One fact, one owner.
Other documents reference, not duplicate.
Conflicts have a resolution order.
Examples define success.
Metadata prevents stale rules.
```

The goal is not to write more documents.

The goal is to make the project easier to build, review, debug, and evolve.
