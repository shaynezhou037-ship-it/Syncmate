# AI Output Schema

> Source of truth for SyncMate M1 raw AI diagnosis output.

Copy the TypeScript block in this file into `src/types/ai.ts` unchanged.

---

## 1. Role of This File

This file defines:

* raw AI diagnosis output types
* success output shape
* fallback output shape
* AI correction block shape

This file does not define:

* domain model
* app-generated IDs
* provider metadata
* token usage
* cost tracking
* runtime validation rules
* prompt text
* API request or response contracts
* UI rendering logic

Runtime validation belongs to `docs/ai/10_RUNTIME_VALIDATION.md`.

Domain types belong to `docs/data/00_DATA_MODEL.md`.

API contract belongs to `docs/api/01_API_CONTRACT.md`.

---

## 2. Raw AI Output Types

```ts
import type {
  BlockFillPolicy,
  CorrectionBlockType,
  CorrectionBlockZone,
  CorrectionMode,
  DiagnosisStep
} from "@/types/domain";

export type DiagnosisOutputSchemaVersion = "diagnosis_output_v1";

export const CURRENT_AI_OUTPUT_SCHEMA_VERSION =
  "diagnosis_output_v1" as const satisfies DiagnosisOutputSchemaVersion;

export const SUPPORTED_AI_OUTPUT_SCHEMA_VERSIONS = [
  CURRENT_AI_OUTPUT_SCHEMA_VERSION
] as const satisfies readonly DiagnosisOutputSchemaVersion[];

export const DIAGNOSIS_OUTPUT_SCHEMA_VERSION = CURRENT_AI_OUTPUT_SCHEMA_VERSION;

export type DiagnosisOutputLanguage = "zh-CN" | "en";

export type DiagnosisOutputStatus =
  | "ok"
  | "needs_more_info"
  | "unsupported";

export type DiagnosisMissingField =
  | "question"
  | "student_wrong_solution"
  | "grade_level"
  | "subject"
  | "reference_solution";

export type DiagnosisUnsupportedReason =
  | "unsupported_subject"
  | "non_structured_question"
  | "unreadable_question"
  | "unsafe_or_irrelevant_content";

export type DiagnosisOutputV1 =
  | DiagnosisSuccessOutputV1
  | DiagnosisNeedsMoreInfoOutputV1
  | DiagnosisUnsupportedOutputV1;

export interface DiagnosisSuccessOutputV1 {
  schemaVersion: DiagnosisOutputSchemaVersion;
  status: "ok";
  mode: CorrectionMode;
  language: DiagnosisOutputLanguage;

  questionType: string;
  coreKeywords: string[];
  knownInfo: string[];
  target: string;

  wrongStep: string;
  mistakeCauses: string[];
  weakKnowledgePoints: string[];

  correctThinkingPath: DiagnosisStep[];

  antiMistakeTip: string;
  reviewSuggestion: string;

  correctionBlocks: AICorrectionBlock[];
}

export interface DiagnosisNeedsMoreInfoOutputV1 {
  schemaVersion: DiagnosisOutputSchemaVersion;
  status: "needs_more_info";
  language: DiagnosisOutputLanguage;
  missingFields: DiagnosisMissingField[];
  message: string;
}

export interface DiagnosisUnsupportedOutputV1 {
  schemaVersion: DiagnosisOutputSchemaVersion;
  status: "unsupported";
  language: DiagnosisOutputLanguage;
  reason: DiagnosisUnsupportedReason;
  message: string;
}

export type AICorrectionBlockType = Exclude<
  CorrectionBlockType,
  "question" | "custom"
>;

export interface AITextSegment {
  kind: "text";
  value: string;
}

export interface AIBlankSegment {
  kind: "blank";
  placeholder: string;
  hint?: string;
  answer?: string;
}

export type AICorrectionContentSegment = AITextSegment | AIBlankSegment;

export type AICorrectionBlockContent =
  | {
      kind: "text";
      text: string;
    }
  | {
      kind: "list";
      items: string[];
    }
  | {
      kind: "steps";
      steps: DiagnosisStep[];
    }
  | {
      kind: "checklist";
      items: AIChecklistItem[];
    }
  | {
      kind: "segments";
      segments: AICorrectionContentSegment[];
    };

export interface AIChecklistItem {
  text: string;
}

export interface AICorrectionBlock {
  type: AICorrectionBlockType;
  zone: CorrectionBlockZone;
  title: string;
  order: number;
  fillPolicy: BlockFillPolicy;
  content: AICorrectionBlockContent;
  isLogicBlock?: boolean;
}


```

---

## 3. Schema Version Policy

M1 uses a single strict schema version:

```ts
CURRENT_AI_OUTPUT_SCHEMA_VERSION === "diagnosis_output_v1"
SUPPORTED_AI_OUTPUT_SCHEMA_VERSIONS === ["diagnosis_output_v1"]
```

The version string format is `diagnosis_output_v<major>`.

A major version identifies a validation and mapping contract. Changing field names, field meanings, correction block content shapes, required fields, or mapping semantics MUST create a new major version string.

M1 supports only `CURRENT_AI_OUTPUT_SCHEMA_VERSION`. Runtime validation MUST reject any `schemaVersion` not listed in `SUPPORTED_AI_OUTPUT_SCHEMA_VERSIONS`.

---

## 4. Output Boundary

Raw AI output MUST NOT include app-generated fields.

Do not include these fields in AI output:

* `Diagnosis.id`
* `Diagnosis.provider`
* `Diagnosis.createdAt`
* `Mistake.id`
* `CorrectionNote.id`
* `CorrectionBlock.id`
* `ReviewTask.id`
* token usage
* model name
* cost
* user ID

The app layer generates those fields after validation.

---

## 5. Success Output

`DiagnosisSuccessOutputV1` is used only when diagnosis can be generated.

It contains:

* diagnosis summary fields
* reasoning path
* correction note blocks

It does not contain:

* app IDs
* provider metadata
* billing metadata
* review task data

---

## 6. Fallback Output

`DiagnosisNeedsMoreInfoOutputV1` is used when the input is insufficient.

`DiagnosisUnsupportedOutputV1` is used when the request cannot be handled by M1.

Fallback output MUST NOT be converted into `Diagnosis`.

Fallback output MUST NOT contain `correctionBlocks`.

---

## 7. AI Correction Blocks

AI correction blocks are raw blocks before domain mapping.

AI correction blocks MUST NOT include `id`.

The app layer maps `AICorrectionBlock` into domain `CorrectionBlock`.

The app layer creates:

* block ID
* note ID
* timestamps
* default editability
* checklist IDs
* checklist checked state
* blank IDs for `AIBlankSegment`

### Simple Mode Blank Segments

A student-completable blank is represented only by `AIBlankSegment` inside `AICorrectionBlockContent` with `kind: "segments"`.

`AIBlankSegment.placeholder` is the short visible prompt for the student.

`AIBlankSegment.hint` is optional helper text.

`AIBlankSegment.answer` is optional. In Simple Mode, if present, it is the expected answer for internal checking or later review UI and MUST NOT be rendered as visible student-facing text before the student answers.

`AIBlankSegment` MUST NOT include `blankId` in raw AI output. The mapper creates app-owned blank IDs after runtime validation.

Only these block types MAY contain `AIBlankSegment` values:

* `formula_or_method`
* `solution_flow`
* `student_blank`

All other block types MUST NOT contain `AIBlankSegment` values.

Complete Mode MUST NOT contain any `AIBlankSegment` values.

For text length validation, each `AITextSegment.value`, `AIBlankSegment.placeholder`, `AIBlankSegment.hint`, and `AIBlankSegment.answer` is validated as an individual text field. The validator MUST NOT concatenate all segments before applying text length limits.

---

## 8. Validation Boundary

This schema does not guarantee runtime correctness.

TypeScript does not enforce:

* schema version compatibility
* string length
* array length
* required block types by mode
* duplicate block order
* compatible block content kind
* Simple Mode `AIBlankSegment` requirements
* Complete Mode no-blank requirements
* unsafe text content

Those checks belong to `docs/ai/10_RUNTIME_VALIDATION.md`.

---

## 9. Minimal Valid Example

```ts
export const MINIMAL_DIAGNOSIS_OUTPUT_V1_EXAMPLE = {
  schemaVersion: "diagnosis_output_v1",
  status: "ok",
  mode: "simple",
  language: "zh-CN",

  questionType: "一次方程",
  coreKeywords: ["移项", "合并同类项"],
  knownInfo: ["2x + 3 = 7"],
  target: "求 x 的值",

  wrongStep: "移项时符号没有改变",
  mistakeCauses: ["符号规则不熟"],
  weakKnowledgePoints: ["移项"],

  correctThinkingPath: [
    {
      order: 1,
      title: "移项",
      content: "把 3 移到等号右边时要变成 -3。",
      isWrongStep: true,
      isBlankInSimpleMode: false
    }
  ],

  antiMistakeTip: "移项先看符号是否改变。",
  reviewSuggestion: "复习 3 道移项题。",

  correctionBlocks: [
    {
      type: "question_type",
      zone: "cue",
      title: "题型",
      order: 1,
      fillPolicy: "ai_filled",
      content: {
        kind: "text",
        text: "一次方程"
      }
    },
    {
      type: "student_blank",
      zone: "main",
      title: "自己补",
      order: 2,
      fillPolicy: "student_blank",
            content: {
        kind: "segments",
        segments: [
          {
            kind: "text",
            value: "请补全关键步骤："
          },
          {
            kind: "blank",
            placeholder: "写出移项后的式子",
            hint: "注意符号变化"
          }
        ]
      },
      isLogicBlock: true
    }
  ]
} satisfies DiagnosisSuccessOutputV1;
```

---

## 10. Handoff Rules for AI Coding Agents

When implementing `src/types/ai.ts`:

* MUST copy the TypeScript block in this file.
* MUST import shared domain types from `src/types/domain.ts`.
* MUST NOT duplicate domain types.
* MUST NOT add runtime validation constants here.
* MUST NOT add provider SDK types here.
* MUST NOT add API request or response types here.
* MUST keep fallback output separate from success output.
