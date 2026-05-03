## 1. Normalize shared workflow contracts

- [x] 1.1 Update `product_manager/nodes/_manage.md` to define the single Memory schema, unified node output structure, and the `PENDING` / `DRAFT` / `CONFIRM` state model.
- [x] 1.2 Standardize all documented version examples and PRD paths to `output/{Version}/PRD_{Version}.md` across `product_manager/SKILL.md`, `product_manager/README.md`, and node docs.
- [x] 1.3 Remove conflicting status expressions such as `LOCKED`, `UNLOCKED`, `COMPLETED`, and `is_confirmed` from workflow documentation where they are presented as persisted state.

## 2. Centralize routing and knowledge-card entry rules

- [x] 2.1 Update `product_manager/nodes/01-router.md` so Router is the only documented knowledge-card entry point and explicitly reads `knowledge_cards/index.md` each round.
- [x] 2.2 Remove duplicated knowledge-card loading rules from business nodes and shared docs, leaving only “consume Router-provided context” guidance.
- [x] 2.3 Align `product_manager/references/knowledge_cards/index.md` and related shared docs with the Router-owned loading model.

## 3. Centralize Penpot guidance and clarify node boundaries

- [x] 3.1 Move shared Penpot preflight guidance into `product_manager/skills/penpot-drawing-skill/SKILL.md` as the canonical source.
- [x] 3.2 Replace repeated Penpot setup sections in `05-drawUserStory.md`, `06-detail.md`, and `07-genPrototype.md` with concise references to `penpot-drawing-skill`.
- [x] 3.3 Refine `06-detail.md` responsibilities so it focuses on page structure, event definitions, exception handling, tracking, and optional state machine design.

## 4. Clean up supporting documentation

- [x] 4.1 Fix duplicated or inconsistent section numbering in affected node docs after the contract cleanup.
- [x] 4.2 Remove obsolete backup or noise files under `product_manager/` that are no longer needed for the maintained workflow docs.
- [x] 4.3 Review the updated docs end-to-end to confirm node inputs, outputs, and references remain internally consistent.
