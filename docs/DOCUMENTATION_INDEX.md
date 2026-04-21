# Project documentation index (GitHub + optional Read the Docs)

**Start here (course rubric):** **[Final project](finalproject.html)** — sprint deliverable panels (syllabus expectations are summarized in the **Documentation** panel, **documentation hub**, and sprint pages). **Rubric narrative** (sprint map, install, deploy, features, extend, FAQs summary, testing, security, accessibility): **[documentation hub](documentation-hub.html)**. **Backlog tables** (B1–B14 + sprint rows): **[backlog-tables.html](backlog-tables.html)**.

**Course expectation:** Documentation should live **in GitHub** (versioned, next to source) for someone with basic CS knowledge who does not know your project. Optional: publish with **[Read the Docs](https://docs.readthedocs.io/en/stable/tutorial/index.html)** (community tier is free) or **[MkDocs](https://www.mkdocs.org/getting-started/)** — import this repo.

**Collaborators:** Add **Dr. Anderson** (`monicadelaine`) and the **TA** (`robello2@crimson.ua.edu`) to the GitHub repo. Submit the repo URL via the course assignment link.

---

## Assign one primary owner per section (edit names)

| Section | Owner | Where it lives (suggested) |
|--------|--------|----------------------------|
| **Final project** — sprint deliverable panels | team | **[website](finalproject.html)** (start here) |
| **Course documentation hub** — sprint map, install, deploy, rubric sections | team | **[website](documentation-hub.html)** |
| **Run / deploy** — Docker, API keys, OAuth workflow (if any) | *assign* | **[Placeholder — website](deploy-run.html)** · `carebot-deployable-crowdsourcing/root/README.md` |
| **User guide (caregivers)** — submit flow, plain language | *assign* | **[website](user-guide.html)** · **[Markdown](userguide.md)** |
| **User guide (staff/sponsor section)** — screenshots, how to use features | *assign* | **[website](user-guide.html#staff)** |
| **Accessibility design & research** — Sprint 3 artifact (WCAG, caregivers, gaps) | Cole Segura | **[website](accessibility-research.html)** · **[Markdown](AccessibilityResearch.md)** |
| **Development environment** — dependencies, OS, first build | *assign* | **[Placeholder — website](development-environment.html)** · `root/README.md` |
| **Development guide** — full technical onboarding (stack, structure, workflows) | team | **[website](development-guide.html)** · **[Markdown](DevelopmentGuide.md)** |
| **Deployment guide** - full guide to cloud deployment | Jarl Evanson | **[deployment.html]** . **[Markdown](CLOUD_DEPLOYMENT.md)** |
| **FAQs** — APIs, OAuth, component services | *Sheala Miller (edit if different)* | **[Live — website](faq-component-services.html)** · [`carebot-deployable-crowdsourcing/docs/FAQ-component-services.md`](https://github.com/Distrubuted-Autonomy-Lab/carebot-deployable-crowdsourcing/blob/main/docs/FAQ-component-services.md) |
| **Test plan** — cases + scripts | *assign* | **[Placeholder — website](test-plan.html)** · `docs/TEST_PLAN.md` + `scripts/retest-local.sh` |
| **Final cybersecurity assessment** | *assign* | **[Placeholder — website](cybersecurity-assessment.html)** |
| **Full project backlog** (B1–B14 + sprint rows) | team | **[website — Backlog tables](backlog-tables.html#full-project-backlog)** |
| **Sprint planning** | team | **[Sprint 1](sprint1.html)** · **[Sprint 2](sprint2.html)** · **[Sprint 3](sprint3.html)** (includes final checklist) |

---

## Required topics (from syllabus)

### How to install software
- OS prerequisites, Docker Desktop, Git, Python (if native run).
- Link to `requirements.txt` / `Dockerfile`.

### How to install / deploy software
- **Docker Compose** from `root/` (`docker-compose.yml` + `docker-compose.local.yml`).
- **Environment:** `.env-local` / secrets — **never commit** real keys.
- **External resources:** Postgres, optional RabbitMQ, **Hugging Face** / embedding model (rate limits; **HF_TOKEN** optional), **Google APIs** if used (tier: free/paid note).

### How to use each completed feature
- **Caregiver-facing:** [`user-guide.html`](user-guide.html) · [`userguide.md`](userguide.md) — submit flow in plain language.
- **Sponsor / stakeholder:** [`user-guide.html#staff`](user-guide.html#staff).
- Link features to **Sprint 1** ([`sprint1.html`](sprint1.html)) and **Sprint 2** ([`sprint2.html`](sprint2.html)) deliverables (submit, moderate, merge, similar search, directory).
- User types (public vs staff/moderator) if applicable.
- How flows connect (e.g. submit → queue → approve/merge).

### How to modify / extend software
- Languages: Python (Django), HTML/CSS/JS, SQL.
- Build: Docker images; `manage.py` migrations.
- **Dependencies:** `requirements.txt`, `uv`/`pip` in Dockerfile.
- **Backlog / bugs:** GitHub Issues or `docs/` — major migrations (e.g. Django major version, API upgrades).
- **Style:** Black/flake8 or team convention — document in `CONTRIBUTING.md`.

### FAQs (3–4+ for end users + devs)
- Install failures, `.env` mistakes, “why doesn’t similarity show my import?” (embeddings / schema).
- External API limits, OAuth redirect URIs (if used).

### Testing
- **Components to test:** web, PostGIS, similarity, forms.
- **Cases:** describe critical paths; link `recommendation.tests`, `scripts/retest-local.sh`.
- **Automated:** `python manage.py test` (limitations: unmanaged GIS tables — document).

---

## Helpful external links
- [Read the Docs tutorial](https://docs.readthedocs.io/en/stable/tutorial/index.html) · [Pricing (community)](https://about.readthedocs.com/pricing/#/community)
- [API documentation best practices](https://blog.hubspot.com/website/api-documentation) · [FreeCodeCamp API docs](https://www.freecodecamp.org/news/how-to-write-api-documentation-like-a-pro/)

---

*Update the owner column and file paths as your team creates them.*
