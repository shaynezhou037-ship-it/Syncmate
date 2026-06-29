# 01_MILESTONE_M1.md

stage: M1_MOCK_WEB_MVP
last_reviewed: 2026-06-29
owner: M1 task queue, task execution order, and milestone definition of done

---

## 0. Purpose

This document owns the M1 implementation plan.

It defines:

* M1 success definition
* M1 task queue
* task order
* task-level acceptance criteria
* GitHub Issue template
* M1 definition of done

It does not own:

* product flow details
* technical stack
* folder structure
* data model
* API contract
* AI output schema
* runtime validation rules

Those topics are owned by their respective source-of-truth documents.

---

## 1. Milestone Identity

Milestone:

```txt
M1_MOCK_WEB_MVP
```

M1 is a local, mock, offline-runnable web MVP.

The goal is to demonstrate the SyncMate mistake diagnosis and correction-note experience without production infrastructure.

M1 is not a production system.

M1 validates:

* product flow
* structured correction-note experience
* data boundaries
* mock service boundaries
* AI-like output validation and mapping
* review and A4-preview direction

---

## 2. Source Documents for M1

Use these owner documents instead of redefining their rules here:

```txt
Project entry map:
docs/00_START_HERE.md

Documentation governance:
docs/00_DOCS_GOVERNANCE.md

Product scope and M1 flow:
docs/product/01_MVP_SCOPE.md

Technical stack and M1 engineering boundaries:
docs/engineering/00_TECH_STACK.md

Folder structure:
docs/engineering/02_FOLDER_STRUCTURE.md

Data model:
docs/data/00_DATA_MODEL.md

Mock service contract:
docs/api/01_API_CONTRACT.md

AI output schema:
docs/ai/07_AI_OUTPUT_SCHEMA.md

Runtime validation:
docs/ai/10_RUNTIME_VALIDATION.md
```

If this document conflicts with an owner document, follow `docs/00_DOCS_GOVERNANCE.md`.

---

## 3. M1 Success Definition

M1 is successful when a user can complete the full M1 product flow defined in:

```txt
docs/product/01_MVP_SCOPE.md
```

The app must run locally and complete the flow with networking disabled.

The final experience should show that SyncMate is not a generic answer generator.

It should help the student understand:

* what the question asks
* where the wrong thinking happened
* why the mistake happened
* what the correct thinking path is
* what should be reviewed later

The detailed product-quality target should be grounded in the golden example document once it is created.

Required golden example backlog item:

```txt
docs/examples/01_GOLDEN_MISTAKE_FLOW.md
```

This file should be created before or alongside the first UI-heavy implementation task.

---

## 4. M1 Hard Reminders

These are execution reminders, not full source-of-truth definitions.

During M1:

* Use mock data only.
* Use clearly fictional sample data only.
* Do not use real student data.
* Do not call real AI.
* Do not call OCR.
* Do not add backend behavior.
* Do not add persistence.
* Do not add analytics, telemetry, or remote logging.
* Do not send user data or student learning data overseas.
* The app must work offline.

Detailed technical boundaries are owned by:

```txt
docs/engineering/00_TECH_STACK.md
```

Detailed product scope is owned by:

```txt
docs/product/01_MVP_SCOPE.md
```

---

## 5. M1 Task Queue

Recommended implementation order:

```txt
T0 Documentation Alignment
T1 Golden Example
T2 Repo Bootstrap
T3 Source-of-Truth Types
T4 Runtime Validator
T5 Domain Mapper
T6 Mock Data and In-Memory Store
T7 Mock Service Functions
T8 Core Product Pages
T9 Review Flow and A4 Preview
T10 QA, Offline Check, and Documentation Polish
```

Do not skip directly to UI before the types, validator, mapper, and mock service boundaries are established.

Do not implement future milestones during M1.

---

## 6. Task Details

### T0 Documentation Alignment

Goal:

Make sure the core documentation files are aligned before implementation starts.

Expected work:

```txt
Confirm AGENTS.md exists.
Confirm docs/00_DOCS_GOVERNANCE.md exists.
Confirm docs/00_START_HERE.md points to governance.
Confirm docs/engineering/00_TECH_STACK.md is updated.
Confirm docs/tasks/01_MILESTONE_M1.md is updated.
```

Acceptance criteria:

* `AGENTS.md` is the only AI-agent instruction filename.
* No `AGENT.md` duplicate remains.
* Governance document defines source-of-truth ownership.
* START_HERE points to governance without duplicating it.
* Major docs have stage, last_reviewed, and owner metadata.
* Any known document conflict is reported.

---

### T1 Golden Example

Goal:

Create the golden end-to-end product example.

Expected file:

```txt
docs/examples/01_GOLDEN_MISTAKE_FLOW.md
```

The example should include:

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

Acceptance criteria:

* The example uses clearly fictional sample data.
* The example shows why SyncMate is not just an answer generator.
* The wrong step and mistake cause are explicit.
* The correction note includes student-facing structure.
* Simple Mode and Complete Mode expectations are distinguishable.
* The example can guide later UI work.

---

### T2 Repo Bootstrap

Goal:

Set up the basic Next.js + TypeScript + Tailwind + Vitest project foundation.

Expected output:

```txt
package.json
tsconfig.json
next.config.*
src/app/
src/components/
src/types/
src/lib/
src/mock/
```

Acceptance criteria:

* Uses Next.js App Router.
* Uses TypeScript.
* Uses Tailwind CSS.
* Uses Vitest.
* Adds validation scripts where appropriate.
* Does not add API routes.
* Does not add server actions.
* Does not add external AI, OCR, analytics, telemetry, payment, or database dependencies.
* `npm run dev` works or any issue is clearly reported.
* `npm run typecheck` exists or missing status is clearly reported.
* `npm run test` exists or missing status is clearly reported.
* `npm run build` works or any issue is clearly reported.

---

### T3 Source-of-Truth Types

Goal:

Create TypeScript types that reflect the owner documents.

Expected files:

```txt
src/types/domain.ts
src/types/ai.ts
src/types/api.ts
```

Acceptance criteria:

* Types are explicit and readable.
* Avoids `any`.
* Raw AI-like input is not trusted as typed domain data.
* Types are traceable to owner documents.
* No UI logic is added.

Relevant owner documents:

```txt
docs/data/00_DATA_MODEL.md
docs/api/01_API_CONTRACT.md
docs/ai/07_AI_OUTPUT_SCHEMA.md
```

---

### T4 Runtime Validator

Goal:

Implement runtime validation for mock AI-like output.

Expected files:

```txt
src/lib/ai/diagnosisOutputValidator.ts
src/lib/ai/diagnosisOutputValidator.test.ts
```

Acceptance criteria:

* Validator accepts raw input as `unknown`.
* Validator follows the runtime validation owner document.
* Required rejection paths are tested.
* No UI code is added.
* No external services are called.

Relevant owner documents:

```txt
docs/ai/07_AI_OUTPUT_SCHEMA.md
docs/ai/10_RUNTIME_VALIDATION.md
```

---

### T5 Domain Mapper

Goal:

Map validated AI-like output into SyncMate domain data.

Expected files:

```txt
src/lib/ai/diagnosisMapper.ts
src/lib/ai/diagnosisMapper.test.ts
```

Acceptance criteria:

* Mapper only accepts validated data.
* Mapper produces domain objects used by the app.
* Mapper does not render UI.
* Mapper does not call external services.
* Mapping behavior is deterministic.
* Unit tests cover important success and failure paths.

Relevant owner documents:

```txt
docs/data/00_DATA_MODEL.md
docs/ai/07_AI_OUTPUT_SCHEMA.md
docs/ai/10_RUNTIME_VALIDATION.md
```

---

### T6 Mock Data and In-Memory Store

Goal:

Create fictional mock data and a single in-memory store for M1 app data.

Expected files:

```txt
src/mock/
src/lib/mock/store.ts
```

Acceptance criteria:

* Mock data is clearly fictional.
* No real or realistic-looking personal data is used.
* Cross-page app data lives in the in-memory store.
* No localStorage, IndexedDB, cookies, database, or remote storage is added.
* Store behavior is simple and inspectable.

Relevant owner document:

```txt
docs/engineering/00_TECH_STACK.md
```

---

### T7 Mock Service Functions

Goal:

Create mock service functions that simulate the product flow without real HTTP APIs.

Expected files:

```txt
src/lib/mock/mistakeService.ts
src/lib/mock/reviewService.ts
```

Possible functions, if consistent with the API contract owner document:

```txt
createMistake()
confirmQuestion()
submitWrongSolution()
generateMockDiagnosis()
saveMistake()
getMistakeById()
getAllMistakes()
getReviewQueue()
markReviewComplete()
```

Acceptance criteria:

* Services use the in-memory store.
* Services follow the API contract owner document.
* Services do not make network requests.
* Services do not use API routes.
* Services do not directly render UI.
* Full service flow can run offline.

Relevant owner document:

```txt
docs/api/01_API_CONTRACT.md
```

---

### T8 Core Product Pages

Goal:

Build the core app pages using established types, validator, mapper, store, and mock services.

Expected routes:

```txt
src/app/app/page.tsx
src/app/app/new/page.tsx
src/app/app/mistakes/page.tsx
src/app/app/mistakes/[id]/page.tsx
```

Acceptance criteria:

* User can create a mistake.
* User can input or simulate uploading a question.
* User must confirm the question before diagnosis.
* User can input wrong solution.
* User can choose correction mode.
* User can generate deterministic mock diagnosis.
* User can view a structured correction note.
* Core business logic remains outside page components.
* UI quality is guided by the golden example.

Relevant owner documents:

```txt
docs/product/01_MVP_SCOPE.md
docs/examples/01_GOLDEN_MISTAKE_FLOW.md
docs/engineering/00_TECH_STACK.md
```

---

### T9 Review Flow and A4 Preview

Goal:

Add review queue and A4-style correction-note preview.

Expected routes or components:

```txt
src/app/app/review/page.tsx
src/app/app/export/page.tsx
src/components/review/
src/components/export/
```

Acceptance criteria:

* Saved mistakes can appear in a review queue.
* User can open a review item.
* User can preview a correction note in an A4-like layout.
* A4 preview is local and does not call external services.
* No PDF generation library is required in M1 unless explicitly approved.
* Print-friendly layout is enough for M1.

---

### T10 QA, Offline Check, and Documentation Polish

Goal:

Verify that M1 is stable, readable, and offline-runnable.

Acceptance criteria:

* Full product flow works locally.
* Full product flow works with networking disabled.
* No hidden external calls are present.
* No real AI, OCR, backend, persistence, or analytics has been introduced.
* Validation commands are run if available.
* Any missing scripts or failures are clearly documented.
* Relevant docs are updated only when the owner document needs updating.
* Known document conflicts are reported.

---

## 7. Validation Expectations

For implementation tasks, run available commands:

```bash
npm run lint
npm run typecheck
npm run test
npm run build
```

If a command does not exist, report it clearly.

Do not claim a command passed unless it actually passed.

---

## 8. Definition of Done for M1

M1 is done when:

* The full product flow defined by the product scope owner document works locally.
* The full flow works offline.
* No real AI is called.
* No OCR is called.
* No backend behavior is introduced.
* No persistence is introduced.
* No analytics, telemetry, or remote logging is introduced.
* No real student data is used.
* Mock data is clearly fictional.
* Runtime validator exists.
* Domain mapper exists.
* Validator and mapper have required unit tests.
* User can create, confirm, diagnose, save, review, and preview a mistake.
* Code structure follows documented folder boundaries.
* Validation commands have been run or missing commands are clearly reported.
* The golden example exists and matches the implemented product direction.

---

## 9. GitHub Issue Template for M1 Tasks

Use this format for M1 GitHub Issues.

```md
# Task: <task name>

## Milestone

M1_MOCK_WEB_MVP

## Goal

Describe the specific goal of this task.

## Required Reading

- docs/00_START_HERE.md
- Add only the relevant owner documents for this task.

## Allowed Files / Areas

List the files or folders this task may create or modify.

## Source-of-Truth Owners

List the owner documents that control this task.

## Out of Scope

This task must not add future-milestone behavior.

Mention task-specific out-of-scope items here.

## Acceptance Criteria

- [ ] Criterion 1
- [ ] Criterion 2
- [ ] Criterion 3
- [ ] Works offline if applicable.
- [ ] No hidden external calls are introduced.

## Validation

Run if available:

- [ ] npm run lint
- [ ] npm run typecheck
- [ ] npm run test
- [ ] npm run build

If any command is missing or fails, report it clearly.

## Completion Report

### Files Created
- ...

### Files Modified
- ...

### What Changed
- ...

### Validation
- ...

### Notes / Risks
- ...
```

---

## 10. First GitHub Issue Recommendation

The first implementation issue should be:

```txt
Task: Documentation Alignment for M1
```

Purpose:

Make sure the documentation system is consistent before coding begins.

After that, create:

```txt
Task: Golden Example for M1 Mistake Flow
```

Then start:

```txt
Task: Bootstrap Next.js M1 Foundation
```

Do not start UI implementation before the golden example exists.

---

## 11. M1 Philosophy Reminder

M1 should stay small and inspectable.

The correct order is:

```txt
Align the docs.
Create the golden example.
Bootstrap the app.
Define types.
Validate and map AI-like output.
Build mock services.
Then build UI.
```

Do not overbuild.

Do not create a black box.

Do not sacrifice clarity for cleverness.
