# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

A single-page internal link hub for **PHATTHA PACKDEE**. It is not an application — it links out to five separately-hosted systems that already exist elsewhere. The entire site is `index.html`: no build step, no package manager, no dependencies, no backend, no login/auth of any kind.

Live at: https://yasmetal.github.io/internal-hub/

## Commands

There is no build, lint, or test tooling in this repo — it's a static HTML file.

- **Preview locally**: just open `index.html` directly in a browser (`file://`), or serve the folder with any static file server. No dev server config exists.
- **Deploy**: push to `master`. `.github/workflows/pages.yml` runs on every push and publishes the repo root straight to GitHub Pages via `actions/upload-pages-artifact` + `actions/deploy-pages`.

## Architecture

**Single file, no build pipeline.** `index.html` contains all HTML/CSS/JS inline — there is nothing to compile or bundle. Edit it directly and push; the GitHub Actions workflow does the rest.

**Logo is embedded, not linked.** The company logo (source: `PTN.jpg`) is inlined into `index.html` as a `data:image/jpeg;base64,...` URI on the `<img>` inside `.logo-badge`, so the deployed page has zero external asset dependencies. If the logo needs to change, regenerate the base64 payload from the new image and replace that one `src` value — do not add an `<img src="PTN.jpg">` reference, since the point of embedding was to keep the page a single deployable file.

**GitHub Pages uses the Actions build type, not the legacy Jekyll pipeline.** This matters if Pages settings ever get reset: the legacy "build from branch" Jekyll pipeline for this repo failed repeatedly with an undiagnosable `Page build failed` error. The working configuration is `build_type: workflow` (set via the Pages API) combined with the `.github/workflows/pages.yml` workflow in this repo. `.nojekyll` is also present at the repo root as a belt-and-suspenders guard against Jekyll processing, though the workflow-based deploy no longer runs Jekyll at all. Don't switch Pages back to "Deploy from a branch" without expecting build failures.

**The destination links are hardcoded** inside the card markup in `index.html` (one `<a class="card card-N">` block per system, each with its own gradient color via `--card-N-a`/`--card-N-b` CSS variables). To add/remove/edit a link, edit the card block directly — there's no data-driven list to update elsewhere. The desktop grid breakpoint (`@media (min-width: 900px)`) uses `grid-template-columns: repeat(3, 1fr)` to balance 5 cards into a 3+2 layout; adjust that column count if the number of cards changes.

Current destinations:
| Card | Thai label | Target |
|---|---|---|
| card-1 | เบิกใช้อุปกรณ์ | https://yasmetal.github.io/packaging-requisition-system/ |
| card-2 | บันทึกนำเข้า | https://yasmetal.github.io/phattha-import-tracker/ |
| card-3 | เช็ค Stock สินค้า | https://phattha-stock-web.vercel.app/ |
| card-4 | ลางาน | https://yasmetal.github.io/Leave_PTC/ |
| card-5 | ระบบเก็บใบแจ้งหนี้ | https://bill-ptc-invoice-app-production.up.railway.app/ |

All five use `target="_blank" rel="noopener noreferrer"` — preserve this when editing, since the whole point of the hub is to hand off to another tab without disturbing this page.

**GitHub identity**: this repo lives under the `yasmetal` GitHub account/org, consistent with the other linked systems (card-5's invoice system is hosted on Railway instead of GitHub Pages/Vercel like the rest).
