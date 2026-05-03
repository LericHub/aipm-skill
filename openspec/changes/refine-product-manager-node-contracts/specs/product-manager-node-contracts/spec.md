## ADDED Requirements

### Requirement: Workflow documents SHALL use a single version and path contract
The `product_manager` workflow documents SHALL use `Vx.y` as the only version format, SHALL store versioned outputs under `output/{Version}/`, and SHALL reference the PRD file using the `PRD_{Version}.md` naming pattern.

#### Scenario: Versioned PRD output is referenced consistently
- **WHEN** any node describes, reads, or writes the current PRD output
- **THEN** it MUST reference `output/{Version}/PRD_{Version}.md`

#### Scenario: Version directory examples are documented consistently
- **WHEN** workflow documentation includes output directory examples
- **THEN** it MUST use `output/{Version}/` and MUST NOT mix `V1` and `V1.0` style directories

### Requirement: Memory SHALL expose a unified node output structure
`Memory.json` SHALL model node outputs under `node_list[].output`, where file-based outputs use `output.path`, Penpot outputs use `output.page_key` and `output.page_id`, and shared Penpot page identifiers are stored in `penpot.pages`.

#### Scenario: File-producing nodes expose output paths uniformly
- **WHEN** a node persists a markdown, PRD, or HTML bundle output
- **THEN** the output MUST be recorded in `node_list[].output.path`

#### Scenario: Penpot-producing nodes expose page references uniformly
- **WHEN** a node persists a Penpot page output
- **THEN** the output MUST be recorded in `node_list[].output.page_key` and `node_list[].output.page_id`

#### Scenario: Penpot page identifiers are globally indexed
- **WHEN** workflow documentation defines the Memory schema
- **THEN** it MUST include a `penpot.pages` object that stores the canonical page identifiers by key

### Requirement: Workflow state SHALL use only three persisted statuses
The workflow state model SHALL persist only `PENDING`, `DRAFT`, and `CONFIRM` as node statuses, and SHALL NOT define alternative persisted status enums for the same lifecycle.

#### Scenario: Node status examples use only the unified enum
- **WHEN** a document defines node status values or status examples
- **THEN** it MUST only use `PENDING`, `DRAFT`, and `CONFIRM`

#### Scenario: Derived state descriptions are not stored as persisted enums
- **WHEN** a document explains concepts such as locked or completed behavior
- **THEN** it MUST describe them as explanatory logic and MUST NOT present them as persisted node status values

### Requirement: Router SHALL be the only knowledge card loading entry point
The workflow SHALL require Router to read `knowledge_cards/index.md` at the start of each round, and Router SHALL be the only document that defines the knowledge card loading entry behavior.

#### Scenario: Router performs knowledge card entry processing
- **WHEN** a new user message enters the workflow
- **THEN** Router MUST read `knowledge_cards/index.md` before selecting relevant knowledge cards

#### Scenario: Business nodes do not redefine knowledge card loading rules
- **WHEN** a business node describes its inputs
- **THEN** it MUST state that it consumes Router-provided knowledge card context and MUST NOT define a separate loading strategy

### Requirement: Penpot preflight guidance SHALL be maintained in a shared drawing skill
The workflow SHALL maintain shared Penpot preflight guidance in `penpot-drawing-skill`, and Penpot-related nodes SHALL reference that shared guidance instead of duplicating the same setup instructions.

#### Scenario: Penpot nodes reference shared preflight guidance
- **WHEN** `drawUserStory`, `detail`, or `genPrototype` describes Penpot setup requirements
- **THEN** the node MUST reference `penpot-drawing-skill` as the shared source for preflight guidance

#### Scenario: Repeated helper and MCP setup text is removed from nodes
- **WHEN** Penpot-related node documents are reviewed
- **THEN** repeated descriptions of helper loading, MCP rules, and layout prechecks MUST be centralized rather than copied into multiple nodes

### Requirement: Detail SHALL focus on page-level detailed design outputs
The `detail` node SHALL define page-level detailed design content, including page structure, event definitions, exception handling, tracking timing, and necessary state machines, and SHALL NOT duplicate workflow-wide shared guidance.

#### Scenario: Detail defines PRD refinement content
- **WHEN** the `detail` node documents its responsibilities
- **THEN** it MUST include page structure, events, exceptions, tracking, and optional state machine design as its core outputs

#### Scenario: Detail does not restate shared workflow guidance
- **WHEN** the `detail` node references shared conventions such as knowledge card loading or Penpot preflight
- **THEN** it MUST reference the shared source instead of restating the full guidance inline
