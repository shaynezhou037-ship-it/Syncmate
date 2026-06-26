# Data Model

> Source of truth for SyncMate M1 domain types.
> Copy the TypeScript definitions in this file into `src/types/domain.ts` unchanged.

---

## 1. Scope

```ts
export const DATA_MODEL_SCOPE = "M1_MOCK_WEB_MVP" as const;
```

This file defines only M1 domain types.

It does not define:

```text
- database schema
- API request / response schema
- AI provider response schema
- UI component props
```

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

export type WeaknessCategory =
  | "knowledge_point"
  | "mistake_type"
  | "thinking_habit"
  | "calculation"
  | "reading"
  | "formula"
  | "reasoning"
  | "other";

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
```

---

## 3. Structured Block Content

```ts
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
```

---

## 4. Core Objects

```ts
export interface UserProfile {
  id: ID;
  displayName: string;
  role: "student";
  gradeLevel: GradeLevel;
}
```

```ts
export interface QuestionSource {
  id: ID;
  type: QuestionSourceType;

  originalText?: string;
  recognizedText?: string;
  confirmedText: string;

  imagePreviewUrl?: string; // local preview or mock image URL for M1 only
  confirmedAt?: ISODateString; // set only after user confirms or edits question text
}
```

```ts
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
```

```ts
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
```

```ts
export interface DiagnosisStep {
  order: number;
  title: string;
  content: string;
  isWrongStep?: boolean;
  isBlankInSimpleMode?: boolean;
}
```

```ts
export interface CorrectionNote {
  id: ID;
  mode: CorrectionMode;
  blocks: CorrectionBlock[];
  createdAt: ISODateString;
  updatedAt: ISODateString;
}
```

```ts
export interface CorrectionBlock {
  id: ID;
  type: CorrectionBlockType;
  zone: CorrectionBlockZone;

  title: string;
  content: CorrectionBlockContent;
  order: number;

  fillPolicy: BlockFillPolicy;
  isLogicBlock?: boolean; // formula / method / solution flow blocks that Simple Mode may leave blank
  isEditable: boolean;
}
```

```ts
export interface ReviewTask {
  id: ID;
  mistakeId: ID;
  dueDate: ISODateString;
  status: ReviewStatus;
  completedAt?: ISODateString;
}
```

```ts
export interface WeaknessTag {
  id: ID;
  ownerUserId: ID;
  subject: Subject;
  label: string;
  category: WeaknessCategory;
  mistakeCount: number;
  updatedAt: ISODateString;
}
```

---

## 5. M1 Mock State

```ts
export interface SyncMateMockState {
  currentUser: UserProfile;
  mistakes: Mistake[];
  reviewTasks: ReviewTask[];
  weaknessTags: WeaknessTag[];
}
```

```ts
export const M1_REQUIRED_MOCK_DATA = {
  users: 1,
  mistakesMin: 3,
  reviewTasksMin: 2,
  weaknessTagsMin: 3,
  includeSimpleModeExample: true,
  includeCompleteModeExample: true
} as const;
```

---

## 6. Mode Constraints

```ts
export const SIMPLE_MODE_AI_FILLED_BLOCKS: CorrectionBlockType[] = [
  "question_type",
  "core_keywords",
  "known_info",
  "target",
  "mistake_cause",
  "weakness"
];
```

```ts
export const SIMPLE_MODE_STUDENT_BLANK_BLOCKS: CorrectionBlockType[] = [
  "formula_or_method",
  "solution_flow",
  "student_blank"
];
```

```ts
export const COMPLETE_MODE_AI_FILLED_BLOCKS: CorrectionBlockType[] = [
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
];
```

---

## 7. M1 Invariants

```ts
export const M1_DATA_INVARIANTS = [
  "mistake.questionSource.confirmedText_required_before_diagnosis",
  "mistake.selectedMode_must_be_simple_or_complete",
  "diagnosis_must_not_be_long_essay_text",
  "diagnosis_weakKnowledgePoints_is_source_for_weakness_display",
  "diagnosis_mistakeCauses_is_source_for_cause_display",
  "correction_note_blocks_must_be_ordered",
  "correction_block_content_must_be_structured",
  "simple_mode_must_keep_logic_blocks_blank_or_student_filled",
  "mock_data_shape_should_match_future_real_data_shape"
] as const;
```

---

## 8. Example M1 Object

```ts
export const EXAMPLE_MISTAKE: Mistake = {
  id: "mistake_001",
  ownerUserId: "user_demo",

  subject: "math",
  gradeLevel: "middle_2",
  title: "圆木截段表面积问题",
  status: "diagnosed",

  questionSource: {
    id: "question_source_001",
    type: "manual_text",
    originalText: "把一根 4 米长的圆木截成 3 小段，表面积增加 24 平方分米，求原来的体积。",
    confirmedText: "把一根 4 米长的圆木截成 3 小段，表面积增加 24 平方分米，求原来的体积。",
    confirmedAt: "2026-06-24T00:00:00.000Z"
  },

  studentWrongSolution: "24 ÷ 6 = 4，4 × 40 = 160",
  referenceSolution: "截成 3 段需要切 2 刀，每刀增加 2 个截面，共 4 个截面。底面积 = 24 ÷ 4 = 6dm²，长 4m = 40dm，体积 = 6 × 40 = 240dm³。",

  selectedMode: "complete",

  diagnosis: {
    id: "diagnosis_001",
    provider: "mock",
    mode: "complete",

    questionType: "圆柱截段表面积问题",
    coreKeywords: ["截成 3 段", "表面积增加", "24dm²", "原体积"],
    knownInfo: ["圆木长 4m = 40dm", "截成 3 段", "表面积增加 24dm²"],
    target: "求原来的体积",

    wrongStep: "把“截成 3 段”理解成切 3 刀。",
    mistakeCauses: ["段数和切刀数混淆", "新增截面数量判断错误"],
    weakKnowledgePoints: ["圆柱体积", "截段表面积变化", "单位换算"],

    correctThinkingPath: [
      {
        order: 1,
        title: "切刀数",
        content: "截成 3 段需要切 2 刀。",
        isWrongStep: true
      },
      {
        order: 2,
        title: "新增截面",
        content: "每切 1 刀增加 2 个圆截面，共增加 4 个截面。"
      },
      {
        order: 3,
        title: "底面积",
        content: "24 ÷ 4 = 6dm²。"
      },
      {
        order: 4,
        title: "体积",
        content: "4m = 40dm，6 × 40 = 240dm³。"
      }
    ],

    antiMistakeTip: "看到“截成 n 段”，先想“切 n−1 刀”。",
    reviewSuggestion: "复习圆柱体积和截段新增表面积问题。",
    createdAt: "2026-06-24T00:01:00.000Z"
  },

  correctionNote: {
    id: "note_001",
    mode: "complete",
    blocks: [
      {
        id: "block_001",
        type: "known_info",
        zone: "main",
        title: "已知",
        content: {
          kind: "list",
          items: ["4m = 40dm", "截成 3 段", "表面积增加 24dm²"]
        },
        order: 1,
        fillPolicy: "ai_filled",
        isEditable: true
      },
      {
        id: "block_002",
        type: "solution_flow",
        zone: "main",
        title: "流程",
        content: {
          kind: "steps",
          steps: [
            {
              order: 1,
              title: "切刀数",
              content: "截成 3 段 → 切 2 刀"
            },
            {
              order: 2,
              title: "新增截面",
              content: "2 刀 × 每刀 2 个截面 = 4 个截面"
            },
            {
              order: 3,
              title: "体积",
              content: "24 ÷ 4 = 6dm²；6 × 40 = 240dm³"
            }
          ]
        },
        order: 2,
        fillPolicy: "ai_filled",
        isLogicBlock: true,
        isEditable: true
      },
      {
        id: "block_003",
        type: "mistake_cause",
        zone: "cue",
        title: "我的错因",
        content: {
          kind: "checklist",
          items: [
            {
              id: "cause_001",
              text: "把“截成 3 段”当成切 3 刀",
              checked: true
            },
            {
              id: "cause_002",
              text: "新增截面数量判断错误",
              checked: true
            }
          ]
        },
        order: 3,
        fillPolicy: "mixed",
        isEditable: true
      }
    ],
    createdAt: "2026-06-24T00:01:00.000Z",
    updatedAt: "2026-06-24T00:01:00.000Z"
  },

  createdAt: "2026-06-24T00:00:00.000Z",
  updatedAt: "2026-06-24T00:01:00.000Z"
};
```

---

## 9. FUTURE

```ts
export const FUTURE_DATA_OBJECTS = [
  "Attachment",
  "OCRResult",
  "AIJob",
  "PromptVersion",
  "ExportRecord",
  "ParentReport",
  "TeacherClass",
  "PaymentSubscription",
  "DiagnosisCreditTransaction",
  "Notification"
] as const;
```

FUTURE objects MUST NOT be added to M1 domain types unless M1 scope changes.
