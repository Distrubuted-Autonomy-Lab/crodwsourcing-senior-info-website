# Carebot Connect — Sprint 3 FAQ

This FAQ is for **end users** of the public site, plus notes for **developers and operators** on setup friction and reliance on third-party services.

**Repo scope:** The same codebase includes **crowdsourcing** (submit → moderation → merge/reject, similarity under `recommendation/`) and an **optional public chat** (`chat/`). **`GOOGLE_API_KEY` / Google Gemini is only for chat**—it is **not required** to run submissions or moderation.

---

## End-user questions

### 1. Do I need to create an account or log in to submit a resource?

**No.** You can submit a caregiver resource without signing up. Only staff who review listings use the login system. If you can fill out a web form, you can contribute.

### 2. What happens after I click “Submit for Review,” and will I get a confirmation email?

Your submission is queued for a moderator. They check accuracy and whether the resource fits the directory before it goes public. **The system does not send email confirmations today.** If you want to verify your entry later, search the directory after a few days or contact the team through the main Carebot page.

### 3. I spent a long time on the form and lost everything. Why?

**The form does not save your work until you submit.** If you close the tab, refresh before submitting, or lose connectivity, unsaved fields can be lost. For long entries, draft your notes elsewhere first, then copy them in and submit in one session.

### 4. How can I tell if a resource is already listed before I duplicate it?

Use the **similarity search** on the home page: describe the service in plain language (for example, “meals for seniors in Tuscaloosa”). If you see a close match, you may not need a new submission. If you still submit something that already exists, moderators can merge duplicates—nothing harmful happens.

---

## Setup gotchas (installing and running the application)

These are issues teams often hit when bringing the stack up locally or in a container environment:

- **Environment variables:** You need **database settings** and Django’s **`SECRET_KEY`** (and related core vars) via files such as `.env-local`. **`GOOGLE_API_KEY` is optional:** only the **public chat** feature (`chat/`) calls Gemini; **crowdsourcing flows do not use it.** If the key is missing, chat may fail when used—the **submit and moderation** paths still run. Copy from the project’s example env template and fill in real values.
- **Database extensions:** PostgreSQL is used with **PostGIS** (and optionally **pgvector**). A plain Postgres image without those extensions will not match production behavior; use the project’s compose or documented image so extensions are available.
- **Docker networking vs. localhost:** Settings may default to `localhost` for services like the message broker, while containers talk to each other by **service name** (for example `rabbitmq`). If Celery or the broker is enabled, broker URLs must match how you run Compose (hostname `rabbitmq` inside the network vs. `localhost` on the host).
- **“Optional” pieces are still wired in config:** RabbitMQ and Celery paths may be present for future async work. Even if you are not running workers, be aware of `CELERY_BROKER_URL` and related settings so you do not assume a single-process app when debugging.

---

## External services and operational risks

The product depends on **PostgreSQL** (with PostGIS / pgvector for crowdsourcing and similarity). Optional services add more risk surface:

| Dependency | What can go wrong | Mitigation mindset |
|------------|-------------------|--------------------|
| **Google Gemini (optional — chat only)** | If you enable public chat: rate limits, billing, outages, or model deprecation change **chat** behavior or latency. **Does not affect** submit, queue, merge, or similarity. | Monitor usage and quotas; set `GEMINI_MODEL` intentionally after testing; degrade chat UI gracefully on API errors. |
| **API keys in general** | Leaked or committed keys, wrong project restrictions. | Keys only in environment/secrets; restrict keys by IP or usage where possible; rotate after incidents. |
| **Hosted PostgreSQL (if not local)** | Connection limits, maintenance windows, version upgrades. | Use connection pooling appropriate to your plan; test backups and restore. |
| **Third-party subprocessors (when chat is on)** | Sponsor or policy constraints (e.g. health/education data) may limit what can be sent to cloud LLM APIs. | Align **chat** traffic and logging with sponsor requirements before production use. |

---

*Last updated: April 2026 — Sprint 3 deliverable.*
