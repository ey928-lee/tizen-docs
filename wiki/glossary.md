# Glossary

Two flavors of vocabulary, kept in one place:

- **Repo / pipeline terms** — words specific to *this repository* and the docs publishing process.
- **Tizen domain terms** — selected entries excerpted from `docs/glossary.md` (the authoritative public glossary). For the full list, see [`docs/glossary.md`](../docs/glossary.md).

## Repo and pipeline terms

| Term | Definition |
| ---- | ---------- |
| **GNB** | Global Navigation Bar. The top-of-page menu on docs.tizen.org. Defined by `docs/menu.yaml`. CONTRIBUTING.md states that the four categories (Open Source Project, Application, IoT, Blog) are GNB items. |
| **LNB** | Left Navigation Bar. The per-category sidebar tree. Defined by each category's `toc_all.md`. |
| **TOC** | Table of contents. Used in two senses in this repo: (a) the sidebar tree built from `toc_all.md`, (b) the in-page `[TOC]` placeholder mentioned by `styleguide/template-guide.md` and `styleguide/style.md`. |
| **`toc_all.md`** | The per-category navigation source file. Heading depth = tree depth. See [architecture → TOC system](architecture.md#toc-system). |
| **`menu.yaml`** | The GNB source file. Lists `name` + `href` (+ optional `external: true`). |
| **Category** | One of the four top-level groupings defined by CONTRIBUTING.md: Open Source Project (`docs/platform/`), Application (`docs/application/`), IoT (`docs/iot/`), Blog (`docs/blog/`). |
| **`master` branch** | Branch holding the latest accepted documentation for the latest published Tizen platform version. PRs target this branch. |
| **`live` branch** | Branch that docs.tizen.org actually renders. Updated by a periodic merge PR from `master` per `reviewguide/update_docs_tizen_org.md`. |
| **`tizen_<version>_prepare` branch** | Holding branch for documentation describing an unreleased Tizen platform version. Merged into `master` when that version ships. |
| **`dev` branch** | In-progress collaboration branch (used as the base for the wiki branch). |
| **stg / staging build** | The `STG_docs_PR_builder` Jenkins job that renders a per-PR preview at `docs.stage.tizen.org/staging/{PR#}/`. Per `reviewguide/stg_build.md`, the URL is ephemeral (deleted after some days). |
| **PRD build** | The `PRD_docs` Jenkins job that publishes the `live` branch to docs.tizen.org. |
| **stg URL** | Short for staging URL. Operators say "trigger the stg build again" when the URL has expired (`reviewguide/stg_build.md`). |
| **Review/Requested** | Initial PR label, set by the contributor on PR open. |
| **Review/Reviewing** | Label set by a reviewer when they begin a substantive review. Can be moved back here from `Review/Scheduled` if more changes are needed. |
| **Review/Scheduled** | Label set after at least one approval, indicating the PR is ready for publishing. |
| **ACR** | API Change Request. The Tizen API governance process. The PR template (`.github/pull_request_template.md`) reserves a section for the ACR link, e.g., `ACR-1120`. |
| **CODEOWNERS** | The `.github/CODEOWNERS` file. Currently a single catch-all assigning all paths to `@theojin @safir-srbd @asm-foysal`. |
| **`media/` folder** | Convention from CONTRIBUTING.md: each article folder gets a sibling `media/` directory holding its images. Top-level images that span categories live in `docs/images/`. |
| **`index.md`** | Convention from CONTRIBUTING.md: the main article for a section folder is named `index.md`. |
| **Custom markdown** | Renderer-specific extensions documented in `styleguide/custom-style.md`: the five `[!ALERT]` admonitions and `{:target="_blank"}` link suffix. |

## Tizen domain terms (excerpted from `docs/glossary.md`)

The full table lives in `docs/glossary.md` with named anchors that articles cross-link to. Selected entries:

### Packaging and signing

| Term | Definition |
| ---- | ---------- |
| **`.tpk` file** | Tizen package file for native applications. Contains `tizen-manifest.xml`. |
| **`.wgt` file** | Tizen package file for Web applications. Contains `config.xml`. Format based on the W3C widget standard. |
| **`.edc` file** | Edje Data Collection text script describing visual layout. Compiled to `.edj`. |
| **`.edj` file** | Compiled binary of an `.edc` — the UI-layout binary used by EFL. |
| **`tizen-manifest.xml`** | Native application package manifest. |
| **`config.xml`** | Web application package manifest. |
| **Tizen certificate** | Pair of author + distributor certificates required to sign an application package. |
| **Author certificate** | Developer-side signing certificate; produces the author signature. |
| **Distributor certificate** | Distributor-side (e.g., Tizen Store) signing certificate; produces the distributor signature. |
| **Privilege** | Declared API-level permission. Public-level privileges are SDK-default; platform-level privileges require a platform-signed certificate. |
| **DUID** | Device Unique ID assigned by the device vendor. Basis for vendor-specific test certificates. |

### Frameworks and graphics

| Term | Definition |
| ---- | ---------- |
| **EFL** | Enlightenment Foundation Libraries. The native UI toolkit. |
| **DALi** | Dynamic Animation Library — cross-platform 3D UI toolkit on top of OpenGL ES 2.0. |
| **Evas** | EFL's scene-graph canvas engine. |
| **Edje** | EFL component for layout and theming, fed by `.edc` / `.edj`. |
| **Cairo** | 2D vector graphics library; in Tizen supports image and GL backends. |
| **OpenGL ES** | Embedded-systems variant of OpenGL used by native applications. |
| **EGL** | Interface between rendering APIs (e.g. OpenGL ES) and the native windowing system. |
| **Elm_GLView** | Elementary UI component that wraps `Evas_GL` so an app can use OpenGL ES without dealing with EGL directly. |
| **Pbuffer surface** | Off-screen graphics surface for accelerated rendering. Supported via Evas_GL. |
| **Stage** | DALi's top-level scene graph node. |
| **Actor** | Primary scene-composition object in DALi. Receives input and contains drawables. |
| **Layout** | Container UI component that wraps an Edje design with thin animation/event support. |
| **TBM** | Tizen Buffer Manager — graphic-buffer allocator for image rendering. |
| **YUV** | Color model used in many Tizen multimedia paths (Y = luminance, U/V = chrominance). |
| **Wayland** | Windowing system used by Tizen, replacing X. |
| **X Window** | Legacy windowing system; still referenced for historical context. |

### Application model

| Term | Definition |
| ---- | ---------- |
| **Native application** | Application written in C/C++ using the Tizen Native API. Typically built with EFL or DALi. |
| **Web application** | Application packaged as a `.wgt` using HTML, CSS, JavaScript and the W3C/HTML5 + Tizen Web Device APIs. |
| **Hybrid Web application** | A `.wgt` package combining one Web application with one or more native service or widget applications. |
| **Service application** | Background application without a UI. Usually shipped together with a native UI app. |
| **Widget application** | Tizen application surfaced as a home-screen widget. |
| **UI components / UI containers** | Elementary library's reusable widgets vs. their organizing layout containers. |
| **Window UI component** | Bottom-most UI component; the root window in a Tizen native application. |

### System APIs

| Term | Definition |
| ---- | ---------- |
| **Native API** | The Tizen-curated subset of native subsystem APIs. Covers more device features than the Web API. |
| **Web Device API** | JavaScript API exposed to Web applications, e.g. for life cycle, schedule, NFC payments. |
| **Push notification** | Server-originated message routed via the Tizen Push server to the on-device push daemon. |
| **Push service** | The on-device push daemon and its management API. |
| **Sync Manager** | Schedules application data synchronization between server and device. |
| **STT** | Speech-to-Text. App connects to the STT daemon as a client. |
| **TTS** | Text-to-Speech. App connects to the TTS daemon as a client. |
| **Voice control** | Mobile-only API to register voice commands with callbacks. |
| **OAuth** | OAuth 2.0 authorization framework as supported by the Tizen Account API. |
| **Access token** | Login-session credentials identifying user, groups, privileges, and (sometimes) the calling application. |

### Development tools

| Term | Definition |
| ---- | ---------- |
| **GDB** | GNU Debugger. Used for debugging C/C++ programs on Tizen. |
| **SDB** | Smart Development Bridge. CLI for emulator/device management; provides file sync, shell, etc. |
| **EXIF** | Image metadata format used by Tizen's image and sound handling on cameras. |
| **EOS** | End Of Stream — multimedia condition where no more stream data is available. |
| **vCalendar / iCalendar** | Calendar file formats (`.vcs` / `.ics`). |
| **vCard** | Electronic business-card file format. |
| **Media packet** | AV packet buffer used between Tizen multimedia framework modules. |

### Security

| Term | Definition |
| ---- | ---------- |
| **SMACK** | Simplified Mandatory Access Control in Kernel. Linux kernel MAC mechanism Tizen uses for sandboxing. Defines subjects, objects, and access types. |
| **Privilege** (security view) | API-level access control declared in `tizen-manifest.xml` or `config.xml`. Levels: public, partner, platform. |
| **Author signature / Distributor signature** | Cryptographic signatures over the application package, asserting authorship and distributor identity respectively. |

### UI / interaction

| Term | Definition |
| ---- | ---------- |
| **Home screen** | Primary user interface for accessing apps and functions on a device. |
| **Surface** | Pixel container being composited to the screen. May be a main surface or sub-surface (the latter offloads compositing work, e.g., for video). |
| **Elementary UI components** | Themable, finger-friendly widget set used to compose the UI of native applications. |
