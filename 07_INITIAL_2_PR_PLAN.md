# 07_INITIAL_2_PR_PLAN.md — First 2 PRs to Open

## Strategy

Start with the two smallest, highest-confidence fixes:
1. **C-01**: Pagespeed null check — trivial fix, immediate value
2. **C-02**: Manager achievement API update — clear migration path

These establish momentum and demonstrate capability to fix bugs.

---

## PR #1: fix(pagespeed): add null check for missing lighthouse categories

**Branch Name**: `fix/pagespeed-null-check-categories`
**Target**: `source/plugins/pagespeed/index.mjs`
**Issue**: #1653

### Files to Modify
```
source/plugins/pagespeed/index.mjs
```

### Current Code (lines 31-38)
```javascript
//Perform audit
console.debug(`metrics/compute/${login}/plugins > pagespeed > performing audit ${categories_required}`)
const request = await imports.axios.get(`https://www.googleapis.com/pagespeedonline/v5/runPagespeed?url=${url}${categories_required}${token ? `&key=${token}` : ""}`)
for (const category of categories) {
  const {score, title} = request.data.lighthouseResult.categories[category]
  result.scores.push({score, title})
  console.debug(`metrics/compute/${login}/plugins > pagespeed > performed audit ${category} (status code ${request.status})`)
}
```

### Implementation Notes

1. **Add null check before destructuring**:
   ```javascript
   for (const category of categories) {
     const categoryData = request.data.lighthouseResult.categories[category]
     if (!categoryData) {
       console.debug(`metrics/compute/${login}/plugins > pagespeed > ${category} category not available`)
       continue
     }
     const {score, title} = categoryData
     result.scores.push({score, title})
     console.debug(`metrics/compute/${login}/plugins > pagespeed > performed audit ${category} (status code ${request.status})`)
   }
   ```

2. **Also add URL validation** (before line 15):
   ```javascript
   if (!url)
     throw { error: { message: "Website URL is not set" } }
   // Add URL format validation
   try {
     new imports.url.URL(url.startsWith('http') ? url : `https://${url}`)
   }
   catch {
     throw { error: { message: "Invalid URL format" } }
   }
   ```

3. **Consider adding try/catch wrapper** around the API call

### Testing
- Run with `plugin_pagespeed_url: invalid-url` — should show friendly error
- Run with valid URL but API returns partial data — should skip missing categories

### Commit Message
```
fix(pagespeed): add null check for missing lighthouse categories

Fixes crash when PageSpeed API returns incomplete data for some
categories. Issue #1653 reported:
  Cannot destructure property 'score' of
  'request.data.lighthouseResult.categories[category]' as it is undefined

Also adds URL validation before making API call.
```

---

## PR #2: fix(achievements): update Manager achievement to use Projects V2 API

**Branch Name**: `fix/achievements-manager-projects-v2`
**Target**: `source/plugins/achievements/list/users.mjs`
**Issue**: #1706

### Files to Modify
```
source/plugins/achievements/list/users.mjs
source/plugins/achievements/queries/achievements.graphql (if exists)
```

### Current Code (lines 56-70)
```javascript
//Manager
{
  const value = user.projects.totalCount
  const unlock = user.projects.nodes?.shift()

  list.push({
    title: "Manager",
    text: `Created ${value} user project${imports.s(value)}`,
    icon: '...',
    ...rank(value, [1, 2, 3, 4, 5]),
    value,
    unlock: new Date(unlock?.createdAt),
  })
}
```

### Implementation Notes

1. **Check if queries are in separate file**:
   ```bash
   ls source/plugins/achievements/queries/
   ```

2. **If queries are inline** (most likely), update the Manager block:
   ```javascript
   //Manager
   {
     // Use Projects V2 API (Projects classic deprecated May 2024)
     const value = user.projectsV2.totalCount
     const unlock = user.projectsV2.nodes?.shift()

     list.push({
       title: "Manager",
       text: `Created ${value} user project${imports.s(value)}`,
       icon: '...',
       ...rank(value, [1, 2, 3, 4, 5]),
       value,
       unlock: new Date(unlock?.createdAt),
     })
   }
   ```

3. **GraphQL query structure** — need to find where `projects` is queried:
   - Look for `projects(first:` in queries
   - Change to `projectsV2(first:`

### Testing
- Create test user with only Projects V2
- Verify Manager achievement works
- Verify existing tests still pass

### Commit Message
```
fix(achievements): update Manager achievement to use Projects V2 API

The "Manager" achievement was using the deprecated Projects (classic) API
which was sunset on May 23, 2024. This caused errors for users who only
have Projects V2.

Update to use user.projectsV2 GraphQL API:
- projects.totalCount → projectsV2.totalCount
- projects.nodes → projectsV2.nodes

Fixes #1706
```

---

## Ready to Open

Both PRs are:
- ✅ Single-file changes
- ✅ Clear problem statements
- ✅ Low risk (bug fixes only)
- ✅ Well-tested conceptually
- ✅ Have clear commit messages

### Next Steps After Merging
1. Monitor for similar deprecated API issues in other plugins
2. Consider adding test coverage for these edge cases
3. Update CHANGELOG if project maintains one