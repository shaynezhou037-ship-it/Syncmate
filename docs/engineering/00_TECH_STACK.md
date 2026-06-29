# 00_TECH_STACK.md

stage: M1_MOCK_WEB_MVP
last_reviewed: 2026-06-29
owner: Engineering stack and M1 technical boundaries

---

## 0. Purpose

This document owns the technical stack and engineering boundaries for SyncMate M1.

It defines:

* framework choice
* language and UI stack
* client-only M1 architecture
* state and persistence rules
* offline technical requirements
* dependency policy
* testing stack

It does not own:

* product flow
* product scope
* data model
* mock service contracts
* AI output schema
* runtime validation rules
* milestone task queue
* high-level data-boundary and privacy principles

Use the relevant owner documents for those topics.

---

## 1. M1 Technical Summary

Use this stack for M1:

```txt
Framework: Next.js App Router
Language: TypeScript
UI: React + Tailwind CSS
State: In-memory store only
Data: Mock data only
AI: Deterministic mock AI-like output only
Testing: Vitest
Package Manager: npm
Deployment: No production deployment in M1
```

M1 is a local mock web MVP, not a production system.

---

## 2. Framework

Use:

```txt
Next.js with App Router
```

Why:

* It matches the planned `src/app/` folder structure.
* It supports route-level page organization.
* It is suitable for a structured web MVP.
* It allows future expansion without requiring production infrastructure now.

Do not use the Pages Router for M1.

Do not introduce another frontend framework such as Vue, Svelte, Angular, or Remix.

M1 has no backend.

Do not add:

* API routes
* server actions
* server-side data fetching
* backend framework behavior

Treat the app as a client-rendered mock.

Default route pages may remain Server Components if they only provide static page shells.

Do not perform data fetching, service calls, server actions, backend logic, or hidden remote calls inside Server Components.

All interaction, form state, step state, and product-flow state should live in Client Components and the M1 in-memory mock service layer.

---

## 3. Language

Use:

```txt
TypeScript
```

Why:

* SyncMate depends heavily on structured data.
* AI-like output must be validated before use.
* Domain models, service contracts, and UI props should be easy to trace.

Avoid `any` unless there is a strong reason.

Raw AI-like input should be accepted as:

```ts
unknown
```

Then validated by the runtime validator before mapping or rendering.

---

## 4. UI Layer

Use:

```txt
React components
Tailwind CSS
```

Why:

* M1 needs clean, readable, card-based UI.
* Tailwind CSS supports fast layout iteration.
* React components make correction blocks, review cards, and A4 preview sections easier to reuse.

UI should be built from small reusable components.

Do not introduce a large UI component library unless explicitly approved.

Avoid complex visual systems in M1.

---

## 5. State and Persistence

### 5.1 M1 State Rule

During M1, application state must be held in memory only.

Allowed:

```txt
React state for local UI state
Small module-level in-memory store
Mock service functions backed by the in-memory store
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

### 5.2 React State Boundary

React state such as `useState` or `useReducer` is for local UI state only.

Examples:

* form inputs
* toggles
* current step
* temporary modal state
* local preview state

Cross-page or cross-component app data must live in the in-memory store under:

```txt
src/lib/mock/
```

Examples of app data:

* mistakes
* saved correction notes
* review queue
* current mock user state if needed

Do not pass core app data around through deeply lifted props.

Do not let different tasks invent different persistence approaches.

### 5.3 Refresh Behavior

Refreshing the page may reset M1 state.

This is acceptable in M1.

Do not “fix” this by adding persistence unless a later milestone explicitly allows it.

---

## 6. Data and Mock Services

M1 uses mock data only.

The exact domain model is owned by:

```txt
docs/data/00_DATA_MODEL.md
```

Mock service contracts are owned by:

```txt
docs/api/01_API_CONTRACT.md
```

Recommended technical location:

```txt
src/mock/
  fictional mock seed data

src/lib/mock/
  in-memory store and mock service functions
```

UI components should call service functions.

UI components should not directly mutate raw mock data.

---

## 7. AI Layer

M1 must not call real AI.

Allowed:

```txt
Mock AI-like JSON output
Deterministic sample diagnosis output
Local mock diagnosis generator
```

The mock diagnosis generator must be deterministic.

The same input should always produce the same output.

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
Remote image processing service
```

Why:

M1 validates the product flow, data structure, and correction-note experience before model quality, cost, latency, and compliance are introduced.

### 7.1 AI Pipeline Boundary

All AI-like output must eventually pass through:

```txt
raw AI-like output
→ runtime validator
→ domain mapper
→ correction note data
→ UI rendering
```

The detailed AI output schema is owned by:

```txt
docs/ai/07_AI_OUTPUT_SCHEMA.md
```

Runtime validation behavior is owned by:

```txt
docs/ai/10_RUNTIME_VALIDATION.md
```

Do not render raw AI-like output directly in UI.

Do not let UI components generate diagnosis data.

Do not let UI components call model providers.

### 7.2 Future Provider Design

Future real AI integration must use a replaceable provider adapter layer.

Recommended future direction:

```txt
mock provider
→ domestic provider adapter
→ local model adapter
```

The UI should not need to change when the provider changes.

For real data, domestic models or locally deployable models should be preferred.

The high-level data-boundary and privacy principle is owned by:

```txt
docs/01_PROJECT_CONTEXT.md
```

This technical document owns only the M1 engineering enforcement details, such as no real AI calls, no hidden remote calls, offline execution, and provider replaceability.

---

## 8. OCR and Image Input

M1 may include a visual placeholder for upload or image input.

M1 must not implement real OCR.

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
Remote image upload
Real image parsing
Automatic formula recognition
```

The product rule for question confirmation is owned by:

```txt
docs/product/01_MVP_SCOPE.md
```

---

## 9. Offline Requirement

M1 must be able to complete the M1 product flow offline.

The exact product flow is owned by:

```txt
docs/product/01_MVP_SCOPE.md
```

If the app breaks when networking is disabled, it may indicate a hidden external call and must be investigated.

M1 should not depend on:

```txt
remote fonts
remote images
remote scripts
analytics
external API calls
CDN-only assets
model provider calls
remote logging
```

Do not use `next/font/google` or any build-time remote font fetch.

Use system fonts or locally bundled font files only.

This prevents hidden network dependency during build or runtime.

---

## 10. Testing Stack

Use:

```txt
Vitest
```

Vitest is enough for pure-function tests such as:

* runtime validator tests
* domain mapper tests
* mock service tests
* review queue logic tests
* correction mode helper tests

Validator and mapper tests do not require `@testing-library`.

`@testing-library/react` may be added only when UI component testing becomes necessary and the task justifies it.

---

## 11. Validation Commands

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

If a command does not exist yet, the task should add it when appropriate or report that it is missing.

Do not claim that a command passed unless it actually passed.

---

## 12. Dependency Policy

Avoid unnecessary dependencies.

Before adding a dependency, check:

```txt
Is this required for M1?
Can this be done with plain TypeScript?
Will this make offline execution harder?
Will this increase maintenance cost?
Does this introduce network, telemetry, or compliance risk?
```

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

Potentially allowed if justified:

```txt
@testing-library/react
@testing-library/jest-dom
```

Do not add in M1 unless explicitly approved:

```txt
Redux
Zustand
Jotai
MobX
database clients
backend frameworks
AI SDKs
OCR SDKs
analytics SDKs
payment SDKs
```

This section owns dependency installation restrictions.

Runtime service-call restrictions are defined in the AI, OCR, and offline sections of this document.

---

## 13. Folder-Level Technical Mapping

Recommended technical mapping:

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
  Mock service functions and in-memory store.

src/mock/
  Fictional mock seed data.

docs/
  Product, engineering, AI, data, API, governance, and task specifications.
```

The detailed folder structure is owned by:

```txt
docs/engineering/02_FOLDER_STRUCTURE.md
```

---

## 14. Technical Philosophy

M1 should be:

* local
* inspectable
* deterministic
* offline-runnable
* easy to modify
* easy to replace later

Do not over-engineer.

Do not introduce hidden external calls.

Do not sacrifice clarity for cleverness.
