# AGENTS.md

## 0. Project Identity

This repository is for **SyncMate**, an AI-assisted mistake diagnosis and correction-note product for students.

The current development stage is:

```txt
M1_MOCK_WEB_MVP
```

The goal of M1 is to build a stable, local, mock web MVP that demonstrates the core mistake-correction flow without real AI, real OCR, real payment, real user accounts, or real production data.

---

## 1. Required Reading Order

Before making any code changes, read these files first:

```txt
docs/00_START_HERE.md
docs/01_PROJECT_CONTEXT.md
docs/product/01_MVP_SCOPE.md
docs/engineering/02_FOLDER_STRUCTURE.md
docs/data/00_DATA_MODEL.md
docs/api/01_API_CONTRACT.md
docs/ai/07_AI_OUTPUT_SCHEMA.md
docs/ai/10_RUNTIME_VALIDATION.md
```

If a task only touches one narrow area, read the relevant documents first and avoid loading unrelated documents.

The documents are the source of truth. If code and docs conflict, follow the docs unless the task explicitly says otherwise.

---

## 2. Hard Constraints

### 2.1 Data Boundary

This project must be designed with the following hard constraint:

```txt
User data and student learning data must not be sent overseas.
```

During M1:

* Do not call any real external AI API.
* Do not call any overseas model provider.
* Do not upload real student data to any third-party service.
* Do not introduce analytics, tracking, telemetry, or remote logging.
* Do not store or commit real student questions, names, phone numbers, school names, or private learning records.
* Use mock data only.
* Mock data must be clearly fictional sample data. Do not use real or realistic-looking names, school names, phone numbers, or addresses.
* Acceptance criterion: the M1 app must be able to run the full product flow with networking disabled. If anything breaks when offline, it indicates a hidden external call and must be investigated.

For future AI integration:

* All AI calls must go through a provider adapter layer.
* The model provider must be replaceable.
* Domestic models or locally deployable models should be preferred when real data is involved.
* Pages and UI components must never call model APIs directly.

### 2.2 Secrets

Never commit:

```txt
.env
.env.local
.env.production
API keys
model provider keys
database credentials
private tokens
```

Only commit safe examples such as:

```txt
.env.example
```

### 2.3 Scope Control

The current stage is M1 only.

Do not implement the following unless a later task explicitly requests it:

* Real OCR
* Real AI provider integration
* Real payment
* Diagnosis coins
* Parent dashboard
* Teacher dashboard
* Admin dashboard
* Login / authentication
* Database persistence
* Mobile app
* WeChat Mini Program
* Cloud deployment
* Real production user data
* Multi-user permission system
* School-level analytics

If a requested change appears to violate M1 scope, stop and explain the issue before coding.

---

## 3. Product Flow Requirements

The M1 product flow must follow this order:

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

Important product rule:

```txt
Question confirmation must happen before diagnosis generation.
```

The app should help the student understand:

* What the question is asking.
* Where the wrong thinking happened.
* Why the mistake happened.
* What the correct thinking path is.
* What should be reviewed later.

The product should not become a generic answer generator.

---

## 4. Engineering Principles

### 4.1 Spec-First Development

Do not start by inventing code structure freely.

Follow the existing docs:

```txt
docs/engineering/02_FOLDER_STRUCTURE.md
docs/data/00_DATA_MODEL.md
docs/api/01_API_CONTRACT.md
docs/ai/07_AI_OUTPUT_SCHEMA.md
docs/ai/10_RUNTIME_VALIDATION.md
```

When adding or changing code, keep it traceable to the relevant document.

### 4.2 Small Task Rule

Each task should be small and atomic.

Prefer changing a small number of files per task.

Do not rewrite large parts of the repository unless explicitly asked.

Do not perform broad refactors during feature implementation.

### 4.3 Separation of Concerns

Keep these layers separate:

```txt
src/types/
  Type definitions only.

src/lib/ai/
  AI output validation, mapping, mock generation, and provider abstraction.

src/lib/mock/
  Mock API service functions.

src/mock/
  Mock data.

src/components/
  Reusable UI components.

src/app/
  Route-level pages and page composition.
```

Do not put core business logic directly inside page components.

Do not let UI components directly generate diagnosis data.

Do not let UI components directly call model providers.

Persistence (M1):

During M1, application state is held in an in-memory store only.

Do not introduce localStorage, IndexedDB, or any other persistence mechanism in this stage.

Persistent storage, such as localStorage, will be introduced later when the memory/review feature requires it.

Do not let different tasks each invent their own persistence approach.

### 4.4 Replaceability

Design for future replacement:

```txt
mock diagnosis generator
→ domestic AI provider adapter
→ local model adapter
```

The UI should not need to change when the AI provider changes.

---

## 5. AI Output Requirements

AI output must be treated as untrusted data.

The required pipeline is:

```txt
raw AI-like output
→ runtime validator
→ domain mapper
→ correction note data
→ UI rendering
```

For M1, use mock AI-like output only.

When implementing AI-related code:

* Accept raw input as `unknown`.
* Validate the runtime structure.
* Reject invalid schema versions.
* Reject missing required fields.
* Reject invalid correction modes.
* Reject invalid block types.
* Apply length limits.
* Never trust TypeScript types alone for AI output.
* Never render raw AI output directly in the UI.

The schema source of truth is:

```txt
docs/ai/07_AI_OUTPUT_SCHEMA.md
docs/ai/10_RUNTIME_VALIDATION.md
```

---

## 6. UI and UX Requirements

The UI should be:

* Clean
* Student-friendly
* Structured
* Easy to read
* Easy to revise
* Suitable for demo
* Suitable for later A4 export

The product should support two correction modes:

```txt
Simple Mode
Complete Mode
```

Simple Mode should preserve blanks or incomplete blocks for student thinking.

Complete Mode may provide fuller explanations, but should not become overly verbose.

Avoid long paragraphs in the UI. Prefer cards, steps, labels, and structured blocks.

---

## 7. Code Quality Requirements

Use TypeScript carefully.

Prefer explicit types for domain data.

Avoid `any` unless there is a strong reason.

Prefer readable code over clever code.

Avoid premature abstraction.

Avoid adding dependencies unless necessary.

Before finishing a task, run available validation commands such as:

```txt
npm run lint
npm run typecheck
npm run test
npm run build
```

If a command does not exist, report that clearly instead of pretending it ran.

The runtime validator and domain mapper must have unit tests. These tests must cover the rejection paths: invalid schema version, missing required fields, invalid correction mode, and invalid block type. This is a hard requirement, not optional. Other tests may be added where appropriate.

---

## 8. Documentation Requirements

When adding important code, update relevant docs if needed.

Do not create unnecessary documentation.

Do not duplicate long content from existing docs.

If a new implementation decision is made, document it briefly in the relevant engineering or task document.

---

## 9. Task Completion Format

At the end of each task, report in this format:

```md
## Task Summary

### Files Created
- ...

### Files Modified
- ...

### What Changed
- ...

### Validation
- [ ] npm run lint
- [ ] npm run typecheck
- [ ] npm run test
- [ ] npm run build

### Notes / Risks
- ...
```

If something could not be completed, say so clearly.

Do not hide errors.

Do not claim a command passed unless it actually passed.

---

## 10. When to Stop and Ask

Stop and ask before coding if:

* The task conflicts with M1 scope.
* The task requires real student data.
* The task requires overseas AI/API calls.
* The task requires a real model provider.
* The task requires payment, login, database, or deployment.
* The task requires large refactoring.
* The task contradicts the existing docs.
* The expected behavior is ambiguous and cannot be safely inferred.

When in doubt, preserve the current scope and explain the tradeoff.

---

## 11. Project Direction Reminder

The goal is not to build a complex AI system first.

The correct order is:

```txt
First close the product loop.
Then make the AI smarter.
First mock locally.
Then integrate real providers.
First structure the data.
Then polish the UI.
First make one student's one mistake useful.
Then expand to review, parent, teacher, and school scenarios.
```

Do not overbuild.

Do not add impressive but unnecessary features.

Do not turn SyncMate into a black-box answer generator.

SyncMate should remain a structured mistake diagnosis and correction-note product.
