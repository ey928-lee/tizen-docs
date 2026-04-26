# Tizen Docs Code Wiki

This wiki documents the **structure, conventions, and pipeline** of the [Samsung/tizen-docs](https://github.com/Samsung/tizen-docs) repository — the source for the public Tizen developer documentation site (docs.tizen.org).

It is aimed at:

- **New contributors** trying to figure out where a topic belongs and how the TOC works.
- **Reviewers** who need a single page that explains the branch and label workflow.
- **Maintainers** who manage the staging/live pipeline and onboard new writers.

The project is a **content repository**, not a code repository. There is no application code, no test suite, and no in-repo build script — markdown files in `docs/` are consumed by an external Jenkins pipeline that renders the Tizen Docs site. This wiki therefore focuses on the *data model* of the docs (folder layout, TOC files, naming rules), not on source code.

## Contents

| File | What you will find |
| ---- | ------------------ |
| [Home](Home.md) | This page. Overview and start guide. |
| [architecture](architecture.md) | Folder layout, TOC system (`toc_all.md`, `menu.yaml`), build/deploy targets. |
| [module](module.md) | Detailed walkthrough of the files that act as **interfaces**: `menu.yaml`, the various `toc_all.md` files, `glossary.md`, the GitHub workflow files. |
| [adr](adr.md) | Architecture decision records — choices the project made (and explicit alternatives that were not chosen). |
| [data-pipeline](data-pipeline.md) | End-to-end flow: write → PR → staging build → review labels → merge to master → push to live → docs.tizen.org. |
| [glossary](glossary.md) | Tizen domain terminology distilled from `docs/glossary.md` plus repo-specific terms (TOC, GNB, LNB, stg/live, etc.). |
| [_Sidebar](_Sidebar.md) | Navigation rendered on every wiki page. |

## Start guide

1. **First-time contributor?** Read the [Contributing Guide](https://github.com/Samsung/tizen-docs/blob/master/CONTRIBUTING.md) first, then come back here for the *why* behind the rules.
2. **Adding a new article?** Jump to [architecture → TOC system](architecture.md#toc-system) to see how to hook it into the left-hand navigation, then to [data-pipeline](data-pipeline.md) for the PR → publish flow.
3. **Reviewing a PR?** Go to [data-pipeline → Review labels](data-pipeline.md#review-labels) and the in-repo `reviewguide/` folder.
4. **Confused by a Tizen term?** Check [glossary](glossary.md).
5. **Wondering why something is the way it is?** [adr](adr.md) records the trade-offs.

## Scope of this wiki

- **In scope**: repo conventions, folder/TOC structure, branch model, review workflow, deploy pipeline as far as it is observable from this repo.
- **Out of scope**: the Jenkins pipeline internals (separate, non-public infra), the doc rendering engine on docs.tizen.org, and the content of individual articles. When this wiki cites build behavior, it cites only what the in-repo README and `reviewguide/` describe.
