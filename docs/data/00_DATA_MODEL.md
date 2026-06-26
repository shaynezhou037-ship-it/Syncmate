# Data Model

> Source of truth for SyncMate M1 domain types.

Copy the TypeScript block in this file into `src/types/domain.ts` unchanged.

---

## 1. Role of This File

This file defines:

* M1 domain types
* M1 core domain objects
* M1 mock app state shape

This file does not define:

* MVP feature scope
* API request or response schema
* raw AI output schema
* database schema
* UI component props
* runtime validation logic

Runtime validation belongs to `docs/ai/10_RUNTIME_VALIDATION.md`.

API contract belongs to `docs/api/01_API_CONTRACT.md`.

---

## 2. Domain Types

```ts
export type ID = string;

export type ISODateString = string;

export type Subject =
  | "math"
  | "physics"
  | "chemistry"
  | "english"
  | "other";

export type GradeLevel =
  | "primary_5"
  | "primary_6"
  | "middle_1"
  | "middle_2"
  | "middle_3"
  | "high_1"
  | "high_2"
  | "high_3"
  | "unknown";

export type CorrectionMode = "simple" | "complete";

export type MistakeStatus =
  | "draft"
  | "question_confirmed"
  | "diagnosed"
  | "reviewing"
  | "mastered";

export type ReviewStatus =
  | "due"
  | "reviewed"
  | "mastered";

export type DiagnosisProvider = "mock";

export type QuestionSourceType =
  | "manual_text"
  | "mock_ocr"
  | "image_upload";

export type CorrectionBlockType =
  | "question"
  | "question_type"
  | "core_keywords"
  | "known_info"
  | "target"
  | "wrong_step"
  | "mistake_cause"
  | "weakness"
  | "formula_or_method"
  | "solution_flow"
  | "student_blank"
  | "anti_mistake_tip"
  | "review_hint"
  | "custom";

export type CorrectionBlockZone =
  | "cue"
  | "main"
  | "summary";

export type BlockFillPolicy =
  | "ai_filled"
  | "student_blank"
  | "student_filled"
  | "mixed";

export type CorrectionBlockContent =
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
      items: ChecklistItem[];
    }
  | {
      kind: "blank";
      placeholder: string;
    };

export interface ChecklistItem {
  id: ID;
  text: string;
  checked: boolean;
}

export interface UserProfile {
  id: ID;
  displayName: string;
  role: "student";
  gradeLevel: GradeLevel;
}

export interface QuestionSource {
  id: ID;
  type: QuestionSourceType;

  originalText?: string;
  recognizedText?: string;
  confirmedText: string;

  imagePreviewUrl?: string; // local preview or mock image URL for M1
  confirmedAt?: ISODateString;
}

export interface Mistake {
  id: ID;
  ownerUserId: ID;

  subject: Subject;
  gradeLevel: GradeLevel;
  title: string;
  status: MistakeStatus;

  questionSource: QuestionSource;
  studentWrongSolution: string;
  referenceSolution?: string;

  selectedMode: CorrectionMode;

  diagnosis?: Diagnosis;
  correctionNote?: CorrectionNote;

  createdAt: ISODateString;
  updatedAt: ISODateString;
}

export interface Diagnosis {
  id: ID;
  provider: DiagnosisProvider;
  mode: CorrectionMode;

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

  createdAt: ISODateString;
}

export interface DiagnosisStep {
  order: number;
  title: string;
  content: string;
  isWrongStep?: boolean;
  isBlankInSimpleMode?: boolean;
}

export interface CorrectionNote {
  id: ID;
  mode: CorrectionMode;
  blocks: CorrectionBlock[];
  createdAt: ISODateString;
  updatedAt: ISODateString;
}

export interface CorrectionBlock {
  id: ID;
  type: CorrectionBlockType;
  zone: CorrectionBlockZone;
  title: string;
  content: CorrectionBlockContent;
  order: number;

  fillPolicy: BlockFillPolicy;
  isLogicBlock?: boolean; // formula, method, or solution-flow block that Simple Mode may leave blank
  isEditable: boolean;
}

export interface ReviewTask {
  id: ID;
  mistakeId: ID;
  dueDate: ISODateString;
  status: ReviewStatus;
  completedAt?: ISODateString;
}

export interface SyncMateMockState {
  currentUser: UserProfile;
  mistakes: Mistake[];
  reviewTasks: ReviewTask[];
}

export const M1_REQUIRED_MOCK_DATA = {
  users: 1,
  mistakesMin: 3,
  reviewTasksMin: 2,
  includeSimpleModeExample: true,
  includeCompleteModeExample: true
} as const;
```

---

## 3. Domain Boundaries

`Mistake` MUST NOT duplicate diagnosis fields.

Do not add these fields to `Mistake`:

* `knowledgePoints`
* `mistakeTypes`
* `mistakeCauses`
* `weakKnowledgePoints`

Use these fields instead:

* `mistake.diagnosis.mistakeCauses`
* `mistake.diagnosis.weakKnowledgePoints`

`Diagnosis` is the source of truth for diagnosis results.

`CorrectionNote` is the source of truth for rendered and editable note blocks.

`CorrectionBlock.content` MUST stay structured. Do not replace it with a raw string.

---

## 4. Validation Boundary

TypeScript does not enforce:

* string length
* array length
* block compatibility by mode
* required blocks by mode
* valid empty or blank content
* AI output safety
* fallback handling

Those checks belong to runtime validators, not domain types.

The validator target file is:

```ts
src/lib/ai/diagnosisOutputValidator.ts
```

---

## 5. M1 Domain Rules

Before diagnosis:

* `Mistake.status` may be `"draft"` or `"question_confirmed"`.
* `Mistake.questionSource.confirmedText` MUST be present before diagnosis generation.

After mock diagnosis:

* `Mistake.status` may become `"diagnosed"`.
* `Mistake.diagnosis` SHOULD be present.
* `Mistake.correctionNote` SHOULD be present.

Review status:

* `"due"` means the mistake should be reviewed.
* `"reviewed"` means the student has reviewed it at least once.
* `"mastered"` means the student marks it as understood.

M1 uses `DiagnosisProvider = "mock"` only.

Real AI providers are future implementation details.
