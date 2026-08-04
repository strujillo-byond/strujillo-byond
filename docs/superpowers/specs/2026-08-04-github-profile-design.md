# GitHub Profile Design — strujillo-byond

**Date:** 2026-08-04
**Status:** Approved by Sergio ("adelante")

## Context

`strujillo-byond/strujillo-byond` is the special profile repository; its README renders on the GitHub profile page. Current content is the default onboarding template. The account has no other public repositories, so there is no "featured projects" material yet.

## Goals

- A visually striking profile README (English) with animated elements that approximate a Magic UI aesthetic within GitHub's Markdown sandbox (no CSS/JS/React — only embedded SVGs and images).
- Auto-updating content (stats, contribution snake) with zero manual maintenance.
- Phase 2 (separate project): a real React portfolio at `strujillo-byond.github.io` using shadcn/ui + Magic UI, deployed via GitHub Pages.

## Decisions

- **Language:** English.
- **Contact:** LinkedIn only (badge). URL pending from Sergio — until provided, the contact section ships with the badge commented out so nothing broken appears publicly. X/Twitter and email deliberately excluded.
- **No projects section** in Phase 1; the Phase 2 portfolio takes that role.

## Phase 1 — Profile README (this repo)

Top-to-bottom structure of `README.md`:

1. **Animated header** — capsule-render `waving` SVG with gradient and the name "Sergio Trujillo".
2. **Typing effect** — readme-typing-svg cycling: `Java & JavaScript Developer` / `Learning Kubernetes ☸️` / `DevOps enthusiast 🚀`.
3. **Tech stack** — shields.io `for-the-badge` badges: Java, Spring, JavaScript, Node.js, Docker, Kubernetes, GitHub Actions, Git, Linux.
4. **GitHub stats** — github-readme-stats card + streak-stats (demolab), tokyonight theme, transparent background, `hide_border`.
5. **Contribution snake** — SVG generated daily by a GitHub Action (Platane/snk), published to an `output` branch; README embeds dark/light variants via `<picture>`.
6. **Currently** — learning Kubernetes; open to collaborating on Java/JS/DevOps.
7. **Contact** — LinkedIn badge (commented out until URL provided) + future link to the Phase 2 portfolio.
8. **Footer** — capsule-render wave footer.

### Workflow: `.github/workflows/snake.yml`

- Triggers: daily cron, `workflow_dispatch`, and push to `main`.
- `permissions: contents: write`; uses the default `GITHUB_TOKEN` (no extra secrets).
- Steps: `Platane/snk/svg-only@v3` generates `github-snake.svg` (light) and `github-snake-dark.svg` (dark palette) into `dist/`; `crazy-max/ghaction-github-pages@v4` publishes `dist/` to the `output` branch.

### Known limitations

- Stats cards are third-party services; outages render as broken images temporarily and self-heal.
- The snake image is broken until the first workflow run completes (the push-to-main trigger runs it immediately).

## Addendum (2026-08-04): widget expansion

Approved additions after Phase 1 shipped:

- **3D contribution graph** (yoshi389111/github-profile-3d-contrib) added to the daily graphics workflow (`snake.yml`, renamed "Generate profile graphics"); publishes `profile-3d-contrib/*.svg` to `output`.
- **lowlighter/metrics**: wired in the same workflow but gated on a `METRICS_TOKEN` secret (requires a classic PAT; the default GITHUB_TOKEN is not usable). README embed commented until enabled. The `mv` fallback path for the rendered file is unverified until the first real run.
- **Recent activity** (jamesgeorge007/github-activity-readme) + **daily dev quote** (local `quotes/quotes.txt`, day-of-year index, awk marker replacement) in `update-readme.yml` (12h cron). Activity only tracks public issues/PRs/releases — placeholder text until those exist.
- **Visitor counter** (komarev) under the typing header.
- **Trophies**: github-profile-trophy.vercel.app is DEPLOYMENT_DISABLED (402) — embed left commented.
- **WakaTime**: pending Sergio's account + editor plugin; commented note in README.

## Phase 2 — Portfolio (separate repo, own spec later)

`strujillo-byond.github.io`: Vite + React + Tailwind + shadcn/ui + Magic UI (animated hero, tech marquee, bento grid for projects), deployed to GitHub Pages via Actions on each push. Sections: hero, about, stack, projects (placeholder), contact. Gets its own brainstorm/spec/plan cycle after Phase 1 ships.

## Verification (Phase 1)

- Workflow YAML parses and `gh workflow run` (or the push trigger) completes green.
- `output` branch contains both snake SVGs.
- All embedded image URLs return HTTP 200.
- Profile page renders without broken images (except documented snake window).
