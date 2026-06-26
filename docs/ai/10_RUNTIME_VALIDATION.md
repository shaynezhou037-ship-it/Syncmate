# Runtime Validation

> Source of truth for validating AI diagnosis output at runtime.

---

## 1. Scope

This file defines how to validate raw AI diagnosis output before converting it into domain objects.

Target implementation file:

```text id="zmfkj3"
src/lib/ai/diagnosisOutputValidator.ts
```

Related files:

```text id="wu1qqd"
docs/ai/07_AI_OUTPUT_SCHEMA.md
docs/data/00_DATA_MODEL.md
src/types/domain.ts
src/types/ai.ts
```

---

## 2. Validation Boundary

Runtime validation MUST run after:

```text id="u1mohp"
raw model response received
```

and before:

```text id="u6oxd5"
AI output is converted into Diagnosis / CorrectionNote / CorrectionBlock
```

Invalid output MUST NOT enter domain state.

---

## 3. Validator Result Type

```ts id="dzqydk"
import type { DiagnosisOutputV1 } from "@/types/ai";

export interface DiagnosisOutputValidationResult {
  ok: boolean;
  data?: DiagnosisOutputV1;
  errors: string[];
}
```

---

## 4. Runtime Limits

These constants MUST be imported and used by the validator.

```ts id="3vqcrn"
export const DIAGNOSIS_OUTPUT_LIMITS = {
  questionTypeMaxChars: 18,
  keywordMaxChars: 12,
  listItemMaxChars: 28,
  targetMaxChars: 24,
  wrongStepMaxChars: 40,
  causeMaxChars: 28,
  weakPointMaxChars: 18,
  stepTitleMaxChars: 12,
  stepContentMaxChars: 42,
  tipMaxChars: 42,
  reviewSuggestionMaxChars: 42,
  blockTitleMaxChars: 10,
  blockTextMaxChars: 48,

  coreKeywordsMaxItems: 5,
  knownInfoMaxItems: 5,
  mistakeCausesMaxItems: 4,
  weakKnowledgePointsMaxItems: 4,
  correctThinkingPathMaxSteps: 6,
  correctionBlocksMaxItems: 12,
  listBlockMaxItems: 6,
  checklistBlockMaxItems: 5
} as const;
```

---

## 5. Valid Block Content Matrix

This object MUST be used by the validator.

```ts id="m7s68u"
import type { AICorrectionBlockContent } from "@/types/ai";
import type { CorrectionBlockType } from "@/types/domain";

export type AIBlockContentKind = AICorrectionBlockContent["kind"];

export const VALID_BLOCK_CONTENT_KIND: Record<
  Exclude<CorrectionBlockType, "question" | "custom">,
  readonly AIBlockContentKind[]
> = {
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
} as const;
```

---

## 6. Required Blocks by Mode

These constants MUST be used by the validator.

```ts id="tg3v6f"
import type { CorrectionMode, CorrectionBlockType } from "@/types/domain";

export type AICorrectionBlockType = Exclude<
  CorrectionBlockType,
  "question" | "custom"
>;

export const REQUIRED_BLOCKS_BY_MODE: Record<
  CorrectionMode,
  readonly AICorrectionBlockType[]
> = {
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
} as const;
```

```ts id="z22fsu"
export const SIMPLE_MODE_BLANK_BLOCKS: readonly AICorrectionBlockType[] = [
  "formula_or_method",
  "solution_flow",
  "student_blank"
] as const;
```

---

## 7. Required Validator Function

```ts id="bnkj7m"
export function validateDiagnosisOutput(
  raw: unknown
): DiagnosisOutputValidationResult {
  const errors: string[] = [];

  if (!isRecord(raw)) {
    return {
      ok: false,
      errors: ["Output must be a JSON object."]
    };
  }

  validateBaseFields(raw, errors);

  if (raw.status === "ok") {
    validateSuccessOutput(raw, errors);
  }

  if (raw.status === "needs_more_info") {
    validateNeedsMoreInfoOutput(raw, errors);
  }

  if (raw.status === "unsupported") {
    validateUnsupportedOutput(raw, errors);
  }

  if (errors.length > 0) {
    return { ok: false, errors };
  }

  return {
    ok: true,
    data: raw as DiagnosisOutputV1,
    errors: []
  };
}
```

---

## 8. Required Helper Functions

The implementation MUST include these helper functions or equivalent logic.

```ts id="2hp3c7"
function isRecord(value: unknown): value is Record<string, unknown> {
  return typeof value === "object" && value !== null && !Array.isArray(value);
}
```

```ts id="4vro9g"
function isString(value: unknown): value is string {
  return typeof value === "string";
}
```

```ts id="tqi4n0"
function isStringArray(value: unknown): value is string[] {
  return Array.isArray(value) && value.every(isString);
}
```

````ts id="lj0lsi"
function hasNoMarkdownOrHtml(value: string): boolean {
  return !value.includes("```") && !/<[a-z][\s\S]*>/i.test(value);
}
````

```ts id="1m64cb"
function isShortText(value: string, maxChars: number): boolean {
  return value.length <= maxChars && hasNoMarkdownOrHtml(value);
}
```

---

## 9. Base Validation

```ts id="mzcue5"
function validateBaseFields(
  value: Record<string, unknown>,
  errors: string[]
): void {
  if (value.schemaVersion !== "diagnosis_output_v1") {
    errors.push("Invalid schemaVersion.");
  }

  if (
    value.status !== "ok" &&
    value.status !== "needs_more_info" &&
    value.status !== "unsupported"
  ) {
    errors.push("Invalid status.");
  }

  if (value.language !== "zh-CN" && value.language !== "en") {
    errors.push("Invalid language.");
  }
}
```

---

## 10. Success Output Validation

```ts id="rad3wk"
function validateSuccessOutput(
  value: Record<string, unknown>,
  errors: string[]
): void {
  if (value.mode !== "simple" && value.mode !== "complete") {
    errors.push("Invalid mode.");
  }

  if (!isShortTextValue(value.questionType, DIAGNOSIS_OUTPUT_LIMITS.questionTypeMaxChars)) {
    errors.push("Invalid questionType.");
  }

  if (!isLimitedStringArray(value.coreKeywords, DIAGNOSIS_OUTPUT_LIMITS.coreKeywordsMaxItems, DIAGNOSIS_OUTPUT_LIMITS.keywordMaxChars)) {
    errors.push("Invalid coreKeywords.");
  }

  if (!isLimitedStringArray(value.knownInfo, DIAGNOSIS_OUTPUT_LIMITS.knownInfoMaxItems, DIAGNOSIS_OUTPUT_LIMITS.listItemMaxChars)) {
    errors.push("Invalid knownInfo.");
  }

  if (!isShortTextValue(value.target, DIAGNOSIS_OUTPUT_LIMITS.targetMaxChars)) {
    errors.push("Invalid target.");
  }

  if (!isShortTextValue(value.wrongStep, DIAGNOSIS_OUTPUT_LIMITS.wrongStepMaxChars)) {
    errors.push("Invalid wrongStep.");
  }

  if (!isLimitedStringArray(value.mistakeCauses, DIAGNOSIS_OUTPUT_LIMITS.mistakeCausesMaxItems, DIAGNOSIS_OUTPUT_LIMITS.causeMaxChars)) {
    errors.push("Invalid mistakeCauses.");
  }

  if (!isLimitedStringArray(value.weakKnowledgePoints, DIAGNOSIS_OUTPUT_LIMITS.weakKnowledgePointsMaxItems, DIAGNOSIS_OUTPUT_LIMITS.weakPointMaxChars)) {
    errors.push("Invalid weakKnowledgePoints.");
  }

  validateDiagnosisSteps(value.correctThinkingPath, errors);

  if (!isShortTextValue(value.antiMistakeTip, DIAGNOSIS_OUTPUT_LIMITS.tipMaxChars)) {
    errors.push("Invalid antiMistakeTip.");
  }

  if (!isShortTextValue(value.reviewSuggestion, DIAGNOSIS_OUTPUT_LIMITS.reviewSuggestionMaxChars)) {
    errors.push("Invalid reviewSuggestion.");
  }

  validateCorrectionBlocks(value.correctionBlocks, value.mode, errors);
}
```

---

## 11. Shared Scalar Validators

```ts id="klzrvd"
function isShortTextValue(value: unknown, maxChars: number): value is string {
  return isString(value) && isShortText(value, maxChars);
}
```

```ts id="wuqkk0"
function isLimitedStringArray(
  value: unknown,
  maxItems: number,
  maxChars: number
): value is string[] {
  return (
    isStringArray(value) &&
    value.length <= maxItems &&
    value.every((item) => isShortText(item, maxChars))
  );
}
```

---

## 12. Diagnosis Step Validation

```ts id="jbicjo"
import type { DiagnosisStep } from "@/types/domain";

function validateDiagnosisSteps(value: unknown, errors: string[]): void {
  if (!Array.isArray(value)) {
    errors.push("correctThinkingPath must be an array.");
    return;
  }

  if (value.length > DIAGNOSIS_OUTPUT_LIMITS.correctThinkingPathMaxSteps) {
    errors.push("correctThinkingPath has too many steps.");
  }

  for (const step of value) {
    if (!isDiagnosisStep(step)) {
      errors.push("Invalid DiagnosisStep.");
    }
  }
}
```

```ts id="8yz4yr"
function isDiagnosisStep(value: unknown): value is DiagnosisStep {
  if (!isRecord(value)) return false;

  return (
    typeof value.order === "number" &&
    isShortTextValue(value.title, DIAGNOSIS_OUTPUT_LIMITS.stepTitleMaxChars) &&
    isShortTextValue(value.content, DIAGNOSIS_OUTPUT_LIMITS.stepContentMaxChars) &&
    (value.isWrongStep === undefined || typeof value.isWrongStep === "boolean") &&
    (value.isBlankInSimpleMode === undefined ||
      typeof value.isBlankInSimpleMode === "boolean")
  );
}
```

---

## 13. Correction Block Validation

```ts id="7jw00s"
import type { AICorrectionBlock } from "@/types/ai";

function validateCorrectionBlocks(
  value: unknown,
  mode: unknown,
  errors: string[]
): void {
  if (!Array.isArray(value)) {
    errors.push("correctionBlocks must be an array.");
    return;
  }

  if (value.length > DIAGNOSIS_OUTPUT_LIMITS.correctionBlocksMaxItems) {
    errors.push("correctionBlocks has too many items.");
  }

  const orders = new Set<number>();
  const blockTypes = new Set<string>();

  for (const block of value) {
    if (!isAICorrectionBlock(block)) {
      errors.push("Invalid correction block.");
      continue;
    }

    if (orders.has(block.order)) {
      errors.push("Duplicate correction block order.");
    }

    orders.add(block.order);
    blockTypes.add(block.type);

    validateBlockContentCompatibility(block, errors);
    validateBlockContentLength(block, errors);
  }

  if (mode === "simple" || mode === "complete") {
    validateRequiredBlocks(mode, blockTypes, errors);
  }

  if (mode === "simple") {
    validateSimpleModeBlankBlocks(value, errors);
  }
}
```

```ts id="qyl1kt"
function isAICorrectionBlock(value: unknown): value is AICorrectionBlock {
  if (!isRecord(value)) return false;

  return (
    isValidAIBlockType(value.type) &&
    isValidZone(value.zone) &&
    isShortTextValue(value.title, DIAGNOSIS_OUTPUT_LIMITS.blockTitleMaxChars) &&
    typeof value.order === "number" &&
    isValidFillPolicy(value.fillPolicy) &&
    isRecord(value.content) &&
    (value.isLogicBlock === undefined || typeof value.isLogicBlock === "boolean")
  );
}
```

---

## 14. Correction Block Enum Validators

These validators MUST derive from source-of-truth unions.

```ts id="u41x41"
const AI_BLOCK_TYPES: readonly AICorrectionBlockType[] = [
  "question_type",
  "core_keywords",
  "known_info",
  "target",
  "wrong_step",
  "mistake_cause",
  "weakness",
  "formula_or_method",
  "solution_flow",
  "student_blank",
  "anti_mistake_tip",
  "review_hint"
] as const;
```

```ts id="7oc3pn"
function isValidAIBlockType(value: unknown): value is AICorrectionBlockType {
  return typeof value === "string" && AI_BLOCK_TYPES.includes(value as AICorrectionBlockType);
}
```

```ts id="w28vla"
function isValidZone(value: unknown): value is CorrectionBlockZone {
  return value === "cue" || value === "main" || value === "summary";
}
```

```ts id="4eh6mv"
function isValidFillPolicy(value: unknown): value is BlockFillPolicy {
  return (
    value === "ai_filled" ||
    value === "student_blank" ||
    value === "student_filled" ||
    value === "mixed"
  );
}
```

---

## 15. Block Content Compatibility Validation

```ts id="cm1iy5"
function validateBlockContentCompatibility(
  block: AICorrectionBlock,
  errors: string[]
): void {
  const validKinds = VALID_BLOCK_CONTENT_KIND[block.type];

  if (!validKinds.includes(block.content.kind)) {
    errors.push(`Invalid content kind for block type: ${block.type}.`);
  }
}
```

---

## 16. Block Content Length Validation

```ts id="pplbvp"
function validateBlockContentLength(
  block: AICorrectionBlock,
  errors: string[]
): void {
  const content = block.content;

  if (content.kind === "text") {
    if (!isShortText(content.text, DIAGNOSIS_OUTPUT_LIMITS.blockTextMaxChars)) {
      errors.push(`Text block too long: ${block.type}.`);
    }
  }

  if (content.kind === "list") {
    if (!isLimitedStringArray(content.items, DIAGNOSIS_OUTPUT_LIMITS.listBlockMaxItems, DIAGNOSIS_OUTPUT_LIMITS.listItemMaxChars)) {
      errors.push(`Invalid list block: ${block.type}.`);
    }
  }

  if (content.kind === "checklist") {
    if (content.items.length > DIAGNOSIS_OUTPUT_LIMITS.checklistBlockMaxItems) {
      errors.push(`Checklist block has too many items: ${block.type}.`);
    }

    for (const item of content.items) {
      if (!isShortText(item.text, DIAGNOSIS_OUTPUT_LIMITS.causeMaxChars)) {
        errors.push(`Checklist item too long: ${block.type}.`);
      }
    }
  }

  if (content.kind === "steps") {
    validateDiagnosisSteps(content.steps, errors);
  }

  if (content.kind === "blank") {
    if (!isShortText(content.placeholder, DIAGNOSIS_OUTPUT_LIMITS.blockTextMaxChars)) {
      errors.push(`Blank placeholder too long: ${block.type}.`);
    }
  }
}
```

---

## 17. Required Block Validation

```ts id="fmi7fw"
function validateRequiredBlocks(
  mode: CorrectionMode,
  blockTypes: Set<string>,
  errors: string[]
): void {
  for (const requiredType of REQUIRED_BLOCKS_BY_MODE[mode]) {
    if (!blockTypes.has(requiredType)) {
      errors.push(`Missing required block: ${requiredType}.`);
    }
  }
}
```

```ts id="k4t39b"
function validateSimpleModeBlankBlocks(
  blocks: unknown[],
  errors: string[]
): void {
  const validBlocks = blocks.filter(isAICorrectionBlock);

  for (const blockType of SIMPLE_MODE_BLANK_BLOCKS) {
    const block = validBlocks.find((item) => item.type === blockType);

    if (!block) {
      errors.push(`Missing simple mode blank block: ${blockType}.`);
      continue;
    }

    if (block.content.kind !== "blank" || block.fillPolicy !== "student_blank") {
      errors.push(`Simple mode block must be blank: ${blockType}.`);
    }
  }
}
```

---

## 18. Fallback Validation

```ts id="rgewc9"
function validateNeedsMoreInfoOutput(
  value: Record<string, unknown>,
  errors: string[]
): void {
  if (!Array.isArray(value.missingFields)) {
    errors.push("missingFields must be an array.");
  }

  if (!isShortTextValue(value.message, 40)) {
    errors.push("Invalid needs_more_info message.");
  }
}
```

```ts id="ml6q4f"
function validateUnsupportedOutput(
  value: Record<string, unknown>,
  errors: string[]
): void {
  if (!isString(value.reason)) {
    errors.push("Invalid unsupported reason.");
  }

  if (!isShortTextValue(value.message, 40)) {
    errors.push("Invalid unsupported message.");
  }
}
```

---

## 19. FUTURE

Future validator improvements:

```text id="95s77c"
- replace manual validator with Zod if dependency is accepted
- add unit tests for every invalid output case
- add prompt repair flow after validation failure
- add provider-specific retry strategy
```
