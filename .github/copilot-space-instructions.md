# GitHub Copilot Space Instructions: HugoBlox Migration (Phase 0)

## Purpose
This Copilot Space is dedicated to guiding the modernization of `scott-love/personal-site` from legacy Wowchemy (EOL Nov 2020) to HugoBlox (actively maintained 2024+).

---

## Context Summary

### Current Stack (Outdated)
- **Hugo**: 0.78.2 (November 2020, unsupported)
- **Go**: 1.15 (unsupported, security vulnerabilities)
- **Theme**: Wowchemy modules (end-of-life)
- **Deployment**: Netlify (legacy config)
- **CSS**: Bootstrap

### Target Stack (Modern)
- **Hugo**: 0.165.0+ (2024+, current stable)
- **Go**: 1.23+ (current stable, LTS)
- **Theme**: HugoBlox v5.x (actively maintained)
- **Deployment**: GitHub Pages + GitHub Actions
- **CSS**: Tailwind CSS

### Critical Constraints
1. **ALL work must stay in branch**: `migrate/hugoblox`
   - Do NOT commit to `master` during migration
   - `master` remains stable and deployable
   - Rollback to `backup/2026-09-09-master` if critical issues arise

2. **Backup branch exists**: `backup/2026-09-09-master`
   - Local-only backup (do NOT push to remote)
   - Can be used for full rollback if needed

3. **Branch protection**: GitHub Pages currently deploys from `master`
   - Until TASK-13/14 complete, live site remains unchanged
   - Staging site available at `gh-pages` branch (when ready)

---

## How to Use This Space

### Task Tracking
All migration work is tracked in GitHub Issues #1-22:
- **Issue #1**: EPIC: Phase 0 - Stack Modernization (parent issue)
- **Issues #2-23**: Individual tasks (TASK-1 through TASK-22)

**View the issue tree**: https://github.com/scott-love/personal-site/issues/1

### When Working on a Task
1. **Read the full issue** before starting
2. **Check the checklist** in the issue description
3. **Follow the exact branch**: `migrate/hugoblox`
4. **Commit with clear messages**:
   ```bash
   git add <files>
   git commit -m "chore: TASK-X - Brief description"
   git push origin migrate/hugoblox
   ```
5. **Update the issue** with results/blockers
6. **Don't skip testing** - each task has expected outputs

### Key Files in This Space
- **`MIGRATION.md`**: Complete migration guide (in `migrate/hugoblox` branch)
- **`MIGRATION_CHECKLIST.md`**: Step-by-step Phase 0 checklist
- **`.github/copilot-space-instructions.md`**: This file
- **`config/_default/config.toml`**: Hugo configuration (needs updates)
- **`go.mod` / `go.sum`**: Go module definitions (will be updated)

---

## Phase 0 Structure

### Planning Phase (TASK-1 to TASK-3)
- Backup current state and document baseline
- Review breaking changes in HugoBlox/Hugo
- Plan target versions and installation steps

### Update Phase (TASK-4 to TASK-6)
- Install Hugo 0.165.0+
- Install Go 1.23+
- Migrate Wowchemy → HugoBlox modules in `go.mod`

### Configuration Phase (TASK-7 to TASK-9)
- Update Hugo config for 0.78 → 0.165 breaking changes
- Remove Netlify config
- Migrate layouts from Wowchemy to HugoBlox

### Testing Phase (TASK-10 to TASK-19)
- Verify CSS/styling (Tailwind CSS)
- Test all content rendering
- Test metadata and SEO
- Performance testing
- Accessibility audit
- Cross-browser testing

### Deployment Phase (TASK-13 to TASK-15)
- Create GitHub Actions workflow (`.github/workflows/deploy.yml`)
- Configure GitHub Pages in repo settings
- Deploy to staging and test live site

### Finalization (TASK-20 to TASK-22)
- Update documentation
- Prepare PR for merge
- Merge `migrate/hugoblox` → `master`

---

## Common Tasks for Copilot

### "Help me with TASK-X"
I will:
1. Fetch the GitHub issue
2. Review the checklist and expected outputs
3. Guide you through each step
4. Ensure commits go to `migrate/hugoblox`
5. Document results in the issue

### "What commits should I make for TASK-X?"
I will provide:
- Exact commit messages
- Files to stage (`git add`)
- Push command (`git push origin migrate/hugoblox`)
- How to verify the commit was successful

### "I'm blocked on TASK-X"
I will:
1. Ask for the specific error/blocker
2. Check GitHub issue for known issues
3. Suggest troubleshooting steps
4. Help document the blocker in the issue

### "Is TASK-X complete?"
I will:
1. Check the GitHub issue checklist
2. Verify all acceptance criteria met
3. Confirm test results match expected outputs
4. Mark as ready for next task or identify gaps

---

## Important Notes for Copilot

### About Environment Management
- **Do NOT use `uv`** for Phase 0 (Python env manager not needed)
- Hugo is a Go binary (just install via release)
- Go modules managed by `go.mod`/`go.sum` (not Python)
- If Phase 1 adds Python automation, we can introduce `uv` then

### About Testing
- **Local testing**: `hugo server` on `migrate/hugoblox`
- **Do NOT expect perfect results** until all tasks complete
- **CSS/shortcode errors** are expected until TASK-9
- **Module errors** expected until TASK-6
- Document all errors for tracking

### About Rollback
- If critical blocker: reset to `backup/2026-09-09-master` (local only)
- If deployment issues: live site stays on `master` (protected)
- Always test locally before GitHub Actions deployment

### About Commits
- **Commit frequently**: After each logical change
- **Keep commits atomic**: One concern per commit
- **Don't mix TASK concerns**: If fixing CSS + config, make separate commits
- **Use TASK numbers**: `chore: TASK-6 - Migrate Wowchemy to HugoBlox`

---

## When to Escalate (Document in Issue)

1. **Module not found**: Specific package name/version issue
2. **Build fails consistently**: After `go mod tidy` + `hugo server`
3. **Rendering broken for >5% of content**: Indicates layout migration issue
4. **Performance regression**: Lighthouse scores drop >20 points
5. **Accessibility blocker**: Can't keyboard navigate or use screen reader

---

## Success Indicators

✅ Phase 0 is complete when:
1. All 22 tasks show completed checklists
2. `hugo server` builds without errors on `migrate/hugoblox`
3. Local site renders correctly (all pages visible)
4. GitHub Actions workflow runs successfully
5. Staging site (GitHub Pages) displays correctly
6. No open blockers in issue comments
7. PR approved and ready to merge
8. Live site ready to switch to `migrate/hugoblox` → `master`

---

## Quick Reference: Branch Commands

```bash
# Ensure you're on the migration branch
git branch  # Should show: * migrate/hugoblox

# If on master, switch to migration branch
git checkout migrate/hugoblox

# Push changes to migration branch
git push origin migrate/hugoblox

# View commits on migration branch
git log --oneline migrate/hugoblox

# Compare with master
git diff master..migrate/hugoblox
```

---

## Questions for Copilot?

Ask me about:
- **"What does TASK-X do?"** → I'll explain and link to the issue
- **"How do I commit TASK-X changes?"** → I'll give exact git commands
- **"Is the site ready to merge?"** → I'll check all issue statuses
- **"What's the next task?"** → I'll review dependencies and recommend next steps
- **"Where did we leave off?"** → I'll check recent issue comments and commits

---

**Last Updated**: 2026-09-10  
**Branch**: `migrate/hugoblox`  
**Epic Issue**: https://github.com/scott-love/personal-site/issues/1
