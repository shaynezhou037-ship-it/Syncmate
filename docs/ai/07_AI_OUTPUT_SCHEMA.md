# AI Output Schema

> Source of truth for SyncMate M1 AI diagnosis output.
> This document defines the raw JSON shape returned by the diagnosis model before conversion into domain objects.

---
Runtime validation rules are defined in docs/ai/10_RUNTIME_VALIDATION.md

## 1. Scope

This file defines:

```text id="lyixte"
- raw diagnosis output JSON
- fallback output JSON
- mapping into domain objects
- runtime validation requirements
```

This file does not define:

```text id="1dsm8i"
- domain model
- API contract
- provider SDK response
- prompt text
- frontend component props
```

Domain types are defined in:

```text id="ms6n62"
docs/data/00_DATA_MODEL.md
src/types/domain.ts
```

---

## 2. Compile Rule

All TypeScript blocks in this file MUST be copyable into real `.ts` files.

Natural-language requirements MUST NOT be written as fake `as const` rule arrays unless they are actually imported and executed by code.

Runtime constraints such as text length, array length, and block compatibility MUST be enforced by validator code.

Required validator target:

```text id="kpgggm"
src/lib/ai/diagnosisOutputValidator.ts
```

---

## 3. Type Imports

```ts id="phsgju"
import type {
  BlockFillPolicy,
  CorrectionBlockType,
  CorrectionBlockZone,
  CorrectionMode,
  DiagnosisStep
} from "@/types/domain";
```

---

## 4. Schema Version

```ts id="qsmkya"
export type DiagnosisOutputSchemaVersion = "diagnosis_output_v1";
```

---

## 5. Output Union

```ts id="pw01hl"
export type DiagnosisOutputV1 =
  | DiagnosisSuccessOutputV1
  | DiagnosisNeedsMoreInfoOutputV1
  | DiagnosisUnsupportedOutputV1;
```

---

## 6. Success Output

```ts id="2pye11"
export interface DiagnosisSuccessOutputV1 {
  schemaVersion: "diagnosis_output_v1";
  status: "ok";
  mode: CorrectionMode;
  language: "zh-CN" | "en";

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
```

Notes:

```text id="nsj6hd"
- The AI output does not include provider, model name, token usage, cost, or internal config.
- The app layer creates Diagnosis.id, Diagnosis.provider, Diagnosis.createdAt, CorrectionNote.id, and CorrectionBlock.id.
- The AI MUST NOT overwrite confirmed question text.
```

---

## 7. Fallback Outputs

```ts id="zwh690"
export interface DiagnosisNeedsMoreInfoOutputV1 {
  schemaVersion: "diagnosis_output_v1";
  status: "needs_more_info";
  language: "zh-CN" | "en";
  missingFields: DiagnosisMissingField[];
  message: string;
}
```

```ts id="40r1jn"
export interface DiagnosisUnsupportedOutputV1 {
  schemaVersion: "diagnosis_output_v1";
  status: "unsupported";
  language: "zh-CN" | "en";
  reason: DiagnosisUnsupportedReason;
  message: string;
}
```

```ts id="i8ze1m"
export type DiagnosisMissingField =
  | "confirmed_question_text"
  | "student_wrong_solution"
  | "subject"
  | "grade_level";
```

```ts id="st48oi"
export type DiagnosisUnsupportedReason =
  | "not_a_learning_question"
  | "subject_not_supported_in_m1"
  | "question_too_unclear"
  | "unsafe_or_irrelevant_input";
```

---

## 8. AI Correction Block

```ts id="f55knl"
export type AICorrectionBlockType = Exclude<
  CorrectionBlockType,
  "question" | "custom"
>;
```

```ts id="073hfm"
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

```ts id="8sjwb6"
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
      kind: "blank";
      placeholder: string;
    };
```

```ts id="sagpxh"
export interface AIChecklistItem {
  text: string;
  checked: boolean;
}
```

App conversion MUST generate `CorrectionBlock.id` and `ChecklistItem.id`.

---

## 9. Required Block Types by Mode

These are schema requirements, but they are not enforced by TypeScript alone.
They MUST be enforced in `diagnosisOutputValidator.ts`.

### Simple Mode

Required block types:

```text id="1fnwns"
question_type
core_keywords
known_info
target
mistake_cause
weakness
formula_or_method
solution_flow
student_blank
```

Blank block types:

```text id="7071yw"
formula_or_method
solution_flow
student_blank
```

### Complete Mode

Required block types:

```text id="r1rnfn"
question_type
core_keywords
known_info
target
wrong_step
mistake_cause
weakness
formula_or_method
solution_flow
anti_mistake_tip
review_hint
```

---

## 10. Block Content Compatibility

This must be enforced by runtime validation.

```text id="jmc8aw"
question_type      → text
core_keywords      → list
known_info         → list
target             → text
wrong_step         → text
mistake_cause      → checklist | list
weakness           → list
formula_or_method  → text | blank
solution_flow      → steps | blank
student_blank      → blank
anti_mistake_tip   → text
review_hint        → text
```

---

## 11. Runtime Validation Requirements

`diagnosisOutputValidator.ts` MUST validate:

```text id="z3sg3p"
- raw value is valid JSON object
- schemaVersion equals "diagnosis_output_v1"
- status is one of: ok, needs_more_info, unsupported
- success output contains all required fields
- fallback output contains required fallback fields
- all correctionBlocks have valid type, zone, fillPolicy, content
- all block content kinds are compatible with block type
- simple mode contains required blank blocks
- complete mode contains required filled blocks
- correctionBlocks order values are unique
- no markdown code fences
- no HTML
- no essay-style paragraph blob
- all text and array length limits are enforced
```

Validator function target:

```ts id="2q67lu"
export interface DiagnosisOutputValidationResult {
  ok: boolean;
  data?: DiagnosisOutputV1;
  errors: string[];
}

export function validateDiagnosisOutput(
  raw: unknown
): DiagnosisOutputValidationResult {
  throw new Error("TODO: implement runtime validation");
}
```

---

## 12. Text and Array Limits

These are runtime validation limits, not TypeScript guarantees.

```text id="qjry98"
questionType: <= 18 chars
coreKeywords: <= 5 items, each <= 12 chars
knownInfo: <= 5 items, each <= 28 chars
target: <= 24 chars
wrongStep: <= 40 chars
mistakeCauses: <= 4 items, each <= 28 chars
weakKnowledgePoints: <= 4 items, each <= 18 chars
correctThinkingPath: <= 6 steps
DiagnosisStep.title: <= 12 chars
DiagnosisStep.content: <= 42 chars
antiMistakeTip: <= 42 chars
reviewSuggestion: <= 42 chars
correctionBlocks: <= 12 items
block.title: <= 10 chars
text block content: <= 48 chars
list block items: <= 6 items
checklist block items: <= 5 items
```

---

## 13. Domain Mapping

The app layer converts successful AI output into:

```text id="kfh8yz"
Diagnosis
CorrectionNote
CorrectionBlock[]
```

Mapping:

```text id="x6kaku"
Diagnosis.questionType          ← output.questionType
Diagnosis.coreKeywords          ← output.coreKeywords
Diagnosis.knownInfo             ← output.knownInfo
Diagnosis.target                ← output.target
Diagnosis.wrongStep             ← output.wrongStep
Diagnosis.mistakeCauses         ← output.mistakeCauses
Diagnosis.weakKnowledgePoints   ← output.weakKnowledgePoints
Diagnosis.correctThinkingPath   ← output.correctThinkingPath
Diagnosis.antiMistakeTip        ← output.antiMistakeTip
Diagnosis.reviewSuggestion      ← output.reviewSuggestion

CorrectionNote.mode             ← output.mode
CorrectionNote.blocks           ← output.correctionBlocks mapped into domain CorrectionBlock[]
```

App-generated fields:

```text id="whe4mh"
Diagnosis.id
Diagnosis.provider
Diagnosis.createdAt
CorrectionNote.id
CorrectionNote.createdAt
CorrectionNote.updatedAt
CorrectionBlock.id
CorrectionBlock.isEditable
ChecklistItem.id
```

---

## 14. Minimal Valid Success Example

```json id="7s6e1m"
{
  "schemaVersion": "diagnosis_output_v1",
  "status": "ok",
  "mode": "complete",
  "language": "zh-CN",
  "questionType": "圆柱截段问题",
  "coreKeywords": ["截成3段", "表面积增加", "求体积"],
  "knownInfo": ["长4m=40dm", "截成3段", "增加24dm²"],
  "target": "求原圆木体积",
  "wrongStep": "把截成3段当成切3刀。",
  "mistakeCauses": ["段数和刀数混淆", "新增截面数算错"],
  "weakKnowledgePoints": ["圆柱体积", "截段表面积", "单位换算"],
  "correctThinkingPath": [
    {
      "order": 1,
      "title": "切刀数",
      "content": "3段需要2刀。",
      "isWrongStep": true
    },
    {
      "order": 2,
      "title": "截面数",
      "content": "2刀共增加4个圆截面。"
    }
  ],
  "antiMistakeTip": "截成n段，先想n−1刀。",
  "reviewSuggestion": "复习截段表面积与单位换算。",
  "correctionBlocks": [
    {
      "type": "known_info",
      "zone": "main",
      "title": "已知",
      "order": 1,
      "fillPolicy": "ai_filled",
      "content": {
        "kind": "list",
        "items": ["长4m=40dm", "截成3段", "增加24dm²"]
      }
    },
    {
      "type": "mistake_cause",
      "zone": "cue",
      "title": "错因",
      "order": 2,
      "fillPolicy": "mixed",
      "content": {
        "kind": "checklist",
        "items": [
          {
            "text": "把3段当成3刀",
            "checked": true
          }
        ]
      }
    },
    {
      "type": "solution_flow",
      "zone": "main",
      "title": "流程",
      "order": 3,
      "fillPolicy": "ai_filled",
      "isLogicBlock": true,
      "content": {
        "kind": "steps",
        "steps": [
          {
            "order": 1,
            "title": "切刀",
            "content": "3段→2刀"
          },
          {
            "order": 2,
            "title": "截面",
            "content": "2刀→4截面"
          }
        ]
      }
    }
  ]
}
```

---

## 15. Minimal Valid Fallback Example

```json id="qmrt83"
{
  "schemaVersion": "diagnosis_output_v1",
  "status": "needs_more_info",
  "language": "zh-CN",
  "missingFields": ["student_wrong_solution"],
  "message": "请补充你的原始解题过程。"
}
```

---

## 16. FUTURE

Future fields MUST NOT be added to M1 output unless M1 scope changes.

Future candidates:

```text id="jkbyyc"
similarQuestions
difficultyEstimate
examFrequency
parentSummary
teacherSummary
costMetadata
promptVersion
modelName
```
