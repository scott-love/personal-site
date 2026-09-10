# HugoBlox Migration Guide

**Status**: In Progress  
**Timeline**: ~7–10 weeks (Phases 0–4)  
**Branch**: `migrate/hugoblox`  
**Target**: Modernize stack (Hugo 0.78 → 0.165+, Wowchemy → HugoBlox) + implement data automation

---

## Quick Links

- **[Migration Checklist](./MIGRATION_CHECKLIST.md)** - Runnable step-by-step checklist
- **[GitHub Project Board](https://github.com/scott-love/personal-site/projects)** - Real-time progress tracking
- **[GitHub Issues](https://github.com/scott-love/personal-site/issues?q=milestone%3A%22HugoBlox+Migration%22)** - Detailed tasks & discussions
- **[GitHub Wiki](https://github.com/scott-love/personal-site/wiki)** - Full documentation

---

## Overview

### Current State (Master)
- **Hugo**: 0.78.2 (November 2020) — **5+ years old**
- **Go**: 1.15 — **Unsupported**
- **Wowchemy Modules**: v0.0.0-20201125 (November 2020) — **End-of-life**
- **Theme**: Wowchemy (Academic) — **Deprecated, rebranded to HugoBlox (2024)**
- **Deployment**: Netlify (`netlify.toml`)
- **Data**: Manual publication updates

### Target State (migrate/hugoblox)
- **Hugo**: 0.165.0+ — Current stable
- **Go**: 1.23+ — Current stable
- **HugoBlox Modules**: Latest stable — Actively maintained (2024+)
- **Theme**: HugoBlox (successor to Wowchemy/Academic)
- **Deployment**: GitHub Pages via GitHub Actions
- **Data**: Semi-automated via academic-cv integration

---

## Timeline & Phases

| Phase | Duration | Focus | Status |
|-------|----------|-------|--------|
| **Phase 0** | 2–3 weeks | Stack Modernization (Hugo, Go, HugoBlox) | 🔵 To Start |
| **Phase 1** | 1 week | Data Pipeline Discovery | ⚪ Planned |
| **Phase 2** | 2–3 weeks | Data Pipeline Implementation | ⚪ Planned |
| **Phase 3** | 1–2 weeks | CI/CD Integration | ⚪ Planned |
| **Phase 4** | 1+ weeks | Extended Automation & Polish | ⚪ Planned |

---

## Phase 0: Stack Modernization (Current)

### Goals
1. ✅ Backup current site
2. ✅ Update Hugo from 0.78.2 → 0.165.0+
3. ✅ Update Go from 1.15 → 1.23+
4. ✅ Update Wowchemy → HugoBlox modules
5. ✅ Migrate layouts and styling (Wowchemy → Tailwind CSS)
6. ✅ Test all content rendering
7. ✅ Update GitHub Pages deployment config
8. ✅ Deploy to staging & comprehensive testing

### Key Changes Expected

#### Breaking Changes (Hugo 0.78 → 0.165)
- Markdown syntax changes (Goldmark updates)
- Shortcode parameter handling
- Image processing pipeline updates
- Configuration schema changes (e.g., `languageCode` → locale)
- Module system improvements

#### Breaking Changes (Wowchemy → HugoBlox)
- CSS framework: Bootstrap → Tailwind CSS
- Component structure changes
- Configuration file organization
- Theme partials refactored
- Publication/content type structures updated

#### GitHub Pages Setup
- Remove Netlify build config
- Create GitHub Actions workflow (`.github/workflows/deploy.yml`)
- Configure repo to use Actions as deployment source
- Set up automatic deployments on push to `migrate/hugoblox`

### Work Items
See **[MIGRATION_CHECKLIST.md](./MIGRATION_CHECKLIST.md)** for detailed step-by-step tasks.

### Migration Tracking Notes (Issue 5)
- Hugo installation validated on migration environment:
  - `hugo v0.165.0-76a5e1880ab46688155b02e99bab9be2a6134492+extended darwin/arm64`
- `hugo server` on `migrate/hugoblox` currently reports deprecations and one fatal render error from legacy Wowchemy templates:
  - deprecated config keys: `languages.fr.languageCode`, `languages.en.languageCode`
  - deprecated template usage: `.Site.LanguageCode`, `.Site.Data`
  - fatal error: `can't evaluate field GoogleAnalytics in type interface {}` from `partials/marketing/google_analytics.html`
- This behavior is treated as expected during Phase 0 modernization after Hugo upgrade and is tracked for remediation in subsequent migration tasks (no ad hoc fix in Issue 5 checkpoint step).

### Migration Tracking Notes (Issue 6 / TASK-6)
- Reviewed HugoBlox module direction using upstream module definition:
  - `https://github.com/HugoBlox/hugo-blox-builder/blob/main/go.mod`
- Confirmed migration policy for module namespace updates:
  - Replace legacy `github.com/wowchemy/...` module references with `github.com/HugoBlox/...` equivalents where applicable.
- Version pinning rule for TASK-6:
  - Reuse module versions agreed in **TASK-3** during initial path migration; avoid unplanned version bumps while resolving path changes.
- Validation sequence for module migration changes:
  1. `go mod tidy`
  2. `hugo mod graph`
  3. `hugo server` (and migration CI on `migrate/hugoblox`)
- Risk control / rollback approach:
  - Use a pre-TASK-6 checkpoint commit/tag on `migrate/hugoblox`.
  - If module resolution or template compatibility regressions occur, restore `go.mod`/`go.sum` from that checkpoint.

### Migration Tracking Notes (Issue 9 / TASK-8: Layout overrides → HugoBlox blocks)
- Layout inventory audit result:
  - Local `layouts/` directory is not present in this repository.
  - No local `layouts/shortcodes/` or `layouts/partials/` overrides were found.
  - Interpretation: template rendering currently inherits from HugoBlox modules, not local Wowchemy overrides.
- Wowchemy-specific template code audit:
  - No `wowchemy` references exist in any local layout/template override files because no local layout override files exist.
  - Remaining Wowchemy references are in legacy comments/content metadata and are tracked separately from layout migration.
- HugoBlox block-system alignment notes:
  - HugoBlox landing pages are block/section based; homepage composition should come from `content/*/home/*.md` sections rather than local `layouts/` overrides.
  - Common HugoBlox block families include hero, features, about/profile, experience, collection/pages, contact, and CTA blocks.
- TASK-8 migration action taken:
  - Added `layouts.wowchemy-backup/` snapshot directory as rollback anchor for this migration step.
  - Since there were no local layout overrides to migrate, no template file deletions or path rewrites were required in this repository.

---

## Phase 1: Data Pipeline Discovery

### Goals
1. Inspect current HugoBlox publication structure
2. Map academic-cv outputs → HugoBlox content format
3. Identify other automatable content (education, employment, talks, etc.)
4. Design unified data schema

### Deliverable
- Architecture document: "Data Flow Specification"
- Schema definition for publications, education, employment, etc.
- Integration points identified

---

## Phase 2: Data Pipeline Implementation

### Goals
1. Refactor academic-cv to support multiple output formats
2. Write Python transforms: publications.json → HugoBlox Markdown/YAML
3. Implement transforms for other content types
4. Write tests for transformation pipeline

### Deliverable
- Python module in academic-cv for Wowchemy/HugoBlox output
- Automated generation of publication content
- Test suite validating transforms

---

## Phase 3: CI/CD Integration

### Goals
1. Create unified GitHub Actions workflow
2. Trigger personal-site rebuild when academic-cv data changes
3. Automatic deployment on successful build
4. Status checks and notifications

### Deliverable
- Integrated CI/CD pipeline
- Automatic site updates when academic-cv publishes new data
- GitHub Pages deployment automation

---

## Phase 4: Extended Automation & Polish

### Goals
1. Extend automation to education, employment, teaching, supervision
2. Optional: Add Ownable CMS for non-technical content edits
3. Document maintenance procedures
4. Clean up tech debt

### Deliverable
- Fully automated academic content updates
- Maintenance playbooks
- Team documentation

---

## Key Decisions & Rationale

### Decision 1: Migrate vs. Rebuild
**Decision**: Migrate to HugoBlox (vs. full rebuild with Astro/Next.js)  
**Rationale**: 
- 80% of benefits with 20% of effort
- HugoBlox is designed for academic sites
- Low migration risk; proven path
- Data automation integrates seamlessly
- **Timeline**: 2–3 weeks vs. 5–7 weeks for rebuild

### Decision 2: Deployment Platform
**Decision**: GitHub Pages (via GitHub Actions) instead of Netlify  
**Rationale**:
- No vendor lock-in
- Tighter integration with repository
- Free tier is unlimited
- Simpler for data sync across repos (academic-cv → personal-site)

### Decision 3: Data Architecture
**Decision**: Python-driven transforms (academic-cv) → YAML/Markdown files → Hugo builds  
**Rationale**:
- academic-cv already has working data pipeline
- Decoupled from site build
- Easy to test independently
- No complex API layer needed

---

## Important Notes

### Branching Strategy
- **migrate/hugoblox**: All migration work stays here until fully tested
- **master**: Untouched until Phase 0 complete and verified
- **Merge plan**: PR with full testing before master merge

### Testing Strategy
- Local builds with `hugo server`
- GitHub Actions CI for automated testing
- Staging deployment (GitHub Pages branch) before production
- Full regression testing (content, layout, performance)

### Rollback Plan
If something breaks:
1. Keep master branch intact (untouched)
2. Can revert to any commit on migrate/hugoblox
3. GitHub Pages can point to master while we fix migrate/hugoblox
4. No production downtime

### GitHub Pages Configuration
- Current: Netlify builds from master
- Migration: GitHub Actions builds from migrate/hugoblox
- Final: GitHub Actions builds from master (after Phase 0 merge)

---

## Next Steps

1. **Read** [MIGRATION_CHECKLIST.md](./MIGRATION_CHECKLIST.md) for detailed step-by-step procedures
2. **View** [GitHub Project Board](https://github.com/scott-love/personal-site/projects) for real-time tracking
3. **Discuss** any Phase 0 tasks in [GitHub Issues](https://github.com/scott-love/personal-site/issues)
4. **Refer to** [GitHub Wiki](https://github.com/scott-love/personal-site/wiki) for detailed documentation

---

## Support & Contact

- **Questions about migration?** Comment on the relevant GitHub Issue
- **Found a blocker?** Create a new issue with `[BLOCKER]` tag
- **Want to discuss architecture?** Check the Wiki or start a Discussion

---

**Last Updated**: September 2026  
**Maintained By**: scott-love  
**Status**: 🔵 Active Migration
