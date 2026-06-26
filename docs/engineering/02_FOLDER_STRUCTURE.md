# Folder Structure

> Source of truth for SyncMate M1 folder ownership and file placement.

---

## 1. Role of This File

This file defines:

* M1 source folder structure
* file ownership boundaries
* implementation target files
* import direction rules
* files AI coding agents may create in M1

This file does not define:

* domain model
* API contract
* AI output schema
* runtime validation logic
* UI copy
* visual design rules
* deployment rules

Domain types belong to `docs/data/00_DATA_MODEL.md`.

API types belong to `docs/api/01_API_CONTRACT.md`.

AI output types belong to `docs/ai/07_AI_OUTPUT_SCHEMA.md`.

Runtime validation belongs to `docs/ai/10_RUNTIME_VALIDATION.md`.

---

## 2. M1 Target Structure

```txt
src/
  app/
    page.tsx
    app/
      page.tsx
      new/
        page.tsx
      mistakes/
        page.tsx
        [id]/
          page.tsx
      review/
        page.tsx
      export/
        [id]/
          page.tsx

  components/
    app/
    mistake/
    correction/
    review/
    export/
    ui/

  lib/
    ai/
      diagnosisOutputValidator.ts
      diagnosisOutputMapper.ts
    mock/
      api.ts
      id.ts
      time.ts

  mock/
    users.ts
    mistakes.ts
    diagnoses.ts
    reviewTasks.ts
    index.ts

  types/
    domain.ts
    ai.ts
    api.ts
```

---

## 3. File Ownership

### `src/types/domain.ts`

Owns:

* domain types
* core app objects
* mock app state shape

Source:

```txt
docs/data/00_DATA_MODEL.md
```

Must not contain:

* API request or response types
* raw AI output types
* provider SDK types
* React props
* mock data values
* validation functions

---

### `src/types/ai.ts`

Owns:

* raw AI output types
* AI correction block types
* AI fallback output types

Source:

```txt
docs/ai/07_AI_OUTPUT_SCHEMA.md
```

Must not contain:

* domain object definitions
* runtime validation constants
* provider SDK types
* API request or response types
* React props
* mock data values

---

### `src/types/api.ts`

Owns:

* app-level request types
* app-level response types
* `ApiResult<T>`
* `SyncMateApi`

Source:

```txt
docs/api/01_API_CONTRACT.md
```

Must not contain:

* domain object definitions
* raw AI output definitions
* provider SDK types
* React props
* implementation logic

---

### `src/lib/ai/diagnosisOutputValidator.ts`

Owns:

* runtime validation constants
* validation helper functions
* `validateDiagnosisOutput(raw: unknown)`

Source:

```txt
docs/ai/10_RUNTIME_VALIDATION.md
```

Must not contain:

* provider SDK calls
* UI rendering logic
* domain mapping
* mock data mutation
* API service functions

---

### `src/lib/ai/diagnosisOutputMapper.ts`

Owns:

* mapping validated AI output into domain objects
* generating domain correction blocks from AI blocks
* assigning block IDs
* assigning checklist IDs
* setting block editability defaults

Uses:

* `src/types/ai.ts`
* `src/types/domain.ts`
* `src/lib/mock/id.ts`
* `src/lib/mock/time.ts`

Must not contain:

* runtime validation
* provider SDK calls
* React components
* route logic
* mock service state mutation

---

### `src/lib/mock/api.ts`

Owns:

* M1 mock API implementation
* in-memory or local mock state updates
* implementation of `SyncMateApi`

Uses:

* `src/types/api.ts`
* `src/types/domain.ts`
* `src/mock`
* `src/lib/ai/diagnosisOutputValidator.ts`
* `src/lib/ai/diagnosisOutputMapper.ts`
* `src/lib/mock/id.ts`
* `src/lib/mock/time.ts`

Must not contain:

* real AI provider calls
* real OCR calls
* payment calls
* database calls
* UI rendering logic

---

### `src/lib/mock/id.ts`

Owns:

* mock ID generation helpers

Must not contain:

* domain objects
* mock data arrays
* UI logic

---

### `src/lib/mock/time.ts`

Owns:

* mock timestamp helpers

Must not contain:

* domain objects
* mock data arrays
* UI logic

---

### `src/mock`

Owns:

* mock users
* mock mistakes
* mock diagnoses
* mock review tasks
* mock state exports

Must not contain:

* React components
* API functions
* validation functions
* mapper functions
* provider SDK calls

---

### `src/components`

Owns:

* reusable UI components
* page sections
* visual rendering components

Must not contain:

* source-of-truth domain types
* raw AI output types
* API contract definitions
* mock data mutation
* provider SDK calls

---

### `src/app`

Owns:

* Next.js routes
* page composition
* navigation between pages
* calling app-level mock API functions

Must not contain:

* source-of-truth types
* validator implementation
* mapper implementation
* mock data arrays
* provider SDK calls

---

## 4. Import Direction Rules

Allowed import direction:

```txt
src/app
  -> src/components
  -> src/lib
  -> src/types

src/app
  -> src/lib/mock/api.ts

src/lib/mock/api.ts
  -> src/mock
  -> src/lib/ai
  -> src/types

src/lib/ai/diagnosisOutputMapper.ts
  -> src/types
  -> src/lib/mock/id.ts
  -> src/lib/mock/time.ts

src/lib/ai/diagnosisOutputValidator.ts
  -> src/types

src/mock
  -> src/types
```

Disallowed import direction:

```txt
src/types -> src/app
src/types -> src/components
src/types -> src/lib
src/types -> src/mock

src/mock -> src/app
src/mock -> src/components

src/lib/ai -> src/app
src/lib/ai -> src/components

src/components -> src/app
```

---

## 5. M1 Allowed New Files

AI coding agents MAY create these files in M1:

```txt
src/types/domain.ts
src/types/ai.ts
src/types/api.ts

src/lib/ai/diagnosisOutputValidator.ts
src/lib/ai/diagnosisOutputMapper.ts

src/lib/mock/api.ts
src/lib/mock/id.ts
src/lib/mock/time.ts

src/mock/users.ts
src/mock/mistakes.ts
src/mock/diagnoses.ts
src/mock/reviewTasks.ts
src/mock/index.ts

src/components/app/AppShell.tsx
src/components/mistake/MistakeForm.tsx
src/components/mistake/MistakeList.tsx
src/components/mistake/MistakeDetail.tsx
src/components/correction/CorrectionNoteView.tsx
src/components/correction/CorrectionBlockEditor.tsx
src/components/review/ReviewTaskList.tsx
src/components/export/A4Preview.tsx
```

AI coding agents MAY update these files in M1:

```txt
src/app/page.tsx
src/app/app/page.tsx
src/app/app/new/page.tsx
src/app/app/mistakes/page.tsx
src/app/app/mistakes/[id]/page.tsx
src/app/app/review/page.tsx
src/app/app/export/[id]/page.tsx
```

AI coding agents MUST NOT create unrelated feature folders during M1.

---

## 6. Out-of-Scope Folders for M1

Do not create these folders in M1:

```txt
src/server/
src/db/
src/auth/
src/payments/
src/ocr/
src/providers/
src/teacher/
src/parent/
src/mobile/
src/miniprogram/
```

Do not create these unless a later stage explicitly changes the project scope.

---

## 7. Route Ownership

```txt
/app
```

Owns:

* M1 dashboard entry
* recent mistakes
* review entry
* create mistake entry

```txt
/app/new
```

Owns:

* create mistake flow
* question input
* question confirmation
* wrong solution input
* correction mode selection
* mock diagnosis generation trigger

```txt
/app/mistakes
```

Owns:

* mistake list
* basic filters
* navigation to mistake detail

```txt
/app/mistakes/[id]
```

Owns:

* mistake detail
* diagnosis display
* correction note editing
* review status update

```txt
/app/review
```

Owns:

* review task list
* due/reviewed/mastered state display

```txt
/app/export/[id]
```

Owns:

* A4-style correction note preview

M1 export route is preview only.

M1 MUST NOT implement real PDF generation.

---

## 8. Component Boundary

Components SHOULD be grouped by product responsibility:

```txt
components/app
components/mistake
components/correction
components/review
components/export
components/ui
```

Component props MAY use domain types.

Component props MUST NOT redefine domain object shapes.

Large page sections SHOULD become components when reused or when page files become hard to scan.

Small page-only JSX MAY stay inside route files.

---

## 9. Mock Data Boundary

Mock data MUST be shaped like domain objects.

Mock data MUST import types from `src/types/domain.ts`.

Mock data MUST include:

* one student user
* at least three mistakes
* at least one Simple Mode example
* at least one Complete Mode example
* at least two review tasks

Mock data MUST NOT include:

* parent accounts
* teacher accounts
* payment records
* real OCR output
* real provider response objects

---

## 10. AI Layer Boundary

The AI layer has two responsibilities in M1:

```txt
validate raw mock AI output
map validated output into domain objects
```

M1 AI layer MUST NOT call real AI providers.

M1 AI layer MUST NOT own prompt text execution.

M1 AI layer MUST NOT own UI rendering.

M1 AI layer MUST NOT mutate app state directly.

---

## 11. Handoff Rules for AI Coding Agents

When adding or editing files:

* MUST follow this folder structure.
* MUST keep source-of-truth types in `src/types`.
* MUST keep mock service logic in `src/lib/mock`.
* MUST keep mock data in `src/mock`.
* MUST keep AI validation and mapping in `src/lib/ai`.
* MUST keep route composition in `src/app`.
* MUST keep reusable visual pieces in `src/components`.
* MUST NOT create new architecture folders without updating this file first.
* MUST NOT copy `prototype/SyncMate_V5.html` into production code.
* SHOULD use the prototype only as visual and interaction reference.
