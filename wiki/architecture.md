# Architecture

This page describes the static layout of the repo, the TOC (table of contents) system that drives navigation on docs.tizen.org, and the external build/deploy targets the repo feeds into.

## Repository layout

```
tizen-docs/
├── README.md               Workflow + label rules + Jenkins build badges
├── CONTRIBUTING.md         Contribution flow, repo structure, file-name rules
├── LICENSE-CODE            BSD-3-Clause for code samples
├── content-license.md      CC-BY 3.0 for documentation content
├── .github/
│   ├── CODEOWNERS          Default reviewers for the whole repo
│   └── pull_request_template.md
├── .gitignore              Ignores _config.yml, _themes/, *.pdf
├── docs/                   ALL published markdown lives here
│   ├── menu.yaml           GNB (top nav) entries
│   ├── glossary.md         Public-facing Tizen glossary
│   ├── get-started.md      Public-facing entry point
│   ├── trademarks.md
│   ├── images/             Top-level shared images
│   ├── platform/           Category 1 — "Open Source Project"
│   ├── application/        Category 2 — Application development
│   ├── iot/                Category 3 — IoT extension SDK
│   ├── blog/               Category 4 — Blog (not yet on GNB)
│   ├── partners/           Tizen Specialist / IoT partners
│   └── extensions/         tizenx (extension SDK)
├── styleguide/             Markdown style + naming rules + template
│   ├── style.md
│   ├── custom-style.md     Tizen custom markdown extensions
│   ├── naming-rules.md
│   ├── template-guide.md
│   └── sample1.md
└── reviewguide/            Review checklists + staging/live operating notes
    ├── review_points_guide.md
    ├── review_points_release_note.md
    ├── review_points_web_api.md
    ├── stg_build.md
    └── update_docs_tizen_org.md
```

Everything inside `docs/` is published. Everything outside `docs/` (README, CONTRIBUTING, styleguide, reviewguide) is for contributors.

## Categories

`CONTRIBUTING.md` defines four GNB categories:

| Category | Folder | TOC file | Note |
| -------- | ------ | -------- | ---- |
| Open Source Project | `docs/platform/` | `docs/platform/toc_all.md` | Linked from `menu.yaml` as "Platform" |
| Application | `docs/application/` | `docs/application/toc_all.md` | Linked externally to samsungtizenos.com (see `menu.yaml`) |
| IoT | `docs/iot/` | `docs/iot/toc_all.md` | |
| Blog | `docs/blog/` | `docs/blog/toc_all.md` | "Not shown on GNB yet" per CONTRIBUTING.md |

Two further folders exist that are not in the four-category list:

- `docs/partners/` with its own `toc_all.md` (Tizen Specialist, IoT Partners).
- `docs/extensions/tizenx/` with `api/toc.md` and `guides/toc.md` (extension SDK).

## TOC system

Navigation on docs.tizen.org has two levels:

- **GNB (Global Navigation Bar)** — top-of-page menu. Defined in `docs/menu.yaml`.
- **LNB (Left Navigation Bar)** — per-category sidebar. Defined per category in `toc_all.md`.

### GNB — `docs/menu.yaml`

A short YAML list. Each entry is `name` + `href`. An optional `external: true` flag tells the renderer to link out instead of staying on docs.tizen.org. As of writing it carries two active items (Platform, Application) and two commented-out items (Partners, Download).

### LNB — `toc_all.md`

A markdown file where heading depth (`#`, `##`, `###`, …) maps to TOC depth, and links inside the headings point to the actual article files. Example fragment from `docs/platform/toc_all.md`:

```markdown
# What is Tizen?
## [Overview](/platform/what-is-tizen/overview.md)
## Devices
### [Products](/platform/what-is-tizen/devices/products.md)
```

Rules (from CONTRIBUTING.md):

- **Heading tags only** (`#`, `##`, `###`, `####`, …) for TOC items. No bullet lists.
- One TOC file per top-level category.
- Each section folder has an `index.md` as its main entry.
- Static assets live under a sibling `media/` folder.

### Inventory of TOC files

```
docs/blog/toc_all.md
docs/platform/toc_all.md
docs/iot/toc_all.md
docs/partners/toc_all.md
docs/application/toc_all.md
docs/application/toc_all_new.md         draft of the next-generation application TOC
docs/application/toc_vs-ext_native.md
docs/application/toc_vs-ext_dotnet.md
docs/application/toc_vs-ext_web.md
docs/application/toc_vscode_native.md
docs/application/toc_vscode_dotnet.md
docs/application/toc_vscode_web.md
docs/platform/HAL/toc.md
docs/platform/HAL/api/1.0/toc.md
docs/extensions/tizenx/api/toc.md
docs/extensions/tizenx/guides/toc.md
docs/application/flutter/toc.md
```

The `application/` folder carries six extra `toc_vs-ext_*` / `toc_vscode_*` variants. These are subset TOCs scoped to a specific IDE extension (Visual Studio extension vs. VS Code extension) crossed with a runtime (native, .NET, web).

## File-naming rules

From CONTRIBUTING.md:

- Lowercase letters, digits, hyphens only.
- No spaces or punctuation.
- Action verbs, no `-ing` forms.
- No stop words (a, and, the, in, or…).
- Must be `.md`.
- Keep names short — they appear in URLs.

Naming for *terms* (product names, feature names) is governed by `styleguide/naming-rules.md`: Brand + Feature + Type + Suffix.

## Custom markdown

`styleguide/custom-style.md` documents non-standard markdown understood by the renderer:

- `> [!NOTE]`, `> [!TIP]`, `> [!IMPORTANT]`, `> [!CAUTION]`, `> [!WARNING]` — alert blocks.
- `[link](url){:target="_blank"}` — opens external links in a new window.

## Build and deploy targets

The README.md exposes two Jenkins jobs:

- `STG_docs_PR_builder` (master build badge) on `jenkins-docs.stg.tizen.org` — runs on each PR. Result is published at `docs.stage.tizen.org/staging/{PR#}/` per CONTRIBUTING.md.
- `PRD_docs` (live build badge) — publishes to `docs.tizen.org`.

There is **no in-repo build script** (no Jenkinsfile, no GitHub Actions workflow, no package manifest). Build configuration lives on the Jenkins server, outside this repo. `.gitignore` ignores `_config.yml` and `_themes/`, suggesting the renderer expects those to be supplied at build time rather than committed.

## Branch model

From `reviewguide/review_points_guide.md` and `reviewguide/update_docs_tizen_org.md`:

- **master** — latest published Tizen platform docs. PRs target this branch.
- **live** — what docs.tizen.org renders. Updated by merging master → live on a cadence.
- **tizen_<version>_prepare** — staging area for the next platform version; merged into master when that version ships.
- **dev** — used for in-progress collaborative work (this wiki branch was cut from `dev`).

See [data-pipeline](data-pipeline.md) for the full flow.
