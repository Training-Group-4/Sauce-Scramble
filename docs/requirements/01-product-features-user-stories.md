# 01 – Product Features and User Stories

**Project:** Demo sauce shop (training app)
**Stack:** Streamlit (UI) · Supabase (database, sign-in, file storage)
**Status:** Draft v0.1

---

## 1. Purpose

This document lists what the demo shop must do. It is written as **epics** (big features) broken into **user stories** (small, testable pieces).

The shop sells a variety of sauces: hot sauces, BBQ sauces, pasta sauces, dips, and more.

> **This is a demo.** No real money is taken and no real emails are sent.
> Checkout is simulated, and confirmation emails are saved to an **outbox** table instead of being sent.

### How to read a story

- **ID**: `US-<epic><number>`. For example, `US-301` is the first story in Epic 3.
- **Priority**:
  - **Must**: the demo is not usable without it.
  - **Should**: important, but the demo works without it.
  - **Could**: nice to have if there is time.
- **Acceptance criteria (AC)**: numbered checks. A story is done when every AC passes.

### Epic list

| Epic | Name                                   | Main roles        |
| ---- | -------------------------------------- | ----------------- |
| E1   | Product search and catalog             | Guest, Customer   |
| E2   | Product detail                         | Guest, Customer   |
| E3   | Add to cart                            | Guest, Customer   |
| E4   | Checkout and order confirmation        | Customer          |
| E5   | Customer register / sign in            | Guest, Customer   |
| E6   | Owner and staff product management     | Staff, Owner      |

---

## 2. Roles

| Role         | Who they are                         | What they can do                                                                 |
| ------------ | ------------------------------------ | -------------------------------------------------------------------------------- |
| **Guest**    | A visitor who has not signed in      | Browse and search products, view details, add to cart, register, sign in         |
| **Customer** | A signed-in shopper                  | Everything a guest can do, plus check out and see their own order confirmations  |
| **Staff**    | A shop worker                        | Add and edit products, upload images, update stock, hide/show products           |
| **Owner**    | The shop owner (admin)               | Everything staff can do, plus archive products and manage categories           |

Rules:

- Every person has **exactly one** role.
- New sign-ups always get the **Customer** role. Staff and Owner accounts are created by the seed script (or by the owner in the Supabase dashboard).
- Roles are checked on the **server side** (Supabase Row Level Security), not only by hiding buttons in the UI.

---

## 3. Key domain terms

| Term                 | Meaning                                                                                          |
| -------------------- | ------------------------------------------------------------------------------------------------ |
| **Product**          | A sauce for sale. Has a name, SKU, category, price, heat level, size, stock, and image.         |
| **SKU**              | Stock Keeping Unit. A unique short code for a product, e.g. `HOT-001`.                          |
| **Category**         | A group of products, e.g. *Hot Sauce*, *BBQ*, *Pasta Sauce*, *Dips & Dressings*, *Asian Sauces*. |
| **Heat level**       | How spicy a sauce is, from 0 (not spicy) to 5 (very hot). Shown as chili icons.                |
| **Stock**            | How many units are available. A product with stock 0 is **out of stock**.                       |
| **Active / Hidden**  | Active products appear in the catalog. Hidden products do not, but are kept in the database.    |
| **Archived**         | A product removed from sale permanently by the owner. Kept for old orders.                       |
| **Cart**             | The list of products and quantities a shopper wants to buy. Not an order yet.                   |
| **Cart item**        | One line in the cart: a product and a quantity.                                                  |
| **Order**            | A cart that has been checked out. Has an order number, items, totals, and a status.             |
| **Order number**     | A human-friendly ID shown to the customer, e.g. `SS-2026-000123`.                               |
| **Order status**     | `placed` → `processing` → `shipped` (this demo only creates `placed`).                         |
| **Price snapshot**   | The price saved on each order line at checkout, so later price changes do not alter old orders.  |
| **Subtotal**         | Sum of (price × quantity) for all items.                                                         |
| **Shipping fee**     | A flat demo fee. Free above a set amount (see seed data).                                       |
| **Total**            | Subtotal + shipping fee. No tax is calculated in this demo.                                     |
| **Fake payment**     | A simulated payment step. Always succeeds unless the demo "fail" card number is used.           |
| **Outbox**           | A database table where emails are saved instead of being sent. Staff can read them in the app.  |
| **Seed data**        | Sample products, categories and accounts loaded into the database for demos.                     |

---

## 4. Epics and user stories

### E1 – Product search and catalog

**Goal:** Shoppers can quickly find sauces they like.

#### US-101 – Browse all products · **Must**

As a **guest or customer**, I want to see a list of all sauces, so that I can see what the shop sells.

- **AC1** The catalog page shows all **active** products.
- **AC2** Hidden and archived products are **not** shown.
- **AC3** Each product card shows: image, name, price, size, and heat level.
- **AC4** If a product has no image, a placeholder image is shown.
- **AC5** Out-of-stock products are shown with an "Out of stock" label.
- **AC6** Products load in under 3 seconds with the seed data.

#### US-102 – Search by keyword · **Must**

As a **shopper**, I want to search by keyword, so that I can find a sauce by name.

- **AC1** A search box is shown at the top of the catalog.
- **AC2** Search matches product **name** and **description**, and ignores upper/lower case.
- **AC3** A partial word works (e.g. "chip" finds "Chipotle Smoky BBQ").
- **AC4** If nothing matches, the page shows "No sauces found" and a "Clear search" button.
- **AC5** Clearing the search shows all active products again.

#### US-103 – Filter by category · **Must**

As a **shopper**, I want to filter by category, so that I only see the type of sauce I want.

- **AC1** A category filter lists all categories from the database.
- **AC2** Choosing a category shows only products in that category.
- **AC3** Search and category filter can be used together.
- **AC4** An "All categories" option removes the filter.

#### US-104 – Filter by heat level · **Should**

As a **shopper**, I want to filter by heat level, so that I can avoid sauces that are too spicy (or find very hot ones).

- **AC1** A heat filter lets the shopper choose a range from 0 to 5.
- **AC2** Only products within the chosen range are shown.
- **AC3** The heat filter works together with search and category.

#### US-105 – Sort products · **Should**

As a **shopper**, I want to sort the list, so that I can compare products easily.

- **AC1** Sort options: Name (A–Z), Price (low to high), Price (high to low), Heat (mild to hot).
- **AC2** Default sort is Name (A–Z).
- **AC3** Sorting keeps the current search and filters.

#### US-106 – Hide out-of-stock items · **Could**

As a **shopper**, I want to hide out-of-stock items, so that I only see what I can buy.

- **AC1** An "In stock only" checkbox is shown.
- **AC2** When ticked, products with stock 0 are hidden.

#### US-107 – Page through results · **Could**

As a **shopper**, I want results split into pages, so that the page is not too long.

- **AC1** The catalog shows 12 products per page.
- **AC2** Next / Previous buttons and the current page number are shown.
- **AC3** Changing search, filter or sort returns to page 1.

---

### E2 – Product detail

**Goal:** Shoppers can learn everything they need about a sauce before buying.

#### US-201 – View product detail · **Must**

As a **shopper**, I want to open a product, so that I can see its full details.

- **AC1** Clicking a product card opens the product detail view.
- **AC2** The view shows: large image, name, SKU, category, price, size, heat level, description, and ingredients.
- **AC3** The view shows stock status: "In stock", "Only N left" (when stock is 5 or less), or "Out of stock".
- **AC4** Opening a hidden, archived, or unknown product shows "Product not found" and a link back to the catalog.

#### US-202 – Show allergen information · **Must**

As a **shopper with allergies**, I want to see allergen information, so that I can buy safely.

- **AC1** The detail view shows an "Allergens" section.
- **AC2** Each allergen in the product's list is shown (e.g. *Contains: soy, sesame*).
- **AC3** If the list is empty, the text "No common allergens listed" is shown.
- **AC4** A note says: "Demo data – not real allergen advice."

#### US-203 – Choose quantity on detail page · **Must**

As a **shopper**, I want to choose a quantity before adding to cart, so that I can buy more than one.

- **AC1** A quantity selector starts at 1.
- **AC2** The minimum is 1 and the maximum is the lower of current stock and 10.
- **AC3** The selector and "Add to cart" button are disabled when the product is out of stock.

#### US-204 – See related products · **Could**

As a **shopper**, I want to see similar sauces, so that I can discover more products.

- **AC1** Up to 4 other active products from the **same category** are shown.
- **AC2** The current product is not included.
- **AC3** If there are none, the section is hidden.

---

### E3 – Add to cart

**Goal:** Shoppers can collect products before checking out.

#### US-301 – Add product to cart · **Must**

As a **shopper**, I want to add a product to my cart, so that I can buy it later.

- **AC1** "Add to cart" is available on the catalog card (quantity 1) and on the detail view (chosen quantity).
- **AC2** After adding, a short message confirms it (e.g. "Added 2 × Mango Habanero to your cart").
- **AC3** Adding a product already in the cart **increases** its quantity instead of adding a new line.
- **AC4** The total quantity in the cart can never be more than available stock. If it would be, the message says how many can still be added.
- **AC5** Out-of-stock products cannot be added.

#### US-302 – View cart · **Must**

As a **shopper**, I want to view my cart, so that I can check what I am buying.

- **AC1** A cart icon in the navigation shows the total number of items.
- **AC2** The cart page lists each item: image, name, unit price, quantity, and line total.
- **AC3** The cart shows subtotal, shipping fee, and total.
- **AC4** An empty cart shows "Your cart is empty" and a link to the catalog.

#### US-303 – Change quantity in cart · **Must**

As a **shopper**, I want to change quantities in my cart, so that I can buy the right amount.

- **AC1** Each line has a quantity control.
- **AC2** Quantity limits are the same as US-203 AC2.
- **AC3** Totals update immediately after a change.

#### US-304 – Remove item from cart · **Must**

As a **shopper**, I want to remove an item, so that I do not buy something I changed my mind about.

- **AC1** Each line has a "Remove" button.
- **AC2** After removing, the item disappears and totals update.

#### US-305 – Keep cart for signed-in customers · **Should**

As a **customer**, I want my cart saved to my account, so that it is still there next time I sign in.

- **AC1** A guest's cart is kept in the browser session only.
- **AC2** When a guest signs in, their session cart is **merged** into their saved cart (quantities added, capped by stock).
- **AC3** A signed-in customer's cart is saved in the database and loaded on next sign-in.

#### US-306 – Warn about price or stock changes · **Should**

As a **shopper**, I want to be told if something in my cart changed, so that I am not surprised at checkout.

- **AC1** If a product's price changed since it was added, the cart shows the new price and a note.
- **AC2** If a product is now hidden, archived, or out of stock, the line is marked "No longer available" and is excluded from totals.
- **AC3** If stock dropped below the cart quantity, the quantity is reduced to the stock level with a note.

---

### E4 – Checkout and order confirmation

**Goal:** Customers can place an order and get a confirmation. No real payment is taken and no real email is sent.

#### US-401 – Sign in required to check out · **Must**

As the **shop**, I want only signed-in customers to check out, so that every order belongs to an account.

- **AC1** A guest who clicks "Checkout" is asked to sign in or register.
- **AC2** After signing in, the guest returns to checkout with their cart kept (see US-305).
- **AC3** Staff and owner accounts cannot place orders in this demo; the button shows "Checkout is for customer accounts".

#### US-402 – Enter shipping details · **Must**

As a **customer**, I want to enter my shipping address, so that the order can be delivered.

- **AC1** Form fields: full name, address line 1, address line 2 (optional), city, postcode, country, phone (optional).
- **AC2** Required fields cannot be empty; errors are shown next to each field.
- **AC3** The form shows a note: "Demo shop – please do not enter your real address."

#### US-403 – Review order before paying · **Must**

As a **customer**, I want to review my order, so that I can check it is correct.

- **AC1** The review step shows items, quantities, prices, subtotal, shipping fee, total, and shipping address.
- **AC2** The customer can go back to edit the cart or address.

#### US-404 – Fake payment · **Must**

As a **customer**, I want a simple payment step, so that the checkout feels real without using real money.

- **AC1** The payment step shows a clear banner: "Demo only – no real payment is taken. Do not enter real card details."
- **AC2** The form accepts only the demo card numbers listed in [Seed data](#5-seed-data-for-demos); any other number is rejected with "Please use a demo card number".
- **AC3** The "success" demo card always succeeds.
- **AC4** The "decline" demo card always fails with "Payment declined (demo)"; no order is created and the cart is kept.
- **AC5** Card details are **never** saved to the database or logs.

#### US-405 – Place order · **Must**

As a **customer**, I want my order created when payment succeeds, so that the shop knows what to send.

- **AC1** An order is created with a unique order number, status `placed`, customer ID, shipping address, and timestamps.
- **AC2** Each order line saves a **price snapshot** and the product name at the time of order.
- **AC3** Stock is reduced by the ordered quantities.
- **AC4** If any item no longer has enough stock, the order is **not** created and the customer sees which items to fix.
- **AC5** Creating the order and reducing stock happen in **one database transaction** (all or nothing).
- **AC6** The cart is emptied after a successful order.
- **AC7** Clicking "Place order" twice does not create two orders.

#### US-406 – Order confirmation page · **Must**

As a **customer**, I want to see a confirmation, so that I know my order worked.

- **AC1** After success, a confirmation page shows the order number, items, total, and shipping address.
- **AC2** The page says: "A confirmation email has been saved to the demo outbox."

#### US-407 – Confirmation email to outbox · **Must**

As the **shop**, I want a confirmation email recorded for each order, so that the demo shows the email flow without sending anything.

- **AC1** When an order is placed, one row is added to the `email_outbox` table.
- **AC2** The row includes: recipient email, subject, plain-text body, related order ID, created time, and status `not_sent`.
- **AC3** The body includes the order number, items, total, and shipping address.
- **AC4** No email is sent by any service. There is no SMTP or email-provider setup in the app.
- **AC5** A customer can see outbox emails for **their own** orders only.

#### US-408 – View outbox (staff) · **Should**

As **staff or owner**, I want to view the outbox, so that I can show trainees what email would have been sent.

- **AC1** An "Outbox" page lists all outbox emails, newest first.
- **AC2** Clicking an email shows its full subject and body.
- **AC3** Customers and guests cannot open this page.

#### US-409 – My orders list · **Could**

As a **customer**, I want to see my past orders, so that I can check what I bought.

- **AC1** A "My orders" page lists the customer's orders with number, date, total, and status.
- **AC2** Clicking an order shows its details.
- **AC3** A customer can never see another customer's orders.

---

### E5 – Customer register / sign in

**Goal:** Shoppers can create an account and sign in securely using Supabase Auth.

#### US-501 – Register · **Must**

As a **guest**, I want to create an account, so that I can check out.

- **AC1** The register form asks for display name, email, password, and confirm password.
- **AC2** Email must be a valid format; password must be at least 8 characters; passwords must match.
- **AC3** If the email is already registered, a friendly message is shown (without revealing private details).
- **AC4** A new account always gets the **Customer** role.
- **AC5** Email confirmation is **turned off** in the demo Supabase project, so the customer can sign in straight away.
- **AC6** The form shows a note: "Demo shop – please use a test email address."

#### US-502 – Sign in · **Must**

As a **registered user**, I want to sign in, so that I can use my account.

- **AC1** The sign-in form asks for email and password.
- **AC2** Wrong details show "Email or password is incorrect" (same message for both cases).
- **AC3** After signing in, the navigation shows the user's display name and role-based menu items.
- **AC4** Staff and owner see a "Manage products" link; customers do not.

#### US-503 – Sign out · **Must**

As a **signed-in user**, I want to sign out, so that others using the device cannot use my account.

- **AC1** A "Sign out" button is in the navigation.
- **AC2** Signing out clears the session and returns to the catalog as a guest.
- **AC3** The saved cart stays in the database for next time (see US-305).

#### US-504 – Protect pages by role · **Must**

As the **shop**, I want pages protected by role, so that users only see what they are allowed to.

- **AC1** Guests opening a customer-only page are sent to sign in.
- **AC2** Customers opening a staff/owner page see "You do not have access to this page."
- **AC3** Database rules (RLS) block the same actions even if the UI check is skipped.

#### US-505 – Edit profile · **Could**

As a **customer**, I want to update my display name and default address, so that checkout is faster.

- **AC1** A profile page lets the customer change display name and default shipping address.
- **AC2** The default address pre-fills the checkout form (US-402).

#### US-506 – Reset password · **Could**

As a **customer**, I want to reset a forgotten password, so that I can get back into my account.

- **AC1** In the demo, the reset link is written to the outbox (US-407 rules apply) instead of being emailed.
- **AC2** The link works once and expires after 1 hour.

---

### E6 – Owner and staff product management

**Goal:** Staff and the owner can keep the catalog up to date.

#### US-601 – View product list (admin) · **Must**

As **staff or owner**, I want to see all products, including hidden ones, so that I can manage them.

- **AC1** The "Manage products" page lists **all** products: active, hidden, and (for owner) archived.
- **AC2** Each row shows SKU, name, category, price, stock, and status.
- **AC3** The list can be searched by name or SKU.
- **AC4** Customers and guests cannot open this page (see US-504).

#### US-602 – Add a product · **Must**

As **staff or owner**, I want to add a new sauce, so that customers can buy it.

- **AC1** Form fields: SKU, name, category, description, ingredients, allergens, size (ml), heat level (0–5), price, stock, and status (active/hidden).
- **AC2** SKU must be unique; name, category, price, and stock are required.
- **AC3** Price must be greater than 0 with at most 2 decimal places; stock must be a whole number 0 or more.
- **AC4** On save, a success message is shown and the product appears in the list.

#### US-603 – Edit a product · **Must**

As **staff or owner**, I want to edit a product, so that its details stay correct.

- **AC1** All fields from US-602 can be edited except SKU.
- **AC2** The same validation rules as US-602 apply.
- **AC3** Changing the price does **not** change prices on existing orders (price snapshot).
- **AC4** The product records who last updated it and when.

#### US-604 – Upload product image · **Must**

As **staff or owner**, I want to upload a product photo, so that customers can see the sauce.

- **AC1** Images are uploaded to the Supabase Storage bucket for products.
- **AC2** Only JPG, PNG, or WEBP files up to 2 MB are accepted.
- **AC3** Uploading a new image replaces the old one for that product.
- **AC4** Only staff and owner can upload; anyone can view product images.

#### US-605 – Update stock · **Must**

As **staff or owner**, I want to change stock levels quickly, so that the catalog shows what is available.

- **AC1** Stock can be edited directly from the product list.
- **AC2** Stock cannot be negative.
- **AC3** Setting stock to 0 makes the product show "Out of stock" in the catalog.

#### US-606 – Hide or show a product · **Must**

As **staff or owner**, I want to hide a product without deleting it, so that I can take it off sale for a while.

- **AC1** Each product has a Hide / Show action.
- **AC2** Hidden products disappear from the catalog, search, and related products.
- **AC3** Hidden products in a cart are handled as in US-306 AC2.

#### US-607 – Archive a product · **Should**

As the **owner**, I want to archive a product, so that it is removed from sale for good but old orders still work.

- **AC1** Only the owner sees the "Archive" action.
- **AC2** The owner must confirm before archiving.
- **AC3** Archived products never show in the catalog and cannot be ordered.
- **AC4** Products are never hard-deleted if they appear on any order.

#### US-608 – Manage categories · **Should**

As the **owner**, I want to add and rename categories, so that the catalog stays organised.

- **AC1** The owner can add a category and rename an existing one.
- **AC2** Category names must be unique.
- **AC3** A category with products cannot be deleted.

#### US-609 – Low-stock list · **Could**

As **staff or owner**, I want to see products that are running low, so that I can restock in time.

- **AC1** A "Low stock" view lists active products with stock of 5 or less.
- **AC2** The list is sorted by stock, lowest first.

---

## 5. Seed data for demos

Seed data lives in `supabase/seed/` and is loaded by a script in `scripts/`. All of it is fictional.

### 5.1 Categories

| Name              | Slug          |
| ----------------- | ------------- |
| Hot Sauce         | `hot-sauce`   |
| BBQ               | `bbq`         |
| Pasta Sauce       | `pasta-sauce` |
| Asian Sauces      | `asian`       |
| Dips & Dressings  | `dips`        |

### 5.2 Products

The set includes edge cases on purpose: one product out of stock, one with low stock, one hidden, one with no image, and one with no allergens.

| SKU       | Name                         | Category         | Size  | Heat | Price  | Stock | Status | Allergens         | Demo purpose          |
| --------- | ---------------------------- | ---------------- | ----- | ---- | ------ | ----- | ------ | ----------------- | --------------------- |
| `HOT-001` | Mango Habanero               | Hot Sauce        | 150ml | 4    | £6.50  | 40    | active | –                 | Normal product        |
| `HOT-002` | Green Jalapeño & Lime        | Hot Sauce        | 150ml | 2    | £5.50  | 25    | active | –                 | Normal product        |
| `HOT-003` | Ghost Pepper Inferno         | Hot Sauce        | 100ml | 5    | £8.00  | 3     | active | –                 | **Low stock** ("Only 3 left") |
| `HOT-004` | Smoky Chipotle Drizzle       | Hot Sauce        | 150ml | 3    | £6.00  | 0     | active | –                 | **Out of stock**      |
| `BBQ-001` | Classic Hickory BBQ          | BBQ              | 300ml | 0    | £4.50  | 60    | active | mustard           | Mild product          |
| `BBQ-002` | Chipotle Smoky BBQ           | BBQ              | 300ml | 2    | £5.00  | 35    | active | mustard           | Search test ("chip")  |
| `BBQ-003` | Honey Mustard Glaze          | BBQ              | 250ml | 0    | £4.75  | 20    | hidden | mustard           | **Hidden** product    |
| `PAS-001` | Tomato & Basil Passata       | Pasta Sauce      | 500ml | 0    | £3.25  | 80    | active | –                 | **No allergens**      |
| `PAS-002` | Creamy Garlic Alfredo        | Pasta Sauce      | 400ml | 0    | £4.25  | 30    | active | milk              | Normal product        |
| `PAS-003` | Arrabbiata Chili Tomato      | Pasta Sauce      | 500ml | 2    | £3.75  | 45    | active | celery            | Heat filter test      |
| `ASN-001` | Sweet Chili Dipping Sauce    | Asian Sauces     | 250ml | 1    | £3.50  | 50    | active | –                 | Normal product        |
| `ASN-002` | Teriyaki Glaze               | Asian Sauces     | 250ml | 0    | £4.00  | 40    | active | soy, wheat        | Multiple allergens    |
| `ASN-003` | Sesame Ginger Stir-Fry       | Asian Sauces     | 250ml | 1    | £4.50  | 15    | active | soy, sesame       | Normal product        |
| `DIP-001` | Garlic Herb Aioli            | Dips & Dressings | 200ml | 0    | £3.95  | 25    | active | egg, mustard      | **No image** (placeholder) |
| `DIP-002` | Spicy Sriracha Mayo          | Dips & Dressings | 200ml | 3    | £4.25  | 30    | active | egg               | Normal product        |

### 5.3 Demo accounts

All accounts use the reserved `example.com` domain, so no real inbox is ever involved. Passwords are set by the seed script and listed in `docs/deployment/` for trainers. They are **demo-only** and must never be reused.

| Display name   | Email                     | Role     | Notes                                   |
| -------------- | ------------------------- | -------- | --------------------------------------- |
| Olivia Owner   | `owner@example.com`       | Owner    | Can archive products, manage categories |
| Sam Staff      | `staff@example.com`       | Staff    | Can add/edit products and stock         |
| Casey Customer | `customer@example.com`    | Customer | Has 2 past orders and 1 outbox email each |
| Jordan Newbie  | `customer2@example.com`   | Customer | No orders, empty cart                   |

### 5.4 Demo settings

| Setting                    | Value                   |
| -------------------------- | ----------------------- |
| Currency                   | GBP (£)                 |
| Flat shipping fee          | £3.99                   |
| Free shipping from         | £30.00 subtotal         |
| Max quantity per product   | 10                      |
| Low-stock threshold        | 5                       |
| Demo card – success        | `4242 4242 4242 4242`   |
| Demo card – decline        | `4000 0000 0000 0002`   |
| Demo card expiry / CVC     | Any future date / any 3 digits |

---

## 6. Out of scope

These items are **not** part of this demo. They may be used as training exercises later.

1. **Real payments**: no payment provider (Stripe, PayPal, etc.) and no storing of card data.
2. **Real emails**: no SMTP or email service; all emails go to the outbox table only.
3. **Real shipping**: no courier integration, tracking numbers, or live shipping rates.
4. **Tax and VAT** calculation.
5. **Multiple currencies or languages**.
6. **Discount codes, coupons, and gift cards**.
7. **Product reviews and ratings**.
8. **Wishlists and favourites**.
9. **Product variants** (e.g. one product with several sizes). Each size is a separate product.
10. **Refunds, returns, and order cancellation**.
11. **Order status updates by staff** (orders stay `placed`).
12. **Social sign-in** (Google, GitHub, etc.) and two-factor authentication.
13. **Staff account management in the app**. Staff and owner accounts are created by the seed script or the Supabase dashboard.
14. **Reports and sales dashboards**.
15. **Mobile app**. The Streamlit web app only.
16. **Production hardening** such as rate limiting, fraud checks, backups, and legal pages (privacy policy, terms).
