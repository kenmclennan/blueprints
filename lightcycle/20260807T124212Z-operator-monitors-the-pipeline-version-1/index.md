---
blueprint_id: "operator-monitors-the-pipeline-version-1"
source_system: "saga"
source_map_id: "af4932a5-2d20-4c72-a08e-d98800ac297a"
source_slice: "Version 1"
project: "Lightcycle"
exported_at: "20260807T124212Z"
---

# Operator monitors the pipeline - Version 1

A single-operator, read-only situational-awareness TUI that runs alongside `lc start`, surfacing pool/pipeline state (status, inbox, active, queue, ps, trace, logs) without any mutating actions.

## Contents

- [Design Principles](principles/index.md) - 4
- [User Stories](user-stories/index.md) - 51
- [Wireframes](wireframes/index.md) - 4
- [Design System](design-system/index.md) - 2
- [Architecture](architecture/index.md) - 1
- [User Flows](user-flows/index.md) - 1
- [API Contracts](api-contracts/index.md) - 1
- [Quality Attributes](quality-attributes/index.md) - 1
- [Domain Model](domain-model/index.md) - 2
- [Glossary](glossary/index.md) - 1

## Source Repositories

The codebase(s) this design targets, referenced for context.

- [lightcycle (engine)](https://github.com/kenmclennan/lightcycle)
- [lightcycle-workflows (built-in workflow origin)](https://github.com/kenmclennan/lightcycle-workflows)
- [lightcycle-plugin (Claude Code companion)](https://github.com/kenmclennan/lightcycle-plugin)
