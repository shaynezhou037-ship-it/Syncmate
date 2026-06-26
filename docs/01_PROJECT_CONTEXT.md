# Project Context

> Source of truth for SyncMate product definition.

---

## 1. Role of This File

This file defines:

```text id="420ibd"
- product identity
- target users
- core product value
- product principles
- product non-goals
- long-term product direction
```

This file does not define:

```text id="fzt14u"
- MVP feature scope
- data model
- API contract
- AI output schema
- code architecture
- folder structure
- implementation tasks
```

---

## 2. Product Identity

```ts id="x43l14"
export const PRODUCT_NAME = "SyncMate" as const;
```

```ts id="3gm89p"
export const PRODUCT_CATEGORY =
  "AI-powered mistake diagnosis and correction tool" as const;
```

```ts id="vvw6a4"
export const PRODUCT_DEFINITION =
  "SyncMate helps students turn mistakes into visible, understandable, and correctable learning processes." as const;
```

---

## 3. Subject Strategy

```ts id="7k68tz"
export const CURRENT_SUBJECT_FOCUS = "math" as const;
```

```ts id="h4n3jq"
export const LONG_TERM_SUBJECT_SCOPE = [
  "math",
  "physics",
  "chemistry",
  "english",
  "other_structured_learning_subjects"
] as const;
```

```ts id="qywl8h"
export const SUBJECT_STRATEGY = {
  current: "Start with mathematics for MVP validation.",
  longTerm: "Do not design SyncMate as a math-only product."
} as const;
```

Math is the first subject because math mistakes are easier to structure through known information, target extraction, formulas, steps, wrong-step localization, and correction flow.

---

## 4. Target Users

```ts id="nhpzu0"
export const PRIMARY_USERS = [
  "upper_primary_students",
  "middle_school_students",
  "high_school_students"
] as const;
```

```ts id="tkpbh4"
export const CURRENT_MVP_USER = "middle_school_math_student" as const;
```

```ts id="m9s1ak"
export const INDIRECT_USERS = [
  "parents"
] as const;
```

```ts id="t3wh0n"
export const FUTURE_USERS = [
  "teachers",
  "schools",
  "training_institutions"
] as const;
```

---

## 5. Core User Problem

```ts id="1udg3r"
export const USER_PROBLEMS = [
  "mistakes_are_saved_but_not_understood",
  "students_do_not_know_the_exact_wrong_step",
  "students_do_not_know_the_real_cause_of_their_mistake",
  "students_repeat_the_same_thinking_error",
  "traditional_mistake_notebooks_require_too_much_copying",
  "ai_tools_often_give_answers_too_directly",
  "long_explanations_are_hard_to_read_and_reuse",
  "weak_knowledge_points_are_not_accumulated_or_visualized"
] as const;
```

SyncMate should solve the gap between:

```text id="j9oq55"
mistake collection
```

and:

```text id="fp9o7y"
real correction
```

---

## 6. Core Product Value

```ts id="pqu1e6"
export const CORE_PRODUCT_VALUE = [
  "locate_wrong_step",
  "break_down_mistake_cause",
  "visualize_hidden_thinking_process",
  "extract_known_information_and_target",
  "identify_weak_knowledge_points",
  "guide_structured_correction",
  "support_review_and_reuse"
] as const;
```

The product should help the student answer:

```text id="ruegb4"
Where did I go wrong?
Why did I go wrong?
Which hidden thinking habit caused it?
What should I correct?
How can I avoid this type of mistake next time?
```

---

## 7. Thinking Visualization

SyncMate should make implicit thinking visible.

```ts id="5e2rgl"
export const THINKING_VISUALIZATION_MODULES = [
  "core_keywords",
  "known_information",
  "target",
  "question_type",
  "easy_trap",
  "wrong_step",
  "mistake_cause",
  "weak_knowledge_point",
  "formula_or_method",
  "problem_solving_flow",
  "anti_mistake_tip",
  "student_blank_area"
] as const;
```

Preferred diagnosis style:

```ts id="eqbebn"
export const DIAGNOSIS_STYLE = [
  "short",
  "keyword_based",
  "card_friendly",
  "checklist_friendly",
  "editable",
  "printable",
  "easy_to_scan"
] as const;
```

Forbidden diagnosis style:

```ts id="meotrw"
export const FORBIDDEN_DIAGNOSIS_STYLE = [
  "long_essay_explanation",
  "generic_be_more_careful_feedback",
  "unstructured_text_wall",
  "answer_only_response"
] as const;
```

Good diagnosis should expose the thinking process.

Example pattern:

```text id="bxx2s5"
Known
→ Target
→ Method
→ Step
→ Wrong Step
→ Cause
→ Correction Tip
```

---

## 8. Correction Modes

```ts id="ojy0vx"
export const CORRECTION_MODES = [
  "simple",
  "complete"
] as const;
```

```ts id="a35jb4"
export const SIMPLE_MODE_INTENT =
  "Provide structure and non-reasoning information while leaving key reasoning work to the student." as const;
```

```ts id="5aoysz"
export const COMPLETE_MODE_INTENT =
  "Provide fuller diagnosis and correction content for fast review or exam preparation." as const;
```

Simple Mode may provide:

```ts id="mlct83"
export const SIMPLE_MODE_AI_FILLED_MODULES = [
  "question_type",
  "core_keywords",
  "known_information",
  "target",
  "easy_trap",
  "mistake_cause_hint",
  "weak_knowledge_point"
] as const;
```

Simple Mode should leave blank:

```ts id="jlm3ei"
export const SIMPLE_MODE_STUDENT_FILLED_MODULES = [
  "key_formula",
  "problem_solving_flow",
  "final_correction_summary",
  "student_reflection"
] as const;
```

Complete Mode may fill:

```ts id="0xewnt"
export const COMPLETE_MODE_MODULES = [
  "wrong_step",
  "mistake_cause",
  "known_information",
  "target",
  "correct_solution_flow",
  "key_formula",
  "anti_mistake_tip",
  "review_suggestion"
] as const;
```

---

## 9. Original Question Confirmation

```ts id="2qkoez"
export const QUESTION_CONFIRMATION_PRINCIPLE =
  "Confirm the question before expensive AI generation." as const;
```

```ts id="2ixvli"
export const QUESTION_CONFIRMATION_REASONS = [
  "avoid_wrong_diagnosis_from_bad_ocr",
  "avoid_wasted_tokens",
  "avoid_wasted_credits_or_diagnosis_coins",
  "protect_user_trust"
] as const;
```

Required product flow:

```text id="qmkwwz"
capture_question
→ show_original_and_recognized_content
→ user_confirms_or_edits
→ generate_diagnosis
```

---

## 10. Weakness Analysis

```ts id="7tqf06"
export const WEAKNESS_ANALYSIS_TARGETS = [
  "weak_knowledge_points",
  "repeated_mistake_types",
  "thinking_habits",
  "calculation_errors",
  "reading_errors",
  "formula_misuse",
  "reasoning_gaps",
  "condition_extraction_errors",
  "unit_conversion_errors"
] as const;
```

Weakness analysis should accumulate from saved mistakes and diagnosis results.

It should not be treated as a separate decorative dashboard.

---

## 11. Product Principles

```ts id="pe1c7p"
export const PRODUCT_PRINCIPLES = [
  "learning_first",
  "thinking_before_answer",
  "structure_before_decoration",
  "short_output_before_long_explanation",
  "question_confirmation_before_ai_cost",
  "student_autonomy_before_full_ai_replacement",
  "review_reuse_before_one_time_answer"
] as const;
```

Commercial content may exist in future versions, but it MUST NOT interrupt correction flow or compete with the learning task.

---

## 12. Product Form Strategy

```ts id="7d269l"
export const PRODUCT_FORM_STRATEGY = [
  "web_mvp_first",
  "mobile_app_later",
  "mini_program_after_product_maturity"
] as const;
```

Mini-program is not the first full-feature target because platform and performance constraints may limit early product validation.

---

## 13. Prototype Reference

```ts id="8unk4h"
export const PROTOTYPE_REFERENCE = "prototype/SyncMate_V5.html" as const;
```

Prototype should inform:

```ts id="lvjb2e"
export const PROTOTYPE_REFERENCE_AREAS = [
  "visual_style",
  "mobile_first_layout",
  "question_capture_flow",
  "question_confirmation",
  "simple_complete_mode_selection",
  "a4_preview",
  "editable_blocks",
  "cause_checklist",
  "known_target_extraction",
  "formula_and_process_blanks",
  "floating_ai_assistant"
] as const;
```

Prototype should not define production architecture.

---

## 14. Product Non-Goals

```ts id="92s7mq"
export const PRODUCT_NON_GOALS = [
  "generic_note_taking_app",
  "homework_answer_search_engine",
  "full_online_tutoring_platform",
  "short_video_learning_app",
  "social_learning_feed",
  "ai_chatbot_as_main_product",
  "hardware_dependent_product"
] as const;
```

---

## 15. Stable Product Direction

```ts id="97971f"
export const STABLE_PRODUCT_DIRECTION = [
  "mistake_correction",
  "mistake_diagnosis",
  "thinking_visualization",
  "weakness_analysis",
  "structured_review",
  "printable_or_reusable_correction_artifact"
] as const;
```

---

## 16. FUTURE

Future product directions may include:

```ts id="cmlf4z"
export const FUTURE_PRODUCT_DIRECTIONS = [
  "additional_subjects",
  "parent_progress_view",
  "teacher_class_view",
  "advanced_weakness_report",
  "diagnosis_credits",
  "mobile_app",
  "mini_program"
] as const;
```

FUTURE items MUST NOT be treated as MVP requirements.
