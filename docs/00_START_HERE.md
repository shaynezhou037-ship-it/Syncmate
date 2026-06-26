# SyncMate Start Here

> Entry document for humans and AI coding agents.

---

## 1. Role of This File

This file defines:

```text
- required reading order
- source-of-truth map
- current project stage
- non-negotiable coding rules
- documentation update rules
```

This file does not define:

```text
- product definition
- MVP scope
- data model
- API contract
- AI output schema
- implementation details
```

---

## 2. Required Reading Order

Before coding, read:

```text
1. docs/00_START_HERE.md
2. docs/02_DOC_STYLE_GUIDE.md
3. docs/01_PROJECT_CONTEXT.md
4. docs/product/01_MVP_SCOPE.md
5. docs/data/00_DATA_MODEL.md
6. docs/api/01_API_CONTRACT.md
7. docs/ai/07_AI_OUTPUT_SCHEMA.md
8. docs/engineering/01_ARCHITECTURE.md
9. docs/engineering/02_FOLDER_STRUCTURE.md
```

If a file does not exist:

```text
TODO: document not created yet
```

Do not invent missing decisions.

---

## 3. Source of Truth Map

```text
Product definition       → docs/01_PROJECT_CONTEXT.md
Documentation rules      → docs/02_DOC_STYLE_GUIDE.md
MVP scope                → docs/product/01_MVP_SCOPE.md
User flow                → docs/product/03_USER_FLOW.md
Page list                → docs/product/04_PAGE_LIST.md
Design style             → docs/design/00_DESIGN_STYLE.md
Prototype notes          → docs/design/02_PROTOTYPE_NOTES.md
Data model               → docs/data/00_DATA_MODEL.md
Database schema          → docs/data/01_DATABASE_SCHEMA.md
API contract             → docs/api/01_API_CONTRACT.md
AI strategy              → docs/ai/00_AI_STRATEGY.md
AI prompt behavior       → docs/ai/02_AI_PROMPTS.md
AI output schema         → docs/ai/07_AI_OUTPUT_SCHEMA.md
Tech stack               → docs/engineering/00_TECH_STACK.md
Architecture             → docs/engineering/01_ARCHITECTURE.md
Folder structure         → docs/engineering/02_FOLDER_STRUCTURE.md
Secret management        → docs/security/00_SECRET_MANAGEMENT.md
Task roadmap             → docs/tasks/00_ROADMAP.md
Current milestone        → docs/tasks/01_MILESTONE_M1.md
Backlog                  → docs/tasks/03_BACKLOG.md
Decision history         → docs/03_DECISION_LOG.md
```

---

## 4. Current Stage

```ts
export const CURRENT_STAGE = "M1_MOCK_WEB_MVP" as const;
```

```ts
export const CURRENT_STAGE_GOALS = [
  "define_mvp_scope",
  "define_data_model",
  "define_api_contract",
  "define_ai_output_schema",
  "build_mock_web_flow",
  "validate_core_correction_experience"
] as const;
```

```ts
export const CURRENT_STAGE_NON_GOALS = [
  "real_payment",
  "diagnosis_coins",
  "teacher_dashboard",
  "parent_report",
  "real_full_page_ocr",
  "mobile_app",
  "mini_program"
] as const;
```

---

## 5. Prototype Reference

```ts
export const PROTOTYPE_REFERENCE = {
  repoPath: "prototype/SyncMate_V5.html",
  localPath: "C:\\Users\\ASUS\\Desktop\\Syncmate\\prototype\\SyncMate_V5.html"
} as const;
```

The prototype is reference material only.

```ts
export const PROTOTYPE_USAGE = [
  "visual_style_reference",
  "mobile_layout_reference",
  "flow_reference",
  "simple_complete_mode_reference",
  "a4_preview_reference",
  "editable_block_reference",
  "cause_checklist_reference"
] as const;
```

```ts
export const PROTOTYPE_FORBIDDEN_USAGE = [
  "copy_entire_html_into_production_app",
  "treat_prototype_as_final_architecture",
  "treat_prototype_as_backend_spec"
] as const;
```

---

## 6. AI Coding Agent Rules

```ts
export const AI_CODING_AGENT_RULES = [
  "read_relevant_docs_before_coding",
  "work_on_one_small_task_at_a_time",
  "do_not_invent_product_decisions",
  "do_not_add_future_features_to_mvp",
  "do_not_duplicate_source_of_truth",
  "do_not_expose_api_keys",
  "separate_mock_logic_from_real_service_logic",
  "state_files_created_and_modified_after_each_change"
] as const;
```

---

## 7. Non-Negotiable Engineering Rules

```ts
export const ENGINEERING_MUST_NOT = [
  "do_not_put_all_business_logic_inside_page_components",
  "do_not_hardcode_domain_data_inside_ui_components",
  "do_not_call_ai_api_directly_from_client_components",
  "do_not_put_private_api_keys_in_next_public_env_vars",
  "do_not_mix_mock_services_and_real_services_without_boundary",
  "do_not_create_duplicate_types_for_the_same_domain_object",
  "do_not_design_the_data_model_as_math_only",
  "do_not_treat_prototype_html_as_production_code"
] as const;
```

---

## 8. Secret Rules

```ts
export const SECRET_FILES_MUST_NOT_COMMIT = [
  ".env",
  ".env.local",
  ".env.production"
] as const;
```

```ts
export const SECRET_FILES_ALLOWED = [
  ".env.example"
] as const;
```

```ts
export const PUBLIC_ENV_RULE =
  "Only values safe for browser exposure may use NEXT_PUBLIC_ prefix.";
```

---

## 9. AI Cost Rules

```ts
export const AI_COST_RULES = [
  "use_mock_ai_during_ui_development",
  "confirm_question_before_real_ai_generation",
  "use_structured_input_schema",
  "use_structured_output_schema",
  "define_fallback_for_ai_failure",
  "set_cost_limit_before_real_ai_testing"
] as const;
```

---

## 10. Documentation Update Rules

```ts
export const DOC_UPDATE_RULES = {
  productDefinitionChanged: "docs/01_PROJECT_CONTEXT.md",
  mvpScopeChanged: "docs/product/01_MVP_SCOPE.md",
  dataModelChanged: "docs/data/00_DATA_MODEL.md",
  apiContractChanged: "docs/api/01_API_CONTRACT.md",
  aiOutputChanged: "docs/ai/07_AI_OUTPUT_SCHEMA.md",
  architectureChanged: "docs/engineering/01_ARCHITECTURE.md",
  majorDecisionMade: "docs/03_DECISION_LOG.md"
} as const;
```

Do not update unrelated files.

Do not repeat the same change across multiple documents unless required by the source-of-truth map.

---

## 11. Task Completion Format

Every coding task should end with:

```text
Files created:
- path/to/file

Files modified:
- path/to/file

What changed:
- concise summary

Notes:
- TODOs or unresolved decisions
```

---

## 12. Final Rule

```text
Human decides direction.
Docs preserve decisions.
AI generates implementation.
Human verifies behavior.
```
