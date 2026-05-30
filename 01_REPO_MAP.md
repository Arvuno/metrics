# 01_REPO_MAP.md — Metrics Repository Structure

## Repository Overview
- **Name**: lowlighter/metrics
- **Description**: An infographics generator with 40+ plugins and 300+ options to display stats about your GitHub account and render them as SVG, Markdown, PDF or JSON!
- **Version**: 3.35.0-beta
- **License**: MIT
- **Main Entry**: `index.mjs`

## Key Files
| File | Purpose |
|------|---------|
| `README.md` | Main documentation (574 lines) with plugin showcase |
| `package.json` | Dependencies and npm scripts |
| `action.yml` | GitHub Action descriptor (1607 lines, all plugin options) |
| `ARCHITECTURE.md` | Project architecture documentation |
| `settings.example.json` | Web instance settings example |

## Directory Structure

```
/root/hard-pr-1/repos/metrics/
├── source/
│   ├── app/
│   │   ├── action/       # GitHub Action entry point
│   │   ├── metrics/     # Core metrics engine
│   │   └── web/         # Web instance (express server)
│   ├── plugins/         # 42 plugins (see below)
│   └── templates/      # 4 templates + community
├── tests/              # Jest test suite
├── .github/
│   ├── scripts/        # Build, presets, quickstart scripts
│   ├── workflows/      # CI/CD pipelines
│   └── readme/         # Documentation partials
└── Dockerfile
```

## Plugin System (42 plugins)

### Core Plugins
| Plugin | Path | Description |
|--------|------|-------------|
| `base` | `source/plugins/base/` | Base content (header, activity, community, repositories, metadata) |
| `core` | `source/plugins/core/` | Core rendering logic |

### GitHub Plugins (30+)
| Plugin | Path | Description |
|--------|------|-------------|
| `achievements` | `source/plugins/achievements/` | GitHub achievements |
| `activity` | `source/plugins/activity/` | Recent activity |
| `calendar` | `source/plugins/calendar/` | Commit calendar |
| `code` | `source/plugins/code/` | Random code snippet |
| `contributors` | `source/plugins/contributors/` | Repository contributors |
| `discussions` | `source/plugins/discussions/` | GitHub Discussions |
| `followup` | `source/plugins/followup/` | Issues/PR follow-up |
| `gists` | `source/plugins/gists/` | Gists |
| `habits` | `source/plugins/habits/` | Coding habits |
| `introduction` | `source/plugins/introduction/` | User intro |
| `isocalendar` | `source/plugins/isocalendar/` | Isometric commit calendar |
| `languages` | `source/plugins/languages/` | Language activity |
| `licenses` | `source/plugins/licenses/` | Repository licenses |
| `lines` | `source/plugins/lines/` | Lines of code |
| `notable` | `source/plugins/notable/` | Notable contributions |
| `people` | `source/plugins/people/` | Followers/following |
| `projects` | `source/plugins/projects/` | GitHub Projects |
| `reactions` | `source/plugins/reactions/` | Comment reactions |
| `repositories` | `source/plugins/repositories/` | Featured repos |
| `stargazers` | `source/plugins/stargazers/` | Stargazers |
| `stars` | `source/plugins/stars/` | Recently starred |
| `starlists` | `source/plugins/starlists/` | Star lists |
| `topics` | `source/plugins/topics/` | Starred topics |
| `traffic` | `source/plugins/traffic/` | Repo traffic |
| `sponsors` | `source/plugins/sponsors/` | GitHub Sponsors |
| `sponsorships` | `source/plugins/sponsorships/` | Sponsorships |
| `support` | `source/plugins/support/` | Community support (deprecated) |
| `tweets` | `source/plugins/tweets/` | Latest tweets (deprecated) |
| `wakatime` | `source/plugins/wakatime/` | WakaTime |
| `stackoverflow` | `source/plugins/stackoverflow/` | Stack Overflow |
| `leetcode` | `source/plugins/leetcode/` | LeetCode |
| `anilist` | `source/plugins/anilist/` | AniList |
| `music` | `source/plugins/music/` | Music activity |
| `posts` | `source/plugins/posts/` | Recent posts |
| `rss` | `source/plugins/rss/` | RSS feed |
| `pagespeed` | `source/plugins/pagespeed/` | Google PageSpeed |
| `skyline` | `source/plugins/skyline/` | GitHub Skyline |
| `steam` | `source/plugins/steam/` | Steam |
| `community/*` | `source/plugins/community/` | Community plugins |

### Templates (4 main + community)
| Template | Path |
|----------|------|
| `classic` | `source/templates/classic/` |
| `repository` | `source/templates/repository/` |
| `terminal` | `source/templates/terminal/` |
| `markdown` | `source/templates/markdown/` |
| `community` | `source/templates/community/` |

## Architecture Pattern

Each plugin follows a pattern:
- `source/plugins/<name>/index.mjs` — Main plugin entry
- `source/plugins/<name>/metadata.yml` — Plugin metadata
- `source/plugins/<name>/examples.yml` — Workflow examples
- `source/plugins/<name>/queries/` — GraphQL queries
- `source/plugins/<name>/README.md` — Plugin documentation

## Configuration
- **GitHub Action**: `action.yml` (1607 lines) defines all inputs with defaults
- **Config File**: `settings.example.json` for web instance
- **Example Workflow**: No root-level `.metrics.yml` example exists

## Key Dependencies
- `@octokit/rest`, `@octokit/graphql` — GitHub API
- `ejs` — Template rendering
- `puppeteer` — Browser rendering for SVG/PNG
- `d3`, `csso`, `svgo` — Visualization and optimization
- `axios` — HTTP requests