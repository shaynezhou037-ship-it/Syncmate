# Runtime Validation

> Source of truth for SyncMate M1 AI diagnosis output runtime validation.

---

## 1. Role of This File

This file defines:

* runtime validator target
* validator result type
* validation error type
* output limits
* required block types by correction mode
* block content compatibility
* validation responsibilities

This file does not define:

* raw AI output schema
* domain model
* API contract
* prompt text
* provider SDK response
* UI rendering logic

Raw AI output types belong to `docs/ai/07_AI_OUTPUT_SCHEMA.md`.

Domain types belong to `docs/data/00_DATA_MODEL.md`.

---

## 2. Target File

```ts
src/lib/ai/diagnosisOutputValidator.ts
```

The validator MUST accept `unknown`.

The validator MUST return a typed validation result.

The validator MUST NOT trust TypeScript types at runtime.

---

## 3. Validator Types

```ts
import type {
  AICorrectionBlock,
  AICorrectionBlockContent,
  AICorrectionBlockType,
  DiagnosisOutputV1
} from "@/types/ai";

import type {
  BlockFillPolicy,
  CorrectionBlockZone,
  CorrectionMode
} from "@/types/domain";

export type DiagnosisOutputValidationErrorCode =
  | "not_object"
  | "invalid_schema_version"
  | "invalid_status"
  | "missing_required_field"
  | "invalid_field_type"
  | "invalid_field_value"
  | "text_too_long"
  | "array_too_long"
  | "array_too_short"
  | "duplicate_order"
  | "missing_required_block"
  | "invalid_block_type"
  | "invalid_block_content_kind"
  | "invalid_block_content"
  | "invalid_simple_mode_blank"
  | "invalid_complete_mode_fill"
  | "unsafe_text_content";

export interface DiagnosisOutputValidationError {
  code: DiagnosisOutputValidationErrorCode;
  path: string;
  message: string;
}

export type DiagnosisOutputValidationResult =
  | {
      ok: true;
      data: DiagnosisOutputV1;
      errors: [];
    }
  | {
      ok: false;
      data?: undefined;
      errors: DiagnosisOutputValidationError[];
    };
```

---

## 4. Output Limits

These constants MUST be imported by the validator implementation.

```ts
export const DIAGNOSIS_OUTPUT_LIMITS = {
  questionTypeMaxChars: 18,
  coreKeywordsMaxItems: 5,
  coreKeywordMaxChars: 12,
  knownInfoMaxItems: 5,
  knownInfoItemMaxChars: 28,
  targetMaxChars: 24,
  wrongStepMaxChars: 40,
  mistakeCausesMaxItems: 4,
  mistakeCauseMaxChars: 28,
  weakKnowledgePointsMaxItems: 4,
  weakKnowledgePointMaxChars: 18,
  correctThinkingPathMaxSteps: 6,
  diagnosisStepTitleMaxChars: 12,
  diagnosisStepContentMaxChars: 42,
  antiMistakeTipMaxChars: 42,
  reviewSuggestionMaxChars: 42,
  correctionBlocksMaxItems: 12,
  blockTitleMaxChars: 10,
  textBlockMaxChars: 48,
  listBlockMaxItems: 6,
  listBlockItemMaxChars: 24,
  checklistMaxItems: 5,
  checklistItemMaxChars: 24,
  blankPlaceholderMaxChars: 24
} as const;
```

These limits are runtime rules.

They are not TypeScript guarantees.

---

## 5. Allowed Values

```ts
export const VALID_SCHEMA_VERSION = "diagnosis_output_v1" as const;

export const VALID_OUTPUT_STATUS = [
  "ok",
  "needs_more_info",
  "unsupported"
] as const;

export const VALID_LANGUAGES = [
  "zh-CN",
  "en"
] as const;

export const VALID_CORRECTION_MODES = [
  "simple",
  "complete"
] as const satisfies readonly CorrectionMode[];

export const VALID_BLOCK_ZONES = [
  "cue",
  "main",
  "summary"
] as const satisfies readonly CorrectionBlockZone[];

export const VALID_BLOCK_FILL_POLICIES = [
  "ai_filled",
  "student_blank",
  "student_filled",
  "mixed"
] as const satisfies readonly BlockFillPolicy[];
```

---

## 6. Required Blocks by Mode

```ts
export const REQUIRED_BLOCKS_BY_MODE = {
  simple: [
    "question_type",
    "core_keywords",
    "known_info",
    "target",
    "mistake_cause",
    "weakness",
    "formula_or_method",
    "solution_flow",
    "student_blank"
  ],
  complete: [
    "question_type",
    "core_keywords",
    "known_info",
    "target",
    "wrong_step",
    "mistake_cause",
    "weakness",
    "formula_or_method",
    "solution_flow",
    "anti_mistake_tip",
    "review_hint"
  ]
} as const satisfies Record<CorrectionMode, readonly AICorrectionBlockType[]>;
```

---

## 7. Simple Mode Blank Blocks

```ts
export const SIMPLE_MODE_BLANK_BLOCKS = [
  "formula_or_method",
  "solution_flow",
  "student_blank"
] as const satisfies readonly AICorrectionBlockType[];
```

For Simple Mode:

* `formula_or_method` MAY be blank.
* `solution_flow` MAY be blank.
* `student_blank` MUST be blank.
* `student_blank.fillPolicy` MUST be `"student_blank"`.

Simple Mode MUST NOT fully solve every reasoning block.

---

## 8. Block Content Compatibility

```ts
export const BLOCK_CONTENT_COMPATIBILITY = {
  question_type: ["text"],
  core_keywords: ["list"],
  known_info: ["list"],
  target: ["text"],
  wrong_step: ["text"],
  mistake_cause: ["checklist", "list"],
  weakness: ["list"],
  formula_or_method: ["text", "blank"],
  solution_flow: ["steps", "blank"],
  student_blank: ["blank"],
  anti_mistake_tip: ["text"],
  review_hint: ["text"]
} as const satisfies Record<
  AICorrectionBlockType,
  readonly AICorrectionBlockContent["kind"][]
>;
```

The validator MUST reject incompatible combinations.

Examples:

* `known_info` with `{ kind: "text" }` is invalid.
* `solution_flow` with `{ kind: "list" }` is invalid.
* `student_blank` with `{ kind: "text" }` is invalid.

---

## 9. Validator Entry

```ts
export function validateDiagnosisOutput(
  raw: unknown
): DiagnosisOutputValidationResult {
  throw new Error("TODO: implement runtime validation");
}
```

Implementation MUST validate the raw object before casting it to `DiagnosisOutputV1`.

---

## 10. Validation Responsibilities

The validator MUST check:

* raw value is a non-array object
* `schemaVersion` equals `"diagnosis_output_v1"`
* `status` is valid
* `language` is valid
* success output contains all required success fields
* fallback output contains required fallback fields
* field types are valid
* text limits are enforced
* array limits are enforced
* `correctionBlocks` length is valid
* block `type` is valid
* block `zone` is valid
* block `fillPolicy` is valid
* block `content.kind` is compatible with block `type`
* block `order` values are unique
* required block types exist for the selected mode
* Simple Mode contains required blank blocks
* Complete Mode does not return blank `solution_flow`
* text fields do not contain markdown code fences
* text fields do not contain HTML
* text fields do not become essay-style paragraph blobs

The validator SHOULD collect all errors instead of stopping at the first error.

---

## 11. Success Output Rules

For `status: "ok"`:

Required top-level fields:

```ts
export const SUCCESS_OUTPUT_REQUIRED_FIELDS = [
  "schemaVersion",
  "status",
  "mode",
  "language",
  "questionType",
  "coreKeywords",
  "knownInfo",
  "target",
  "wrongStep",
  "mistakeCauses",
  "weakKnowledgePoints",
  "correctThinkingPath",
  "antiMistakeTip",
  "reviewSuggestion",
  "correctionBlocks"
] as const;
```

The validator MUST reject success output if:

* `mode` is missing
* `correctionBlocks` is empty
* any required block type is missing
* any text field exceeds limits
* any block content kind is incompatible
* Simple Mode has no student-completable blank space

---

## 12. Fallback Output Rules

For `status: "needs_more_info"`:

```ts
export const NEEDS_MORE_INFO_REQUIRED_FIELDS = [
  "schemaVersion",
  "status",
  "language",
  "missingFields",
  "message"
] as const;
```

For `status: "unsupported"`:

```ts
export const UNSUPPORTED_REQUIRED_FIELDS = [
  "schemaVersion",
  "status",
  "language",
  "reason",
  "message"
] as const;
```

Fallback output MUST NOT include `correctionBlocks`.

Fallback output MUST NOT be converted into `Diagnosis`.

---

## 13. Unsafe Text Content

The validator MUST reject text containing:

````ts
export const UNSAFE_TEXT_PATTERNS = [
  /```/,
  /<[^>]+>/,
  /<\/[^>]+>/
] as const;
````

The validator SHOULD reject text that is too long even if it does not match these patterns.

---

## 14. Helper Validator Targets

The implementation SHOULD be organized around these helper functions:

```ts
function isRecord(value: unknown): value is Record<string, unknown> {
  return typeof value === "object" && value !== null && !Array.isArray(value);
}

function validateSuccessOutput(
  raw: Record<string, unknown>
): DiagnosisOutputValidationError[] {
  throw new Error("TODO: implement success output validation");
}

function validateFallbackOutput(
  raw: Record<string, unknown>
): DiagnosisOutputValidationError[] {
  throw new Error("TODO: implement fallback output validation");
}

function validateCorrectionBlock(
  block: unknown,
  path: string
): DiagnosisOutputValidationError[] {
  throw new Error("TODO: implement correction block validation");
}

function validateBlockContent(
  blockType: AICorrectionBlock["type"],
  content: unknown,
  path: string
): DiagnosisOutputValidationError[] {
  throw new Error("TODO: implement block content validation");
}
```

---

## 15. Handoff Rules for AI Coding Agents

When implementing `diagnosisOutputValidator.ts`:

* MUST import raw AI output types from `src/types/ai.ts`.
* MUST import domain helper types from `src/types/domain.ts`.
* MUST validate `unknown`, not trusted typed input.
* MUST return `DiagnosisOutputValidationResult`.
* MUST not silently repair invalid AI output.
* MUST not convert AI output into domain objects.
* MUST not call provider SDKs.
* MUST not render UI.
* SHOULD collect multiple validation errors.
* SHOULD keep helper functions small.
* SHOULD keep constants exported for tests.

Domain mapping belongs to a separate mapper file.

Recommended future target:

```ts
src/lib/ai/diagnosisOutputMapper.ts
```
