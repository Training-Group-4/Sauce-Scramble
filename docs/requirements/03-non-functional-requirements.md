# 03 – Non-Functional Requirements

**Project:** Sauce Scramble: demo sauce shop (training app)
**For:** Developers, testers and trainers
**Status:** Draft v0.1
**Related:** [00 – Consolidated](00-consolidated-requirements.md) · [01 – Stories](01-product-features-user-stories.md) · [02 – UI/UX](02-ui-ux-requirements.md)

---

## How to read this document

This is a **classroom demo**, so the **targets are small** (one class, a few hundred products, free tiers).
The **practices are real**: security, privacy and data integrity are done the way a production app should do them, so learners see the right habits.

Where the demo deliberately does something differently from production, it is marked **Demo exception** and explained in [section 13](#13-demo-vs-production).

**IDs:** `NFR-<AREA>-<nn>`.

| Prefix | Area |
| --- | --- |
| `NFR-AUTH` | Authentication |
| `NFR-SEC` | Security |
| `NFR-PRIV` | Privacy and child safety |
| `NFR-DATA` | Data integrity |
| `NFR-PERF` | Performance |
| `NFR-UX` | Usability and accessibility |
| `NFR-REL` | Reliability and error handling |
| `NFR-MAINT` | Maintainability and code quality |
| `NFR-LOG` | Logging |
| `NFR-DEP` | Deployment and environments |
| `NFR-SCALE` | Scalability limits |
| `C-`, `A-` | Constraints and assumptions |

**Priority:** **Must** (required for v1) · **Should** (expected, may slip) · **Could** (if time allows).

Each requirement has a **How to check** column so it can be tested or reviewed.

---

## 1. Authentication

| ID | Priority | Requirement | How to check |
| --- | --- | --- | --- |
| NFR-AUTH-01 | Must | Use **Supabase Auth** (email + password) for all sign-ins. The app never stores, hashes or compares passwords itself. | Code review: no password columns or hashing code in `app/`. |
| NFR-AUTH-02 | Must | Password rules (minimum 8 characters) are set in **Supabase Auth settings**, not only in the UI form. | Try registering via the API with a 7-character password: it is rejected. |
| NFR-AUTH-03 | Must | Access and refresh tokens are kept **only in server-side `st.session_state`**. They are never put in URLs or query parameters, never logged and never shown on screen. | Code review + log review after a sign-in. |
| NFR-AUTH-04 | Must | Each browser session uses **its own Supabase client** carrying that user's token, so RLS runs as that user. A signed-in client is **never** stored in `st.cache_resource` or any other cache shared between users. | Code review; test: two browsers signed in as different users see only their own data. |
| NFR-AUTH-05 | Must | A user's **role is read from the `profiles` table** in the database. It is never taken from form input, query parameters or `user_metadata` (which users can edit themselves). | Code review; RLS test: a customer who edits their own metadata gains no staff rights. |
| NFR-AUTH-06 | Must | Sign-in errors use one generic message ("Email or password is incorrect"), and register errors do not reveal private details. | Manual test with wrong password and with unknown email: same message. |
| NFR-AUTH-07 | Must | Sign-out calls Supabase sign-out **and** clears all user data from `st.session_state`. | Sign out, then use browser back: no customer data visible. |
| NFR-AUTH-08 | Should | When a token expires and cannot be refreshed, the user is asked to sign in again with a friendly message; no crash. | Manually expire session (short JWT expiry in test project). |
| NFR-AUTH-09 | Should | Keep Supabase Auth's built-in **rate limits** at their defaults or stricter. | Check Auth settings in the dashboard. |
| NFR-AUTH-10 | Must | **Demo exception:** email confirmation is **turned off** so learners can sign in straight away without real emails. This is documented in `docs/deployment/` as something production must turn on. | Auth settings + deployment doc. |

---

## 2. Security

### 2.1 Row Level Security (RLS)

| ID | Priority | Requirement | How to check |
| --- | --- | --- | --- |
| NFR-SEC-01 | Must | **RLS is enabled on every table** in the `public` schema. With no matching policy, access is denied (default deny). | SQL check: no `public` table has `relrowsecurity = false`. Supabase Security Advisor shows no "RLS disabled" errors. |
| NFR-SEC-02 | Must | Policies are written **per table and per operation** (`select`, `insert`, `update`, `delete`) and match the access rules in [00 §6](00-consolidated-requirements.md#6-logical-data-model). Policies use `auth.uid()`; no policy uses `true` for writes. | Migration review against the 00 §6 table. |
| NFR-SEC-03 | Must | Role checks use small helper functions (e.g. `is_staff()`, `is_owner()`) declared `security definer` with a **fixed, empty `search_path`** (`set search_path = ''`) and fully qualified table names. | Migration review; Security Advisor shows no "function search path mutable" warnings. |
| NFR-SEC-04 | Must | Users **cannot change their own role**. The `role` column on `profiles` cannot be updated by customers or staff (column privileges or a trigger). Only the owner, via the dashboard or seed script, sets roles. | RLS test: a customer updating their own `role` to `owner` fails. |
| NFR-SEC-05 | Must | Writes that touch several tables (place order, write outbox) go through **database functions (RPC)** that check `auth.uid()` and validate every input. Clients have **no direct `insert`/`update`** on `orders`, `order_items` or `email_outbox`. | RLS test: a direct insert into `orders` from a customer client fails. |
| NFR-SEC-06 | Must | **RLS tests** in `tests/integration/` prove the key rules: a customer cannot read another customer's orders, cart or outbox emails; a customer cannot write products; staff cannot archive; guests cannot read hidden products. | Tests pass in CI against the test project. |

### 2.2 Keys and secrets

| ID | Priority | Requirement | How to check |
| --- | --- | --- | --- |
| NFR-SEC-07 | Must | The app uses **only the public anon (publishable) key**. The **service_role (secret) key** is never in the app code, the repository, `.env`, or Streamlit secrets. It is only used by trainers in the dashboard or by local admin scripts that are never deployed. | Search the repo and deployed secrets for `service_role` / secret key: none found. |
| NFR-SEC-08 | Must | **Never commit secrets.** `.env` and `.streamlit/secrets.toml` are in `.gitignore`; only the `.example` files with **empty values** are committed. | `git ls-files` shows no secret files; example files contain no values. |
| NFR-SEC-09 | Should | Turn on **GitHub secret scanning and push protection** for the repository. Optionally run a secret scanner (e.g. gitleaks) as a pre-commit hook. | Repository settings; a test commit with a fake key is blocked. |
| NFR-SEC-10 | Should | A **key rotation procedure** is written in `docs/deployment/`: if any key is exposed, rotate it in Supabase and update the Streamlit secrets the same day. Removing a key from git history is **not** enough on its own. | Procedure exists and has been practised once in class. |

### 2.3 Application security

| ID | Priority | Requirement | How to check |
| --- | --- | --- | --- |
| NFR-SEC-11 | Must | **No SQL built from strings.** All database access uses the Supabase client's query builder or RPC parameters. | Code review: no f-strings or concatenation in queries. |
| NFR-SEC-12 | Must | **No `unsafe_allow_html=True` with data from users or the database.** Product names, descriptions and outbox bodies are shown as plain text or Markdown with HTML off. `unsafe_allow_html` / `st.html` are only used for fixed CSS/markup written by developers. | Code review; test: a product named `<script>alert(1)</script>` shows as text. |
| NFR-SEC-13 | Must | **Image uploads are checked on the server side:** the Storage bucket allows only `image/jpeg`, `image/png`, `image/webp` and max **2 MB** (bucket settings). The app also checks the file opens as an image before uploading. Storage policies: public read, staff/owner write. | Upload a renamed `.txt` as `.png` and a 3 MB image: both rejected. Customer upload via API: rejected. |
| NFR-SEC-14 | Must | All traffic uses **HTTPS** (provided by Streamlit Community Cloud and Supabase). The app never calls `http://` URLs. | Code review. |
| NFR-SEC-15 | Should | **Dependencies are pinned** in `requirements.txt` and checked with `pip-audit` before each release; no known high-severity issues. | `pip-audit` output in the release checklist. |
| NFR-SEC-16 | Should | Run the **Supabase Security Advisor** after each migration milestone; zero errors, warnings reviewed. | Screenshot or note in the release checklist. |

---

## 3. Privacy and child safety

Learners may include **minors**. The app must be safe for them to use and must not collect more about them than it needs.

| ID | Priority | Requirement | How to check |
| --- | --- | --- | --- |
| NFR-PRIV-01 | Must | **Collect the minimum.** Only display name, email and a (fake) shipping address. No date of birth, no photos of people, no free-text "about me". Phone stays optional. | Schema review. |
| NFR-PRIV-02 | Must | **Younger learners never use personal emails.** They register with made-up addresses on the reserved `example.com` domain (e.g. `alex.k@example.com`), or use trainer-issued demo accounts. | Trainer guide in `docs/deployment/`; register screen note (US-501 AC6). |
| NFR-PRIV-03 | Must | **Demo notices** appear wherever real data could be entered: register, shipping, payment (doc 02 S6, S7, S9). | UI review. |
| NFR-PRIV-04 | Must | **No card data** is stored, logged or sent anywhere. Card fields are cleared from `st.session_state` after the payment step. | Code review; database and log search for demo card numbers: none found. |
| NFR-PRIV-05 | Must | **No real emails.** No SMTP or email provider is configured in the app or in Supabase Auth; all app emails go to `email_outbox`. | Supabase Auth SMTP settings empty; code review. |
| NFR-PRIV-06 | Must | **No tracking.** No analytics, ads, social widgets, or third-party embeds. Streamlit usage statistics are switched off (`browser.gatherUsageStats = false` in `.streamlit/config.toml`). | Config review; browser network tab shows calls only to the app, Supabase and allowed font CDN. |
| NFR-PRIV-07 | Must | **All content is suitable for all ages:** seed data, images, copy, sample orders and test fixtures (see doc 02 §8.2). | Content review before each release. |
| NFR-PRIV-08 | Must | **No public user-generated content.** Nothing one learner types is shown to other learners (no reviews, comments or public profiles). Outbox emails are visible only to their recipient and to staff/owner. | Feature review; RLS tests (NFR-SEC-06). |
| NFR-PRIV-09 | Must | **Customers see only their own data** (cart, orders, outbox, profile). | RLS tests (NFR-SEC-06). |
| NFR-PRIV-10 | Should | **Delete learner data after the course.** A trainer-run reset script removes all customer accounts, carts, orders and outbox rows and re-seeds the demo data. | Script exists in `scripts/` and is tested on the test project. |
| NFR-PRIV-11 | Should | **No personal data in logs** (see NFR-LOG-02). | Log review. |
| NFR-PRIV-12 | Could | An **"About this demo"** note (footer or page) says what data is stored, who can see it, and that it is deleted after the course. | UI review. |

---

## 4. Data integrity

The two key rules: **never oversell stock** and **never create a double order**.

| ID | Priority | Requirement | How to check |
| --- | --- | --- | --- |
| NFR-DATA-01 | Must | **Stock can never go below zero.** `products.stock` has a database `check (stock >= 0)` constraint. | Direct update to `-1` fails. |
| NFR-DATA-02 | Must | **No overselling, even when two people check out at once.** The `place_order` function checks and reduces stock **inside one transaction** using row locks (`select … for update`) or a conditional update (`update … set stock = stock - qty where stock >= qty`). If any line fails, the whole order rolls back. | Integration test: two customers buy the last unit of `HOT-003` at the same time; exactly one order succeeds, stock ends at 0. |
| NFR-DATA-03 | Must | **No double orders.** Checkout creates an **idempotency key** when the review step opens and sends it with "Place order". `orders.idempotency_key` is unique; a repeat call with the same key returns the existing order instead of creating a new one. The button is also disabled while processing (US-405 AC7). | Test: call `place_order` twice with the same key; one order exists. Double-click test in the UI. |
| NFR-DATA-04 | Must | **The server decides prices.** `place_order` reads prices, names and stock from `products` itself. Any price sent from the client is ignored. | Test: call the RPC with a changed price; the order uses the database price. |
| NFR-DATA-05 | Must | **Order lines keep snapshots** of product name and unit price, so later product edits do not change old orders. | Edit a product's price after ordering; old order unchanged. |
| NFR-DATA-06 | Must | **Database constraints** back up every rule: unique `sku`, `order_number`, category `name`; `price > 0`; `heat_level between 0 and 5`; `quantity between 1 and 10`; enum or check constraints for `role` and `status`; foreign keys with `on delete restrict` for products referenced by orders. | Migration review; constraint tests. |
| NFR-DATA-07 | Must | **Money is never a float.** Store as `numeric(10,2)`; use Python `Decimal` in the app; round half-up to 2 decimals only for display. | Code review; unit test: 3 × £3.35 = £10.05 exactly. |
| NFR-DATA-08 | Must | **Schema changes only through migration files** in `supabase/migrations/`. No manual table changes in the dashboard. | Fresh project built from migrations matches the demo project. |
| NFR-DATA-09 | Should | Order numbers come from a **database sequence** (e.g. `SS-2026-000123`), so they are unique without app-side counting. | Migration review. |
| NFR-DATA-10 | Should | `updated_at` and `updated_by` on products are set by a **database trigger**, not by the app. | Edit a product via the API without these fields; they are still set. |
| NFR-DATA-11 | Should | All timestamps use `timestamptz` stored in UTC and are shown in UK time (Europe/London). | Schema review; UI check. |

---

## 5. Performance

Targets are for the demo scale in [section 11](#11-scalability-limits), measured on Streamlit Community Cloud **after the app is awake** (cold start not included).

| ID | Priority | Requirement | How to check |
| --- | --- | --- | --- |
| NFR-PERF-01 | Must | The catalog (S2) shows products within **3 seconds** with seed data (US-101 AC6). | Manual timing, 5 tries, all ≤ 3 s. |
| NFR-PERF-02 | Should | Common interactions (filter, sort, add to cart, change quantity) finish within **1 second** in most tries. | Manual timing. |
| NFR-PERF-03 | Must | "Place order" completes within **3 seconds**. | Manual timing. |
| NFR-PERF-04 | Should | **Public catalog data is cached** with `st.cache_data` (short TTL, e.g. 60 s) and cleared when staff save a product. **Per-user data is never put in shared caches** (see NFR-AUTH-04). | Code review. |
| NFR-PERF-05 | Should | Search, filters, sorting and pagination run **in the database query** (`ilike`, `eq`, `order`, `range`), not by loading every row into Python. | Code review. |
| NFR-PERF-06 | Could | Product images are delivered as **WEBP or JPG ≤ 300 KB** at 800 × 800 px (designer export rule, doc 02 §3.4). | Check file sizes in the bucket. |

---

## 6. Usability and accessibility

Details are in [doc 02 §7](02-ui-ux-requirements.md#7-accessibility-and-mobile). These are the testable rules.

| ID | Priority | Requirement | How to check |
| --- | --- | --- | --- |
| NFR-UX-01 | Must | Meet **WCAG 2.2 level AA** where Streamlit allows; known Streamlit gaps are listed in the release notes instead of hidden. | axe DevTools (or similar) on S2, S3, S4, S7, S9: no "critical" or "serious" issues caused by our code. |
| NFR-UX-02 | Must | Text contrast at least **4.5:1** (large text/UI parts **3:1**), using the doc 02 tokens. | Contrast checker on every token pair. |
| NFR-UX-03 | Must | **Never colour alone** for heat, stock, status or errors. | UI review in greyscale. |
| NFR-UX-04 | Must | Every input has a **visible label**; every error says what is wrong and how to fix it. | UI review. |
| NFR-UX-05 | Must | All actions work **by keyboard**; focus is always visible (custom CSS must not remove outlines). | Keyboard-only run of flows F1 and F3. |
| NFR-UX-06 | Must | Product images have **alt text** (product name + short description). | Code review. |
| NFR-UX-07 | Must | Screens are usable at **375 px** and **1280 px** wide. | Browser device toolbar check of every screen. |
| NFR-UX-08 | Should | Works in the **latest versions** of Chrome, Edge, Firefox and Safari. | Smoke test (F1, F3) in each. |
| NFR-UX-09 | Should | Copy follows the **plain-English, all-ages** rules in doc 02 §8. | Content review. |

---

## 7. Reliability and error handling

| ID | Priority | Requirement | How to check |
| --- | --- | --- | --- |
| NFR-REL-01 | Must | **Users never see raw tracebacks** in the deployed app. Set `client.showErrorDetails` in `.streamlit/config.toml` to hide details in the cloud; show full details only locally. | Force an error in the deployed app: friendly message only. |
| NFR-REL-02 | Must | **Every Supabase call is handled:** on failure the user sees a friendly message with what to do next (doc 02 S16), and the error is logged with a short **reference ID** that is also shown to the user. | Turn off network / use a wrong URL in test: friendly message + reference ID. |
| NFR-REL-03 | Must | **Failures leave data consistent.** A failed checkout creates no order, changes no stock and keeps the cart (NFR-DATA-02). | Integration test with a forced failure inside `place_order`. |
| NFR-REL-04 | Must | **Missing configuration fails clearly.** If a required secret is missing at startup, the app shows "Configuration missing: SUPABASE_URL" (names only, never values) and stops. | Start without secrets. |
| NFR-REL-05 | Should | Database calls have a **timeout** (about 10 s) and show "We couldn't reach the shop. Please try again." with a retry button. | Simulated slow network. |
| NFR-REL-06 | Should | **Session loss is safe.** Refreshing the page starts a new Streamlit session: signed-in carts survive (stored in the database); guest carts are lost. This is accepted (A-04). | Manual test. |
| NFR-REL-07 | Should | The **seed and reset scripts are idempotent:** running them twice gives the same result with no errors or duplicates. | Run twice on the test project. |
| NFR-REL-08 | Must | **No uptime promise.** Best-effort availability only; the trainer wakes the app and Supabase project before each class (NFR-DEP-08). | – |

---

## 8. Maintainability and code quality

Code is also **teaching material**, so clarity matters more than cleverness.

| ID | Priority | Requirement | How to check |
| --- | --- | --- | --- |
| NFR-MAINT-01 | Must | **Follow the folder structure** in the root `README.md`. Pages and components never call Supabase directly; **all database, auth and storage calls live in `app/services/`**. | Code review: no `supabase` imports in `app/pages/` or `app/components/`. |
| NFR-MAINT-02 | Must | Settings and secrets are loaded in **one place** (`app/config/`). Demo settings (shipping fee, limits) are named constants there. | Code review. |
| NFR-MAINT-03 | Must | **Python version is pinned** (3.12) for local work, CI and Community Cloud. | CI config + Community Cloud advanced settings. |
| NFR-MAINT-04 | Must | **Dependencies pinned** to exact versions in `requirements.txt` at the repo root. | File review. |
| NFR-MAINT-05 | Must | **Unit tests** (pytest, no network) cover business rules: totals, shipping fee, cart merge, quantity limits, validation, price rounding. | `pytest tests/unit` passes. |
| NFR-MAINT-06 | Should | Unit-test coverage of `app/services`, `app/models` and `app/utils` is at least **70 %**. | `pytest --cov` report. |
| NFR-MAINT-07 | Should | **Integration tests** run against a separate **test** Supabase project (or local Supabase via the CLI), including the RLS tests (NFR-SEC-06) and concurrency test (NFR-DATA-02). | `pytest tests/integration` passes. |
| NFR-MAINT-08 | Should | **Linting and formatting** with Ruff; zero errors. | `ruff check` and `ruff format --check` pass. |
| NFR-MAINT-09 | Should | **CI** (GitHub Actions) runs Ruff and unit tests on every push and pull request. | Green CI badge. |
| NFR-MAINT-10 | Should | **Type hints** on all functions in `services`, `models` and `utils`; models are dataclasses or Pydantic models. | Code review (optional: mypy/pyright with no errors). |
| NFR-MAINT-11 | Should | Code comments explain **why**, not what. Each service module has a short docstring. Non-obvious security choices (RLS, idempotency, no shared clients) are explained where they appear. | Code review. |
| NFR-MAINT-12 | Must | **No secrets or real personal data in tests or fixtures.** Tests use `example.com` accounts and seed-style data. | Code review. |
| NFR-MAINT-13 | Should | Changes go through **pull requests** into a protected `main` branch with at least one review. | Repository settings. |

---

## 9. Logging

| ID | Priority | Requirement | How to check |
| --- | --- | --- | --- |
| NFR-LOG-01 | Must | Use Python's **`logging` module** (no `print`), configured once in `app/config/`. Level comes from a `LOG_LEVEL` setting: `INFO` in the cloud, `DEBUG` allowed locally. | Code review. |
| NFR-LOG-02 | Must | **Never log secrets or personal data:** no passwords, tokens, keys, card numbers or addresses. Emails are masked (`c***@example.com`); users are identified by user ID. | Log review after running flows F1–F9. |
| NFR-LOG-03 | Should | **Log key events** at `INFO`: sign-in success/failure, sign-out, order placed (order number, user ID), payment declined, product created/updated/hidden/archived (who, which SKU), image uploaded. | Log review. |
| NFR-LOG-04 | Must | **Log unexpected errors** at `ERROR` with stack trace (server-side only) and the reference ID shown to the user (NFR-REL-02). | Forced error appears once in logs with the same reference ID. |
| NFR-LOG-05 | Should | Permission errors from RLS (e.g. `42501`) are logged at `WARNING` with user ID and action, so trainers can show RLS working. | Trigger a blocked action; check log. |
| NFR-LOG-06 | Could | Lightweight **audit trail** for products via `updated_by` / `updated_at` (NFR-DATA-10). No separate audit table in v1. | – |

**Where logs live:** app logs in Streamlit Community Cloud ("Manage app" → logs); database, auth and storage logs in the Supabase dashboard. Platform default retention; logs are not exported.

---

## 10. Deployment and environments

| ID | Priority | Requirement | How to check |
| --- | --- | --- | --- |
| NFR-DEP-01 | Must | **Three environments, each with its own Supabase project:** **local** (developer's own project or local Supabase CLI), **test** (integration tests and CI), **demo** (the Streamlit Community Cloud app used in class). Integration tests **never** run against the demo project. | Environment table in `docs/deployment/`. |
| NFR-DEP-02 | Must | **Configuration only from the environment:** locally from `.env` or `.streamlit/secrets.toml`; in the cloud from the app's **Secrets** settings. No environment-specific values in code. | Code review. |
| NFR-DEP-03 | Must | The demo app deploys from the **`main` branch on GitHub**, entry point **`app/main.py`**, with `requirements.txt` at the repo root and the pinned Python version. | Community Cloud app settings. |
| NFR-DEP-04 | Must | **Migrations are applied before the app is deployed** (e.g. `supabase db push`), test project first, then demo. Seed data is loaded only on test and demo projects. | Release checklist. |
| NFR-DEP-05 | Must | `.streamlit/config.toml` (theme, `gatherUsageStats = false`, error details setting) **is committed**; `.streamlit/secrets.toml` **is not**. | `git ls-files`. |
| NFR-DEP-06 | Should | A **release checklist** in `docs/deployment/`: CI green, `pip-audit` clean, migrations applied, Security Advisor checked, secrets set, smoke test of flows F1, F3 and F9 on the deployed app. | Checklist exists and is used. |
| NFR-DEP-07 | Should | **Rollback:** redeploy the previous commit. Migrations are forward-only; a mistake is fixed with a new migration, never by editing an applied one. | Documented in `docs/deployment/`. |
| NFR-DEP-08 | Should | **Before each class**, the trainer opens the app (Community Cloud apps sleep when unused) and checks the Supabase project is not paused (free projects pause after a period of inactivity). | Trainer checklist. |
| NFR-DEP-09 | Should | When used with minors, **restrict who can view the app** using Community Cloud's sharing settings where available. If the app must be public, it still contains only demo data. | App sharing settings; see open question in doc 00. |

---

## 11. Scalability limits

The app is built for **one class at a time**. These limits are deliberate; going beyond them needs redesign, not just more hardware.

| ID | Priority | Requirement / limit | How to check |
| --- | --- | --- | --- |
| NFR-SCALE-01 | Must | Supports up to **30 concurrent users** (one class) while meeting the performance targets in section 5. | Classroom trial with 30 users running F1 and F3. |
| NFR-SCALE-02 | Must | Designed for up to **500 products**, **50 categories**, **2,000 customers** and **10,000 orders**. | Load the test project with this volume; catalog still within NFR-PERF-01. |
| NFR-SCALE-03 | Must | Stays within **free tiers**: Supabase Free plan and Streamlit Community Cloud. No paid add-ons. | Billing pages show free plan. |
| NFR-SCALE-04 | Should | **Storage use** stays well under the free limit: images ≤ 2 MB each (target ≤ 300 KB), one image per product. | Bucket size in dashboard. |

**Known limits (for teaching):**

- Streamlit keeps **one session per open browser tab in server memory** and **reruns the script on every interaction**. This is simple to build with, but limits how many users one app instance can serve.
- Community Cloud runs **one instance** with limited CPU and memory, and **sleeps** when unused.
- Supabase Free projects have limits on database size, storage, and monthly active users, and **pause** when inactive. Check current limits on the Supabase pricing page.
- Not designed for: high traffic, flash sales, multiple regions, background jobs, or real-time stock updates between users.

---

## 12. Constraints and assumptions

### 12.1 Constraints (fixed decisions)

| ID | Constraint |
| --- | --- |
| C-01 | **Stack:** Python, Streamlit, Supabase (Postgres, Auth, Storage), Streamlit Community Cloud. No other backend server. |
| C-02 | **Free tiers only.** No paid services or add-ons. |
| C-03 | **No real payments and no real emails** (doc 01). |
| C-04 | **All-ages audience.** Learners may be minors; content and data handling must suit them (section 3). |
| C-05 | **The app never holds the service_role key** (NFR-SEC-07). Features that need it (e.g. password reset links, US-506) are deferred or done by trainers in the dashboard. |
| C-06 | **Streamlit's components and layout limits** apply (doc 02 §9). No custom JavaScript components in v1. |
| C-07 | **Streamlit and Python versions are pinned** at the start of M0 and only changed deliberately. |
| C-08 | **GBP, UK English, Europe/London time zone.** |

### 12.2 Assumptions

| ID | Assumption | If wrong… |
| --- | --- | --- |
| A-01 | The trainer has a Supabase account and a GitHub account and creates the test and demo projects. | Setup guide must include account creation. |
| A-02 | Learners have supervised internet access and a modern browser; learners who write code have GitHub accounts. | Provide trainer-run demo only. |
| A-03 | Class size is 30 or fewer. | Re-check NFR-SCALE-01; split classes across several demo apps/projects. |
| A-04 | Losing a guest cart on page refresh is acceptable for a demo. | Add a cookie-based guest cart (needs a custom component; out of scope). |
| A-05 | One trainer account acts as owner; one or two act as staff. | Adjust seed script. |
| A-06 | Free-tier limits stay roughly as they are today. | Revisit section 11 and C-02. |
| A-07 | Learner data has no value after the course and can be deleted (NFR-PRIV-10). | Agree a retention rule with the organisation. |

---

## 13. Demo vs production

So learners know what a real shop would add.

| Topic | This demo | A production shop would… |
| --- | --- | --- |
| Email confirmation | Off (NFR-AUTH-10) | Require confirmed emails; use a real email provider |
| Multi-factor sign-in | None | Offer MFA, require it for staff/owner |
| Payments | Fake card numbers | Use a payment provider (card data never touches our servers; PCI DSS) |
| Emails | Saved to outbox | Send via a transactional email service, with bounce handling |
| Password reset | Deferred; trainer resets | Self-service reset by email |
| Backups | Free-tier defaults only | Point-in-time recovery, tested restores |
| Monitoring | Platform logs | Error tracking, uptime alerts, dashboards |
| Hosting | One Community Cloud instance | Scalable hosting, staging environment, custom domain |
| Legal | Demo notices | Privacy policy, terms, cookie notice, data protection compliance |

---

## Summary

| Area | Must | Should | Could | Total |
| --- | --- | --- | --- | --- |
| Authentication (AUTH) | 8 | 2 | 0 | 10 |
| Security (SEC) | 12 | 4 | 0 | 16 |
| Privacy & child safety (PRIV) | 9 | 2 | 1 | 12 |
| Data integrity (DATA) | 8 | 3 | 0 | 11 |
| Performance (PERF) | 2 | 3 | 1 | 6 |
| Usability & accessibility (UX) | 7 | 2 | 0 | 9 |
| Reliability (REL) | 5 | 3 | 0 | 8 |
| Maintainability (MAINT) | 6 | 7 | 0 | 13 |
| Logging (LOG) | 3 | 2 | 1 | 6 |
| Deployment (DEP) | 5 | 4 | 0 | 9 |
| Scalability (SCALE) | 3 | 1 | 0 | 4 |
| **Total** | **68** | **33** | **3** | **104** |
