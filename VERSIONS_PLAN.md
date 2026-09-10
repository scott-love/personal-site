# Version Plan for Phase 0

This document records the target versions for the HugoBlox migration planning work in TASK-3 / issue #4.

## Selected target versions

| Component | Current | Target | Reason |
|---|---:|---:|---|
| Hugo | 0.78.2 | 0.165.0 | Latest stable Hugo release and matches the migration checklist target (`0.165.0+`). |
| Go | 1.15 | 1.27.1 | Current stable Go release suitable for building Hugo 0.165.0 from source. |
| HugoBlox modules | Wowchemy 2020-era modules | Latest stable HugoBlox v5.x | HugoBlox is the maintained successor to Wowchemy/Academic. |

## Compatibility matrix

| Hugo | Go | HugoBlox modules | Status |
|---|---:|---|---|
| 0.165.0 | 1.27.1 | Latest stable v5.x | Planned target |
| 0.78.2 | 1.15 | Wowchemy 2020-era modules | Current legacy stack |

## Why Hugo 0.165.0

- It is the current latest stable Hugo release.
- It aligns with the migration checklist recommendation of `0.165.0+`.
- It provides a clean baseline for moving off the unsupported 0.78.2 stack.
- It is consistent with the broader HugoBlox migration target documented in `MIGRATION.md`.

## Installation approach

### Hugo
Recommended installation method: download the official binary release for your OS from the Hugo releases page.

Example validation command:
```bash
hugo version
```

### Go
Recommended installation method: use the official Go installer or platform package manager, then verify the version.

Example validation command:
```bash
go version
```

### HugoBlox modules
Update the module dependencies in `go.mod` and then run module cleanup.

Example commands:
```bash
go mod tidy
hugo mod graph
```

## Rollback plan

If the target Hugo version causes issues during migration:

1. Keep the backup branch intact.
2. Reinstall or restore the previous Hugo version (`0.78.2`) for temporary validation.
3. Revert HugoBlox module changes in `go.mod` if required.
4. Continue testing on `migrate/hugoblox` before any merge to `master`.

## Notes

- The migration checklist’s note about Go 1.23+ is outdated relative to the current Hugo release line.
- Hugo 0.165.0 should be treated as the target for the migration plan.
- This document is planning only and does not modify the live site.
