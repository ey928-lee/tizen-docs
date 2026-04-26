# Module reference

This page covers the files that act as **interfaces** for the repository — the ones whose schema or contract other parts of the system (humans, the Jenkins renderer, GitHub) depend on. Articles under `docs/<category>/...` are *content*, not interfaces, and are intentionally not listed here.

The selection criterion: a file is included only if changing its shape changes the behavior of the docs site, the review workflow, or the contributor experience.

## `docs/menu.yaml` — GNB definition

**Role**: Defines the top-of-page Global Navigation Bar on docs.tizen.org.

**Schema** (inferred from current content):

```yaml
- name: "<display label>"
  href: "<path or URL>"
  external: true        # optional; renders as outbound link
```

**Current content**: two active entries (Platform → `/platform/what-is-tizen/overview`, Application → samsungtizenos.com), plus two commented-out entries (Partners, Download).

**Why this is an interface**: each `name`/`href` becomes a top-level tab. The `href` of an internal entry must resolve to a real markdown route inside `docs/`; an `external: true` entry skips that check.

**When you change it**: any time the GNB layout changes. CONTRIBUTING.md explicitly notes that Blog "is not shown on GNB yet" — adding it here is the mechanism for that.

## `docs/<category>/toc_all.md` — LNB per category

**Role**: Defines the left-side navigation tree for one of the four categories (platform, application, iot, blog) plus the auxiliary `partners`.

**Schema** (from CONTRIBUTING.md and observed file contents):

- Lines starting with `#`, `##`, `###`, … are TOC nodes.
- Heading depth equals tree depth.
- A heading may be plain text (a non-clickable group label) or a markdown link (a clickable leaf or section index).
- Use `[Title](/category/path/file.md)` for internal links.
- Append `{:target="_blank"}` to open in a new window (mostly used for external API reference HTML — see `docs/iot/toc_all.md`).

**Algorithmic note**: this file is the *only* place that defines navigation order. Filesystem order does not matter. Adding a `.md` under `docs/` without adding it to a TOC means it is unreachable from navigation.

**Variants in `docs/application/`**:

- `toc_all.md` — primary application TOC.
- `toc_all_new.md` — work-in-progress next-generation TOC. Treated as draft, not a live alias.
- `toc_vs-ext_native.md`, `toc_vs-ext_dotnet.md`, `toc_vs-ext_web.md` — TOCs scoped to the Visual Studio extension docs, one per runtime.
- `toc_vscode_native.md`, `toc_vscode_dotnet.md`, `toc_vscode_web.md` — same idea but for the VS Code extension.

These per-tool TOCs let the renderer present a focused sidebar when a reader enters via an IDE-extension landing page, instead of the full application catalog.

**Per-section toc.md** under deeper folders (`docs/platform/HAL/toc.md`, `docs/extensions/tizenx/api/toc.md`, `docs/extensions/tizenx/guides/toc.md`, `docs/application/flutter/toc.md`, `docs/platform/HAL/api/1.0/toc.md`) follow the same heading-as-tree convention but are scoped to one subtree.

## `docs/glossary.md` — terminology contract

**Role**: Public-facing glossary that articles link into via fragment anchors (e.g., `#Native_application`, `#OpenGL_ES`).

**Schema**: a markdown table with columns `Term | Definition`. Inside `Term`, named anchors like `<a name="Tizen_certificate"></a>` are inserted to make terms linkable.

**Why this is an interface**: many `.md` files cross-link into glossary terms by anchor. Renaming an anchor breaks those links silently, because there is no automated link checker in the repo (one experimental branch `test-broken-link-checker*` exists upstream but is not merged).

## `.github/CODEOWNERS` — review routing

**Role**: GitHub uses this to auto-request reviewers.

**Current content**: a single catch-all line — `* @theojin @safir-srbd @asm-foysal` — assigning all paths to three default owners.

**Why this is an interface**: anyone wanting per-folder review routing (e.g., separate owners for `iot/` vs `platform/`) extends this file. The current configuration is intentionally flat.

## `.github/pull_request_template.md` — PR shape

**Role**: GitHub auto-fills the PR description with this template.

**Schema** (three required sections): Change Description, Bugs Fixed, API Changes.

**Why this is an interface**: review checklists in `reviewguide/` assume these sections exist. The `API Changes` section also encodes the cross-link to the Tizen ACR (API Change Request) tracker.

## `styleguide/template-guide.md` — article skeleton

**Role**: The canonical layout every new article should follow.

**Contract**:

```
# Document Title          (matches filename)
Short introduction.       (1–3 sentences)
[TOC] or [Main features]
## Prerequisites          (only if needed)
## <Topic sections>
## Related information
- Dependencies
- See also
  - https://...
```

**Why this is an interface**: this skeleton plus `styleguide/style.md` and `styleguide/custom-style.md` form the *content schema* — the renderer and reviewers both assume articles look like this.

## `styleguide/naming-rules.md` — name composition algorithm

**Role**: Rules for composing the names of Tizen products, features, APIs, and tools so that documentation stays consistent.

**Algorithm**: every compound term is `Brand + Feature + Type + Suffix`, where:

- **Brand** is one of three things — service/platform name (Tizen, Tizen Studio), profile/device brand (Tizen TV, Tizen Wearable, Tizen IoT), or application type (Tizen Native, Tizen Web, Tizen .NET).
- **Feature** is a short main feature or technology (Alarm, Wi-Fi, NFC).
- **Type** is the artifact category (SDK, API, Guide, Studio).
- **Suffix** carries qualifiers.

**Why this is an interface**: PRs that introduce new product names are reviewed against this file. Without it, the same concept gets multiple names across articles.

## `styleguide/custom-style.md` — renderer extensions

**Role**: Documents the custom markdown the docs.tizen.org renderer understands beyond GFM.

**Listed extensions**:

- Five alert blocks: `[!NOTE]`, `[!TIP]`, `[!IMPORTANT]`, `[!CAUTION]`, `[!WARNING]`.
- `{:target="_blank"}` suffix on links to force a new window.

**Why this is an interface**: this is the contract between author and renderer. Authors using anything not listed here are gambling on it rendering correctly on docs.tizen.org.

## `reviewguide/update_docs_tizen_org.md` — promotion runbook

**Role**: The operating procedure that promotes content from `master` to `live` (and therefore onto docs.tizen.org).

**Algorithm**:

1. Open GitHub's "Next pull request" UI.
2. Set base = `live`, compare = `master`.
3. Create the merge PR.
4. Resolve conflicts on that PR using branch history.

**Why this is an interface**: there is no automated promotion. This file *is* the promotion mechanism, and the cadence of running it determines when public readers see content.

## `reviewguide/stg_build.md` — staging operations

**Role**: Operating notes for the Jenkins staging build.

**Key contracts captured**:

- Staging URLs are *ephemeral* — deleted after some days due to capacity.
- Closing and reopening the PR re-triggers the staging build.
- Failed builds escalate to `iljooo.kim`.

## What is intentionally *not* listed here

- All articles under `docs/<category>/...` — these are content, not interface. They follow the schema in `template-guide.md` but they do not define a schema.
- `LICENSE-CODE`, `content-license.md`, `trademarks.md` — legal text, not behavioral.
- `docs/get-started.md`, `docs/<category>/index.md` — public landing content; not in the contributor-facing interface surface.
- The `media/` image folders — no schema, just static assets.
