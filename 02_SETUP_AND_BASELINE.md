# 02_SETUP_AND_BASELINE.md — Metrics Setup and Baseline

## Environment

- **Node.js**: v22.22.2
- **npm**: 10.9.7
- **OS**: Linux (6.8.0-71-generic)
- **Repo Path**: /root/hard-pr-1/repos/metrics

## Installation Test

### Step 1: Clone and Install
```bash
git clone https://github.com/lowlighter/metrics.git
cd metrics/
npm install
```

### Issue Observed (from Issue #1598)
- **Problem**: postinstall script fails because `puppeteer/install.js` has been renamed to `puppeteer/install.mjs` in newer versions
- **Error**: `Cannot find module '/metrics/node_modules/puppeteer/install.js'`
- **Note**: The main repo may need to update the postinstall script path or this is an npm version compatibility issue

### Current State
- `package-lock.json` exists (353KB) — dependencies are locked
- `node_modules/` may need reinstallation

## Run Test

### Available npm Scripts
```json
{
  "start": "node source/app/web/index.mjs",
  "test": "jest --runInBand",
  "test-contrib": "jest --runInBand ci.test.js --noStackTrace",
  "test-presets": "jest --runInBand presets.test.js --noStackTrace",
  "test-metrics": "jest --runInBand metrics.test.js",
  "build": "node .github/scripts/build.mjs",
  "presets": "node .github/scripts/presets_examples.mjs",
  "quickstart": "node .github/scripts/quickstart/index.mjs",
  "preview": "node .github/scripts/preview.mjs",
  "linter": "eslint source/**/*.mjs --quiet",
  "dev": "nodemon source/app/web/index.mjs -e mjs,css,ejs,json",
  "indepth": "node source/plugins/languages/analyzers.mjs"
}
```

### Web Instance
- Entry: `npm start` → `source/app/web/index.mjs`
- Port: Configured in `settings.json` (defaults in `settings.example.json`)
- Templates served from `source/templates/`

### GitHub Action
- Entry: `source/app/action/index.mjs`
- Config: `action.yml` (1607 lines with all inputs)

## Baseline Behavior

### Architecture Flow
1. **Input**: GitHub username/token or web query params
2. **Validation**: Config parsed from query or action inputs
3. **Plugin Execution**: Each plugin runs via `source/plugins/core/index.mjs`
4. **Rendering**: EJS templates + CSS → SVG via puppeteer
5. **Output**: SVG, PNG, PDF, or JSON

### Plugin Execution Pattern
```javascript
// source/plugins/core/index.mjs
export default async function metrics({plugin}) {
  // Each plugin returns { name, result }
  // Result can be data or { error: { message, instance } }
}
```

### Error Handling Pattern
- Plugins use `try/catch` and return `{ error: { message, instance } }`
- Core engine catches and logs via `console.debug`
- Action retries up to 3 times

### Debug Output Format
```
metrics/compute/{login}/plugins > {plugin} > {action}
```

## Known Baseline Issues

### 1. Puppeteer Postinstall Failure
- **File**: `package.json` line 18
- **Command**: `node node_modules/puppeteer/install.js`
- **Issue**: Path changed to `.mjs` in newer puppeteer

### 2. Deprecated Node APIs
- **Warning**: `abab@2.0.6` uses deprecated `atob()/btoa()`
- **Warning**: `domexception@4.0.0` deprecated
- **Warning**: `vue@2.7.16` reached EOL

### 3. Projects Classic API Deprecated
- **File**: `source/plugins/achievements/list/users.mjs` (Manager achievement)
- **Issue**: Uses deprecated Projects (classic) API (sunset May 23, 2024)
- **Affected**: Issue #1706

## Test Suite

### Running Tests
```bash
npm test          # All tests
npm run test-presets  # Preset validation
npm run test-metrics  # Metrics rendering
```

### Mock System
- Located in `tests/mocks/index.mjs`
- Uses JavaScript Proxies + Faker.js
- Mocks GitHub API (REST + GraphQL)
- Mocks external services

## CI/CD

- **Workflow**: `.github/workflows/ci.yml`
- **Badge**: `https://github.com/lowlighter/metrics/actions/workflows/ci.yml/badge.svg`
- **Tests**: Run on Node versions and on PRs