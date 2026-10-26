# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Development Commands

```bash
# Install dependencies
bundle install

# Start local dev server (available at localhost:4000)
bundle exec jekyll serve

# Build static site
bundle exec jekyll build
```

**Note:** Changes to `_config.yml` require restarting the Jekyll server to take effect.

### Docker alternative

```bash
docker image build -t resume-template .
docker run --rm --name resume-template -v "$PWD":/home/app --network host resume-template
```

Jekyll 4.4 (plain `jekyll` gem, no `github-pages`) needs Ruby >= 3.2. Local Ruby is 3.4 via rvm; the Docker image uses the official `ruby:3.4`, the same Ruby Amplify builds with.

### Deployment

AWS Amplify app `resume` (`drpwo2606hncl`, eu-central-1) auto-builds branch `cv` and serves `crichter.io` / `www.crichter.io`.
**Pushing to `origin/cv` deploys to production.** Check a deploy with
`aws --profile richter amplify list-jobs --app-id drpwo2606hncl --branch-name cv`.
Builds run in the custom image `public.ecr.aws/docker/library/ruby:3.4` (app env var `_CUSTOM_IMAGE`) with the build spec stored in the Amplify console (`bundle install`, `bundle exec jekyll build`, artifacts in `_site`).

## Architecture

This is a **Jekyll static site** — a personal resume/CV for Christian Richter (Data & AI Engineer). The architecture separates content (YAML data) from presentation (HTML/SCSS).

### Content lives in `_data/`

All resume content is stored as YAML files: `experience.yml`, `projects.yml`, `skills.yml`, `education.yml`, `services.yml`, `additional.yml`, `references.yml`, `links.yml`. Editing resume content means editing these files.

Content source of truth is the English reference CV (`~/Nextcloud/CV/ai/CV_Christian_Richter_EN_2026.docx`, rules in `~/Nextcloud/CV/ai/CLAUDE.md`). Exception: the website headline "Data & AI Engineer" deliberately differs from the CV headline "Data Engineer & Cloud Architect" to compare positioning per channel (since 2026-10-27); don't sync it back. The website shows no client names in projects (generic titles + industry only). Projects with `earlier: true` render in the "Earlier Projects" section.

### Presentation in `_layouts/` and `_includes/`

- `_layouts/resume.html` — the main resume template; iterates over `_data/` files using Liquid templating
- `_includes/` — reusable partials (head, navigation, footer, icon-links)

### Styling in `_sass/`

SCSS partials compiled via `css/main.scss`. Key files: `_resume.scss` (resume-specific), `_variables.scss`, `_layout.scss`.

### Global configuration in `_config.yml`

Controls site metadata, contact info, the intro paragraph (`resume_header_intro`), and boolean toggles to show/hide each resume section (e.g., `resume_section_projects: true`).

### Static pages

`services.html`, `impressum.html`, `dsgvo.html`, `agent-ready-data.html` are standalone Jekyll pages outside the main resume layout. The pipeline patterns on `services.html` live in `_data/pipelines.yml` and are drawn by `_includes/pipeline-diagram.html` (HTML/CSS boxes, styles in `_resume.scss`).

`agent-ready-data.pdf` (linked from the agent page) is printed from `_pdf/agent-ready-data.html`, a standalone two-page A4 version of that page (not published; loads fonts from `../fonts/`). Keep both in sync and regenerate the PDF from the repo root after every change:

```bash
google-chrome --headless --no-pdf-header-footer --print-to-pdf=agent-ready-data.pdf _pdf/agent-ready-data.html
```

### JavaScript

`js/widget.js` and `css/widget.css` power the Calendly meeting-booking widget.
