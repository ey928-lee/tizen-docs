# Data pipeline

This page traces a single change from a writer's keyboard all the way to docs.tizen.org. It is intentionally focused on the parts that are visible from inside this repo — references to the external Jenkins infrastructure are noted but not invented.

## End-to-end flow

```
[author edits *.md]
        │
        ▼
[fork + feature branch]                CONTRIBUTING.md "How to PR"
        │
        ▼
[PR opened against master]             pull_request_template.md
        │
        ├── label: Review/Requested     README.md workflow §1
        │
        ▼
[Jenkins STG_docs_PR_builder]          README.md badge,
        │                              reviewguide/stg_build.md
        │                              → docs.stage.tizen.org/staging/{PR#}/
        │
        ▼
[reviewer label: Review/Reviewing]     README.md workflow §2
        │                              reviewguide/review_points_*.md checklist
        │
        ▼
[reviewer label: Review/Scheduled]     README.md workflow §3
        │
        ▼
[merge into master]                    Tizen Document team
        │
        ▼
[periodic master → live PR]            reviewguide/update_docs_tizen_org.md
        │
        ▼
[Jenkins PRD_docs]                     README.md badge
        │
        ▼
[docs.tizen.org]
```

## Stages

### 1. Authoring

CONTRIBUTING.md, *Process for contributing*:

1. Open an issue describing the change (skippable for tiny fixes).
2. Decide where the topic goes in the relevant `toc_all.md` *before* writing.
3. Place the markdown next to its peers (`docs/<category>/<section>/...`), with images under a sibling `media/` folder.
4. Use `styleguide/template-guide.md` as the starting skeleton (`# Title`, short intro, `[TOC]` or `[Main features]`, `## Prerequisites`, body, `## Related information`).
5. Stay within `styleguide/style.md` and `styleguide/custom-style.md`.

### 2. PR submission

CONTRIBUTING.md "How to PR" gives the literal git commands. The PR description follows `.github/pull_request_template.md`:

- **Change Description** — what changed.
- **Bugs Fixed** — `Issue #NNN` references; closes the issue when merged.
- **API Changes** — link to ACR (API Change Request), e.g. `ACR-1120`, when applicable.

`.github/CODEOWNERS` requests `@theojin @safir-srbd @asm-foysal` as default reviewers.

### 3. Staging build

The README.md badge `STG_docs_PR_builder` triggers automatically on PR. `reviewguide/stg_build.md` describes the operator-visible behavior:

- A staging URL is rendered for the PR. Per CONTRIBUTING.md the URL pattern is `docs.stage.tizen.org/staging/{PR#}/`.
- Staging URLs are **deleted after some days** for capacity reasons; closing and reopening the PR re-triggers the build.
- When the staging build fails, contact `iljooo.kim` (named in `stg_build.md`).

### 4. Review

#### Review labels

Defined in README.md, *Workflow*:

| Label | Meaning | Set by |
| ----- | ------- | ------ |
| `Review/Requested` | Author has just opened the PR. | Author, on PR creation |
| `Review/Reviewing` | A reviewer has started looking. | Reviewer |
| `Review/Scheduled` | At least one reviewer approved; ready for publishing. | Approving reviewer |

The label can move *backwards* (`Scheduled` → `Reviewing`) if a reviewer requests further changes.

#### Review checklists

`reviewguide/` contains the operating checklists reviewers run through:

- `review_points_guide.md` — generic guide pages: branches, headings, page creation, rename/move, tags, code blocks, hyperlinks, images, "Related information".
- `review_points_release_note.md` — release notes for Tizen Studio and Tizen Platform.
- `review_points_web_api.md` — web API pages.
- `stg_build.md` — staging URL operations.
- `update_docs_tizen_org.md` — operator runbook for promoting master → live.

### 5. Merge to master

Once `Review/Scheduled` is reached and approval count is met, the Tizen Document team merges into `master`. Per CONTRIBUTING.md each PR should normally address one issue.

### 6. Master → live promotion

`reviewguide/update_docs_tizen_org.md` is the runbook:

- The repo runs **two long-lived branches**: `master` (latest content) and `live` (what docs.tizen.org actually renders).
- On a cadence, an operator opens "Next pull request" in GitHub with `live` as base and `master` as compare, creating a merge PR.
- Conflicts are resolved on that merge PR using branch history.
- The runbook cites PR #1551 as a reference example.

### 7. Production publish

The `PRD_docs` Jenkins job (README.md badge) renders the `live` branch and pushes the result to docs.tizen.org. The job lives on `jenkins-docs.stg.tizen.org`; the in-repo files do not describe its internals.

## Branch lifecycle for next-version content

Per `reviewguide/review_points_guide.md`:

- A `tizen_<version>_prepare` branch holds documentation for an upcoming Tizen platform version that is not yet released.
- When that platform ships, the prepare branch is merged into `master` and conflicts resolved at that point.
- Reviewers are responsible for redirecting PRs to the right branch — for example, content describing an unreleased version belongs on the prepare branch, not on master.

## What is *not* in the pipeline (to avoid surprise)

- **No GitHub Actions** — `.github/` contains only `CODEOWNERS` and the PR template. No `workflows/` directory.
- **No Jenkinsfile** in the repo — Jenkins config is server-side.
- **No test suite** — review is human, with the staging URL as the verification surface.
- **No automatic master → live promotion** — it is a manual, scheduled merge PR per the runbook.
