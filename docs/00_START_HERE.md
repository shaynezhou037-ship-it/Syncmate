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
8. docs/ai/10_RUNTIME_VALIDATION.md
9. docs/engineering/01_ARCHITECTURE.md
10. docs/engineering/02_FOLDER_STRUCTURE.md
```

If a file does not exist:

```text
TODO: document not created yet
```

Do not invent missing decisions.

---

## 3. Source of Truth Map

```text
Product definition       -> docs/01_PROJECT_CONTEXT.md
Documentation rules      -> docs/02_DOC_STYLE_GUIDE.md
MVP scope                -> docs/product/01_MVP_SCOPE.md
User flow                -> docs/product/03_USER_FLOW.md
Page list                -> docs/product/04_PAGE_LIST.md
Design style             -> docs/design/00_DESIGN_STYLE.md
Prototype notes          -> docs/design/02_PROTOTYPE_NOTES.md
Data model               -> docs/data/00_DATA_MODEL.md
Database schema          -> docs/data/01_DATABASE_SCHEMA.md
API contract             -> docs/api/01_API_CONTRACT.md
AI strategy              -> docs/ai/00_AI_STRATEGY.md
AI prompt behavior       -> docs/ai/02_AI_PROMPTS.md
AI output schema         -> docs/ai/07_AI_OUTPUT_SCHEMA.md
Runtime validation       -> docs/ai/10_RUNTIME_VALIDATION.md
Tech stack               -> docs/engineering/00_TECH_STACK.md
Architecture             -> docs/engineering/01_ARCHITECTURE.md
Folder structure         -> docs/engineering/02_FOLDER_STRUCTURE.md
Secret management        -> docs/security/00_SECRET_MANAGEMENT.md
Task roadmap             -> docs/tasks/00_ROADMAP.md
Current milestone        -> docs/tasks/01_MILESTONE_M1.md
Backlog                  -> docs/tasks/03_BACKLOG.md
Decision history         -> docs/03_DECISION_LOG.md
```

---

## 4. Current Stage

Current stage:

- `M1_MOCK_WEB_MVP`

Stage goals:

- define MVP scope
- define data model
- define API contract
- define AI output schema
- build mock web flow
- validate core correction experience

Stage non-goals:

- real payment
- diagnosis coins
- teacher dashboard
- parent report
- real full-page OCR
- mobile app
- mini program

---

## 5. Prototype Reference

Prototype path:

- `prototype/SyncMate_V5.html`

The prototype is reference material only.

Use the prototype for:

- visual style reference
- mobile layout reference
- flow reference
- Simple / Complete Mode reference
- A4 preview reference
- editable block reference
- cause checklist reference

Do not use the prototype to:

- copy entire HTML into production app
- treat prototype as final architecture
- treat prototype as backend spec

---

## 6. AI Coding Agent Rules

AI coding agents MUST:

- read relevant docs before coding
- work on one small task at a time
- not invent product decisions
- not add future features to M1
- not duplicate source of truth
- not expose API keys
- separate mock logic from real service logic
- state files created and modified after each change

---

## 7. Non-Negotiable Engineering Rules

Do not:

- put all business logic inside page components
- hardcode domain data inside UI components
- call AI APIs directly from client components
- put private API keys in `NEXT_PUBLIC_` env vars
- mix mock services and real services without a boundary
- create duplicate types for the same domain object
- design the data model as math-only
- treat prototype HTML as production code

---

## 8. Secret Rules

Never commit:

- `.env`
- `.env.local`
- `.env.production`

Allowed secret template file:

- `.env.example`

Only values safe for browser exposure may use the `NEXT_PUBLIC_` prefix.

---

## 9. AI Cost Rules

M1 MUST:

- use mock AI during UI development
- confirm question before real AI generation
- use structured input schema
- use structured output schema
- define fallback for AI failure
- set cost limit before real AI testing

---

## 10. Documentation Update Rules

Update the matching source-of-truth document when decisions change:

- product definition changes -> `docs/01_PROJECT_CONTEXT.md`
- MVP scope changes -> `docs/product/01_MVP_SCOPE.md`
- data model changes -> `docs/data/00_DATA_MODEL.md`
- API contract changes -> `docs/api/01_API_CONTRACT.md`
- AI output changes -> `docs/ai/07_AI_OUTPUT_SCHEMA.md`
- runtime validation changes -> `docs/ai/10_RUNTIME_VALIDATION.md`
- architecture changes -> `docs/engineering/01_ARCHITECTURE.md`
- major decision made -> `docs/03_DECISION_LOG.md`

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
