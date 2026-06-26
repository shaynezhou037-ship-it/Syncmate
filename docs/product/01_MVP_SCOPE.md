# MVP Scope

> Source of truth for SyncMate M1 MVP scope.

---

## 1. Role of This File

This file defines:

* M1 product scope
* MVP platform
* MVP user
* MVP core flow
* MVP routes
* P0 feature boundary
* out-of-scope items

This file does not define:

* product definition
* data model
* API contract
* AI output schema
* database schema
* folder structure
* implementation tasks

---

## 2. MVP Identity

```ts
export const MVP_STAGE = "M1_MOCK_WEB_MVP" as const;

export const MVP_PLATFORM = "web" as const;

export const MVP_SUBJECT_FOCUS = "math" as const;

export const MVP_PRIMARY_USER = "middle_school_math_student" as const;

export const MVP_CORE_QUESTION =
  "Can a student turn one mistake into a clear, visible, and useful correction note?" as const;
```

M1 validates the correction experience before real OCR, real AI billing, parent reports, teacher dashboards, mobile app, or mini program.

M1 starts with math, but SyncMate MUST NOT be designed as a math-only product.

---

## 3. MVP Core Flow

```ts
export const MVP_CORE_FLOW = [
  "open_app",
  "create_mistake",
  "input_or_upload_question",
  "confirm_question",
  "input_wrong_solution",
  "choose_correction_mode",
  "generate_mock_diagnosis",
  "view_correction_note",
  "edit_or_complete_blocks",
  "save_mistake",
  "review_mistake",
  "preview_a4_note"
] as const;
```

Question confirmation MUST happen before diagnosis generation.

Simple Mode MUST keep space for student thinking.

Complete Mode MAY provide a fuller diagnosis, but MUST NOT become a long essay.

---

## 4. MVP Routes

```ts
export const MVP_ROUTES = [
  "/app",
  "/app/new",
  "/app/mistakes",
  "/app/mistakes/[id]",
  "/app/review",
  "/app/export/[id]"
] as const;
```

---

## 5. P0 Features

```ts
export const MVP_P0_FEATURES = [
  "manual_mistake_creation",
  "question_text_input",
  "mock_image_upload_preview",
  "question_confirmation",
  "wrong_solution_input",
  "correction_mode_selection",
  "simple_mode",
  "complete_mode",
  "mock_diagnosis_generation",
  "wrong_step_display",
  "mistake_cause_display",
  "weak_knowledge_point_display",
  "known_information_display",
  "target_display",
  "correction_block_display",
  "editable_correction_blocks",
  "mistake_list",
  "mistake_detail",
  "basic_review_status",
  "mark_as_reviewed",
  "mark_as_mastered",
  "a4_preview"
] as const;
```

---

## 6. Correction Modes

```ts
export const MVP_CORRECTION_MODES = [
  "simple",
  "complete"
] as const;
```

Simple Mode:

* shows the question structure
* shows known information
* shows target
* shows mistake cause
* shows weak knowledge points
* leaves key reasoning blocks blank or student-completable

Complete Mode:

* includes wrong-step diagnosis
* includes mistake cause
* includes weak knowledge points
* includes correct thinking path
* includes anti-mistake tip
* includes review suggestion

Exact block types belong to `docs/data/00_DATA_MODEL.md`.

Exact AI output shape belongs to `docs/ai/07_AI_OUTPUT_SCHEMA.md`.

Exact runtime validation belongs to `docs/ai/10_RUNTIME_VALIDATION.md`.

---

## 7. Out of Scope for M1

```ts
export const MVP_OUT_OF_SCOPE = [
  "real_payment",
  "diagnosis_coins",
  "real_full_page_ocr",
  "automatic_multi_question_cutting",
  "parent_account",
  "parent_report",
  "teacher_dashboard",
  "school_dashboard",
  "real_pdf_generation",
  "mobile_app",
  "mini_program",
  "real_ai_provider_billing",
  "multi_user_collaboration",
  "cross_subject_full_support"
] as const;
```

Out-of-scope items MUST NOT appear as required fields in M1 domain types.

Future items MAY be mentioned only when clearly marked as FUTURE in their own documents.

---

## 8. M1 Acceptance Boundary

M1 is acceptable when a student can complete this loop:

1. create one mistake
2. confirm the question
3. enter the wrong solution
4. choose Simple or Complete Mode
5. generate mock diagnosis
6. see a structured correction note
7. edit or complete note blocks
8. save the mistake
9. revisit it from the mistake list
10. mark review status
11. preview an A4-style correction note

M1 is not acceptable if:

* the app only stores mistakes without diagnosis
* the diagnosis is a long answer instead of structured correction blocks
* Simple Mode gives away all reasoning
* the user can generate diagnosis before confirming the question
* the app requires real OCR, real payment, parent accounts, or teacher dashboards to demonstrate the core loop

---

## 9. Handoff Rules for AI Coding Agents

When implementing M1:

* MUST follow this scope file.
* MUST NOT add out-of-scope features.
* MUST use `docs/data/00_DATA_MODEL.md` for domain types.
* MUST use `docs/ai/07_AI_OUTPUT_SCHEMA.md` for raw AI output types.
* MUST use `docs/ai/10_RUNTIME_VALIDATION.md` for output validation.
* MUST keep mock data shaped close to future real data.
* MUST not copy `prototype/SyncMate_V5.html` into production code.
* SHOULD use the prototype only as visual and interaction reference.
