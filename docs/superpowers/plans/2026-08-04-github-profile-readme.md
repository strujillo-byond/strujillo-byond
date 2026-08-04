# GitHub Profile README (Phase 1) Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Replace the default profile README with an animated, auto-updating profile (header, typing effect, badges, stats, contribution snake) per the approved spec.

**Architecture:** Pure Markdown/HTML README embedding third-party SVG services (capsule-render, readme-typing-svg, shields.io, github-readme-stats, streak-stats). One GitHub Actions workflow (Platane/snk) regenerates the contribution-snake SVGs daily and publishes them to an `output` branch that the README references via raw URLs.

**Tech Stack:** GitHub Flavored Markdown, GitHub Actions, Platane/snk@v3, crazy-max/ghaction-github-pages@v4.

## Global Constraints

- Language of all README copy: English.
- Contact: LinkedIn only, badge **commented out** until Sergio provides the URL. No X/Twitter, no email.
- No projects section in Phase 1.
- Stats theme: `tokyonight`, `hide_border=true`, transparent background (`bg_color=00000000` / `background=00000000`).
- Repo pushes directly to `main` (existing history is all on `main`).
- Commits: conventional-commit style, English, no attribution footers.

---

### Task 1: Snake workflow

**Files:**
- Create: `.github/workflows/snake.yml`

**Interfaces:**
- Produces: `output` branch containing `github-snake.svg` and `github-snake-dark.svg`, referenced by Task 2's README at `https://raw.githubusercontent.com/strujillo-byond/strujillo-byond/output/<name>.svg`.

- [x] **Step 1: Write the workflow file**

```yaml
name: Generate contribution snake

on:
  schedule:
    - cron: "0 3 * * *"
  workflow_dispatch:
  push:
    branches:
      - main

permissions:
  contents: write

jobs:
  generate:
    runs-on: ubuntu-latest
    timeout-minutes: 10
    steps:
      - name: Generate snake SVGs
        uses: Platane/snk/svg-only@v3
        with:
          github_user_name: ${{ github.repository_owner }}
          outputs: |
            dist/github-snake.svg?palette=github
            dist/github-snake-dark.svg?palette=github-dark

      - name: Publish SVGs to output branch
        uses: crazy-max/ghaction-github-pages@v4
        with:
          target_branch: output
          build_dir: dist
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```

- [x] **Step 2: Validate the YAML parses**

Run: `python3 -c "import yaml,sys; yaml.safe_load(open('.github/workflows/snake.yml')); print('OK')"`
Expected: `OK`

- [x] **Step 3: Commit**

```bash
git add .github/workflows/snake.yml
git commit -m "feat(profile): add contribution snake workflow"
```

### Task 2: README rewrite

**Files:**
- Modify: `README.md` (full replacement)

**Interfaces:**
- Consumes: raw snake SVG URLs from Task 1's `output` branch.

- [x] **Step 1: Pre-flight — verify every third-party asset URL returns HTTP 200**

Run each (expect `200`; the two raw.githubusercontent URLs will 404 until Task 3 runs the workflow — that is expected here):

```bash
curl -s -o /dev/null -w "%{http_code} capsule-header\n" "https://capsule-render.vercel.app/api?type=waving&color=gradient&customColorList=6,11,20&height=190&section=header&text=Sergio%20Trujillo&fontSize=44&fontColor=ffffff&animation=fadeIn&fontAlignY=36"
curl -s -o /dev/null -w "%{http_code} typing\n" "https://readme-typing-svg.demolab.com?font=Fira+Code&weight=600&size=22&duration=3500&pause=1000&color=58A6FF&center=true&vCenter=true&width=480&lines=Java+%26+JavaScript+Developer;DevOps+%26+Cloud+enthusiast;Currently+learning+Kubernetes"
curl -s -o /dev/null -w "%{http_code} stats\n" "https://github-readme-stats.vercel.app/api?username=strujillo-byond&show_icons=true&theme=tokyonight&hide_border=true&bg_color=00000000"
curl -s -o /dev/null -w "%{http_code} streak\n" "https://streak-stats.demolab.com?user=strujillo-byond&theme=tokyonight&hide_border=true&background=00000000"
curl -s -o /dev/null -w "%{http_code} badge-java\n" "https://img.shields.io/badge/Java-ED8B00?style=for-the-badge&logo=openjdk&logoColor=white"
curl -s -o /dev/null -w "%{http_code} footer\n" "https://capsule-render.vercel.app/api?type=waving&color=gradient&customColorList=6,11,20&height=120&section=footer"
```

If any non-snake URL is not 200, fix the URL before writing the README.

- [x] **Step 2: Write the new README.md (full content)**

````markdown
<div align="center">

<img src="https://capsule-render.vercel.app/api?type=waving&color=gradient&customColorList=6,11,20&height=190&section=header&text=Sergio%20Trujillo&fontSize=44&fontColor=ffffff&animation=fadeIn&fontAlignY=36" alt="Header" width="100%">

<img src="https://readme-typing-svg.demolab.com?font=Fira+Code&weight=600&size=22&duration=3500&pause=1000&color=58A6FF&center=true&vCenter=true&width=480&lines=Java+%26+JavaScript+Developer;DevOps+%26+Cloud+enthusiast;Currently+learning+Kubernetes" alt="Typing animation">

</div>

## 🛠️ Tech Stack

<div align="center">

<img src="https://img.shields.io/badge/Java-ED8B00?style=for-the-badge&logo=openjdk&logoColor=white" alt="Java">
<img src="https://img.shields.io/badge/Spring-6DB33F?style=for-the-badge&logo=spring&logoColor=white" alt="Spring">
<img src="https://img.shields.io/badge/JavaScript-F7DF1E?style=for-the-badge&logo=javascript&logoColor=black" alt="JavaScript">
<img src="https://img.shields.io/badge/Node.js-339933?style=for-the-badge&logo=nodedotjs&logoColor=white" alt="Node.js">
<br>
<img src="https://img.shields.io/badge/Docker-2496ED?style=for-the-badge&logo=docker&logoColor=white" alt="Docker">
<img src="https://img.shields.io/badge/Kubernetes-326CE5?style=for-the-badge&logo=kubernetes&logoColor=white" alt="Kubernetes">
<img src="https://img.shields.io/badge/GitHub%20Actions-2088FF?style=for-the-badge&logo=githubactions&logoColor=white" alt="GitHub Actions">
<img src="https://img.shields.io/badge/Git-F05032?style=for-the-badge&logo=git&logoColor=white" alt="Git">
<img src="https://img.shields.io/badge/Linux-FCC624?style=for-the-badge&logo=linux&logoColor=black" alt="Linux">

</div>

## 📊 GitHub Stats

<div align="center">

<img height="170" src="https://github-readme-stats.vercel.app/api?username=strujillo-byond&show_icons=true&theme=tokyonight&hide_border=true&bg_color=00000000" alt="GitHub stats">
<img height="170" src="https://streak-stats.demolab.com?user=strujillo-byond&theme=tokyonight&hide_border=true&background=00000000" alt="GitHub streak">

</div>

## 🐍 Contribution Snake

<div align="center">

<picture>
  <source media="(prefers-color-scheme: dark)" srcset="https://raw.githubusercontent.com/strujillo-byond/strujillo-byond/output/github-snake-dark.svg">
  <img src="https://raw.githubusercontent.com/strujillo-byond/strujillo-byond/output/github-snake.svg" alt="Contribution snake">
</picture>

</div>

## 🌱 Currently

- ☸️ Learning **Kubernetes**
- 🤝 Open to collaborating on **Java**, **JavaScript** and **DevOps** projects

## 📫 Reach me

<!-- Pending LinkedIn URL — uncomment and fill in when available:
<a href="https://www.linkedin.com/in/PENDING-URL/">
  <img src="https://custom-icon-badges.demolab.com/badge/LinkedIn-0A66C2?style=for-the-badge&logo=linkedin-white&logoColor=white" alt="LinkedIn">
</a>
-->

*Portfolio site coming soon.*

<div align="center">

<img src="https://capsule-render.vercel.app/api?type=waving&color=gradient&customColorList=6,11,20&height=120&section=footer" alt="Footer" width="100%">

</div>
````

- [x] **Step 3: Commit**

```bash
git add README.md
git commit -m "feat(profile): animated profile readme with stats and snake"
```

### Task 3: Push, run workflow, verify

**Files:** none (remote verification only)

**Interfaces:**
- Consumes: Task 1's workflow (triggered automatically by the push to `main`).

- [x] **Step 1: Push to main**

```bash
git push origin main
```

- [x] **Step 2: Watch the workflow triggered by the push**

Run: `gh run list --workflow=snake.yml --limit 1` then `gh run watch <run-id> --exit-status`
Expected: run concludes `success`. If no run appears (push race), trigger manually: `gh workflow run snake.yml` and watch again.

- [x] **Step 3: Verify the output branch and SVGs**

```bash
curl -s -o /dev/null -w "%{http_code} snake-light\n" "https://raw.githubusercontent.com/strujillo-byond/strujillo-byond/output/github-snake.svg"
curl -s -o /dev/null -w "%{http_code} snake-dark\n" "https://raw.githubusercontent.com/strujillo-byond/strujillo-byond/output/github-snake-dark.svg"
```

Expected: both `200`.
