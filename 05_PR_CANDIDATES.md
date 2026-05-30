# 05_PR_CANDIDATES.md — PR Candidates

## Candidate List (10 PRs)

| ID | Title | Type | Linked Issue | Source | Risk | Size | Mergeability | Selected |
|----|-------|------|--------------|--------|------|------|--------------|----------|
| C-01 | Fix pagespeed plugin null check on categories | bug | #1653 | Issues | Low | Small | High | ✓ |
| C-02 | Update Manager achievement to use Projects V2 API | bug | #1706 | Issues | Low | Small | High | ✓ |
| C-03 | Add example .metrics.yml to repository root | docs | — | Quality Audit | Low | Small | High | ✓ |
| C-04 | Fix repositories_skipped pattern matching | bug | #1587 | Issues | Medium | Medium | High | ✓ |
| C-05 | Add error handling documentation to README | docs | — | Quality Audit | Low | Small | High | — |
| C-06 | Add token scope documentation matrix | docs | #1558, #1581 | Issues | Low | Small | High | — |
| C-07 | Fix stale bot configuration for PRs | ci | #1672 | Issues | Low | Small | High | — |
| C-08 | Add validation for pagespeed URL input | bug | #1653 | Quality Audit | Low | Small | High | — |
| C-09 | Fix languages plugin recently-used section | bug | #1488 | Issues | Medium | Medium | Medium | — |
| C-10 | Add config validation schema for .metrics.yml | feature | — | Quality Audit | Medium | Large | Medium | — |

---

## Candidate Details

### C-01: Fix pagespeed plugin null check on categories
- **Type**: bug
- **Linked Issue**: #1653
- **Source**: Issues
- **Risk**: Low — isolated change to one plugin
- **Size**: Small (3-5 lines)
- **Mergeability**: High — single file, clear fix
- **Selected**: ✓

**Files**:
- `source/plugins/pagespeed/index.mjs`

**Implementation**:
```javascript
// Add null check before line 35
const categoryData = request.data.lighthouseResult.categories[category];
if (!categoryData) {
  console.debug(`metrics/compute/${login}/plugins > pagespeed > ${category} category not found`);
  continue;
}
const {score, title} = categoryData;
```

---

### C-02: Update Manager achievement to use Projects V2 API
- **Type**: bug
- **Linked Issue**: #1706
- **Source**: Issues
- **Risk**: Low — single achievement plugin
- **Size**: Small (5-10 lines)
- **Mergeability**: High — known fix, deprecated API
- **Selected**: ✓

**Files**:
- `source/plugins/achievements/list/users.mjs`
- `source/plugins/achievements/queries/achievements.graphql` (if exists)

**Implementation**:
- Query `user.projectsV2` instead of `user.projects`
- Update Manager achievement logic to use new API
- Handle users with no projects gracefully

---

### C-03: Add example .metrics.yml to repository root
- **Type**: docs
- **Linked Issue**: —
- **Source**: Quality Audit
- **Risk**: Low — documentation only
- **Size**: Small (1 file, ~30 lines)
- **Mergeability**: High — no code change
- **Selected**: ✓

**Files**:
- `.github/readme/examples/metrics.yml` (new)
- Update `README.md` to reference it

**Implementation**:
- Create `.github/readme/examples/metrics.yml` with common configuration
- Link from README setup section
- Include comments explaining key options

---

### C-04: Fix repositories_skipped pattern matching
- **Type**: bug
- **Linked Issue**: #1587
- **Source**: Issues
- **Risk**: Medium — pattern matching logic across repos
- **Size**: Medium (needs investigation first)
- **Mergeability**: High — bug fix
- **Selected**: ✓

**Files**:
- Likely in `source/app/metrics/` or `source/plugins/base/`

**Implementation**:
- Debug how `repositories_skipped` patterns are applied
- Fix pattern matching to correctly filter organization repos
- Add tests for pattern matching edge cases

---

### C-05: Add error handling documentation to README
- **Type**: docs
- **Linked Issue**: —
- **Source**: Quality Audit
- **Risk**: Low — documentation
- **Size**: Small (1-2 sections)
- **Mergeability**: High
- **Selected**: —

**Files**:
- `README.md`
- `.github/readme/partials/documentation/` (new partial)

**Implementation**:
- Add "Troubleshooting" section to README
- Document "Bad Credentials", "Insufficient token scopes", etc.
- Include common solutions

---

### C-06: Add token scope documentation matrix
- **Type**: docs
- **Linked Issue**: #1558, #1581
- **Source**: Issues
- **Risk**: Low
- **Size**: Small
- **Mergeability**: High
- **Selected**: —

**Files**:
- `README.md` or docs partial

**Implementation**:
- Create table showing which plugins require which token scopes
- Document minimum required scopes for basic operation
- Note which plugins need `repo` scope

---

### C-07: Fix stale bot configuration for PRs
- **Type**: ci
- **Linked Issue**: #1672
- **Source**: Issues
- **Risk**: Low
- **Size**: Small
- **Mergeability**: High
- **Selected**: —

**Files**:
- `.github/workflows/close-issues.yml` or similar stale config

**Implementation**:
- Adjust stale bot settings to be less aggressive
- Add exemption for PRs
- Add message explaining project inactivity vs PR staleness

---

### C-08: Add validation for pagespeed URL input
- **Type**: bug
- **Linked Issue**: #1653
- **Source**: Quality Audit
- **Risk**: Low
- **Size**: Small
- **Mergeability**: High
- **Selected**: —

**Files**:
- `source/plugins/pagespeed/index.mjs`

**Implementation**:
- Validate URL format before API call
- Check URL is reachable before pagespeed API
- Improve error message for invalid URLs

---

### C-09: Fix languages plugin recently-used section
- **Type**: bug
- **Linked Issue**: #1488
- **Source**: Issues
- **Risk**: Medium
- **Size**: Medium
- **Mergeability**: Medium — complex logic
- **Selected**: —

**Files**:
- `source/plugins/languages/index.mjs`
- `source/plugins/languages/analyzers.mjs`

**Implementation**:
- Investigate recent activity parsing
- Fix filtering/sorting of language data
- Add debug output for troubleshooting

---

### C-10: Add config validation schema for .metrics.yml
- **Type**: feature
- **Linked Issue**: —
- **Source**: Quality Audit
- **Risk**: Medium
- **Size**: Large
- **Mergeability**: Medium — requires design
- **Selected**: —

**Files**:
- Schema file (new)
- Potentially validation code in `source/app/metrics/`

**Implementation**:
- Create JSON Schema for .metrics.yml configuration
- Add validation on web instance startup
- Document schema in docs