# AGENTS.md

stage: M1_MOCK_WEB_MVP
last_reviewed: 2026-06-29
owner: AI coding agent behavior and task execution rules

---

## 0. Purpose

This file tells AI coding agents how to work in this repository.

It owns AI-agent behavior, not product truth or engineering truth.

It does not own:

* product scope
* technical stack
* folder structure
* data model
* API contract
* AI output schema
* runtime validation rules
* milestone task queue

Source-of-truth ownership and conflict resolution are defined in:

```txt
docs/00_DOCS_GOVERNANCE.md
```

---

## 1. Project Identity

This repository is for **SyncMate**, an AI-assisted mistake diagnosis and correction-note product for students.

Current stage:

```txt
M1_MOCK_WEB_MVP
```

M1 is a local, mock, offline-runnable web MVP.

The purpose of M1 is to validate the core product flow and structured correction-note experience before adding real AI, OCR, payment, login, database, or production data.

---

## 2. Reading Behavior

Do not read every document by default.

For each task, read:

1. The GitHub Issue or user task.
2. `docs/00_START_HERE.md`.
3. The relevant owner documents for the task.
4. Any task-specific documents named by the issue.

Read `docs/00_DOCS_GOVERNANCE.md` when:

* editing documentation
* resolving document conflicts
* deciding source-of-truth ownership
* changing reading order
* moving to a new milestone

For normal coding tasks, use the relevant owner documents instead of loading the whole repository documentation set.

---

## 3. Source-of-Truth Rule

Do not invent requirements.

Do not duplicate full rules from other documents.

Source-of-truth ownership for product, engineering, folder structure, data, API, AI output, validation, and task queue is defined in:

```txt
docs/00_DOCS_GOVERNANCE.md
```

Reference the relevant owner document for the task.

Do not copy its rules into this file.

If documents conflict, follow the conflict-resolution rules in `docs/00_DOCS_GOVERNANCE.md`.

Do not silently choose the easier rule.

---

## 4. Non-Negotiable M1 Reminders

These are short execution reminders. Detailed rules live in the owner documents.

During M1:

* Use mock data only.
* Use clearly fictional sample data only.
* Do not use real student data.
* Do not call real AI.
* Do not call OCR.
* Do not add backend behavior.
* Do not add persistence.
* Do not add analytics, telemetry, or remote logging.
* Do not send user data or student learning data overseas.
* The app must be able to complete the M1 flow offline.

If a task appears to require breaking one of these rules, stop and report the issue before coding.

---

## 5. Data Boundary

User data and student learning data must not be sent overseas.

For M1, no real user data or student learning data should be used at all.

Detailed data-boundary, provider, and future AI integration rules belong to the relevant owner documents defined in `docs/00_DOCS_GOVERNANCE.md`.

Do not let UI components call model providers directly.

---

## 6. Task Execution Rules

Work in small, reviewable steps.

Prefer tasks that modify a small number of files.

Do not perform broad refactors unless the task explicitly asks for one.

Do not add impressive but unnecessary features.

Do not implement future milestones during M1.

Do not change product scope, data models, service contracts, or AI schemas unless the task explicitly targets the owner document or owner code.

If a task has an "Allowed Files / Areas" section, stay within it.

---

## 7. Coding Rules

Write clear TypeScript.

Avoid `any` unless there is a strong reason.

Do not put core business logic directly inside page components.

Do not let UI components generate diagnosis data.

Do not introduce new dependencies unless they are necessary for the task and allowed by the technical stack owner document.

---

## 8. Validation Rules

Before finishing a task, run the available validation commands requested by the task, such as:

```bash
npm run lint
npm run typecheck
npm run test
npm run build
```

If a command does not exist, report that clearly.

Do not claim that a command passed unless it actually passed.

Do not hide errors.

---

## 9. When to Stop

Stop and ask before coding if:

* The task conflicts with M1 scope.
* The task requires real student data.
* The task requires overseas AI or API calls.
* The task requires a real model provider.
* The task requires OCR.
* The task requires payment, login, database, deployment, or persistence.
* The task requires large refactoring.
* The task contradicts owner documents.
* The expected behavior is ambiguous and cannot be safely inferred.

When in doubt, preserve the current M1 scope.

---

## 10. Task Completion Format

At the end of each task, report:

```md
## Task Summary

### Files Created
- ...

### Files Modified
- ...

### What Changed
- ...

### Validation
- [ ] npm run lint
- [ ] npm run typecheck
- [ ] npm run test
- [ ] npm run build

Only check a validation item if the command was actually run and passed.
If a command was not run, missing, or failed, leave it unchecked and summarize the reason in Notes / Risks.

### Notes / Risks
- ...
```

If something could not be completed, say so clearly.

If a document conflict was found, mention it in `Notes / Risks`.

If a validation command failed, include the failure summary.

---

## 11. Project Direction Reminder

SyncMate should not become a black-box answer generator.

Good SyncMate output should help the student understand the mistake, not merely provide a complete answer.

For what good output looks like, including positive behavior, negative behavior, and an end-to-end product example, follow the golden example owner document defined in `docs/00_DOCS_GOVERNANCE.md`.

If the golden example does not exist yet, create it before starting UI-heavy implementation.

M1 should stay small, local, inspectable, and easy to modify.

The working order is:

```txt
First close the product loop.
Then improve intelligence.
First mock locally.
Then integrate real providers.
First define structure.
Then polish UI.
```

Do not overbuild.
