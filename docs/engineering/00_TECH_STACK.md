# 00_TECH_STACK.md

## 0. Purpose

This document defines the technical stack and engineering boundaries for **SyncMate M1 Mock Web MVP**.

The purpose of this file is to prevent different AI coding tasks from inventing different technical approaches.

Current stage:

```txt
M1_MOCK_WEB_MVP
```

M1 must be:

* Local-first
* Mock-data only
* Offline-runnable
* Type-safe
* Easy to inspect
* Easy to modify
* Easy to replace later
* Free from real AI, real OCR, payment, login, database, or production user data

---

## 1. M1 Technical Summary

Use the following stack for M1:

```txt
Framework: Next.js App Router
Language: TypeScript
UI: React + Tailwind CSS
State: In-memory store only
Data: Mock data only
AI: Mock AI-like output only
Testing: Vitest
Package Manager: npm
Deployment: No production deployment in M1
```

M1 is a local web MVP, not a production system.

---

## 2. Core Stack

### 2.1 Framework

Use:

```txt
Next.js with App Router
```

Reason:

* It matches the planned `src/app/` folder structure.
* It supports route-level pages clearly.
* It is suitable for a structured web MVP.
* It keeps future expansion possible without forcing production infrastructure now.

Do not use the Pages Router for M1.

Do not introduce another frontend framework such as Vue, Svelte, Angular, or Remix.

M1 has no backend. Do not add API routes, server actions, or server-side data fetching. Treat the app as a client-rendered mock; use Server Components only as static shells if at all.

---

### 2.2 Language

Use:

```txt
TypeScript
```

Reason:

* SyncMate depends heavily on structured data.
* AI-like output must be validated and mapped safely.
* TypeScript makes domain models, API contracts, and UI props easier to trace.

Avoid `any` unless absolutely necessary.

If raw AI-like data is handled, accept it as:

```ts
unknown
```

Then validate it at runtime before mapping it into domain data.

---

### 2.3 UI Layer

Use:

```txt
React components
Tailwind CSS
```

Reason:

* M1 needs clean, readable, card-based UI.
* Tailwind CSS is suitable for fast layout iteration.
* React components make it easier to reuse mistake cards, correction blocks, review cards, and A4 preview components.

UI should be built from small reusable components.

Avoid complex visual systems in M1.

Do not introduce a large UI component library unless explicitly approved.

---

## 3. State and Persistence

### 3.1 M1 State Rule

During M1, application state must be held in memory only.

Allowed:

```txt
In-memory store
React state
Small local module-level mock store
```

Not allowed in M1:

```txt
localStorage
IndexedDB
cookies for app state
database persistence
remote storage
cloud sync
browser cache as product persistence
```

Clarification: React state such as `useState` or `useReducer` is for local UI state only, such as form inputs, toggles, and current step.

Cross-page or cross-component app data, such as mistakes and review queue, must live in the in-memory store under:

```txt
src/lib/mock/
```

Do not pass core app data around through props or lift it ad hoc across pages.

Reason:

* M1 is for validating the product flow.
* Persistent storage will be introduced later when the memory/review feature requires it.
* Different tasks should not invent different persistence approaches.

### 3.2 Consequence

Refreshing the page may reset M1 state.

This is acceptable in M1.

Do not “fix” this by adding localStorage unless a later milestone explicitly allows it.

---

## 4. Data Layer

### 4.1 Mock Data Only

M1 must use mock data only.

Mock data must be clearly fictional sample data.

Do not use:

* Real student names
* Real school names
* Real phone numbers
* Real addresses
* Real private learning records
* Real uploaded student images
* Real exam papers copied from private sources

Mock examples should use obviously fictional identifiers such as:

```txt
Demo Student
Sample Class
Mock Mistake 001
Fictional Middle School
```

Avoid realistic-looking personal details.

### 4.2 Mock API Layer

Use mock service functions instead of real HTTP APIs.

Recommended location:

```txt
src/lib/mock/
```

Example responsibility:

```txt
createMistake()
confirmQuestion()
generateMockDiagnosis()
saveMistake()
getMistakeById()
getReviewQueue()
```

The UI should call mock service functions.

The UI should not directly mutate raw mock data.

---

## 5. AI Layer

### 5.1 M1 AI Rule

M1 must not call real AI.

Allowed:

```txt
Mock AI-like JSON output
Deterministic sample diagnosis output
Local mock diagnosis generator
```

The mock diagnosis generator must be deterministic: the same input must always produce the same output.

Do not use randomness that makes demos or tests non-reproducible.

Not allowed in M1:

```txt
OpenAI API
Claude API
Gemini API
Overseas AI model API
Domestic AI model API
OCR API
Remote model endpoint
```

Reason:

* M1 validates the product flow and data structure.
* Real AI quality, cost, latency, and compliance will be handled in later milestones.

### 5.2 Required AI Pipeline

All AI-like output must follow this pipeline:

```txt
raw AI-like output
→ runtime validator
→ domain mapper
→ correction note data
→ UI rendering
```

Do not render raw AI output directly in UI.

Do not let UI components generate diagnosis data.

Do not let UI components call model providers.

### 5.3 Future AI Provider Design

Future AI integration must use a provider adapter layer.

Recommended future structure:

```txt
src/lib/ai/providers/
  mockProvider.ts
  domesticProvider.ts
  localModelProvider.ts
```

The UI should not need to change when the provider changes.

For real data, domestic models or locally deployable models should be preferred.

User data and student learning data must not be sent overseas.

---

## 6. OCR and Image Input

M1 may include a visual placeholder for upload or image input.

However, M1 must not implement real OCR.

Allowed in M1:

```txt
Upload UI placeholder
Manual question input
Mock extracted question text
Question confirmation step
```

Not allowed in M1:

```txt
Real OCR API
Real image parsing
Remote image upload
Automatic formula recognition
```

Important product rule:

```txt
Question confirmation must happen before diagnosis generation.
```

---

## 7. Testing Stack

Use:

```txt
Vitest
```

Required tests:

```txt
runtime validator tests
domain mapper tests
```

The runtime validator and domain mapper must have unit tests.

These tests must cover rejection paths:

```txt
invalid schema version
missing required fields
invalid correction mode
invalid block type
```

This is a hard requirement.

Optional tests may be added for:

```txt
mock API behavior
review queue logic
correction mode behavior
A4 note rendering helpers
```

UI tests are optional in early M1 unless a task explicitly requires them.

Note: validator and mapper tests need only Vitest. They do not require `@testing-library`, since they test pure functions, not UI.

---

## 8. Validation Commands

The project should support these commands where possible:

```bash
npm run dev
npm run lint
npm run typecheck
npm run test
npm run build
```

Expected meaning:

```txt
npm run dev
  Start local development server.

npm run lint
  Check code style and common issues.

npm run typecheck
  Run TypeScript type checking.

npm run test
  Run unit tests.

npm run build
  Verify the app can build.
```

If a command does not exist yet, the task should either add it or clearly report that it is missing.

Do not claim that a command passed unless it actually passed.

---

## 9. Offline Acceptance Criterion

M1 must be able to run the full product flow with networking disabled.

Required offline flow:

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

If anything breaks when offline, it indicates a hidden external call and must be investigated.

This means M1 should not depend on:

```txt
remote fonts
remote images
remote scripts
analytics
external API calls
CDN-only assets
model provider calls
```

Use local assets or plain UI where possible.

---

## 10. Dependency Policy

Avoid unnecessary dependencies.

Before adding a dependency, check:

```txt
Is this required for M1?
Can this be done with plain TypeScript?
Will this make offline execution harder?
Will this increase future maintenance cost?
Does this introduce network, telemetry, or compliance risk?
```

Do not add dependencies only for convenience.

Allowed by default:

```txt
next
react
react-dom
typescript
tailwindcss
eslint
vitest
```

Potentially allowed if justified by a specific task:

```txt
@testing-library/react
@testing-library/jest-dom
```

Do not add state management libraries such as Redux, Zustand, Jotai, or MobX in M1 unless explicitly approved.

Do not add backend frameworks in M1.

Do not add database clients in M1.

Do not add AI SDKs in M1.

---

## 11. Folder-Level Stack Mapping

Recommended mapping:

```txt
src/app/
  Next.js route-level pages.

src/components/
  Reusable React UI components.

src/types/
  TypeScript domain, API, and AI output types.

src/lib/ai/
  Runtime validator, domain mapper, mock diagnosis generator, future provider abstraction.

src/lib/mock/
  Mock API service functions and in-memory store.

src/mock/
  Fictional mock data.

docs/
  Product, engineering, AI, and task specifications.
```

Route-level pages should compose components and call service functions.

They should not contain core diagnosis logic.

---

## 12. What Not to Build in M1

Do not build:

* Real AI integration
* Real OCR
* Real payment
* Diagnosis coins
* Login
* Real database
* Parent dashboard
* Teacher dashboard
* Admin dashboard
* Mobile app
* WeChat Mini Program
* Production deployment
* School analytics
* Multi-user permission system
* Real student data import

These are future milestones.

M1 should stay focused on the local mock product loop.

---

## 13. M1 Engineering Philosophy

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

The M1 codebase should be easy to understand, easy to inspect, and easy to modify.

Do not over-engineer.

Do not create a black box.

Do not introduce hidden external calls.

Do not sacrifice clarity for cleverness.
