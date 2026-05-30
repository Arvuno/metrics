# 06_SELECTED_5_PR_PLAN.md — Top 5 PR Plan

## Selection Rationale

Based on issue triage and quality audit, the following criteria were used:
1. **Clear problem statement** — Issue has reproducible steps
2. **Low risk** — Single plugin or documentation change
3. **High impact** — Fixes common user-facing errors
4. **Size** — Small to medium (quick wins)
5. **Mergeability** — No breaking changes

## Top 5 Selected PRs

| Rank | ID | Title | Type | Risk | Size | Rationale |
|------|----|-------|------|------|------|-----------|
| 1 | C-01 | Fix pagespeed plugin null check | bug | Low | Small | Fixes crash on line 35, clear 1-line fix |
| 2 | C-02 | Update Manager achievement to Projects V2 | bug | Low | Small | Fixes deprecated API, impacts all users |
| 3 | C-03 | Add example .metrics.yml to root | docs | Low | Small | Improves DX, reduces support burden |
| 4 | C-04 | Fix repositories_skipped pattern matching | bug | Medium | Medium | High user impact, common complaint |
| 5 | C-05 | Add error handling docs to README | docs | Low | Small | Reduces support volume, easy win |

---

## PR #1: Fix pagespeed plugin null check on categories

**Candidate ID**: C-01
**Title**: fix(pagespeed): add null check for missing lighthouse categories
**Type**: bug fix
**Risk**: Low
**Size**: Small

### Problem
Line 35 in `source/plugins/pagespeed/index.mjs` destructures `score` and `title` from `request.data.lighthouseResult.categories[category]` without checking if the category exists, causing crashes reported in Issue #1653.

### Implementation
```javascript
// Before (line 34-36):
for (const category of categories) {
  const {score, title} = request.data.lighthouseResult.categories[category]
  result.scores.push({score, title})
}

// After:
for (const category of categories) {
  const categoryData = request.data.lighthouseResult.categories[category]
  if (!categoryData) {
    console.debug(`metrics/compute/${login}/plugins > pagespeed > ${category} category not found`)
    continue
  }
  const {score, title} = categoryData
  result.scores.push({score, title})
}
```

### Files
- `source/plugins/pagespeed/index.mjs`

### Testing
- Manual: Run with various PageSpeed URL
- Automated: Add mock test case for missing category

### Notes
- Also add URL validation before API call (Issue #1653 mentions URL may be malformed)
- Consider adding `verify` parameter handling if PageSpeed returns verification errors

---

## PR #2: Update Manager achievement to use Projects V2 API

**Candidate ID**: C-02
**Title**: fix(achievements): update Manager achievement to use Projects V2 API
**Type**: bug fix
**Risk**: Low
**Size**: Small

### Problem
The "Manager" achievement queries `user.projects` which uses the deprecated Projects (classic) API sunset on May 23, 2024. Users with only Projects V2 see errors (Issue #1706).

### Implementation
1. Update GraphQL query in `achievements.graphql` or inline query
2. Change from `user.projects` to `user.projectsV2`
3. Update totalCount and nodes accessors
4. Handle case where user has no projects

### Files
- `source/plugins/achievements/list/users.mjs` (Manager block, lines 56-70)
- `source/plugins/achievements/queries/` (if queries are separate)

### GraphQL Change
```graphql
# Old (deprecated)
user {
  projects(first: 1, orderBy: { direction: DESC, field: CREATED_AT }) {
    totalCount
    nodes { createdAt }
  }
}

# New (Projects V2)
user {
  projectsV2(first: 1, orderBy: { direction: DESC, field: CREATED_AT }) {
    totalCount
    nodes { createdAt }
  }
}
```

### Testing
- Manual: Run with user that has Projects V2
- Verify Manager achievement still works for classic projects users

---

## PR #3: Add example .metrics.yml to repository root

**Candidate ID**: C-03
**Title**: docs: add example .metrics.yml configuration file
**Type**: documentation
**Risk**: Low
**Size**: Small

### Problem
No example configuration file exists at repository root. Users must piece together configuration from plugin READMEs.

### Implementation
1. Create `.github/readme/examples/metrics.yml`
2. Include common configuration options with comments
3. Link from main README setup section

### Example Content
```yaml
# Example metrics configuration
# Copy to your .github/workflows/metrics.yml

name: Metrics
on:
  schedule: [{ cron: '0 0 * * *' }]
  workflow_dispatch:
  push: { branches: ['main'] }

jobs:
  github-metrics:
    runs-on: ubuntu-latest
    permissions:
      contents: write
    steps:
      - uses: lowlighter/metrics@latest
        with:
          token: ${{ secrets.METRICS_TOKEN }}
          user: YOUR_USERNAME
          template: classic
          base: header, activity, community, repositories, metadata
          config_timezone: America/New_York
          # Add plugins below
          plugin_languages: yes
          plugin_languages_sections: most-used, recently-used
```

### Files
- `.github/readme/examples/metrics.yml` (new)

### Notes
- Reference from README setup section
- Consider adding `template:` parameter examples

---

## PR #4: Fix repositories_skipped pattern matching

**Candidate ID**: C-04
**Title**: fix(base): correct repositories_skipped pattern matching
**Type**: bug fix
**Risk**: Medium
**Size**: Medium

### Problem
Issue #1587 reports that `repositories_skipped` patterns don't correctly filter organization repositories. Patterns like `HWR-Ubungen/*` don't match.

### Implementation
1. Find where `repositories_skipped` is processed
2. Add debug logging for pattern matching
3. Fix pattern matching to handle org/repo format
4. Ensure affiliations filter is applied before skip filter

### Files (investigative)
- `source/app/metrics/` — likely in core engine
- `source/plugins/base/` — base plugin handles repositories

### Testing
- Create test with organization repos
- Verify patterns match correctly
- Test with `repositories_affiliations: owner`

### Notes
- May be related to how GraphQL queries filter repositories
- Consider adding unit tests for pattern matching

---

## PR #5: Add error handling documentation to README

**Candidate ID**: C-05
**Title**: docs: add troubleshooting section with common errors
**Type**: documentation
**Risk**: Low
**Size**: Small

### Problem
Users encounter errors like "Bad Credentials" and "Insufficient token scopes" with no documentation on how to resolve them.

### Implementation
1. Create `.github/readme/partials/documentation/troubleshooting.md`
2. Add section to README referencing it
3. Document common errors and solutions

### Content Structure
```markdown
## Troubleshooting

### Bad Credentials
**Error**: `HttpError: Bad credentials`
**Cause**: GitHub token is invalid or expired
**Solution**:
1. Generate new token at https://github.com/settings/tokens
2. Ensure token has required scopes
3. Verify token is correctly set in secrets

### Insufficient Token Scopes
**Error**: `Insufficient token scopes`
**Cause**: Token missing required permissions
**Solution**: Add `repo` scope for private repository metrics

### Blank SVG Output
**Error**: Metrics render but show no data
**Cause**: API rate limit or token issues
**Solution**: Check GitHub API rate limits with `debug: yes`
```

### Files
- `.github/readme/partials/documentation/troubleshooting.md` (new)
- `README.md` (add link)

### Notes
- Cross-reference with existing issues
- Include debug mode instructions