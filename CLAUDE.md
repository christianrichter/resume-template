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

## Architecture

This is a **Jekyll static site** — a personal resume/CV for Christian Richter (Data Engineer). The architecture separates content (YAML data) from presentation (HTML/SCSS).

### Content lives in `_data/`

All resume content is stored as YAML files: `experience.yml`, `projects.yml`, `skills.yml`, `education.yml`, `services.yml`, `references.yml`, `links.yml`. Editing resume content means editing these files.

### Presentation in `_layouts/` and `_includes/`

- `_layouts/resume.html` — the main resume template; iterates over `_data/` files using Liquid templating
- `_includes/` — reusable partials (head, navigation, footer, icon-links)

### Styling in `_sass/`

SCSS partials compiled via `css/main.scss`. Key files: `_resume.scss` (resume-specific), `_variables.scss`, `_layout.scss`.

### Global configuration in `_config.yml`

Controls site metadata, contact info, the intro paragraph (`resume_header_intro`), and boolean toggles to show/hide each resume section (e.g., `resume_section_projects: true`).

### Static pages

`services.html`, `impressum.html`, `dsgvo.html` are standalone Jekyll pages outside the main resume layout.

### JavaScript

`js/widget.js` and `css/widget.css` power the Calendly meeting-booking widget.
