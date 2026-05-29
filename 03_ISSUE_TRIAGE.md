# 03_ISSUE_TRIAGE.md — Metrics Issue Triage

## Recent Issues Summary (30 issues from `gh issue list`)

| # | Title | Type | Labels | Problem Statement |
|---|-------|------|--------|------------------|
| 1713 | sharp fails to compile | bug | — | sharp requires vips, node-gyp fails on Alpine |
| 1706 | "Manager" Achievement Broken Due to Projects Classic Deprecation | bug | — | Deprecated Projects API (sunset May 2024) causes error |
| 1694 | Language metrics fails to query data and generate blank SVG | bug | — | Starting Aug 2024, language metrics return blank SVG |
| 1674 | Explicit Music should have [E] instead of complete "explicit" | feature | — | Display issue with explicit music label |
| 1672 | stale bot closing PRs is too harsh when project inactive | ci | — | Dependabot-style stale bot closes valid PRs |
| 1653 | PageSpeed Insight throws Unexpected error in github actions | bug | — | Cannot destructure `score` from undefined `categories` |
| 1644 | Anilist API error 404 | bug | archived | User not found in AniList |
| 1643 | Status complete (archived duplicate) | bug | archived | Duplicate of 1644 |
| 1629 | Inaccurate results regarding certain metrics (commit count) | bug | — | Commit count inaccurate; PRs reflect removed org membership |
| 1598 | Broken - Local Setup for Development | docs | — | puppeteer/install.js path issue during npm install |
| 1587 | repositories_skipped with pattern matching not working | bug | — | Pattern matching for repo skip doesn't filter correctly |
| 1581 | Error "Bad Credentials" when running github action | bug | archived | Token scope or validity issue |
| 1578 | plugin_languages_colors not working properly for go lang | bug | — | Go language color not applying with `github` preset |
| 1576 | Support limiting available plugins and options for deployed instances | feature | 🆕 v4 | Need feature allowlist for self-hosted deployments |
| 1575 | Route the `dev` environment variable in preview environment | feature | 🆕 v4 | Environment variable routing for preview |
| 1574 | Support markdown and insights outputs | feature | 🆕 v4, ✨ metrics insights | Markdown template not implemented |
| 1573 | Umbrella issue for metrics v4 | other | 🆕 v4 | Large tracking issue for v4 development |
| 1572 | Support mobile devices on web version | feature | 🆕 v4, 📊 metrics embed (web) | Mobile menu not implemented |
| 1571 | Patch Deno.test permissions for --allow-run | bug | 🆕 v4, ↩️ external issue | Deno test permission issue |
| 1565 | Color language used not show | bug | — | Language color display issue |
| 1558 | Insufficient token scopes | bug | — | Traffic plugin requires repo token scope |
| 1519 | Language activity in the habits plugin not appearing | bug | — | Habits plugin doesn't show language activity |
| 1518 | Some content (achievements) not loading - Unexpected error | bug | archived | Achievements stuck at "Fetching..." |
| 1516 | metrics.lecoq.io website 502 error | other | archived | Public instance downtime |
| 1488 | Recently used languages section doesn't display anything | bug | — | Languages plugin recent-activity broken since Sep 2022 |
| 1480 | Lines plugin not working correctly | bug | archived | Wrapping issue leaving whitespace |
| 1479 | Achievements metrics shows (Unexpected Error) | bug | archived | Achievement rendering error |
| 1398 | feat(plugins/leetcode): support skills filters | feature | 🧧 feature request, 🗳️ plugin leetcode | Add skill filter to leetcode |
| 1392 | Invalid SSL certificate | other | archived | Public instance SSL issue |
| 1371 | fix(plugins/notable): fails when org uses SAML | bug | 🎩 plugin notable | Notable plugin fails with SAML-protected orgs |

## Issue Categories

### 🐛 Bugs (18 issues)
- **High Priority**: 1706 (Manager achievement), 1653 (PageSpeed), 1694 (Languages blank), 1371 (SAML org)
- **Medium Priority**: 1587 (repositories_skipped), 1578 (languages_colors), 1558 (token scopes), 1488 (recent languages)
- **Low Priority**: 1644 (Anilist), 1629 (commit count), 1565 (color display)

### ✨ Features (5 issues)
- 1576 (plugin allowlist), 1575 (env var routing), 1574 (markdown output), 1572 (mobile), 1398 (leetcode filters)

### 📚 Docs (1 issue)
- 1598 (local setup broken)

### 🔧 CI (1 issue)
- 1672 (stale bot too aggressive)

### 🆕 v4 (4 issues)
- 1573 (umbrella), 1576, 1575, 1574, 1572, 1571

## Top 3 Issues with Clear Problem Statements

### Issue #1706 — "Manager" Achievement Broken
- **Problem**: The "Manager" achievement in `plugins/achievements/list/users.mjs` uses the deprecated Projects (classic) API which was sunset on May 23, 2024
- **Error**: `NOT_FOUND: Projects (classic) is being deprecated`
- **Root Cause**: Line 58-59 queries `user.projects` which maps to classic projects API
- **Fix**: Switch to Projects V2 API (`user.projectsV2`)
- **Risk**: Low — single plugin, localized change
- **Size**: Small

### Issue #1653 — PageSpeed Insight throws Unexpected error
- **Problem**: Pagespeed plugin crashes when API response lacks expected `lighthouseResult.categories` structure
- **Error**: `Cannot destructure property 'score' of 'request.data.lighthouseResult.categories[category]' as it is undefined`
- **Root Cause**: Line 35 in `source/plugins/pagespeed/index.mjs` assumes all categories exist
- **Fix**: Add defensive check for undefined categories before destructuring
- **Risk**: Low — only affects pagespeed plugin, error is caught and displayed
- **Size**: Small (3-5 lines)

### Issue #1488 — Recently used languages section doesn't display anything
- **Problem**: `plugin_languages_sections: recently-used` produces empty results
- **Symptoms**: Languages don't update even when pushing to repos
- **Root Cause**: Likely in `source/plugins/languages/index.mjs` — recent activity parsing issue
- **Fix**: Investigate how `recent` algorithm filters and sorts language data
- **Risk**: Medium — could affect language calculation overall
- **Size**: Medium (needs debugging first)