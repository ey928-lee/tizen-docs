# Architecture decision records

Each entry records a decision that shapes how the repository works, the alternatives that were considered (or are obviously available), and the source in the repo where the decision is visible. Decisions without a clear in-repo source are explicitly marked as inferred.

## ADR-001: Documentation lives in plain markdown, not in a CMS

**Decision**: All published content is markdown under `docs/`, edited via PR.

**Why**: Enables versioning, code review, label-based workflow, and works for both small typo fixes (GitHub web edit) and large structural changes (local clone). CONTRIBUTING.md explicitly mentions both paths.

**Alternatives not chosen**:

- A CMS (Contentful, Strapi, Notion-as-CMS). Trade-off: easier WYSIWYG for non-engineers, but loses git history, PR review, and offline editing.
- AsciiDoc / reStructuredText. Trade-off: richer cross-references and admonitions out of the box, but markdown is more familiar to typical contributors and renders natively in GitHub.
- HTML. Trade-off: full layout control, but raises the contribution barrier substantially.

**Source**: README.md, CONTRIBUTING.md.

## ADR-002: Single content repo per documentation site (no multi-repo)

**Decision**: One repo, `Samsung/tizen-docs`, holds the content for the entire developer site (platform + application + IoT + blog + partners + extensions).

**Why**: The site has a unified GNB and a unified review group; splitting per category would multiply PR routing and CODEOWNERS overhead.

**Alternatives not chosen**:

- One repo per category (tizen-docs-platform, tizen-docs-application, …). Trade-off: smaller blast radius per change, but `docs/menu.yaml` and cross-category links would need an aggregator step.
- Mono-repo containing both content and renderer. Trade-off: atomic content/renderer changes, but couples writer release cadence to engineering release cadence.

**Source**: Single repo with `docs/<category>/` subtrees.

## ADR-003: Heading-depth markdown is the TOC schema

**Decision**: `toc_all.md` files use markdown heading depth (`#`, `##`, `###`, …) to express tree depth. Each line is either a group label or a `[Title](path)` link.

**Why**: Authors already know markdown; no extra DSL to learn. Pull-request diffs of TOC changes are immediately readable.

**Alternatives not chosen**:

- YAML / JSON TOC manifests (e.g. DocFusion, MkDocs). Trade-off: machine-friendly and easier to validate, but less ergonomic for handwritten edits and worse PR diffs.
- Filesystem-derived TOC (renderer walks directories). Trade-off: zero TOC files to maintain, but loses control over ordering and grouping.
- A single global TOC file. Trade-off: one place to look, but huge merge conflicts and harder to scope ownership per category.

**Source**: CONTRIBUTING.md explicitly mandates `#` heading tags. All `toc_all.md` files use this pattern.

## ADR-004: Multi-branch publishing model (master vs live)

**Decision**: Two long-lived branches — `master` collects accepted changes, `live` is what docs.tizen.org actually serves. Promotion is a manual merge PR on a cadence.

**Why** (from `reviewguide/update_docs_tizen_org.md`): Decouples content acceptance from publishing. A piece can be merged and reviewed for several days before going live, allowing reviewers to bundle a release and react to last-minute issues.

**Alternatives not chosen**:

- Single `main` branch, deploy on every merge. Trade-off: simpler, but the public site flickers continuously and last-minute reverts are noisier.
- Tag-driven releases (`v2026.04.01`). Trade-off: cleaner audit trail, but adds an extra step writers don't currently perform.
- Time-based scheduled merges via automation. Trade-off: removes manual step, but the conflict-resolution behavior described in `update_docs_tizen_org.md` argues for human review of the merge PR.

**Source**: `reviewguide/update_docs_tizen_org.md`, `reviewguide/review_points_guide.md`.

## ADR-005: Per-version branches via `tizen_<version>_prepare`

**Decision**: Documentation for an *upcoming* Tizen platform version is staged on a `tizen_<version>_prepare` branch and merged into `master` only when that platform ships.

**Why**: The published `master`/`live` content must describe the currently shipping platform. Pre-release docs would misinform readers if merged early. The prepare branch lets writers progress in parallel.

**Alternatives not chosen**:

- Feature flags / version selectors in articles. Trade-off: keeps everything on one branch, but each article carries inline conditional content and reviewers must mentally diff per version.
- Separate site per version (versioned site URLs). Trade-off: cleanest reader experience for historical versions, but doubles the renderer cost.

**Source**: `reviewguide/review_points_guide.md` "Branches" section.

## ADR-006: Label-driven review state machine

**Decision**: PR review state is tracked by GitHub labels: `Review/Requested` → `Review/Reviewing` → `Review/Scheduled`, with backward transitions allowed.

**Why**: Maintainers can see at a glance which PRs are waiting on whom. The README.md explicitly defines the state machine.

**Alternatives not chosen**:

- GitHub's native review status (Approved / Changes Requested). Trade-off: native, but doesn't capture "scheduled for publishing" — that is a content-pipeline state, not a review state.
- Project boards / kanban. Trade-off: better visualization, but extra overhead per PR.
- A custom GitHub Action that derives state from approvals. Trade-off: less manual work, but the project has no Actions infrastructure today.

**Source**: README.md "Workflow" section.

## ADR-007: Flat CODEOWNERS — three default reviewers for everything

**Decision**: `.github/CODEOWNERS` assigns all paths to three reviewers (`@theojin @safir-srbd @asm-foysal`).

**Why** (inferred): the review group is small enough that explicit per-folder routing would only add maintenance burden without changing who actually reviews.

**Alternatives not chosen**:

- Per-category owners (`/docs/iot/* @iot-team`, `/docs/platform/* @platform-team`). Trade-off: closer alignment with subject-matter expertise, but assumes there are distinct teams to assign — currently there are not.
- No CODEOWNERS at all (free-for-all review). Trade-off: less noise from auto-requests, but new PRs sit unreviewed until someone notices.

**Source**: `.github/CODEOWNERS`.

## ADR-008: Build/render system lives outside the repo

**Decision**: There is no Jenkinsfile, GitHub Actions workflow, or build script in the repo. All build configuration lives on the Jenkins server (`jenkins-docs.stg.tizen.org`).

**Why** (inferred): the renderer is shared with other Samsung documentation properties and would not be useful as in-repo config. It also means the writer-facing repo stays free of toolchain churn.

**Alternatives not chosen**:

- Static site generator committed to the repo (Jekyll, Hugo, MkDocs, Docusaurus). Trade-off: writers can preview locally without Jenkins, but binds content schema to one tool's conventions.
- Move build into GitHub Actions. Trade-off: visible CI status per PR without Jenkins login, but requires sharing the renderer image as a public artifact.

**Source**: absence of build files; README.md links to Jenkins jobs.

## ADR-009: Custom markdown extensions for alerts and external links

**Decision**: Five `[!ALERT]` admonition blocks and `{:target="_blank"}` link-attribute syntax are added on top of GFM, documented in `styleguide/custom-style.md`.

**Why**: GFM lacks first-class admonitions; existing solutions (blockquote conventions) render inconsistently across viewers. Targeting external links in a new window is a UX choice the renderer enforces.

**Alternatives not chosen**:

- Pure GFM, no extensions. Trade-off: portable, but loses the visual grammar that makes admonitions stand out.
- Embedded HTML (`<aside class="note">`). Trade-off: works everywhere, but produces unreadable raw markdown.
- Adopt MyST or another superset. Trade-off: more powerful, but pulls in tooling far beyond what the docs site needs.

**Source**: `styleguide/custom-style.md`.

## ADR-010: Single global glossary instead of per-article terms

**Decision**: All Tizen-specific terminology is defined once in `docs/glossary.md` and articles cross-link by anchor.

**Why**: Avoids divergent definitions across articles and lets readers learn terms in context.

**Alternatives not chosen**:

- Per-category glossaries. Trade-off: closer to the reader's current context, but invites duplication and drift.
- Inline definitions per article. Trade-off: best for one-off readers, but turns into copy-paste maintenance hell.

**Source**: `docs/glossary.md`, plus the named-anchor pattern (`<a name="Tizen_certificate"></a>`).

## ADR-011: PR template encodes ACR linkage

**Decision**: `.github/pull_request_template.md` reserves a section for **API Changes** and points to the ACR (API Change Request) tracker.

**Why**: API documentation must align with the platform's actual API change governance. Forcing a section in the template makes the linkage routine instead of an afterthought.

**Alternatives not chosen**:

- A free-form PR description. Trade-off: lighter, but the link to ACR gets dropped on busy days.
- A GitHub Action that blocks merge until an ACR is referenced. Trade-off: harder enforcement, but adds Actions infra the repo currently lacks.

**Source**: `.github/pull_request_template.md`.

## ADR-012: Filenames are URL-shaped (lowercase, hyphenated, action-verb)

**Decision**: Filenames may contain only lowercase letters, digits, and hyphens; must use action verbs (no `-ing`); must not contain stop words.

**Why**: Filenames become path segments in published URLs. The constraints make URLs short, readable, and stable.

**Alternatives not chosen**:

- Free-form filenames + a separate `slug:` front-matter field. Trade-off: more flexibility for the on-disk name, but the duplication invites drift.
- CamelCase or snake_case. Trade-off: works fine on the filesystem, but produces uglier URLs and is inconsistent with the rest of the docs ecosystem the project references (dotnet/docs, NuGet docs, google/styleguide).

**Source**: CONTRIBUTING.md "File name" section.
