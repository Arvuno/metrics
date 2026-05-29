# 04_QUALITY_AUDIT.md — Metrics Quality Audit

## 1. README.md Gaps

### Length and Structure
- **Total**: 574 lines, 34,873 bytes
- **Plugin showcase**: Heavy HTML table with screenshots (good for marketing, hard to navigate)
- **Documentation section**: Starts at line 446, relatively short

### Missing Sections
1. **No example `.metrics.yml` at root** — Users must search for workflow examples
2. **No troubleshooting section** — Common errors like "Bad Credentials" not documented
3. **No migration guide** — v3→v4 changes not documented
4. **No keyboard shortcuts** — For web UI
5. **No API reference** — Web instance API endpoints not documented

### Plugin Documentation Quality
- Each plugin has `README.md`, `metadata.yml`, `examples.yml`
- But `examples.yml` format is not validated or enforced
- No shared schema for plugin configuration documentation

## 2. Config Validation Gaps

### action.yml (1607 lines)
- All inputs have `description` but minimal validation
- No JSON Schema for `.metrics.yml` validation
- Example at root would help users

### Error Handling Issues Found

#### Critical: Unchecked Destructuring
**File**: `source/plugins/pagespeed/index.mjs:35`
```javascript
const {score, title} = request.data.lighthouseResult.categories[category]
```
- No check if `categories[category]` exists before destructuring
- Causes crash reported in Issue #1653

#### Critical: Manager Achievement Deprecated API
**File**: `source/plugins/achievements/list/users.mjs:58-59`
```javascript
const value = user.projects.totalCount
const unlock = user.projects.nodes?.shift()
```
- Uses deprecated Projects (classic) API
- Fails for users with only Projects V2

#### Error Pattern: Generic "Unexpected error"
Multiple plugins use generic error messages:
```javascript
throw { error: { message: "Unexpected error" } }
```
Makes debugging difficult for users.

#### Missing Validation
- `repositories_skipped` patterns not validated before use
- `token` scopes not checked before API calls
- `url` for pagespeed not validated before API call

## 3. Docs Gaps

### Critical Missing Docs
1. **Error code reference** — What do "Bad Credentials", "Insufficient token scopes" mean?
2. **Token permission matrix** — Which plugins need which scopes
3. **Rate limit handling** — How to avoid hitting GitHub API limits
4. **Self-hosting troubleshooting** — Common deployment issues

### Local Setup Docs (Issue #1598)
- File: `.github/readme/partials/documentation/setup/local.md`
- Problem: `puppeteer/install.js` → should be `.mjs`
- Not fixed or documented as known issue

### Contributing Docs
- `CONTRIBUTING.md` exists but short (3,259 bytes)
- No commit message convention
- No PR template linked
- Testing guidelines vague: "let GitHub Actions do the testing"

## 4. Missing Error Handling

### Pattern Analysis
Searched for `throw new Error` in source — found 20+ instances mostly in community plugins.

#### Common Issues
1. **No try/catch wrappers** around async API calls in some plugins
2. **No null checks** before property access
3. **No graceful degradation** — one plugin failure kills entire metrics render

### Specific Gaps

#### pagespeed/index.mjs
```javascript
// Line 35: No null check
const {score, title} = request.data.lighthouseResult.categories[category]
```

#### achievements/list/users.mjs
```javascript
// Line 58: Uses deprecated API
const value = user.projects.totalCount
```

#### traffic/index.mjs
```javascript
// Line 27: Throws raw error without context
throw new Error(promised[0].reason.message)
```

## 5. Testing Gaps

### Test Coverage
- `tests/presets.test.js` — Preset validation
- `tests/metrics.test.js` — Metrics rendering
- `tests/mocks/index.mjs` — Mock data

### Missing Tests
- No unit tests for individual plugins
- No integration tests for token scope validation
- No snapshot tests for SVG output structure
- No tests for error conditions (404, 401, 500)

## 6. Security Gaps

### Token Handling
- Tokens passed as input, stored in logs if debug mode
- No mention of token security in docs
- `committer_token` default `${{ github.token }}` — good default but scope unclear

### Input Validation
- No sanitization of user inputs in web instance
- `repositories_skipped` could contain paths like `../../../etc/passwd`?

## Summary of Quality Issues

| Category | Count | Severity |
|----------|-------|----------|
| README gaps | 5 | Medium |
| Config validation | 3 | High |
| Error handling | 8 | High |
| Docs gaps | 4 | Medium |
| Testing gaps | 4 | Medium |
| Security gaps | 2 | Medium |