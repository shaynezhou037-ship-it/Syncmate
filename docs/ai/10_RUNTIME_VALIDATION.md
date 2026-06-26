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
  DiagnosisOutputStatus,
  DiagnosisOutputV1
} from "@/types/ai";

import type {
  BlockFillPolicy,
  CorrectionBlockZone,
  CorrectionMode
} from "@/types/domain";

export type DiagnosisOutputValidationErrorCode =
  | "NOT_OBJECT"
  | "MISSING_SCHEMA_VERSION"
  | "UNSUPPORTED_SCHEMA_VERSION"
  | "INVALID_SCHEMA_VERSION"
  | "INVALID_STATUS"
  | "WRONG_STATUS_FOR_BRANCH"
  | "INVALID_LANGUAGE"
  | "MISSING_REQUIRED_FIELD"
  | "INVALID_FIELD_TYPE"
  | "INVALID_FIELD_VALUE"
  | "EMPTY_CORRECTION_BLOCKS"
  | "ARRAY_TOO_LONG"
  | "ARRAY_TOO_SHORT"
  | "TEXT_TOO_LONG"
  | "TOO_MANY_NEWLINES"
  | "DUPLICATE_ORDER"
  | "MISSING_REQUIRED_BLOCK"
  | "INVALID_BLOCK_TYPE"
  | "INVALID_BLOCK_ZONE"
  | "INVALID_BLOCK_FILL_POLICY"
  | "INVALID_BLOCK_CONTENT_KIND"
  | "INVALID_BLOCK_CONTENT"
  | "MISSING_BLANK_IN_SIMPLE_MODE"
  | "BLANK_NOT_ALLOWED_IN_COMPLETE_MODE"
  | "INVALID_COMPLETE_MODE_FILL"
  | "UNSAFE_TEXT"
  | "FALLBACK_CONTAINS_CORRECTION_BLOCKS";

export type DiagnosisOutputValidationErrorContext = Record<
  string,
  string | number | boolean | null | readonly (string | number | boolean | null)[]
>;

export interface DiagnosisOutputValidationError {
  code: DiagnosisOutputValidationErrorCode;
  path: string;
  message: string;
  context?: DiagnosisOutputValidationErrorContext;
}

export type NonEmptyDiagnosisOutputValidationErrors = [
  DiagnosisOutputValidationError,
  ...DiagnosisOutputValidationError[]
];

export type DiagnosisOutputValidationResult =
  | {
      ok: true;
      status: DiagnosisOutputStatus;
      data: DiagnosisOutputV1;
      errors?: never;
    }
  | {
      ok: false;
      data?: undefined;
      status?: DiagnosisOutputStatus;
      errors: NonEmptyDiagnosisOutputValidationErrors;
    };
```

These types MUST be exported from `src/lib/ai/diagnosisOutputValidator.ts`.

`DiagnosisOutputValidationError.code` is a stable machine-readable error code for branching, logging, tests, and UI grouping.

`DiagnosisOutputValidationError.path` is the field path where the error occurred, for example `target`, `correctionBlocks[2].content`, or `correctThinkingPath[0].content`.

`DiagnosisOutputValidationError.message` is a short human-readable English explanation. Its JavaScript string `.length` MUST NOT exceed `TEXT_FIELD_MAX_LENGTHS.message`.

`DiagnosisOutputValidationError.context` MAY carry structured values needed by UI or tests, such as `maxLength`, `actualLength`, `expectedStatus`, `actualStatus`, `missingField`, or `blockType`.

When `DiagnosisOutputValidationResult.ok` is `true`, validation passed. The result MUST include `status` and `data`, and MUST NOT include `errors`.

When `DiagnosisOutputValidationResult.ok` is `false`, validation failed. The result MUST include a non-empty `errors` array with at least one `DiagnosisOutputValidationError`. It MAY include `status` only when a valid output status was found before branch-specific validation failed.

---

## 4. Output Limits

These constants MUST be imported by the validator implementation.

```ts
export const TEXT_FIELD_MAX_LENGTHS = {
  questionType: 18,
  coreKeyword: 12,
  coreKeywordMaxCount: 5,
  knownInfo: 28,
  knownInfoMaxCount: 5,
  target: 24,
  wrongStep: 40,
  mistakeCause: 28,
  mistakeCauseMaxCount: 4,
  weakKnowledgePoint: 18,
  weakKnowledgePointMaxCount: 4,
  correctThinkingPath: 42,
  correctThinkingPathMaxCount: 6,
  diagnosisStepTitle: 12,
  antiMistakeTip: 42,
  reviewSuggestion: 42,
  correctionBlockTitle: 10,
  correctionBlockText: 48,
  correctionBlockListItem: 24,
  correctionBlockListItemMaxCount: 6,
  correctionBlockChecklistItem: 24,
  correctionBlockChecklistItemMaxCount: 5,
  blankPlaceholder: 24,
  message: 80
} as const;

export const TEXT_FIELD_MAX_NEWLINES = {
  perTextField: 1
} as const;

export const DIAGNOSIS_OUTPUT_LIMITS = {
  questionTypeMaxChars: TEXT_FIELD_MAX_LENGTHS.questionType,
  coreKeywordsMaxItems: TEXT_FIELD_MAX_LENGTHS.coreKeywordMaxCount,
  coreKeywordMaxChars: TEXT_FIELD_MAX_LENGTHS.coreKeyword,
  knownInfoMaxItems: TEXT_FIELD_MAX_LENGTHS.knownInfoMaxCount,
  knownInfoItemMaxChars: TEXT_FIELD_MAX_LENGTHS.knownInfo,
  targetMaxChars: TEXT_FIELD_MAX_LENGTHS.target,
  wrongStepMaxChars: TEXT_FIELD_MAX_LENGTHS.wrongStep,
  mistakeCausesMaxItems: TEXT_FIELD_MAX_LENGTHS.mistakeCauseMaxCount,
  mistakeCauseMaxChars: TEXT_FIELD_MAX_LENGTHS.mistakeCause,
  weakKnowledgePointsMaxItems: TEXT_FIELD_MAX_LENGTHS.weakKnowledgePointMaxCount,
  weakKnowledgePointMaxChars: TEXT_FIELD_MAX_LENGTHS.weakKnowledgePoint,
  correctThinkingPathMaxSteps: TEXT_FIELD_MAX_LENGTHS.correctThinkingPathMaxCount,
  diagnosisStepTitleMaxChars: TEXT_FIELD_MAX_LENGTHS.diagnosisStepTitle,
  diagnosisStepContentMaxChars: TEXT_FIELD_MAX_LENGTHS.correctThinkingPath,
  antiMistakeTipMaxChars: TEXT_FIELD_MAX_LENGTHS.antiMistakeTip,
  reviewSuggestionMaxChars: TEXT_FIELD_MAX_LENGTHS.reviewSuggestion,
  correctionBlocksMaxItems: 12,
  blockTitleMaxChars: TEXT_FIELD_MAX_LENGTHS.correctionBlockTitle,
  textBlockMaxChars: TEXT_FIELD_MAX_LENGTHS.correctionBlockText,
  listBlockMaxItems: TEXT_FIELD_MAX_LENGTHS.correctionBlockListItemMaxCount,
  listBlockItemMaxChars: TEXT_FIELD_MAX_LENGTHS.correctionBlockListItem,
  checklistMaxItems: TEXT_FIELD_MAX_LENGTHS.correctionBlockChecklistItemMaxCount,
  checklistItemMaxChars: TEXT_FIELD_MAX_LENGTHS.correctionBlockChecklistItem,
  blankPlaceholderMaxChars: TEXT_FIELD_MAX_LENGTHS.blankPlaceholder,
  fallbackMessageMaxChars: TEXT_FIELD_MAX_LENGTHS.message,
  textFieldMaxNewlines: TEXT_FIELD_MAX_NEWLINES.perTextField
} as const;
```

These text length values are measured with JavaScript string `.length`.

If visible-character counting is required, a later document update MUST define it explicitly.

These constants are runtime rules.

They are not TypeScript guarantees.

---

## 5. Allowed Values

```ts
export const CURRENT_AI_OUTPUT_SCHEMA_VERSION = "diagnosis_output_v1" as const;

export const SUPPORTED_AI_OUTPUT_SCHEMA_VERSIONS = [
  CURRENT_AI_OUTPUT_SCHEMA_VERSION
] as const;

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

The schema version constants MUST match `CURRENT_AI_OUTPUT_SCHEMA_VERSION` and `SUPPORTED_AI_OUTPUT_SCHEMA_VERSIONS` from `docs/ai/07_AI_OUTPUT_SCHEMA.md`.

---

## 6. Schema Version Gate

`schemaVersion` validation is the first gate in `validateDiagnosisOutput`.

The validator MUST validate `schemaVersion` before validating `status`, branch-specific required fields, text limits, correction blocks, or fallback fields.

M1 uses strict schema version matching:

```ts
raw.schemaVersion MUST be included in SUPPORTED_AI_OUTPUT_SCHEMA_VERSIONS
```

The validator MUST apply these rules:

* If `schemaVersion` is missing, `null`, or `undefined`, reject with `MISSING_SCHEMA_VERSION` at path `schemaVersion`.
* If `schemaVersion` is not a string, reject with `MISSING_SCHEMA_VERSION` at path `schemaVersion`.
* If `schemaVersion` is a string but is not included in `SUPPORTED_AI_OUTPUT_SCHEMA_VERSIONS`, reject with `UNSUPPORTED_SCHEMA_VERSION` at path `schemaVersion`.
* If `schemaVersion` is included in `SUPPORTED_AI_OUTPUT_SCHEMA_VERSIONS`, continue with normal validation.

When `schemaVersion` is missing, non-string, or unsupported, the validator MUST return immediately with the schema version error and MUST NOT continue validating other fields.

This immediate return is an intentional exception to the rule that the validator MUST collect all errors, because field names and field meanings may differ across schema versions.

M1 MUST NOT best-effort parse unknown schema versions.

---

## 7. Required Blocks by Mode

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

## 8. Simple Mode Blank Segments

```ts
export const BLANK_ALLOWED_BLOCK_TYPES = [
  "formula_or_method",
  "solution_flow",
  "student_blank"
] as const satisfies readonly AICorrectionBlockType[];

export const SIMPLE_MODE_MIN_BLANK_SEGMENTS = 1 as const;
```

For Simple Mode:

* A student-completable blank is an `AIBlankSegment` inside `AICorrectionBlockContent` with `kind: "segments"`.
* `correctionBlocks` MUST contain at least `SIMPLE_MODE_MIN_BLANK_SEGMENTS` `AIBlankSegment` across blocks whose `type` is listed in `BLANK_ALLOWED_BLOCK_TYPES`.
* `AIBlankSegment` values MUST appear only in blocks whose `type` is listed in `BLANK_ALLOWED_BLOCK_TYPES`.
* `student_blank.fillPolicy` MUST be `"student_blank"`.

For Complete Mode:

* `correctionBlocks` MUST NOT contain any `AIBlankSegment`.

Simple Mode MUST leave at least one machine-detectable blank segment for the student to complete.

---

## 9. Block Content Compatibility

```ts
export const BLOCK_CONTENT_COMPATIBILITY = {
  question_type: ["text"],
  core_keywords: ["list"],
  known_info: ["list"],
  target: ["text"],
  wrong_step: ["text"],
  mistake_cause: ["checklist", "list"],
  weakness: ["list"],
  formula_or_method: ["text", "segments"],
  solution_flow: ["steps", "segments"],
  student_blank: ["segments"],
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
* `student_blank` with `{ kind: "segments", segments: [] }` is invalid because it contains no `AIBlankSegment`.

---

## 10. Validator Entry

```ts
export function validateDiagnosisOutput(
  raw: unknown
): DiagnosisOutputValidationResult {
  throw new Error("TODO: implement runtime validation");
}
```

Implementation MUST validate the raw object before casting it to `DiagnosisOutputV1`.

---

## 11. Validation Responsibilities

The validator MUST check:

* raw value is a non-array object
* `schemaVersion` exists, is a string, and is included in `SUPPORTED_AI_OUTPUT_SCHEMA_VERSIONS` before any other field validation runs
* `status` is valid
* `language` is valid
* success output contains all required success fields
* fallback output contains required fallback fields
* field types are valid
* text fields are validated against the matching `TEXT_FIELD_MAX_LENGTHS` value
* array fields are validated against the matching max-count value in `TEXT_FIELD_MAX_LENGTHS`
* `correctionBlocks` length is valid
* block `type` is valid
* block `zone` is valid
* block `fillPolicy` is valid
* block `content.kind` is compatible with block `type`
* block `order` values are unique
* required block types exist for the selected mode
* Simple Mode contains at least `SIMPLE_MODE_MIN_BLANK_SEGMENTS` `AIBlankSegment` in allowed block types
* Complete Mode contains no `AIBlankSegment`
* text fields do not contain markdown code fences
* text fields do not contain HTML
* each text field contains at most `TEXT_FIELD_MAX_NEWLINES.perTextField` newline characters

The validator MUST collect all errors instead of stopping at the first error.

---

## 12. Success Output Rules

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

When `status` is `"ok"`, if any field listed in `SUCCESS_OUTPUT_REQUIRED_FIELDS` except `schemaVersion` is missing, `null`, or `undefined`, the validator MUST reject the output and produce one independent validation error for each missing field.

`schemaVersion` is also required, but it is validated first by the schema version gate in Section 6.

The validator MUST reject success output if:

* any field listed in `SUCCESS_OUTPUT_REQUIRED_FIELDS` except `schemaVersion` is missing, `null`, or `undefined`
* `status` is not `"ok"` when the success validation branch is used
* `correctionBlocks` is an empty array
* any required block type is missing
* any text field exceeds the matching value in `TEXT_FIELD_MAX_LENGTHS` measured by JavaScript string `.length`
* any text field contains more than `TEXT_FIELD_MAX_NEWLINES.perTextField` newline characters
* any block content kind is incompatible with its block type
* Simple Mode contains fewer than `SIMPLE_MODE_MIN_BLANK_SEGMENTS` `AIBlankSegment` values across `BLANK_ALLOWED_BLOCK_TYPES`
* Complete Mode contains any `AIBlankSegment`

The validator MUST return `MISSING_BLANK_IN_SIMPLE_MODE` when Simple Mode contains fewer than `SIMPLE_MODE_MIN_BLANK_SEGMENTS` `AIBlankSegment` values. If an allowed blank block exists but contains no `AIBlankSegment`, `path` MUST point to that block, for example `correctionBlocks[2].content.segments`. If no allowed blank block exists, `path` MUST be `correctionBlocks`.

The validator MUST return `BLANK_NOT_ALLOWED_IN_COMPLETE_MODE` when Complete Mode contains any `AIBlankSegment`. `path` MUST point to the offending segment, for example `correctionBlocks[3].content.segments[1]`.

For segmented content, text length validation applies to each `AITextSegment.value`, `AIBlankSegment.placeholder`, `AIBlankSegment.hint`, and `AIBlankSegment.answer` separately. The validator MUST NOT concatenate all segment text before applying `TEXT_FIELD_MAX_LENGTHS`.

---

## 13. Fallback Output Rules

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

When `status` is `"needs_more_info"`, if any field listed in `NEEDS_MORE_INFO_REQUIRED_FIELDS` except `schemaVersion` is missing, `null`, or `undefined`, the validator MUST reject the output and produce one independent validation error for each missing field.

`schemaVersion` is also required, but it is validated first by the schema version gate in Section 6.

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

When `status` is `"unsupported"`, if any field listed in `UNSUPPORTED_REQUIRED_FIELDS` except `schemaVersion` is missing, `null`, or `undefined`, the validator MUST reject the output and produce one independent validation error for each missing field.

`schemaVersion` is also required, but it is validated first by the schema version gate in Section 6.

Fallback output MUST NOT include `correctionBlocks`.

Fallback output MUST NOT be converted into `Diagnosis`.

---

## 14. Unsafe Text Content

The validator MUST reject text containing markdown code fences or literal HTML tag syntax.

````ts
export const UNSAFE_TEXT_PATTERNS = [
  /```/,
  /<\/?[a-zA-Z][a-zA-Z0-9]*(?:\s+[a-zA-Z_:][\w:.-]*(?:\s*=\s*(?:"[^"]*"|'[^']*'|[^\s"'=<>`]+))?)*\s*\/?>/
] as const;
````

The HTML pattern MUST match only tag-like syntax where `<` is followed by an optional `/` and an ASCII tag name.

The HTML pattern MUST NOT treat mathematical comparison operators as HTML.

These examples MUST NOT match `UNSAFE_TEXT_PATTERNS`:

```txt
x < 3 > y
0 < x < 10
a<b
成本 > 收益
```

These examples MUST match `UNSAFE_TEXT_PATTERNS`:

```txt
<div>
</span>
<img src=x>
<b>加粗</b>
```

HTML entities such as `&lt;`, `&gt;`, and `&#60;` are not matched by `UNSAFE_TEXT_PATTERNS` in M1 because fallback and diagnosis text is rendered as plain text, and literal tag syntax is the unsafe input this rule targets.

The validator MUST reject any text field whose JavaScript string `.length` exceeds the matching value in `TEXT_FIELD_MAX_LENGTHS`, even if it does not match these patterns.

The validator MUST reject any text field containing more than `TEXT_FIELD_MAX_NEWLINES.perTextField` newline characters.

---

## 15. Helper Validator Targets

The implementation SHOULD be organized around these helper functions:

The helper validators MUST return `DiagnosisOutputValidationError[]` and MUST NOT throw for expected validation failures.

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

## 16. Handoff Rules for AI Coding Agents

When implementing `diagnosisOutputValidator.ts`:

* MUST import raw AI output types from `src/types/ai.ts`.
* MUST import domain helper types from `src/types/domain.ts`.
* MUST validate `unknown`, not trusted typed input.
* MUST return `DiagnosisOutputValidationResult`.
* MUST not silently repair invalid AI output.
* MUST not convert AI output into domain objects.
* MUST not call provider SDKs.
* MUST not render UI.
* MUST collect multiple validation errors.
* SHOULD keep helper functions small.
* SHOULD keep constants exported for tests.

Domain mapping belongs to a separate mapper file.

Recommended future target:

```ts
src/lib/ai/diagnosisOutputMapper.ts
```
