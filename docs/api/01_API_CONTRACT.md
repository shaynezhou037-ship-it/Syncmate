# API Contract

> Source of truth for SyncMate M1 app-level API contracts.

Copy the TypeScript block in this file into `src/types/api.ts` unchanged.

---

## 1. Role of This File

This file defines:

* M1 app-level request types
* M1 app-level response types
* shared API result shape
* mock service contract
* future-compatible action boundary

This file does not define:

* domain model
* raw AI output schema
* runtime validation logic
* database schema
* provider SDK types
* UI component props
* real HTTP route implementation

Domain types belong to `docs/data/00_DATA_MODEL.md`.

Raw AI output types belong to `docs/ai/07_AI_OUTPUT_SCHEMA.md`.

Runtime validation belongs to `docs/ai/10_RUNTIME_VALIDATION.md`.

---

## 2. Target File

```txt id="k5ubpv"
src/types/api.ts
```

M1 implementation target:

```txt id="qcef43"
src/lib/mock/api.ts
```

M1 uses mock service functions.

M1 does not require real HTTP endpoints.

---

## 3. API Contract Types

```ts id="nqrrv3"
import type {
  CorrectionBlock,
  CorrectionMode,
  GradeLevel,
  ID,
  Mistake,
  QuestionSourceType,
  ReviewStatus,
  ReviewTask,
  Subject
} from "@/types/domain";

export type ApiErrorCode =
  | "not_found"
  | "invalid_request"
  | "invalid_state"
  | "validation_failed"
  | "mock_generation_failed"
  | "unsupported_operation";

export interface ApiError {
  code: ApiErrorCode;
  message: string;
  path?: string;
}

export type ApiResult<T> =
  | {
      ok: true;
      data: T;
    }
  | {
      ok: false;
      error: ApiError;
    };

export interface CreateMistakeRequest {
  ownerUserId: ID;
  subject: Subject;
  gradeLevel: GradeLevel;
  title?: string;
  questionInput: QuestionInput;
}

export interface QuestionInput {
  type: QuestionSourceType;
  text?: string;
  imagePreviewUrl?: string;
}

export interface CreateMistakeResponse {
  mistake: Mistake;
}

export interface ConfirmQuestionRequest {
  mistakeId: ID;
  confirmedText: string;
}

export interface ConfirmQuestionResponse {
  mistake: Mistake;
}

export interface UpdateWrongSolutionRequest {
  mistakeId: ID;
  studentWrongSolution: string;
}

export interface UpdateWrongSolutionResponse {
  mistake: Mistake;
}

export interface SelectCorrectionModeRequest {
  mistakeId: ID;
  mode: CorrectionMode;
}

export interface SelectCorrectionModeResponse {
  mistake: Mistake;
}

export interface GenerateMockDiagnosisRequest {
  mistakeId: ID;
  mode: CorrectionMode;
}

export interface GenerateMockDiagnosisResponse {
  mistake: Mistake;
}

export interface UpdateCorrectionBlockRequest {
  mistakeId: ID;
  blockId: ID;
  content: CorrectionBlock["content"];
}

export interface UpdateCorrectionBlockResponse {
  mistake: Mistake;
}

export interface ListMistakesRequest {
  ownerUserId: ID;
  subject?: Subject;
  gradeLevel?: GradeLevel;
  reviewStatus?: ReviewStatus;
}

export interface ListMistakesResponse {
  mistakes: Mistake[];
}

export interface GetMistakeRequest {
  mistakeId: ID;
}

export interface GetMistakeResponse {
  mistake: Mistake;
}

export interface UpdateReviewStatusRequest {
  mistakeId: ID;
  status: ReviewStatus;
}

export interface UpdateReviewStatusResponse {
  mistake: Mistake;
  reviewTask?: ReviewTask;
}

export interface ListReviewTasksRequest {
  ownerUserId: ID;
  status?: ReviewStatus;
}

export interface ListReviewTasksResponse {
  reviewTasks: ReviewTask[];
}

export interface GetA4PreviewRequest {
  mistakeId: ID;
}

export interface GetA4PreviewResponse {
  mistake: Mistake;
  blocks: CorrectionBlock[];
}

export interface SyncMateApi {
  createMistake(
    request: CreateMistakeRequest
  ): Promise<ApiResult<CreateMistakeResponse>>;

  confirmQuestion(
    request: ConfirmQuestionRequest
  ): Promise<ApiResult<ConfirmQuestionResponse>>;

  updateWrongSolution(
    request: UpdateWrongSolutionRequest
  ): Promise<ApiResult<UpdateWrongSolutionResponse>>;

  selectCorrectionMode(
    request: SelectCorrectionModeRequest
  ): Promise<ApiResult<SelectCorrectionModeResponse>>;

  generateMockDiagnosis(
    request: GenerateMockDiagnosisRequest
  ): Promise<ApiResult<GenerateMockDiagnosisResponse>>;

  updateCorrectionBlock(
    request: UpdateCorrectionBlockRequest
  ): Promise<ApiResult<UpdateCorrectionBlockResponse>>;

  listMistakes(
    request: ListMistakesRequest
  ): Promise<ApiResult<ListMistakesResponse>>;

  getMistake(
    request: GetMistakeRequest
  ): Promise<ApiResult<GetMistakeResponse>>;

  updateReviewStatus(
    request: UpdateReviewStatusRequest
  ): Promise<ApiResult<UpdateReviewStatusResponse>>;

  listReviewTasks(
    request: ListReviewTasksRequest
  ): Promise<ApiResult<ListReviewTasksResponse>>;

  getA4Preview(
    request: GetA4PreviewRequest
  ): Promise<ApiResult<GetA4PreviewResponse>>;
}

export const M1_API_ACTIONS = [
  "createMistake",
  "confirmQuestion",
  "updateWrongSolution",
  "selectCorrectionMode",
  "generateMockDiagnosis",
  "updateCorrectionBlock",
  "listMistakes",
  "getMistake",
  "updateReviewStatus",
  "listReviewTasks",
  "getA4Preview"
] as const;

export type M1ApiAction = (typeof M1_API_ACTIONS)[number];
```

---

## 4. State Transition Rules

`createMistake` creates a draft mistake.

`confirmQuestion` moves a mistake toward diagnosis readiness.

`generateMockDiagnosis` MUST NOT run unless:

* `questionSource.confirmedText` is present
* `studentWrongSolution` is present
* `selectedMode` is present

`generateMockDiagnosis` returns a `Mistake` with:

* `status: "diagnosed"`
* `diagnosis`
* `correctionNote`

`updateCorrectionBlock` only updates existing correction note blocks.

`updateReviewStatus` changes review state only. It MUST NOT modify diagnosis content.

---

## 5. Mock Implementation Boundary

M1 mock API implementation MUST:

* use in-memory or local mock data
* return `ApiResult<T>`
* preserve domain object shape
* generate stable mock IDs
* keep mock data close to future real data shape
* call runtime validator before accepting mock diagnosis output
* map validated AI output into domain objects

M1 mock API implementation MUST NOT:

* call real AI providers
* call payment services
* call OCR services
* require user login
* require a database
* create parent accounts
* create teacher dashboard data
* generate real PDFs

---

## 6. Error Handling

Use `not_found` when a requested mistake or review task does not exist.

Use `invalid_request` when request fields are missing or malformed.

Use `invalid_state` when the action is not allowed in the current mistake state.

Use `validation_failed` when mock diagnosis output fails runtime validation.

Use `mock_generation_failed` when mock diagnosis generation cannot produce output.

Use `unsupported_operation` when the requested action is outside M1 scope.

---

## 7. Handoff Rules for AI Coding Agents

When implementing `src/types/api.ts`:

* MUST copy the TypeScript block in this file.
* MUST import domain types from `src/types/domain.ts`.
* MUST NOT duplicate domain object definitions.
* MUST NOT add database-specific fields.
* MUST NOT add provider SDK response types.
* MUST NOT add UI component props.
* MUST keep all API responses wrapped in `ApiResult<T>`.

When implementing `src/lib/mock/api.ts`:

* MUST implement `SyncMateApi`.
* MUST use mock data from `src/mock`.
* MUST validate diagnosis output before mapping.
* MUST return typed API results.
* MUST not throw for expected user errors.
* SHOULD return `ApiResult` errors instead.
