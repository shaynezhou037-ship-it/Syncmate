# Documentation Style Guide

> This file defines how SyncMate project documents should be written.
> All future docs should follow this guide.

---

## 1. Target Reader

The target reader is:

```text
AI coding agent
```

Secondary readers:

```text
human maintainer
human product owner
```

Docs should help an AI coding agent generate correct code with fewer assumptions.

Do not write docs as beginner tutorials.

Avoid:

```text
long explanations
duplicated definitions
concept introductions
marketing language
unnecessary background
```

Prefer:

```text
types
constants
states
constraints
input/output schema
acceptance criteria
explicit non-goals
```

---

## 2. Source of Truth Rule

Each topic must have one source-of-truth document.

Do not maintain the same detail in multiple places.

```text
Product definition           → docs/01_PROJECT_CONTEXT.md
MVP scope                    → docs/product/01_MVP_SCOPE.md
Data model                   → docs/data/00_DATA_MODEL.md
Database schema              → docs/data/01_DATABASE_SCHEMA.md
API contract                 → docs/api/01_API_CONTRACT.md
AI output schema             → docs/ai/07_AI_OUTPUT_SCHEMA.md
AI prompt behavior           → docs/ai/02_AI_PROMPTS.md
Architecture                 → docs/engineering/01_ARCHITECTURE.md
Folder structure             → docs/engineering/02_FOLDER_STRUCTURE.md
Task planning                → docs/tasks/
Major decisions              → docs/03_DECISION_LOG.md
```

If another file needs the same information, link to the source file.

Do not copy the same explanation into multiple files.

---

## 3. Type Definition Rule

For data, API, and AI schema docs:

```text
Type definitions are the source of truth.
```

Do not write both:

```text
1. Type definition
2. Field explanation table
```

The type definition should carry the meaning.

Use inline comments only when a field is ambiguous.

Good:

```ts
export interface Mistake {
  id: ID;
  subject: Subject;
  questionText: string;
  studentWrongSolution: string;
  confirmedAt?: ISODateString; // set only after user confirms recognized question text
}
```

Bad:

```ts
export interface Mistake {
  id: ID;
  subject: Subject;
  questionText: string;
}
```

```text
| Field | Meaning |
|---|---|
| id | unique ID |
| subject | subject |
| questionText | question text |
```

Do not explain obvious fields.

---

## 4. MVP-Only Rule

Main sections should include only MVP-required objects, fields, pages, APIs, and behavior.

Do not mix future features into MVP definitions.

Allowed:

```text
MVP
FUTURE
OUT_OF_SCOPE
```

Not allowed:

```text
MVP object with many unused future fields
MVP API with future payment fields
MVP data model with teacher dashboard fields
MVP page spec with parent report requirements
```

Future ideas should be placed in a separate `FUTURE` section.

---

## 5. Code Over Prose Rule

If a constraint can be expressed in code, use code.

Prefer:

```ts
export type CorrectionMode = "simple" | "complete";
```

Avoid:

```text
There are two correction modes. The first one is simple mode and the second one is complete mode.
```

Prefer:

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

Avoid:

```text
The MVP should probably have several pages, including home, add mistake, mistake list, detail page, review page, and export page.
```

Use prose only when code cannot express the decision clearly.

---

## 6. No Fixed Template Rule

Do not force every object or section into the same template.

Avoid repeated empty sections like:

```text
Purpose
Field Explanation
MVP Required Fields
Future Notes
Design Notes
```

Only include a section if it adds useful constraints.

If the type definition is enough, stop there.

---

## 7. Brevity Rule

Docs should be concise.

Good documentation:

```text
specific
maintainable
easy for AI to parse
hard to misinterpret
```

Bad documentation:

```text
long
repetitive
essay-like
full of vague product language
```

Prefer short sections.

Prefer lists, constants, and schemas.

Avoid long paragraphs.

---

## 8. Constraint Language

Use strong constraint words when needed.

Allowed:

```text
MUST
MUST NOT
SHOULD
SHOULD NOT
MAY
OUT_OF_SCOPE
TODO
FUTURE
```

Meaning:

```text
MUST          required
MUST NOT      forbidden
SHOULD        recommended unless there is a reason
SHOULD NOT    discouraged unless there is a reason
MAY           optional
OUT_OF_SCOPE  not part of current scope
TODO          decision required
FUTURE        not for MVP
```

---

## 9. TODO Rule

Use TODO only for real unresolved decisions.

Good:

```text
TODO: choose first AI provider.
TODO: decide whether M1 uses localStorage or Supabase.
```

Bad:

```text
TODO: make this better.
TODO: improve later.
TODO: add more features.
```

Every TODO should be actionable.

---

## 10. Decision Log Rule

If a change affects product direction, MVP scope, architecture, data model, API contract, or AI behavior, record it in:

```text
docs/03_DECISION_LOG.md
```

Decision format:

```md
## YYYY-MM-DD — Decision title

Status: accepted

Decision:
<one clear decision>

Reason:
<short reason>

Affected files:
- path/to/file
```

Do not bury major decisions inside random documents.

---

## 11. Document Length Rule

Recommended maximum length:

```text
00_START_HERE.md              short
01_PROJECT_CONTEXT.md         medium
product/01_MVP_SCOPE.md       medium
data/00_DATA_MODEL.md         short-to-medium, type-heavy
api/01_API_CONTRACT.md        type-heavy
ai/07_AI_OUTPUT_SCHEMA.md     type-heavy
engineering docs              concise and operational
```

If a document becomes too long, split it.

---

## 12. Writing Style

Use English for repository documents.

Use Chinese only when:

```text
the product UI copy requires Chinese
example question content is in Chinese
a Chinese learning phrase is necessary
```

Use consistent naming:

```text
SyncMate
Mistake
Diagnosis
CorrectionNote
CorrectionBlock
ReviewTask
WeaknessTag
Simple Mode
Complete Mode
```

Do not use multiple names for the same thing.

---

## 13. Markdown Rules

Use Markdown headings clearly.

Use fenced code blocks for:

```text
types
constants
routes
schemas
examples
```

Use tables only when they reduce repetition.

Do not use tables to duplicate type definitions.

---

## 14. Example: Good Data Model Style

```ts
export type ID = string;
export type ISODateString = string;

export type Subject = "math" | "physics" | "chemistry" | "english" | "other";

export type CorrectionMode = "simple" | "complete";

export interface Mistake {
  id: ID;
  subject: Subject;
  gradeLevel: GradeLevel;
  questionText: string;
  studentWrongSolution: string;
  selectedMode: CorrectionMode;
  status: MistakeStatus;
  createdAt: ISODateString;
  updatedAt: ISODateString;
}
```

No extra field table is needed.

---

## 15. Example: Good Scope Style

```ts
export const MVP_P0_FEATURES = [
  "manual_mistake_creation",
  "question_confirmation",
  "wrong_solution_input",
  "simple_mode",
  "complete_mode",
  "mock_diagnosis",
  "mistake_list",
  "mistake_detail",
  "basic_review_status",
  "a4_preview"
] as const;

export const MVP_OUT_OF_SCOPE = [
  "real_payment",
  "diagnosis_coins",
  "teacher_dashboard",
  "parent_report",
  "real_full_page_ocr",
  "mini_program"
] as const;
```

Add prose only for non-obvious product decisions.

---

## 16. Example: Bad Style

Avoid this:

```text
The Mistake object is very important because it represents a mistake made by the student. It contains many fields, such as id, title, subject, grade level, and other information. The id is used to identify the mistake. The subject means the subject of the mistake. The grade level means the student's grade level.
```

Replace with:

```ts
export interface Mistake {
  id: ID;
  subject: Subject;
  gradeLevel: GradeLevel;
  title: string;
}
```

---

## 17. Update Rule

Before updating a document, identify its role.

```text
If product definition changes       → update docs/01_PROJECT_CONTEXT.md
If MVP scope changes                → update docs/product/01_MVP_SCOPE.md
If data fields change               → update docs/data/00_DATA_MODEL.md
If API shape changes                → update docs/api/01_API_CONTRACT.md
If AI output shape changes          → update docs/ai/07_AI_OUTPUT_SCHEMA.md
If implementation architecture changes → update docs/engineering/01_ARCHITECTURE.md
```

Do not update unrelated files.

Do not repeat the same change across many documents unless the source-of-truth map requires it.

---

## 18. Final Rule

Write docs so that an AI coding agent can act correctly without inventing missing decisions.

```text
Less explanation.
More constraints.
One source of truth.
Types before prose.
MVP before future.
```
