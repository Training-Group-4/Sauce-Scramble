# Work Division Plan – Team of 4

**Project:** Sauce Scramble: demo sauce shop (training app)
**Status:** Draft v0.3 · 12 Nov 2026 (stories organised by User 1–3 with pick codes)
**Based on:** [00 – Consolidated](../requirements/00-consolidated-requirements.md) · [01 – Stories](../requirements/01-product-features-user-stories.md) · [02 – UI/UX](../requirements/02-ui-ux-requirements.md) · [03 – NFRs](../requirements/03-non-functional-requirements.md)

---

## 1. Overview

```
Phase 1: FOUNDATION         Phase 2: STORIES BY USER                   Phase 3: RELEASE
(Tech Lead, ~2 weeks)       (User 1, 2, 3, ~4 weeks)                   (everyone, ~1 week)

Repo, CI, schema, RLS,      Each user works down their own list,       Code freeze → regression
seed data, sign-in,   ──►   ONE story at a time:                 ──►   → NFR checks → bug fixes
app shell + page stubs,     U1-01, U1-02, …                            → release v1.0.0
service stubs, shared       branch → build → test → PR → review →
components, test setup      merge → next pick code
        │
        └─ tag v0.1.0-foundation
```

### 1.1 Roles

| Role | Name | Phase 1 | Phase 2 | Phase 3 |
| --- | --- | --- | --- | --- |
| **Tech Lead (TL)** | ______ | Builds the foundation | **No story list.** Second reviewer for security-sensitive PRs, unblocks users, backup for User 2, keeps the board and release checklist | Release manager |
| **User 1** | ______ | Onboarding, reviews foundation PRs | **Shop & My account**: `U1-01` … `U1-14` | Regression testing, accessibility check |
| **User 2** | ______ | Onboarding, reviews foundation PRs | **Cart & Checkout**: `U2-01` … `U2-12` | Regression testing, data-integrity checks |
| **User 3** | ______ | Onboarding, reviews foundation PRs | **Admin & Emails**: `U3-01` … `U3-11` | Regression testing, classroom trial |

The **trainer** is the product owner. They answer open questions ([doc 00 §8](../requirements/00-consolidated-requirements.md#8-open-questions-with-default-answers)); if there's no answer in time, the default answer applies.

### 1.2 Timeline

| Week | Phase | Milestone |
| --- | --- | --- |
| 1–2 | **Phase 1 – Foundation** | `v0.1.0-foundation` tagged and deployed |
| 3–6 | **Phase 2 – Stories by user** | All Must and Should stories merged (Could stories if time allows) |
| 7 | **Phase 3 – Integration & release** | `v1.0.0` released and demo app live |

---

## 2. Phase 1 – Foundation (Tech Lead)

The Tech Lead builds everything that **more than one person needs**, so each user's stories only fill in their own pieces.

### 2.1 What the foundation delivers

Pushed as **small PRs in this order**. Users review each one to learn the codebase.

| # | PR | Contents | Key NFRs |
| --- | --- | --- | --- |
| FND-01 | **Repo setup** | Branch protection on `main`, labels, issue/PR templates, `CODEOWNERS`, `pre-commit` (Ruff + secret scanner), secret scanning + push protection, `CONTRIBUTING.md` (the story loop from §3.3) | SEC-08, SEC-09, MAINT-13 |
| FND-02 | **CI and test setup** | GitHub Actions: Ruff lint/format + unit tests on every PR; integration tests against the **test** Supabase project; `pytest` config and markers; `tests/conftest.py` with signed-in clients per role and seed IDs | MAINT-05, 07, 08, 09 |
| FND-03 | **Config, logging, errors** | `app/config/settings.py` (secrets, demo constants, "Configuration missing" check), `logging.py` (masking), `utils/errors.py` (error reference IDs), `.streamlit/config.toml` (default theme, `gatherUsageStats = false`, error details off in cloud), pinned `requirements.txt`, Python 3.12 | REL-01, 02, 04; LOG-01, 02; PRIV-06; MAINT-02–04 |
| FND-04 | **Database schema** | One migration for **all** tables, constraints, enums, sequences and triggers in [00 §6](../requirements/00-consolidated-requirements.md#6-logical-data-model) | DATA-01, 05–11 |
| FND-05 | **Security rules** | RLS on every table, per-operation policies, `is_staff()`/`is_owner()` (`security definer`, empty `search_path`), role column locked, storage bucket with type/size limits and policies; **RLS tests** | SEC-01–06, SEC-13 |
| FND-06 | **Order function** | `place_order` RPC: row locks, idempotency key, price/name snapshots, order number, outbox row via a **stub** `build_order_email()` (filled in by `U3-07`); integration tests incl. **two buyers, one last bottle** | DATA-02–04, SEC-05, REL-03 |
| FND-07 | **Seed data** | Categories, 15 products (doc 01 §5.2), demo accounts, **2 past orders + outbox emails** for Casey Customer, placeholder image; `scripts/seed_accounts.py`, `scripts/reset_demo_data.py` (idempotent) | PRIV-07, PRIV-10, REL-07 |
| FND-08 | **Sign-in** (= **US-501–504**) | Per-session Supabase client, register, sign in, sign out, role from `profiles`, page guard, return-to-page; **hook** calling `cart_service.merge_guest_cart()` after sign-in (stub until `U2-11`) | AUTH-01–10 |
| FND-09 | **App shell** | `app/main.py` + `navigation.py` with **all 15 pages registered**, role-based menu, every page a **stub** (title, guard, "Coming soon – U#-##"); `session_keys.py` with **all** session keys, namespaced per user list | – |
| FND-10 | **Models, services, money** | **All** models; **all** service files with every function's **signature, type hints and docstring naming its pick code**; `utils/money.py` complete (Decimal, subtotal, shipping, total) with unit tests | DATA-07 |
| FND-11 | **Shared components** | `components/shared/`: product card (with **actions slot**), product grid, heat indicator, price, stock badge, S16 states, demo banner, step indicator; `components/cart/add_to_cart.py` **stub** already placed on S2 and S3 | UX-03, 06 |
| FND-12 | **First deploy** | Demo app on Community Cloud from `main`; migrations + seed on test and demo projects; deployment guide draft | DEP-01–05 |

### 2.2 Real code vs stubs

| Fully working after Phase 1 | Stub after Phase 1 (filled in by a pick code) |
| --- | --- |
| Config, logging, error IDs, theme | Page bodies ("Coming soon – U#-##") |
| Schema, RLS, storage rules, `place_order` | `build_order_email()` SQL function (`U3-07`) |
| Sign-in, register, sign-out, page guards (US-501–504) | All other service functions |
| `profile_service.get_profile()`, `get_default_address()` | `components/cart/add_to_cart.py` (`U2-01`) |
| `product_service.get_product()`, `get_products_by_ids()`, `clear_cache()` | |
| `utils/money.py`, shared components, navigation, session keys | |
| Seed data, seed/reset scripts, test fixtures, CI | |

**Stub convention:** final signature, type hints, docstring with the pick code and story, and `raise NotImplementedError("U2-02 / US-302")`. Pages don't call a stub until its story fills it in.

### 2.3 Folder layout and owners after Phase 1

```
app/
├── main.py · config/ · models/ · utils/money.py · utils/errors.py · utils/validation.py   (TL)
├── services/
│   ├── supabase_client.py · auth_service.py                                (TL)
│   ├── product_service.py · category_service.py ·
│   │   order_history_service.py · profile_service.py*                      (User 1)
│   ├── cart_service.py · order_service.py                                  (User 2)
│   └── admin_product_service.py · admin_category_service.py ·
│       storage_service.py · outbox_service.py                              (User 3)
├── components/
│   ├── shared/ · auth/                                                     (TL)
│   ├── catalog/ · account/                                                 (User 1)
│   ├── cart/ · checkout/                                                   (User 2)
│   └── admin/                                                              (User 3)
├── pages/
│   ├── home.py · shop.py · product.py · my_orders.py · profile.py          (User 1)  S1–S3, S11–S12
│   ├── cart.py · checkout_shipping.py · checkout_review.py ·
│   │   checkout_payment.py · order_confirmation.py                         (User 2)  S4, S7–S10
│   ├── sign_in.py · register.py                                            (TL)      S5–S6 – done
│   └── admin_products.py · admin_product_editor.py · admin_outbox.py       (User 3)  S13–S15
└── utils/formatting.py                                                     (User 1)
supabase/ · scripts/ · tests/conftest.py · tests/integration/rls/ · .github/   (TL)
tests/unit/<area>/ · tests/integration/<area>/                               (owner of the area)
```

\* `profile_service.py`: the TL builds `get_profile()` and `get_default_address()`; User 1 fills in `update_profile()` (`U1-13`).

### 2.4 Foundation Definition of Done

- [ ] All FND PRs merged; CI green (lint, unit, integration, RLS tests).
- [ ] Demo app deployed; every page S1–S15 reachable with the right role guard.
- [ ] Sign-in works for all four seed accounts; customers can't open admin pages.
- [ ] Every service function and model exists; every stub names its pick code.
- [ ] Seed and reset scripts run twice without errors on the test project.
- [ ] Two-buyers test for `place_order` passes.
- [ ] No secrets in the repo; Security Advisor shows no errors.
- [ ] Foundation walkthrough held; all users can run the app and tests locally.
- [ ] Tag `v0.1.0-foundation` created.

### 2.5 What users do in Phase 1

Users **do not write app code** in Phase 1.

| Task | Who |
| --- | --- |
| Local setup: Python 3.12, clone, `pre-commit`, own local Supabase project or CLI | All |
| Streamlit and Supabase getting-started tutorials | All |
| Read docs 00–03, focusing on your own list's stories, screens and NFRs | All |
| **Review each FND PR** (learning reviews) | All, rotating |
| Create a GitHub issue for **every pick code in your list** (template in §6.1) with a short test plan | Each user, own list |
| Meet the UI/UX designer; collect early designs for S1–S3 | User 1 |
| Content check of seed products and copy (all ages) | User 3 |

---

## 3. Phase 2 – Stories by user

### 3.1 Summary

| User | Theme | Pick codes | Stories | Must / Should / Could | Est. days | Reviewer |
| --- | --- | --- | --- | --- | --- | --- |
| **User 1** | Shop & My account | `U1-01` – `U1-14` | 14 | 6 / 3 / 5 | ~12 | User 2 |
| **User 2** | Cart & Checkout | `U2-01` – `U2-12` | 12 | 10 / 2 / 0 | ~14.5 | User 3 (+ TL for `U2-07`–`U2-10`) |
| **User 3** | Admin & Emails | `U3-01` – `U3-11` | 11 | 7 / 3 / 1 | ~11.5 | User 1 |
| **TL** | Foundation (Phase 1) | – | 4 (US-501–504) + US-506 deferred | 4 / 0 / 1 | – | All users |

**Total: 42 stories** (41 from doc 01 plus the proposed US-108). **Phase 2: 37 stories, about 38 days of work, about 12.5 days per user.**

**Balance:** User 2 is about 2.5–3 days above the others, because cart and checkout are one dependency chain and work best in one person's hands. To even this out, **the TL takes `U2-12` (US-306)** if User 2 is behind at the week 5 checkpoint. Users who finish early help others test or pick stretch items (§3.7).

**Size:** **S** = up to half a day (0.5) · **M** = 1–2 days (1.5) · **L** = about 3 days (3).

### 3.2 How to use pick codes

- A **pick code** is `U<user>-<order>`, e.g. `U2-03` = User 2's third story.
- **Work in order.** The order already puts Must stories first and respects dependencies.
- Use the pick code everywhere:
  - in the issue title, e.g. `U2-03 · US-303 Change quantity in cart`
  - in the branch name, e.g. `feature/U2-03-US-303-change-quantity`
  - in the PR title, e.g. `U2-03 · US-303 Change quantity in cart`
  - at stand-up, e.g. "I merged U2-03 and I'm starting U2-04."
- If your next pick is blocked (rare, see §3.6), take the **next unblocked** pick in your list and say so at stand-up.

### 3.3 The story loop

**One story at a time:** don't start the next pick until the current PR is merged.

```
 ┌──────────────────────────────────────────────────────────────────────────────┐
 │ 1. PICK      Next pick code in your list → board: "In progress"               │
 │ 2. BRANCH    git switch main && git pull                                      │
 │              git switch -c feature/U2-03-US-303-change-quantity               │
 │ 3. BUILD     Only the files listed for the pick. Replace the stub.            │
 │ 4. TEST      Unit tests (+ integration if DB). ruff check · ruff format ·     │
 │              pytest. Run the app and try it as each relevant role.            │
 │ 5. PR        Push, open PR with template, "Closes #<issue>", screenshots      │
 │              (1280 px + 375 px). Board: "In review".                          │
 │ 6. REVIEW    Your reviewer (+TL if needed) within 1 working day.              │
 │ 7. MERGE     CI green + approved → squash merge → delete branch.              │
 │ 8. VERIFY    Check it on the demo app. Close issue. Board: "Done".            │
 │ 9. NEXT      Next pick code.                                                  │
 └──────────────────────────────────────────────────────────────────────────────┘
```

**While your PR waits:** do your review duties first, then help test someone else's merged story. Flag reviews waiting more than 1 working day at stand-up.

### 3.4 Rules

1. **Touch only the files listed for your pick.** New files in your own folders are fine.
2. **Need to change another user's file or a shared component?** Open a **separate small `chore/` PR** reviewed by that file's owner.
3. **Need to change a service signature or model?** Ask the TL first; it's its own PR.
4. **No schema changes** except `U3-07`. If a story seems to need one, stop and talk to the TL.
5. **Session state:** only your own keys from `app/config/session_keys.py`; read other users' data through their service functions.
6. **Pull `main` before every new branch.** Branches live **≤ 3 days**.

### 3.5 Story lists

Paths are under `app/` unless shown. **+** = new file. `svc.fn()` = the stub function the pick fills in. **Depends on** shows only dependencies: `F` = foundation, `U#-##` = another pick. ⚠ = a pick from **another user's** list (all collected in §3.6).

#### User 1 – Shop & My account (reviewer: User 2)

| Pick | Story | Title | Pri. | Size | Files | Depends on |
| --- | --- | --- | --- | --- | --- | --- |
| **U1-01** | US-101 | Browse all products | Must | M | `pages/shop.py`; `product_service.list_products()`; + `tests/integration/catalog/test_list_products.py` | F |
| **U1-02** | US-102 | Search by keyword | Must | S | `pages/shop.py`; `product_service.list_products(search=)`; + `tests/integration/catalog/test_search.py` | U1-01 |
| **U1-03** | US-103 | Filter by category | Must | S | `pages/shop.py`; `category_service.list_categories()`; `product_service.list_products(category=)`; tests | U1-01 |
| **U1-04** | US-201 | View product detail | Must | M | `pages/product.py`; + `components/catalog/stock_status.py`; + `tests/unit/catalog/test_stock_status.py` | F |
| **U1-05** | US-202 | Show allergen information | Must | S | `pages/product.py`; + `components/catalog/allergens.py`; unit test | U1-04 |
| **U1-06** | US-203 | Choose quantity on detail page | Must | S | `pages/product.py`; + `components/catalog/quantity.py`; + `tests/unit/catalog/test_quantity_limits.py` | U1-04 · ⚠ soft: U2-01 |
| **U1-07** | US-104 | Filter by heat level | Should | S | `pages/shop.py`; + `components/catalog/filters.py`; `product_service.list_products(heat=)`; tests | U1-03 |
| **U1-08** | US-105 | Sort products | Should | S | `components/catalog/filters.py`; `product_service.list_products(sort=)`; tests | U1-07 |
| **U1-09** | US-108 | Home page *(proposed)* | Should | M | `pages/home.py`; + `components/catalog/hero.py`; + `components/catalog/category_tiles.py`; `product_service.list_featured()` | U1-03 |
| **U1-10** | US-106 | Hide out-of-stock items | Could | S | `components/catalog/filters.py`; `product_service.list_products(in_stock_only=)`; tests | U1-08 |
| **U1-11** | US-107 | Page through results | Could | M | `pages/shop.py`; + `components/catalog/pagination.py`; `product_service.list_products(page=)`, `count_products()`; tests | U1-01 |
| **U1-12** | US-204 | See related products | Could | S | `pages/product.py`; `product_service.list_related()`; tests | U1-04 |
| **U1-13** | US-505 | Edit profile | Could | S | `pages/profile.py`; + `components/account/profile_form.py`; `profile_service.update_profile()`; tests | F |
| **U1-14** | US-409 | My orders list | Could | M | `pages/my_orders.py`; + `components/account/order_list.py`; `order_history_service.list_my_orders()`, `get_order_detail()`; tests | F (uses seeded orders) |

**User 1 total:** 14 stories · 6 Must, 3 Should, 5 Could · S×9, M×5 · **~12 days**

#### User 2 – Cart & Checkout (reviewer: User 3; TL also reviews U2-07 to U2-10)

| Pick | Story | Title | Pri. | Size | Files | Depends on |
| --- | --- | --- | --- | --- | --- | --- |
| **U2-01** | US-301 | Add product to cart | Must | M | `components/cart/add_to_cart.py` (replaces stub; already on S2/S3); `cart_service.add_item()`; + `tests/unit/cart/test_add_item.py` | F |
| **U2-02** | US-302 | View cart | Must | M | `pages/cart.py`; `cart_service.get_cart()`; + `components/cart/cart_line.py`; + `components/cart/order_summary.py`; + `tests/unit/cart/test_get_cart.py` | U2-01 |
| **U2-03** | US-303 | Change quantity in cart | Must | S | `components/cart/cart_line.py`; `cart_service.update_quantity()`; tests | U2-02 |
| **U2-04** | US-304 | Remove item from cart | Must | S | `components/cart/cart_line.py`; `cart_service.remove_item()`, `clear_cart()`; tests | U2-02 |
| **U2-05** | US-401 | Sign in required to check out | Must | S | `pages/cart.py` (Checkout button: guest → sign in with return target; staff/owner message); tests | U2-02 |
| **U2-06** | US-402 | Enter shipping details | Must | M | `pages/checkout_shipping.py`; + `components/checkout/address_form.py`; uses `profile_service.get_default_address()` (F); + `tests/unit/checkout/test_address_validation.py` | U2-05 |
| **U2-07** | US-403 | Review order before paying | Must | S | `pages/checkout_review.py`; + `components/checkout/order_review.py`; creates the idempotency key | U2-06 |
| **U2-08** | US-404 | Fake payment | Must | M | `pages/checkout_payment.py`; + `components/checkout/payment_form.py`; `order_service.check_demo_card()`; + `tests/unit/checkout/test_demo_card.py` | U2-07 |
| **U2-09** | US-405 | Place order | Must | M | `pages/checkout_payment.py`; `order_service.place_order()` (calls F's RPC); `cart_service.clear_cart()`; + `tests/integration/checkout/test_place_order_flow.py` | U2-08, U2-04 |
| **U2-10** | US-406 | Order confirmation page | Must | S | `pages/order_confirmation.py`; `order_service.get_order()`; tests | U2-09 |
| **U2-11** | US-305 | Keep cart for signed-in customers | Should | L | `cart_service.load_saved_cart()`, `save_cart()`, `merge_guest_cart()` (hook already called by F); + `tests/unit/cart/test_merge.py`; + `tests/integration/cart/test_saved_cart.py` | U2-04 |
| **U2-12** | US-306 | Warn about price or stock changes | Should | M | `cart_service.check_cart_changes()`; `components/cart/cart_line.py`; tests | U2-11 · *TL backup* |

**User 2 total:** 12 stories · 10 Must, 2 Should · S×5, M×6, L×1 · **~14.5 days**

#### User 3 – Admin & Emails (reviewer: User 1)

| Pick | Story | Title | Pri. | Size | Files | Depends on |
| --- | --- | --- | --- | --- | --- | --- |
| **U3-01** | US-601 | View product list (admin) | Must | M | `pages/admin_products.py`; `admin_product_service.list_all_products()`; + `components/admin/product_table.py`; + `tests/integration/admin/test_list_all.py` | F |
| **U3-02** | US-602 | Add a product | Must | M | `pages/admin_product_editor.py`; + `components/admin/product_form.py`; `admin_product_service.create_product()`; + `tests/unit/admin/test_product_validation.py`; integration test | U3-01 |
| **U3-03** | US-603 | Edit a product | Must | S | `components/admin/product_form.py`; `admin_product_service.update_product()`; tests | U3-02 |
| **U3-04** | US-605 | Update stock | Must | S | `components/admin/product_table.py`; `admin_product_service.update_stock()`; tests | U3-01 |
| **U3-05** | US-606 | Hide or show a product | Must | S | `components/admin/product_table.py`; `admin_product_service.set_status()`; calls `product_service.clear_cache()` (F); tests | U3-01 |
| **U3-06** | US-604 | Upload product image | Must | M | `pages/admin_product_editor.py`; `storage_service.upload_product_image()`; + `tests/unit/admin/test_image_checks.py`; integration test | U3-03 |
| **U3-07** | US-407 | Confirmation email to outbox | Must | M | + `supabase/migrations/<ts>_order_email.sql` (fills in `build_order_email()`); + `tests/integration/admin/test_order_email.py` (calls F's `place_order` RPC directly) | F · ⚠ soft: U2-09 |
| **U3-08** | US-408 | View outbox (staff) | Should | M | `pages/admin_outbox.py`; `outbox_service.list_emails()`, `get_email()`; + `components/admin/email_preview.py`; tests | F (uses seeded outbox rows) |
| **U3-09** | US-607 | Archive a product | Should | S | `components/admin/product_table.py`; + `components/admin/confirm_dialog.py`; `admin_product_service.archive_product()`; tests | U3-05 |
| **U3-10** | US-608 | Manage categories | Should | M | `pages/admin_products.py` (Categories tab); + `components/admin/category_manager.py`; `admin_category_service.*`; tests | U3-01 |
| **U3-11** | US-609 | Low-stock list | Could | S | `pages/admin_products.py` (Low stock tab); `admin_product_service.list_low_stock()`; tests | U3-01 |

**User 3 total:** 11 stories · 7 Must, 3 Should, 1 Could · S×5, M×6 · **~11.5 days**

#### Done by the Tech Lead in Phase 1

| Story | Title | Pri. | Delivered in |
| --- | --- | --- | --- |
| US-501 | Register | Must | FND-08 |
| US-502 | Sign in | Must | FND-08 |
| US-503 | Sign out | Must | FND-08 |
| US-504 | Protect pages by role | Must | FND-08, FND-09 |
| US-506 | Reset password | Could | **Deferred** (doc 00 Q5) |

### 3.6 Cross-user dependencies

There are **no hard cross-user dependencies.** Every pick can be built, tested and merged using only the foundation and earlier picks in the same list. Only these **soft** links exist: the story works on its own, but the full user flow needs the other pick.

| Pick | Linked to | What works without it | What needs it | Expected ready |
| --- | --- | --- | --- | --- |
| **U1-06** (quantity selector) | **U2-01** (add to cart) | Selector, limits, tests | Clicking "Add to cart" adds the chosen quantity | Week 3 |
| **U3-07** (order email body) | **U2-09** (place order) | Email body built and tested by calling `place_order` directly | Seeing the email after a real checkout in the UI | Week 4 |
| **U1-14** (my orders) | **U3-07** (order email body) | Order list and detail using seeded orders and emails | New orders show the final email text | Week 4 |

**Shared files** are touched only through **separate functions**, never edited by two users:

| File | Owner | Other user's use |
| --- | --- | --- |
| `services/product_service.py` | User 1 | User 3 calls `clear_cache()` (built in F) |
| `services/profile_service.py` | User 1 (`update_profile`) | TL built `get_profile()`, `get_default_address()` in F; User 2 calls `get_default_address()` |
| `services/cart_service.py` | User 2 | F's sign-in hook calls `merge_guest_cart()` |

### 3.7 Progress checkpoints

Checked at the weekly demo (estimates include review time).

| End of | User 1 | User 2 | User 3 | Flows working on demo app |
| --- | --- | --- | --- | --- |
| **Week 3** | U1-01 – U1-06 | U2-01 – U2-05 | U3-01 – U3-05 | F1 browse → cart, F7 stock/hide |
| **Week 4** | U1-07 – U1-10 | U2-06 – U2-09 | U3-06 – U3-08 | F2, F6 add product, F9 outbox |
| **Week 5** | U1-11 – U1-14 | U2-10 – U2-11 | U3-09 – U3-11 | F3 full checkout, F4, F5, F8 |
| **Week 6** | Buffer and stretch | U2-12 (**TL takes it if User 2 is behind**) | Buffer and stretch | All flows F1–F9 |

**Stretch list** (when your list is done): help another user test, extra tests, accessibility fixes, the "Great with…" product field (doc 00 Q4) if the trainer agrees.

---

## 4. Phase 3 – Integration and release (week 7)

| Day | Step | Who | Done when |
| --- | --- | --- | --- |
| 1 | **Code freeze.** Unfinished Could picks go back to the backlog. | TL | All pick PRs merged or closed |
| 1–2 | **Regression test** of flows F1–F9 (doc 02 §5) on the test environment, as each role, at 1280 px and 375 px. **Each user tests another user's flows:** User 1 → admin (F6–F9), User 2 → shop (F1), User 3 → checkout (F2–F5). | Users 1–3 | Every flow passes or has a bug issue |
| 1–3 | **NFR verification** using doc 03's "How to check" | TL: AUTH, SEC, LOG, DEP, REL · User 1: UX, PERF · User 2: DATA, PRIV · User 3: MAINT, SCALE | Each NFR checked or has an issue |
| 2–4 | **Bug fixing** with the story loop (`fix/<issue>-<name>`); the owner of the pick fixes it. Order: security/data → Must stories → others. | All | No open `severity:high` bugs |
| 4 | **Release checks:** `pip-audit`, Security Advisor, secrets check, content check (all ages), migrations applied to demo | TL (User 1: content) | Release checklist (NFR-DEP-06) complete |
| 5 | **Classroom trial**, ~30 users (NFR-SCALE-01) | User 3 leads | No blocking issues |
| 5 | **Release:** tag `v1.0.0`, release notes (pick codes done, known limits, deferred items), deploy, wake app and Supabase project | TL | Demo app live on `v1.0.0` |
| 5 | **Final retrospective** | All | Lessons recorded in `docs/planning/` |

---

## 5. File ownership

| Owner | Files |
| --- | --- |
| **TL** | `app/main.py`, `app/config/*`, `app/models/*`, `utils/money.py`, `utils/errors.py`, `utils/validation.py`, `components/shared/*`, `components/auth/*`, `services/supabase_client.py`, `auth_service.py`, `pages/sign_in.py`, `register.py`, `supabase/`, `scripts/`, `tests/conftest.py`, `tests/integration/rls/`, `.github/`, `.streamlit/`, `requirements.txt`, `docs/deployment/`, `docs/architecture/` |
| **User 1** | `pages/home.py`, `shop.py`, `product.py`, `my_orders.py`, `profile.py`; `components/catalog/*`, `components/account/*`; `services/product_service.py`, `category_service.py`, `order_history_service.py`, `profile_service.update_profile()`; `utils/formatting.py`; `assets/`; `tests/*/catalog/`, `tests/*/account/` |
| **User 2** | `pages/cart.py`, `checkout_*.py`, `order_confirmation.py`; `components/cart/*`, `components/checkout/*`; `services/cart_service.py`, `order_service.py`; `tests/*/cart/`, `tests/*/checkout/` |
| **User 3** | `pages/admin_*.py`; `components/admin/*`; `services/admin_product_service.py`, `admin_category_service.py`, `storage_service.py`, `outbox_service.py`; the `U3-07` migration; `tests/*/admin/` |
| **Trainer** | `docs/requirements/` |

### 5.1 CODEOWNERS

`.github/CODEOWNERS` (replace placeholders with real GitHub usernames). **Later rules override earlier ones.**

```
# Default: Tech Lead
*                                       @tech-lead

# User 1 – Shop & My account
/app/pages/home.py                      @user-1
/app/pages/shop.py                      @user-1
/app/pages/product.py                   @user-1
/app/pages/my_orders.py                 @user-1
/app/pages/profile.py                   @user-1
/app/components/catalog/                @user-1
/app/components/account/                @user-1
/app/services/product_service.py        @user-1
/app/services/category_service.py       @user-1
/app/services/order_history_service.py  @user-1 @tech-lead
/app/services/profile_service.py        @user-1 @tech-lead
/assets/                                @user-1
/tests/*/catalog/                       @user-1
/tests/*/account/                       @user-1

# User 2 – Cart & Checkout
/app/pages/cart.py                      @user-2
/app/pages/checkout_*                   @user-2 @tech-lead
/app/pages/order_confirmation.py        @user-2
/app/components/cart/                   @user-2
/app/components/checkout/               @user-2 @tech-lead
/app/services/cart_service.py           @user-2
/app/services/order_service.py          @user-2 @tech-lead
/tests/*/cart/                          @user-2
/tests/*/checkout/                      @user-2

# User 3 – Admin & Emails
/app/pages/admin_*                      @user-3
/app/components/admin/                  @user-3
/app/services/admin_*                   @user-3
/app/services/storage_service.py        @user-3 @tech-lead
/app/services/outbox_service.py         @user-3
/tests/*/admin/                         @user-3

# Always Tech Lead (security-sensitive and foundation)
/supabase/                              @tech-lead
/app/config/                            @tech-lead
/app/models/                            @tech-lead
/app/components/shared/                 @tech-lead
/app/services/auth_service.py           @tech-lead
/app/services/supabase_client.py        @tech-lead
/requirements.txt                       @tech-lead
/.streamlit/                            @tech-lead
/.github/                               @tech-lead

# Requirements
/docs/requirements/                     @trainer
```

---

## 6. GitHub workflow

### 6.1 Issues and board

- **One issue per pick code.** Title: `U2-03 · US-303 Change quantity in cart`. Body:
  ```markdown
  **Pick:** U2-03 · **Story:** US-303 · **Priority:** Must · **Size:** S
  **Depends on:** U2-02
  **Files:** components/cart/cart_line.py; cart_service.update_quantity(); tests
  **Screens:** S4 · **NFRs:** DATA-06, UX-04

  ### Acceptance criteria (doc 01)
  - [ ] AC1 …
  - [ ] AC2 …

  ### Test plan
  - …
  ```
- **Foundation issues:** `FND-01` … `FND-12`. **Bugs:** bug template (steps, expected, actual, screenshot, environment, related pick code).
- **Labels:** `user:1`, `user:2`, `user:3`, `foundation` · `priority:must|should|could` · `size:S|M|L` · `type:story|bug|chore|docs` · `severity:high|low` · `blocked`, `needs-decision`.
- **GitHub Milestones:** `Phase 1 – Foundation`, `Phase 2 – Stories`, `Phase 3 – Release`.
- **Project board:** Backlog → Ready → In progress (**limit 1 per person**) → In review → Done. One view per user, **sorted by pick code**.

### 6.2 Branches and merging

| Type | Pattern | Example |
| --- | --- | --- |
| Foundation | `foundation/FND-<nn>-<name>` | `foundation/FND-05-rls` |
| Story | `feature/U<n>-<nn>-US-<id>-<name>` | `feature/U2-03-US-303-change-quantity` |
| Bug | `fix/<issue>-<name>` | `fix/57-cart-total-rounding` |
| Small cross-user change | `chore/<name>` | `chore/product-card-badge-slot` |
| Docs | `docs/<name>` | `docs/deployment-guide` |

**`main` is protected:** PR required · 1 approval + CODEOWNERS · CI green · up to date with `main` · **squash merge** · delete branch · no force pushes.
**Commits:** Conventional Commits with the pick code, e.g. `feat(cart): change quantity in cart (U2-03, US-303)`.

### 6.3 PR template

```markdown
## Pick
Closes #<issue> · U_-__ · US-___ · Size: S/M/L

## What changed
- 

## Screenshots
Desktop 1280 px and mobile 375 px (UI changes)

## How I tested
- [ ] Unit tests added/updated
- [ ] Integration tests added/updated (if database or auth involved)
- [ ] Ran the app as: guest / customer / staff / owner

## Checklist
- [ ] Only files listed in the issue changed (or a separate chore PR was used)
- [ ] No service signature, model, or migration changes (or TL approved)
- [ ] All acceptance criteria met
- [ ] No secrets or real personal data (NFR-SEC-08, NFR-MAINT-12)
- [ ] No `unsafe_allow_html` with user/database data (NFR-SEC-12)
- [ ] No per-user data in shared caches (NFR-AUTH-04)
- [ ] Logs contain no personal data (NFR-LOG-02)
- [ ] Copy is plain English and suitable for all ages
```

### 6.4 Definition of Done (pick)

1. All acceptance criteria in doc 01 pass.
2. Only the pick's files changed; its stubs replaced; no `NotImplementedError` left for this pick code.
3. Unit tests for the rules; integration tests where the database is involved; CI green.
4. Relevant NFRs from doc 03 checked.
5. Screen matches doc 02 at 1280 px and 375 px, keyboard usable, labels and alt text, S16 states handled.
6. Reviewed and approved (reviewer + TL where required), squash-merged.
7. Works on the deployed demo app; issue closed.

---

## 7. Meetings

| Meeting | When | Length | Who | Purpose |
| --- | --- | --- | --- | --- |
| **Kick-off** | Day 1 | 60 min | All + trainer | This plan, the docs, each user's list; agree working hours and review times |
| **Stand-up** | Every working day / class session | 15 min | All | "Merged U#-##, starting U#-##, blocked by …"; PRs waiting > 1 day (async in a pinned GitHub Discussion if needed) |
| **Foundation walkthrough** | End of Phase 1 | 90 min | All | TL demos structure, stubs, session keys, RLS, `place_order`, tests and the story loop; everyone runs the app and tests locally before leaving |
| **Weekly demo** | End of each Phase 2 week | 30 min | All + trainer | Merged picks on the demo app vs §3.7 checkpoints; decide on TL backup for `U2-12` at week 5 |
| **Backlog check** | After weekly demo | 15 min | All + trainer | Re-balance if someone is blocked or ahead; answer `needs-decision` issues |
| **Design sync** | Weekly, Phase 1–2 | 30 min | User 1 + designer | Check designs against doc 02 §9 Streamlit limits |
| **Retrospective** | End of Phase 1, mid Phase 2, end of Phase 3 | 30 min | All | What to keep / change |
| **Bug triage** | Daily in Phase 3 | 15 min | All | Prioritise and assign bugs |

---

## 8. Risks

| # | Risk | Likelihood | Impact | Mitigation | Owner |
| --- | --- | --- | --- | --- | --- |
| R1 | **Foundation takes longer than 2 weeks** | Medium | High | Fixed PR order; time-box; users use the time for onboarding, issues and test plans; if late, ship FND-01–10 first | TL |
| R2 | **Tech Lead is a single point of failure** in Phase 1 | Medium | High | `CONTRIBUTING.md` + walkthrough; TL has no Phase 2 list, so delays can be absorbed in week 3 | TL |
| R3 | **Stub signatures turn out wrong** | Medium | Medium | All users review FND-10; changes via small TL-approved PRs, announced at stand-up | TL |
| R4 | **Review bottleneck** (one pick at a time) | High | Medium | 1-working-day review rule; reviews before own work; stand-up flags waiting PRs | All |
| R5 | **User 2 overloaded** (longest list, security-sensitive checkout) | Medium | High | Must picks first; TL reviews checkout picks; **TL takes `U2-12`** if behind at week 5; early finishers help test | TL |
| R6 | **Merge conflicts** | Low | Medium | Shared code built in Phase 1; per-user file ownership; `chore/` PRs for cross-user edits; short branches | All |
| R7 | **Someone changes the schema** casually | Low | High | Full schema in FND-04; `/supabase/` owned by TL; only `U3-07` adds a migration | TL |
| R8 | **Secret committed** | Medium | High | pre-commit scanner, push protection, PR checklist, rotation procedure (NFR-SEC-10) | TL |
| R9 | **User data shared between sessions** via caches | Medium | High | Per-session client in F; PR checklist; two-browser test | TL |
| R10 | **Story sizes underestimated** (esp. `U2-11`, size L) | Medium | Medium | Weekly checkpoints; split a large pick into two PRs under the same issue; week 6 buffer | Pick owner |
| R11 | **Designs late or don't fit Streamlit** | Medium | Low | Build with shared components and default theme; apply design in week 6 / Phase 3 | User 1 |
| R12 | **Free tier sleeps or pauses** before a demo | Medium | Medium | Wake app and Supabase project before each demo (NFR-DEP-08) | TL |
| R13 | **Unsuitable content** in seed data, copy or tests | Low | High | Content checks in Phase 1 (User 3) and Phase 3 (User 1); PR checklist | Users 1, 3 |
| R14 | **One user finishes much earlier** | Medium | Low | Early finisher helps test, takes stretch items, or takes a Could pick from another list by agreement (re-labelled, not re-coded) | All |
