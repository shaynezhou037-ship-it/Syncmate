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

- Product name: SyncMate
- Category: AI-powered mistake diagnosis and correction tool
- Definition: SyncMate helps students turn mistakes into visible, understandable, and correctable learning processes.

---

## 3. Subject Strategy

- Current subject focus: math
- Long-term subject scope:
  - math
  - physics
  - chemistry
  - english
  - other structured learning subjects

Math is the first subject because math mistakes are easier to structure through known information, target extraction, formulas, steps, wrong-step localization, and correction flow.

SyncMate MUST NOT be designed as a math-only product.

---

## 4. Target Users

Primary users:

- upper primary students
- middle school students
- high school students

Current MVP user:

- middle school math student

Indirect users:

- parents

Future users:

- teachers
- schools
- training institutions

---

## 5. Core User Problem

Students often save mistakes without understanding:

- the exact wrong step
- the real cause of the mistake
- the hidden thinking habit behind it
- how to avoid repeating it

SyncMate should solve the gap between mistake collection and real correction.

---

## 6. Core Product Value

SyncMate should help students:

- locate the wrong step
- break down the mistake cause
- visualize hidden thinking process
- extract known information and target
- identify weak knowledge points
- follow structured correction
- review and reuse correction artifacts

The product should help the student answer:

```text
Where did I go wrong?
Why did I go wrong?
Which hidden thinking habit caused it?
What should I correct?
How can I avoid this type of mistake next time?
```

---

## 7. Thinking Visualization

SyncMate should make implicit thinking visible.

Useful thinking modules include:

- core keywords
- known information
- target
- question type
- easy trap
- wrong step
- mistake cause
- weak knowledge point
- formula or method
- problem-solving flow
- anti-mistake tip
- student blank area

Preferred diagnosis style:

- short
- keyword-based
- card-friendly
- checklist-friendly
- editable
- printable
- easy to scan

Forbidden diagnosis style:

- long essay explanation
- generic "be more careful" feedback
- unstructured text wall
- answer-only response

Good diagnosis should expose the thinking process.

Example pattern:

```text
Known
-> Target
-> Method
-> Step
-> Wrong Step
-> Cause
-> Correction Tip
```

---

## 8. Correction Modes

Simple Mode:

- provides structure and non-reasoning information
- leaves key reasoning work to the student
- protects student autonomy before full AI replacement

Complete Mode:

- provides fuller diagnosis and correction content
- supports fast review or exam preparation

---

## 9. Original Question Confirmation

Question confirmation principle:

- Confirm the question before expensive AI generation.

Reasons:

- avoid wrong diagnosis from bad OCR
- avoid wasted tokens
- avoid wasted credits or diagnosis coins
- protect user trust

Required product flow:

```text
capture_question
-> show_original_and_recognized_content
-> user_confirms_or_edits
-> generate_diagnosis
```

---

## 10. Weakness Analysis

Weakness analysis should accumulate from saved mistakes and diagnosis results.

It may include:

- weak knowledge points
- repeated mistake types
- thinking habits
- calculation errors
- reading errors
- formula misuse
- reasoning gaps
- condition extraction errors
- unit conversion errors

It should not be treated as a separate decorative dashboard.

---

## 11. Product Principles

SyncMate follows these principles:

- learning first
- thinking before answer
- structure before decoration
- short output before long explanation
- question confirmation before AI cost
- student autonomy before full AI replacement
- review and reuse before one-time answer

Commercial content may exist in future versions, but it MUST NOT interrupt correction flow or compete with the learning task.

---

## 12. Product Form Strategy

Product form direction:

- web MVP first
- mobile app later
- mini-program after product maturity

Mini-program is not the first full-feature target because platform and performance constraints may limit early product validation.

---

## 13. Prototype Reference

Prototype path:

- `prototype/SyncMate_V5.html`

Prototype may inform:

- visual style
- mobile-first layout
- question capture flow
- question confirmation
- Simple / Complete Mode selection
- A4 preview
- editable blocks
- cause checklist
- known target extraction
- formula and process blanks
- floating AI assistant

Prototype should not define production architecture.

---

## 14. Product Non-Goals

SyncMate is not:

- a generic note-taking app
- a homework answer search engine
- a full online tutoring platform
- a short-video learning app
- a social learning feed
- an AI chatbot as the main product
- a hardware-dependent product

---

## 15. Stable Product Direction

Stable product direction:

- mistake correction
- mistake diagnosis
- thinking visualization
- weakness analysis
- structured review
- printable or reusable correction artifact

---

## 16. FUTURE

Future product directions may include:

- additional subjects
- parent progress view
- teacher class view
- advanced weakness report
- diagnosis credits
- mobile app
- mini-program

FUTURE items MUST NOT be treated as MVP requirements.
