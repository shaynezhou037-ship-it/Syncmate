# 01_MILESTONE_M1.md

## 0. Milestone Identity

Milestone name:

```txt
M1_MOCK_WEB_MVP
```

Milestone goal:

Build a local, mock, offline-runnable web MVP for SyncMate that demonstrates the full mistake diagnosis and correction-note flow without real AI, real OCR, payment, login, database, cloud deployment, or real student data.

M1 is not a production system.

M1 is a product-flow and data-structure validation milestone.

---

## 1. M1 Success Definition

M1 is successful when a user can complete the following flow locally:

```txt
open_app
→ create_mistake
→ input_or_upload_question
→ confirm_question
→ input_wrong_solution
→ choose_correction_mode
→ generate_mock_diagnosis
→ view_correction_note
→ edit_or_complete_blocks
→ save_mistake
→ review_mistake
→ preview_a4_note
```

The experience should clearly demonstrate that SyncMate is not just giving an answer, but helping the student understand and organize a mistake.

The app must be able to run the full flow with networking disabled.

If anything breaks when offline, it indicates a hidden external call and must be investigated.

---

## 2. Hard Boundaries

### 2.1 No Real Data

M1 must use mock data only.

Mock data must be clearly fictional.

Do not use:

* Real student names
* Real school names
* Real phone numbers
* Real addresses
* Real private learning records
* Real uploaded student images
* Real exam papers copied from private sources

Use obvious fictional identifiers such as:

```txt
Demo Student
Sample Class
Mock Mistake 001
Fictional Middle School
```

### 2.2 No External AI or OCR

M1 must not call:

* OpenAI API
* Claude API
* Gemini API
* Overseas AI model APIs
* Domestic AI model APIs
* OCR APIs
* Remote model endpoints
* Remote image processing services

M1 uses deterministic mock AI-like output only.

The same input should always produce the same mock diagnosis output.

### 2.3 No Backend

M1 has no backend.

Do not add:

* API routes
* Server actions
* Server-side data fetching
* Database clients
* Backend frameworks
* Cloud storage
* Remote logging
* Analytics
* Telemetry

Treat the app as a client-rendered mock.

Server Components may only be used as static shells if needed.

### 2.4 No Persistence

M1 application state must be held in memory only.

Do not add:

* localStorage
* IndexedDB
* cookies for app state
* database persistence
* remote persistence
* browser cache as product persistence

React state is allowed only for local UI state, such as form inputs, toggles, and current step.

Cross-page or cross-component app data, such as mistakes and review queue, must live in the in-memory store under:

```txt
src/lib/mock/
```

---

## 3. Product Requirements

### 3.1 Core Product Rule

Question confirmation must happen before diagnosis generation.

The app must not generate a diagnosis before the user confirms the question.

### 3.2 Correction Modes

M1 must support two modes:

```txt
Simple Mode
Complete Mode
```

Simple Mode:

* Preserves blanks or incomplete blocks.
* Encourages the student to think.
* Should not over-explain everything.

Complete Mode:

* Provides fuller explanation.
* Should still be structured.
* Should not become a long generic answer.

### 3.3 Correction Note

The correction note should include structured blocks such as:

* Question summary
* Known information
* Target
* Mistake location
* Mistake cause
* Weak knowledge points
* Correct thinking path
* Review reminder
* Student editable area

The exact structure should follow the data model and AI output schema documents.

---

## 4. Engineering Requirements

### 4.1 Required Stack

Use:

```txt
Next.js App Router
TypeScript
React
Tailwind CSS
Vitest
npm
```

Do not introduce another framework unless explicitly approved.

### 4.2 Required Layers

Keep these layers separate:

```txt
src/types/
  Type definitions only.

src/lib/ai/
  Runtime validator, domain mapper, mock diagnosis generator, future provider abstraction.

src/lib/mock/
  Mock API service functions and in-memory store.

src/mock/
  Fictional mock data.

src/components/
  Reusable UI components.

src/app/
  Route-level pages and page composition.
```

Do not put core business logic directly inside page components.

Do not let UI components directly generate diagnosis data.

Do not let UI components call model providers.

### 4.3 Required AI-like Pipeline

All mock AI-like output must follow this pipeline:

```txt
raw AI-like output
→ runtime validator
→ domain mapper
→ correction note data
→ UI rendering
```

Raw AI-like output is untrusted.

It must be validated before it is mapped or rendered.

---

## 5. Required Validation

The project should support these commands where possible:

```bash
npm run dev
npm run lint
npm run typecheck
npm run test
npm run build
```

Before marking a task complete, run available validation commands.

If a command does not exist, report that clearly.

Do not claim a command passed unless it actually passed.

---

## 6. Required Tests

The runtime validator and domain mapper must have unit tests.

These tests must cover rejection paths:

```txt
invalid schema version
missing required fields
invalid correction mode
invalid block type
```

This is a hard requirement.

Validator and mapper tests need only Vitest.

They do not require `@testing-library`, because they test pure functions, not UI.

Optional tests may be added for:

* Mock API behavior
* Review queue logic
* Correction mode behavior
* A4 note rendering helpers

---

## 7. M1 Task Queue

The recommended M1 implementation order is:

```txt
T0 Repo Bootstrap
T1 Source-of-Truth Types
T2 Runtime Validator
T3 Domain Mapper
T4 Mock Data and In-Memory Store
T5 Mock API Service Functions
T6 Core Product Pages
T7 A4 Preview and Review Flow
T8 QA, Offline Check, and Documentation Polish
```

Do not skip directly to UI before the types, validator, mapper, and mock service boundaries are established.

---

## 8. Task Details

### T0 Repo Bootstrap

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

* `npm run dev` works.
* `npm run typecheck` exists.
* `npm run test` exists.
* `npm run build` works or any failure is clearly documented.
* No API routes are added.
* No external AI, OCR, analytics, telemetry, or remote calls are added.

---

### T1 Source-of-Truth Types

Goal:

Create TypeScript types that reflect the existing product, data, API, and AI schema documents.

Expected files:

```txt
src/types/domain.ts
src/types/ai.ts
src/types/api.ts
```

Acceptance criteria:

* Types are explicit and readable.
* Avoid `any`.
* AI raw input is not trusted as typed data.
* Types are traceable to the docs.
* No UI logic is added in this task.

---

### T2 Runtime Validator

Goal:

Implement runtime validation for mock AI-like output.

Expected files:

```txt
src/lib/ai/diagnosisOutputValidator.ts
src/lib/ai/diagnosisOutputValidator.test.ts
```

Acceptance criteria:

* Validator accepts raw input as `unknown`.
* Validator rejects invalid schema version.
* Validator rejects missing required fields.
* Validator rejects invalid correction mode.
* Validator rejects invalid block type.
* Unit tests cover required rejection paths.
* No UI code is added in this task.

---

### T3 Domain Mapper

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
* Unit tests cover required rejection or failure paths where applicable.
* Output is deterministic.

---

### T4 Mock Data and In-Memory Store

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
* Mistakes and review queue data live in the in-memory store.
* No localStorage, IndexedDB, cookies, or database is added.
* Store behavior is simple and inspectable.

---

### T5 Mock API Service Functions

Goal:

Create mock service functions that simulate the product flow without real HTTP APIs.

Expected files:

```txt
src/lib/mock/mistakeService.ts
src/lib/mock/reviewService.ts
```

Possible functions:

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

* UI can call service functions.
* Services use the in-memory store.
* Services do not make network requests.
* Services do not use API routes.
* Services do not directly render UI.
* Full service flow can run offline.

---

### T6 Core Product Pages

Goal:

Build the core app pages using the established mock services.

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
* User can choose Simple Mode or Complete Mode.
* User can generate deterministic mock diagnosis.
* User can view a structured correction note.
* Core business logic remains outside page components.

---

### T7 A4 Preview and Review Flow

Goal:

Add review queue and A4 correction-note preview.

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
* User can preview a correction note in A4-like layout.
* A4 preview is local and does not call external services.
* No PDF generation library is required in M1 unless explicitly approved.
* Print-friendly layout is enough for M1.

---

### T8 QA, Offline Check, and Documentation Polish

Goal:

Verify that M1 is stable, readable, and offline-runnable.

Acceptance criteria:

* Full flow works locally.
* Full flow works with networking disabled.
* No hidden external calls are present.
* `npm run lint` is run if available.
* `npm run typecheck` is run if available.
* `npm run test` is run if available.
* `npm run build` is run if available.
* Any missing scripts or failures are clearly documented.
* Relevant docs are updated if implementation decisions changed.

---

## 9. Definition of Done for M1

M1 is done when all of the following are true:

* The full product flow works locally.
* The full product flow works offline.
* No real AI is called.
* No OCR is called.
* No external API is called.
* No real student data is used.
* No backend is introduced.
* No persistence is introduced.
* Mock data is fictional.
* Runtime validator exists.
* Domain mapper exists.
* Validator and mapper have required unit tests.
* User can create, confirm, diagnose, save, review, and preview a mistake.
* Code structure follows the documented folder boundaries.
* Validation commands have been run or missing commands are clearly reported.

---

## 10. GitHub Issue Template for M1 Tasks

Use this format when creating GitHub Issues for M1.

```md
# Task: <task name>

## Milestone

M1_MOCK_WEB_MVP

## Goal

Describe the specific goal of this task.

## Required Reading

- docs/00_START_HERE.md
- docs/engineering/00_TECH_STACK.md
- docs/tasks/01_MILESTONE_M1.md
- Add task-specific docs here.

## Allowed Files / Areas

List the files or folders this task may create or modify.

## Out of Scope

This task must not add:

- Real AI calls
- OCR
- API routes
- Server actions
- Database
- localStorage / IndexedDB
- Login
- Payment
- Analytics / telemetry
- Real student data

## Acceptance Criteria

- [ ] Criterion 1
- [ ] Criterion 2
- [ ] Criterion 3
- [ ] Full task works offline if applicable.
- [ ] No hidden external calls are introduced.

## Validation

Run if available:

- [ ] npm run lint
- [ ] npm run typecheck
- [ ] npm run test
- [ ] npm run build

If any command is missing or fails, report it clearly.

## Completion Report

Use the required task completion format:

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

## 11. First GitHub Issue Recommendation

The first implementation issue should be:

```txt
Bootstrap Next.js M1 foundation
```

Purpose:

Create the basic local app foundation without implementing product logic yet.

Important:

The first issue should not implement the full SyncMate flow.

It should only prepare the project structure, scripts, and baseline app shell.

Recommended scope:

```txt
Next.js App Router
TypeScript
Tailwind CSS
Vitest
Basic folder structure
Basic local landing/app shell
Validation scripts
No API routes
No server actions
No external calls
```

---

## 12. M1 Philosophy Reminder

M1 should stay small and inspectable.

The correct order is:

```txt
First close the product loop.
Then improve intelligence.
First mock locally.
Then integrate real providers.
First define data structure.
Then polish UI.
First validate one student's one mistake.
Then expand to review, parent, teacher, and school scenarios.
```

Do not overbuild.

Do not create a black box.

Do not add impressive but unnecessary features.

Do not sacrifice clarity for cleverness.
