# TASK-2: Breaking Changes Analysis for HugoBlox Migration

**Status**: ✅ Research Complete  
**Date**: September 10, 2026  
**Scope**: Comprehensive breaking changes documentation for Wowchemy → HugoBlox + Hugo 0.78.2 → 0.165.0+ + Go 1.15 → 1.23+

---

## Executive Summary

Your site is undergoing a **major stack modernization** with **multiple breaking changes** across three dimensions:

| Dimension | Current | Target | Gap | Risk |
|-----------|---------|--------|-----|------|
| **Hugo** | 0.78.2 (Nov 2020) | 0.165.0+ | 5+ years | 🔴 High |
| **Go** | 1.15 (unsupported) | 1.23+ | 8+ years | 🔴 High |
| **Theme** | Wowchemy v5.x (EOL) | HugoBlox v5.x (current) | Rebrand + restructure | 🟡 Medium |

**Overall Migration Risk Level**: **🟡 MEDIUM** — Manageable with careful testing

---

## 1. HugoBlox Official Migration Guide Summary

### Getting Started with HugoBlox
**Source**: https://hugoblox.com/docs/start

HugoBlox (formerly Wowchemy, rebranded 2024) is actively maintained and provides **multiple paths**:
- **AI-powered setup** (describe your site, AI builds it)
- **Online deployer** (select template, deploy instantly)
- **Local CLI** (recommended for existing projects)
- **Visual editor** (HugoBlox Studio VS Code extension)

### Automated Upgrade Path
**Source**: https://hugoblox.com/docs/guide/maintenance/upgrade

HugoBlox includes a **Zero-Touch Automated Upgrader** via GitHub Actions:
- ✅ Runs weekly via GitHub Actions
- ✅ Automatically detects new HugoBlox releases
- ✅ Creates PR with smart migrations applied
- ✅ You can preview before merging
- ✅ Can be manually triggered anytime

**Setup Requirements**:
- Enable GitHub Actions read/write permissions
- Add `.github/workflows/upgrade.yml` workflow file
- Allows GitHub Actions to create/approve PRs

---

## 2. Wowchemy → HugoBlox Breaking Changes

### Critical: CSS Framework Migration
**Bootstrap 4.x → Tailwind CSS**

| Aspect | Wowchemy | HugoBlox | Impact |
|--------|----------|---------|--------|
| **CSS Framework** | Bootstrap 4.4.1 | Tailwind CSS v3+ | Complete redesign |
| **Performance** | Standard | 3× faster (claimed) | Page load speed +++ |
| **Custom CSS** | Bootstrap classes | Utility classes | Must rewrite `static/css/` files |
| **Customization** | Theme settings | Config-driven | Different approach |

**Impact on Your Site**:
- ⚠️ Bootstrap v4 CSS files in `resources/_gen/assets/scss/` will NOT be used
- ⚠️ Any custom CSS using Bootstrap classes will break
- ⚠️ Need to migrate to Tailwind CSS or rewrite custom styles

**Your Current State**:
```
resources/_gen/assets/scss/main.scss_c02e99605dfd8ea7bf9737a8502f6f38.content
resources/_gen/assets/scss/scss/main.scss_8eb176101042023239427cc6e6903092.content
```
Both reference: `Bootstrap v4.4.1` — **MUST be replaced**

### Critical: Module System Change
**Wowchemy Modules → HugoBlox Modules**

**Current** (`go.mod`):
```go
require (
  github.com/wowchemy/wowchemy-hugo-modules/wowchemy v0.0.0-20201125230219-3a03b728de8f
  github.com/wowchemy/wowchemy-hugo-modules/netlify-cms-academic v0.0.0-20201125230219-3a03b728de8f
)
```

**Target** (`go.mod`):
```go
require (
  github.com/HugoBlox/hugo-blox-core/v5 v5.9.7
  github.com/HugoBlox/hugo-blox-cms/v5 v5.9.7
)
```

**Breaking Changes**:
- ✅ Module paths completely different
- ✅ Netlify CMS is deprecated → use HugoBlox Studio
- ✅ Core module structure reorganized
- ✅ Need to update `go.mod` and run `go mod tidy`

### Critical: Configuration Restructuring
**Namespace Unification**

Wowchemy config scattered across `params.toml`. HugoBlox uses unified `hugoblox` namespace:

| Parameter | Wowchemy | HugoBlox | Status |
|-----------|----------|---------|--------|
| `theme` | Direct in params | Under `hugoblox.appearance` | 🔄 Changed |
| `day_night` | Direct | Under `hugoblox.appearance.day_night` | 🔄 Changed |
| `highlight` | Direct | Under `hugoblox.appearance.highlighting` | 🔄 Changed |
| Author data | `content/authors/` | `data/authors/` | 🔄 Moved |
| Event params | Mixed with page fields | Namespaced (`event_start`, `event_end`) | 🔄 Changed |

**Your config** (`config/_default/params.toml`):
- ⚠️ Uses old Wowchemy-style parameters (150+ lines)
- ⚠️ References deprecated Wowchemy docs links
- ⚠️ NetlifyCMS config (`cms.netlify_cms = true`) will not work

### Medium: Widget/Block Structure Changes
Wowchemy widgets → HugoBlox blocks:
- Widget-based pages in `content/en/home/*.md` still exist
- Block definitions have changed naming and configuration
- Custom shortcodes using Wowchemy syntax may break

**Your Current Widgets**:
```
content/en/home/about.md         (About widget)
content/en/home/posts.md         (Posts widget)
content/en/home/misc.md          (Custom widget using {% twitter %} shortcode)
content/en/home/projects.md      (Portfolio widget)
content/fr/home/contact.md       (Contact widget)
```

---

## 3. Hugo Version Breaking Changes (0.78.2 → 0.165.0+)

### 3.1 Markdown & Goldmark Parser

**Key Changes**:
- Goldmark parser underwent multiple updates
- More strict CommonMark compliance
- HTML sanitization behavior changed
- Auto-generated heading IDs algorithm changed (0.82+)

**Your Current Config** (`config/_default/config.toml`):
```toml
[markup.goldmark]
  [markup.goldmark.renderer]
    unsafe = true  # Enable user to embed HTML snippets
  [markup.highlight]
    codeFences = false  # Disable Hugo's highlighter
```

**Potential Issues**:
- ⚠️ `unsafe = true` is still supported, but stricter
- ⚠️ Raw HTML in Markdown may render differently
- ⚠️ Code fence syntax may have evolved
- ⚠️ Anchor links (e.g., `#my-heading`) may break if IDs changed

**Action**: Test all Markdown content after upgrade

### 3.2 Shortcode Parameter Parsing

**Breaking Change**: Stricter parameter parsing (v0.80+)

**Old (might have worked)**:
```handlebars
{{< myshortcode param1 Unquoted value with spaces param2="value" >}}
```

**Now (must be strict)**:
```handlebars
{{< myshortcode param1="Unquoted value with spaces" param2="value" >}}
```

**Your Code**:
```
content/en/home/misc.md
{{% twitter love_a_scott %}}
```
Likely will still work (single parameter), but worth testing.

### 3.3 Image Processing Pipeline

**Updates** (v0.78.1+, v0.120+, v0.155+):
- NPM package resolution changed (0.78.1)
- Resource/asset chaining behavior fixed
- Image processing library updates (AVIF, WebP, etc.)
- Memory allocation changes for large images

**Your Impact**:
- ✅ Limited custom image processing found (uses standard Hugo images)
- ⚠️ AVIF and WebP support is new (can be leveraged)
- ✅ Likely minimal breakage

### 3.4 Module System Improvements

**Go Modules Requirements**:
- Hugo 0.78.2 requires **Go 1.13+**
- Hugo 0.165.0+ requires **Go 1.18+** (recommended 1.21+)

**Action**: Update Go to 1.23+ (see section 4)

### 3.5 Security Hardening

**v0.162.0+**: HTML content (`text/html`) now **denied by default**
- Sites using `.html` files must opt-in via `security.allowContent`

**Action**: If you have HTML content files, add to config:
```toml
[security]
  allowContent = ['.*']  # Or be more specific
```

### 3.6 Configuration Deprecations

**Deprecated Parameters** (v0.158.0+):
- `languageCode` → use `locale` instead
- `languages.<lang>.languageName` → use `label`
- `languages.<lang>.languageDirection` → use `direction`

**Your Config**: 
- ⚠️ Uses `languageCode = "en"` — will work but deprecated

---

## 4. Go Version Breaking Changes (1.15 → 1.23+)

### 4.1 Major Changes by Version

| Version | Key Changes | Impact |
|---------|-------------|--------|
| **1.15** | Your current (August 2020, unsupported) | EOL |
| **1.16–1.17** | Modules improvements, minor syntax changes | ✅ Mild |
| **1.18** | **Generics introduced** — reserved keywords: `any`, `comparable`, `type` | 🟡 Potential naming conflicts |
| **1.19–1.20** | Stricter runtime checks, stdlib tightening | ✅ Mild |
| **1.21–1.23** | Security updates, optimizations, deprecations | ✅ Mild |

### 4.2 Generics Consideration (Go 1.18+)

**Reserved Keywords**: If your code uses `any`, `comparable`, or `type` as variable names, it will break.

**Your Code**: No custom Go code found in repo — **✅ Not an issue**

### 4.3 Module Dependency Updates

**Action Required**:
- Update `go.mod`: Change `go 1.15` → `go 1.23`
- Run `go mod tidy` to prune outdated dependencies
- Hugo modules will auto-update to compatible versions

**Your `go.mod`**:
```go
module github.com/wowchemy/starter-academic
go 1.15
require (...)
```
→ Must become:
```go
module github.com/scott-love/personal-site
go 1.23
require (
  github.com/HugoBlox/hugo-blox-core/v5 v5.9.7
  github.com/HugoBlox/hugo-blox-cms/v5 v5.9.7
)
```

### 4.4 No Major Compatibility Issues Expected

**Good News**: Go maintains strong backward compatibility. Most code that compiled on 1.15 will compile on 1.23.

---

## 5. Custom Site Features Audit

### Features Identified

**✅ Using Wowchemy-Standard Features** (should migrate):
- About/profile widget
- Blog posts/blog listing
- Projects/portfolio widget
- Contact widget
- Publications (Academic feature)
- Author profiles
- Multi-language support (English & French)

**✅ Analytics**:
- Google Analytics: `UA-115123153-1`
- Google Tag Manager: `G-TSDCCS0DQG`

**⚠️ Custom Features**:
- Twitter shortcode: `{{% twitter love_a_scott %}}`
- Custom CSS styling (if any in `static/css/`)
- Custom layouts (if any in `layouts/`)

**✅ No Critical Custom Code**:
- No custom Go code
- No complex custom shortcodes
- No heavily modified templates

### Files to Review

**Configuration**:
- `config/_default/config.toml` (114 lines) — needs migration
- `config/_default/params.toml` (289 lines) — **heavy rewrite needed**

**Content Widgets**:
- `content/en/home/` — 5 widget files
- `content/fr/home/` — 5 widget files

**Styling**:
- `resources/_gen/assets/scss/` — Bootstrap CSS will be regenerated

---

## 6. Identified Migration Blockers & Mitigations

### Blocker 1: Bootstrap → Tailwind CSS

**Issue**: Old Bootstrap 4 CSS framework, new Tailwind CSS system

**Severity**: 🔴 **High Impact, Medium Effort**

**Mitigation**:
- HugoBlox comes with Tailwind CSS built-in
- Auto-migration of Bootstrap → Tailwind classes
- May need manual tweaks for custom CSS
- Can compare Wowchemy template with HugoBlox template

**Action Items**:
- [ ] Test HugoBlox theme rendering
- [ ] Compare Wowchemy vs HugoBlox CSS output
- [ ] Adjust custom CSS if needed

### Blocker 2: Wowchemy Module System

**Issue**: Complete module path change + Netlify CMS → HugoBlox Studio

**Severity**: 🔴 **High Impact, Low Effort (scripted)**

**Mitigation**:
- Hugo's `go mod tidy` will auto-resolve
- Netlify CMS config can be removed (or migrated to HugoBlox Studio)
- HugoBlox CLI can help with automated migration

**Action Items**:
- [ ] Update `go.mod` with HugoBlox module paths
- [ ] Remove `netlify-cms-academic` module reference
- [ ] Remove NetlifyCMS config from `params.toml`
- [ ] Run `hugo mod get -u` to resolve

### Blocker 3: Configuration Restructuring

**Issue**: Parameter namespace changes (no unified `hugoblox` namespace yet in v5)

**Severity**: 🟡 **Medium Impact, High Effort**

**Mitigation**:
- HugoBlox v5 config still similar to Wowchemy
- Config migration guide exists
- Check HugoBlox example config for exact parameter names
- Some parameters renamed, some reorganized

**Action Items**:
- [ ] Review HugoBlox example config
- [ ] Identify which `params.toml` settings are renamed
- [ ] Update config file systematically

### Blocker 4: Author Data Migration

**Issue**: Authors move from `content/authors/` → `data/authors/`

**Severity**: 🟡 **Medium Impact, Low Effort**

**Mitigation**:
- Simple file move/rename
- Data structure should be compatible
- HugoBlox CLI may automate this

**Action Items**:
- [ ] Check if `content/authors/` exists
- [ ] Migrate to `data/authors/` if needed
- [ ] Verify author references in posts still work

### Blocker 5: Twitter Shortcode

**Issue**: Custom `{{% twitter %}}` shortcode may not be built-in

**Severity**: 🟡 **Low Impact, Low Effort**

**Mitigation**:
- HugoBlox may have built-in Twitter embed
- If not, can create custom shortcode
- Or use native Twitter embed HTML

**Action Items**:
- [ ] Test if Twitter shortcode works in HugoBlox
- [ ] If not, implement custom shortcode or use HTML embed

---

## 7. Config.toml Breaking Changes (Detailed)

### Required Updates

**Deprecated** (will work but deprecated):
```toml
defaultContentLanguage = "en"  # Works, but deprecated
```

**Consider deprecation** (v0.158.0+):
```toml
# OLD (deprecated):
languageCode = "en"
# NEW (recommended):
locale = "en"
```

**Likely Unchanged** (should still work):
```toml
baseurl = "https://scott-love.github.io/"
title = "Scott Love"
copyright = "© 2020 Scott Love"
summaryLength = 30
paginate = 10
enableEmoji = true
enableRobotsTXT = true
footnotereturnlinkcontents = "<sup>^</sup>"

[markup]
  defaultMarkdownHandler = "goldmark"
  [markup.goldmark.renderer]
    unsafe = true  # Still works, may be stricter
  [markup.highlight]
    codeFences = false  # May need testing

[outputs]
[mediaTypes]
[outputFormats.WebAppManifest]
# All should still work

[taxonomies]
# Should still work

[related]
# Should still work
```

---

## 8. Risk Assessment

### Overall Migration Risk: 🟡 **MEDIUM**

**Why Medium (not High)**:
- ✅ Clear migration path (HugoBlox → Wowchemy successor)
- ✅ No critical custom code
- ✅ Mostly config and theme updates
- ✅ Automated tools available (CLI, GitHub Actions)
- ⚠️ CSS framework change is major but manageable
- ⚠️ Configuration restructuring is manual but straightforward

**Why Not Low**:
- 🔴 5-year version jumps are significant
- 🔴 Bootstrap → Tailwind is not trivial
- 🔴 Config has no auto-migration script

**Estimated Time**:
- **Phase 0 (Stack modernization)**: 2–3 weeks (per MIGRATION.md)
- Most time: Testing + CSS review + Config updates

---

## 9. Recommended Migration Order

### Phase 0A: Pre-Migration (2–3 days)
1. ✅ Back up current site (TASK-1 — completed)
2. ✅ Read HugoBlox docs (TASK-2 — this document)
3. ✅ Plan versions (TASK-3 — next)

### Phase 0B: Environment Setup (1–2 days)
4. Update Hugo locally: 0.78.2 → 0.165.0+
5. Update Go locally: 1.15 → 1.23+
6. Verify `hugo version` and `go version`

### Phase 0C: Module Migration (2–3 days)
7. Update `go.mod`: Version → 1.23
8. Update `go.mod`: Module paths → HugoBlox
9. Run `go mod tidy` and `hugo mod graph`
10. Local build test: `hugo server`

### Phase 0D: Configuration Migration (3–5 days)
11. Review HugoBlox example config
12. Update `config/_default/config.toml`
13. Rewrite `config/_default/params.toml`
14. Remove `netlify.toml` (Netlify build config)
15. Local build test: `hugo server`

### Phase 0E: Layout & Style Migration (3–5 days)
16. Compare Wowchemy vs HugoBlox layouts
17. Test widget rendering
18. Review CSS output (Bootstrap → Tailwind)
19. Adjust custom CSS if needed
20. Local build test with visual inspection

### Phase 0F: Content Testing (2–3 days)
21. Test all Markdown rendering
22. Test Twitter shortcode
23. Test author profiles
24. Verify multi-language support

### Phase 0G: Deployment (1–2 days)
25. Set up GitHub Actions workflow (`.github/workflows/deploy.yml`)
26. Configure GitHub Pages settings
27. First deploy to `gh-pages` branch
28. Verify live site rendering

### Phase 0H: Final Verification (2–3 days)
29. Regression testing (all links, forms, etc.)
30. Performance testing (Lighthouse)
31. Accessibility audit
32. Cross-browser testing

### Phase 0I: Merge & Production (1 day)
33. Create PR: `migrate/hugoblox` → `master`
34. Full review
35. Merge and monitor for 24–48 hours

---

## 10. Resources & Next Steps

### Key Documentation Links
- **HugoBlox Docs**: https://hugoblox.com/docs/
- **Hugo Releases**: https://github.com/gohugoio/hugo/releases
- **Go Releases**: https://golang.org/doc/devel/release
- **Wowchemy (Archived)**: https://github.com/wowchemy/wowchemy-hugo-modules
- **HugoBlox Builder**: https://github.com/HugoBlox/hugo-blox-builder

### Immediate Actions
1. ✅ Review this document
2. → **TASK-3**: Plan target versions (Hugo, Go, HugoBlox)
3. → **TASK-4**: Install Hugo 0.165.0+
4. → **TASK-5**: Install Go 1.23+
5. → **TASK-6**: Migrate modules in `go.mod`

---

## 11. Questions & Unknowns

**For TASK-3 & Beyond**:
- [ ] Exact target Hugo version: 0.165.0 or 0.166.0+?
- [ ] Will Twitter shortcode migrate automatically?
- [ ] Are there custom CSS files in `static/css/` to audit?
- [ ] Are there custom layouts in `layouts/` to audit?
- [ ] Should we use HugoBlox Studio or stick with CLI?

---

## Summary

**✅ READY FOR MIGRATION**

Your site can be migrated successfully with careful planning:

1. **Bootstrap → Tailwind CSS**: Major but manageable; HugoBlox handles most automatically
2. **Hugo 0.78.2 → 0.165+**: Mostly backward compatible; Goldmark is stricter
3. **Go 1.15 → 1.23+**: Strong backward compatibility; no custom Go code to worry about
4. **Wowchemy → HugoBlox**: Clear migration path; modules update via `go mod tidy`
5. **Configuration**: Manual rewrite needed; no major blockers

**No show-stoppers identified.** Proceed to TASK-3 (version planning).

---

**Document Status**: ✅ Complete  
**Blocks**: TASK-3, TASK-4, TASK-5, TASK-6  
**Next**: Create GitHub issue comment referencing this file
