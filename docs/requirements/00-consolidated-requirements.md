# 00 – Consolidated Requirements (start here)

**Project:** Sauce Scramble: demo sauce shop (training app)
**Stack:** Streamlit (UI) · Supabase (Postgres, Auth, Storage) · Streamlit Community Cloud (deployment)
**Status:** Draft v0.1 · 9 Nov 2026

This page summarises all requirement documents. For detail, follow the links.

| Doc | Content |
| --- | --- |
| [01 – Product features and user stories](01-product-features-user-stories.md) | Epics E1–E6, stories US-101…US-609, seed data, out of scope |
| [02 – UI/UX requirements](02-ui-ux-requirements.md) | Vision, visual direction, screens S1–S16, Streamlit limits |
| [03 – Non-functional requirements](03-non-functional-requirements.md) | 104 NFRs (`NFR-<AREA>-nn`) across 11 areas, constraints `C-`, assumptions `A-`, demo vs production |

---

## 1. Goals

1. **Teach** how to build and deploy a realistic web app with Streamlit and Supabase.
2. **Demo** a complete shopping journey: browse → cart → sign in → checkout → confirmation.
3. **Show safe practice:** role-based access (RLS), secrets handling, no real payments, no real emails.
4. **Be repeatable:** any trainee can create a Supabase project, run migrations and seed data, and deploy to Streamlit Community Cloud.
5. **Be suitable for all ages,** including younger learners.

---

## 2. Users and roles

| Role | Who | Can |
| --- | --- | --- |
| **Guest** | Not signed in | Browse, search, view products, use a session cart, register, sign in |
| **Customer** | Signed-in shopper (default role for sign-ups) | Guest actions + saved cart, checkout, own orders and own outbox emails |
| **Staff** | Shop worker (created by seed/dashboard) | Manage products, images, stock, hide/show; view outbox. Cannot place orders. |
| **Owner** | Shop admin (created by seed/dashboard) | Staff actions + archive products, manage categories |

One role per user. Roles are enforced in the database with **Row Level Security (RLS)**, not only in the UI.

---

## 3. Feature summary

41 stories: **27 Must · 7 Should · 7 Could.**

| Epic | Feature | Stories | M / S / C | Key points |
| --- | --- | --- | --- | --- |
| **E1** | Product search and catalog | US-101–107 | 3 / 2 / 2 | Active products only; keyword search; category & heat filters; sort; 12 per page |
| **E2** | Product detail | US-201–204 | 3 / 0 / 1 | Full details, stock status, allergens (demo disclaimer), quantity 1–min(stock,10) |
| **E3** | Add to cart | US-301–306 | 4 / 2 / 0 | Merge same product; capped by stock; guest cart in session, customer cart in DB; warn on price/stock changes |
| **E4** | Checkout and order confirmation | US-401–409 | 7 / 1 / 1 | Customers only; demo card numbers; atomic order + stock; price snapshot; email to **outbox**, never sent |
| **E5** | Customer register / sign in | US-501–506 | 4 / 0 / 2 | Supabase Auth; email confirmation off; role-protected pages |
| **E6** | Owner and staff product management | US-601–609 | 6 / 2 / 1 | Add/edit, image upload (≤2 MB), stock, hide/show; owner archives and manages categories |

---

## 4. UI/UX summary

- **Vision:** *"Exciting, yet comfortable, with a warm colour palette. It should make people curious to buy the sauce."*
- **Core idea:** *exciting moments, comfortable base.* Bold colour and big type in a few places (hero, category tiles, product names, heat). Calm cream backgrounds and plain, clear facts elsewhere. Home is the boldest screen, checkout calmer, admin calmest.
- **Palette (suggested):** cream base `#FFF8F0`, paprika primary `#B5451B`, chilli orange highlight `#E8542A` (large text/decoration only), mustard accent `#E0A526`, roasted brown `#3A1F14`, text `#2B2118`. Teal `#1F5F7A` only for "Demo only" banners.
- **Type:** bold display font for headings/product names; Source Sans 3 (Streamlit default) for body.
- **Heat scale:** 0–5 chilli icons **plus** a text label.
- **Screens:** S1 Home · S2 Shop · S3 Product detail · S4 Cart · S5 Sign in · S6 Register · S7 Shipping · S8 Review · S9 Payment (demo) · S10 Confirmation · S11 My orders · S12 Profile · S13 Manage products · S14 Product editor · S15 Outbox · S16 System states.
- **Streamlit limits to respect:** no clickable whole cards, no hover-only content, no sticky bars, no animations/carousels, no input masks, validation after submit, keep custom CSS small.
- **Accessibility & mobile:** WCAG 2.2 AA where possible; design at 375 px and 1280 px.

---

## 5. Non-functional summary

Full detail in [03 – Non-functional requirements](03-non-functional-requirements.md). **Small targets, real practices.** 104 requirements: **68 Must · 33 Should · 3 Could.**

| Area | IDs | M / S / C | Key points |
| --- | --- | --- | --- |
| Authentication | NFR-AUTH-01–10 | 8 / 2 / 0 | Supabase Auth only; tokens only in server-side session; **one Supabase client per user, never in a shared cache**; role read from `profiles`, never `user_metadata`; email confirmation off (demo exception) |
| Security | NFR-SEC-01–16 | 12 / 4 / 0 | **RLS on every table**, default deny, per-operation policies; `security definer` helpers with empty `search_path`; users can't change their own role; multi-table writes via RPC only; RLS tests; **anon key only, never service_role; never commit secrets**; no `unsafe_allow_html` with data; bucket-level upload limits |
| Privacy & child safety | NFR-PRIV-01–12 | 9 / 2 / 1 | Minimum data; younger learners use `example.com` addresses; no card data, no real emails, no tracking (`gatherUsageStats = false`); all-ages content; no public user content; delete learner data after the course |
| Data integrity | NFR-DATA-01–11 | 8 / 3 / 0 | `stock >= 0` constraint; **no overselling** (locking in one transaction); **no double orders** (idempotency key); server decides prices; snapshots; `numeric` + `Decimal`, never float; schema only via migrations |
| Performance | NFR-PERF-01–06 | 2 / 3 / 1 | Catalog ≤ 3 s; interactions ≤ 1 s; place order ≤ 3 s; cache public catalog only; filter/paginate in the database |
| Usability & accessibility | NFR-UX-01–09 | 7 / 2 / 0 | WCAG 2.2 AA where possible; contrast; never colour alone; labels; keyboard; alt text; 375 px and 1280 px |
| Reliability | NFR-REL-01–08 | 5 / 3 / 0 | No raw tracebacks; friendly errors with reference ID; failures leave data consistent; clear "configuration missing"; no uptime promise |
| Maintainability | NFR-MAINT-01–13 | 6 / 7 / 0 | All Supabase calls in `app/services/`; pinned Python 3.12 and dependencies; pytest unit + integration; 70 % coverage; Ruff; CI; protected `main` |
| Logging | NFR-LOG-01–06 | 3 / 2 / 1 | `logging` module; **no secrets or personal data in logs** (masked emails); key events; errors with reference ID; RLS denials as warnings |
| Deployment | NFR-DEP-01–09 | 5 / 4 / 0 | Separate local / test / demo Supabase projects; config from environment only; deploy `main` → `app/main.py`; migrations before deploy; release checklist; wake app before class |
| Scalability | NFR-SCALE-01–04 | 3 / 1 / 0 | ≤ 30 concurrent users, ≤ 500 products, ≤ 10,000 orders; free tiers only |

Constraints (`C-01`–`C-08`) and assumptions (`A-01`–`A-07`) are in [03 §12](03-non-functional-requirements.md#12-constraints-and-assumptions).

---

## 6. Logical data model

Supabase Auth provides `auth.users`. All tables below live in `public` with RLS on.

| Table | Key fields | Notes / main rules |
| --- | --- | --- |
| **profiles** | `id` (PK, = `auth.users.id`), `display_name`, `role` (`customer`/`staff`/`owner`), default address fields, `created_at` | Created on sign-up with role `customer`. Users read/update their own row (not `role`). |
| **categories** | `id` (PK), `name` (unique), `slug` (unique) | Public read; owner write. Cannot delete if products exist. |
| **products** | `id` (PK), `sku` (unique), `name`, `category_id` (FK), `description`, `ingredients`, `allergens` (text[]), `size_ml`, `heat_level` (0–5), `price` (numeric 10,2, > 0), `stock` (int ≥ 0), `status` (`active`/`hidden`/`archived`), `image_path`, `updated_by` (FK profiles), `created_at`, `updated_at` | Public read of `active` only; staff/owner read all and write; only owner sets `archived`. Never hard-deleted if ordered. |
| **carts** | `id` (PK), `customer_id` (FK, unique), `updated_at` | One saved cart per customer. Guest carts live in Streamlit session state only. |
| **cart_items** | `cart_id` (FK), `product_id` (FK), `quantity` (1–10), `price_when_added`, PK (`cart_id`, `product_id`) | `price_when_added` lets the cart detect price changes (US-306). |
| **orders** | `id` (PK), `order_number` (unique, e.g. `SS-2026-000123`), `customer_id` (FK), `status` (`placed`), shipping address fields (snapshot), `subtotal`, `shipping_fee`, `total`, `idempotency_key` (unique), `created_at` | Customer reads own; staff/owner read all. Created only via a database function. |
| **order_items** | `id` (PK), `order_id` (FK), `product_id` (FK), `product_name` (snapshot), `unit_price` (snapshot), `quantity`, `line_total` | Same access as parent order. |
| **email_outbox** | `id` (PK), `recipient_user_id` (FK), `recipient_email`, `kind` (`order_confirmation`/`password_reset`), `subject`, `body` (plain text), `order_id` (FK, nullable), `status` (`not_sent`), `created_at` | Customer reads own; staff/owner read all. Insert only by database function. |
| **Storage: `product-images`** | object path = `products/<sku>.<ext>` | Public read; staff/owner upload/replace. |

```
auth.users 1─1 profiles 1─0..1 carts 1─* cart_items *─1 products *─1 categories
                  │                                          │
                  └─1─* orders 1─* order_items *─────────────┘
                          └─1─* email_outbox
```

Demo settings (shipping fee £3.99, free from £30, max qty 10, low stock ≤ 5) live in `app/config`, not the database.

---

## 7. Scope

**In scope for v1:** all **Must** and **Should** stories (34), plus screens S1–S16 at the level each story needs. **Could** stories (7) are built only if time allows.

**Out of scope** (full list in [01 §6](01-product-features-user-stories.md#6-out-of-scope)): real payments, real emails, real shipping, tax/VAT, multiple currencies/languages, discounts, reviews, wishlists, product variants, refunds/cancellations, staff order-status updates, social sign-in and 2FA, in-app staff account management, reports/dashboards, mobile app, production hardening.

---

## 8. Open questions (with default answers)

If nobody answers by the start of the related milestone, **the default applies**.

| # | Question | Default answer |
| --- | --- | --- |
| Q1 | ~~Doc 03 (non-functional requirements) is missing.~~ | **Answered:** doc 03 written (9 Nov 2026). The provisional `NFR-01`–`11` IDs are retired. |
| Q2 | What is the shop's display name? | "Sauce Scramble" (the repository name). |
| Q3 | Add the proposed **US-108 Home page** (02, S1) to doc 01? | Yes, as **Should** in E1. |
| Q4 | Add the optional "Great with…" product field (02, S3)? | Not in v1. Record as a Could for later. |
| Q5 | How do we do password reset (US-506) without real emails? Supabase's reset flow sends its own email, and generating links needs the `service_role` key, which the app must not hold (NFR-SEC-07, C-05). | **Defer US-506.** Trainers reset passwords in the Supabase dashboard. |
| Q6 | How is "place order" made atomic and safe (US-405)? | A Postgres function (RPC) that checks stock, creates the order and lines, reduces stock and writes the outbox row in one transaction, using row locking and an idempotency key (NFR-DATA-02, 03, 04). |
| Q7 | One shared Supabase project for all trainees, or one each? | One per trainee or team (free tier), built from migrations + seed script. Plus a separate **test** project for integration tests (NFR-DEP-01). |
| Q8 | Currency and spelling? | GBP (£) and UK English. |
| Q9 | Should staff see an orders list? | No. Staff see orders only through the Outbox in v1. |
| Q10 | Which Streamlit and Python versions? Streamlit's version affects top navigation, custom fonts and `st.badge`. | Python **3.12**; pin the latest stable Streamlit at the start of M0 in `requirements.txt` (NFR-MAINT-03/04, C-07). Designs fall back to sidebar nav and the default font if not supported. |
| Q11 | Where do product photos come from, and are they licensed? | Start with the placeholder image. The designer supplies licensed images with source noted. No real brand products. |
| Q12 | How are demo account passwords shared? | Set by the seed script from environment variables; listed for trainers in `docs/deployment/`, never in the repo. |
| Q13 | Dark mode? | Not in v1. |
| Q14 | Should the demo app be public or restricted to invited viewers when used with minors (NFR-DEP-09)? | Restrict viewers using Community Cloud sharing settings where available; if it must be public, it holds demo data only. |
| Q15 | Do all learners write code, or do some only use the app? This affects A-02 and whether younger learners need GitHub accounts. | Younger learners only use the app with `example.com` accounts; learners who code use GitHub under trainer supervision. |

---

## 9. Suggested build order

Each milestone ends with a working app deployed to Streamlit Community Cloud.

| Milestone | Build | Stories | Screens |
| --- | --- | --- | --- |
| **M0 Foundations** | Test + demo Supabase projects; migrations (all tables, constraints, RLS, helper functions, storage bucket); RLS tests; seed and reset scripts; config/secrets loading; logging; `.streamlit/config.toml`; CI (Ruff + pytest); app shell with `st.navigation`; first deploy | – | S16 (basics) |
| **M1 Catalog** | Read-only shop | US-101–103, US-201, US-202 | S2, S3 |
| **M2 Accounts** | Register, sign in/out, role-based nav and page guards | US-501–504 | S5, S6 |
| **M3 Cart** | Session cart, saved cart, merge on sign-in | US-203, US-301–305 | S4 |
| **M4 Checkout** | Shipping, review, demo payment, `place_order` function (locking + idempotency), concurrency test, confirmation, outbox row | US-401–407 | S7–S10 |
| **M5 Admin** | Product list, add/edit, image upload, stock, hide/show | US-601–606 | S13, S14 |
| **M6 Shoulds** | Heat filter, sort, price/stock warnings, outbox page, archive, categories, home page | US-104, US-105, US-306, US-408, US-607, US-608, (US-108) | S1, S15 |
| **M7 Coulds** | In-stock filter, pagination, related products, my orders, profile, low stock | US-106, US-107, US-204, US-409, US-505, US-609 | S11, S12 |
| **M8 Polish** | Visual design applied, accessibility & mobile pass, coverage, `pip-audit`, Security Advisor, release checklist, deployment guide, classroom trial (NFR-SCALE-01) | – | All |

---

## 10. Traceability

Features → stories → screens → NFRs. Flows (F1–F9) are in [02 §5](02-ui-ux-requirements.md#5-key-user-flows). NFR IDs are shortened (e.g. `SEC-01` = `NFR-SEC-01`) and refer to [doc 03](03-non-functional-requirements.md).

| Feature | Stories | Screens | NFRs | Milestone |
| --- | --- | --- | --- | --- |
| E1 Browse, search, filter | US-101–103 | S2, S16 | PERF-01, PERF-04, PERF-05, SEC-12, UX-01, UX-07, PRIV-07 | M1 |
| E1 Heat filter, sort | US-104, 105 | S2, S1 | PERF-05, UX-03 | M6 |
| E1 In-stock filter, pagination | US-106, 107 | S2 | PERF-05 | M7 |
| E1 Home page (proposed) | US-108 | S1 | UX-01, UX-07, PRIV-07 | M6 |
| E2 Product detail & allergens | US-201, 202 | S3, S16 | SEC-12, UX-06, PRIV-07 | M1 |
| E2 Quantity selector | US-203 | S3 | DATA-06 | M3 |
| E2 Related products | US-204 | S3 | PERF-04 | M7 |
| E3 Add, view, change, remove | US-301–304 | S2, S3, S4 | DATA-06, DATA-07, PERF-02 | M3 |
| E3 Saved cart & merge | US-305 | S4, S5 | AUTH-04, SEC-02, PRIV-09, REL-06 | M3 |
| E3 Price/stock change warnings | US-306 | S4 | DATA-04 | M6 |
| E4 Sign-in gate | US-401 | S4, S5 | AUTH-05, SEC-02 | M4 |
| E4 Shipping, review | US-402, 403 | S7, S8 | PRIV-01, PRIV-03, UX-04 | M4 |
| E4 Demo payment | US-404 | S9 | PRIV-03, PRIV-04 | M4 |
| E4 Place order | US-405 | S9 | DATA-01–05, DATA-09, SEC-05, PERF-03, REL-03, LOG-03 | M4 |
| E4 Confirmation & outbox email | US-406, 407 | S10, S11 | PRIV-05, PRIV-09, SEC-05 | M4 |
| E4 Outbox page | US-408 | S15 | SEC-02, PRIV-08 | M6 |
| E4 My orders | US-409 | S11 | PRIV-09, SEC-06 | M7 |
| E5 Register, sign in/out | US-501–503 | S5, S6 | AUTH-01–03, AUTH-06, AUTH-07, AUTH-09, AUTH-10, PRIV-02, LOG-02 | M2 |
| E5 Role-protected pages | US-504 | S13–S15, S16 | AUTH-05, SEC-01–04, SEC-06, LOG-05 | M2 |
| E5 Profile | US-505 | S12, S7 | SEC-04, PRIV-01 | M7 |
| E5 Password reset | US-506 | S5, S15 | SEC-07, C-05 | Deferred (Q5) |
| E6 Product list, add, edit | US-601–603 | S13, S14 | SEC-02, SEC-03, DATA-06, DATA-10, PERF-04, LOG-03 | M5 |
| E6 Image upload | US-604 | S14 | SEC-13, PERF-06, SCALE-04 | M5 |
| E6 Stock, hide/show | US-605, 606 | S13 | DATA-01, SEC-02, LOG-03 | M5 |
| E6 Archive, categories | US-607, 608 | S13 | SEC-03, DATA-06 | M6 |
| E6 Low-stock list | US-609 | S13 | – | M7 |
| All features | – | All | AUTH-04, AUTH-08, SEC-07–11, SEC-14–16, PRIV-06, PRIV-10–12, DATA-08, DATA-11, UX-02, UX-05, UX-08, UX-09, REL-01, REL-02, REL-04, REL-05, REL-07, REL-08, MAINT-01–13, LOG-01, LOG-04, LOG-06, DEP-01–09, SCALE-01–03 | M0, M8 |
