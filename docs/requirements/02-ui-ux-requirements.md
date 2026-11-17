# 02 – UI/UX Requirements

**Project:** Demo sauce shop (training app)
**For:** UI/UX designer
**Status:** Draft v0.1
**Related:** [01 – Product Features and User Stories](01-product-features-user-stories.md) (story IDs like `US-101` refer to that document)

---

## Contents

1. [The vision, word by word](#1-the-vision-word-by-word)
2. [Design principles](#2-design-principles)
3. [Visual direction](#3-visual-direction)
4. [Sitemap and navigation](#4-sitemap-and-navigation)
5. [Key user flows](#5-key-user-flows)
6. [Screen-by-screen requirements](#6-screen-by-screen-requirements)
7. [Accessibility and mobile](#7-accessibility-and-mobile)
8. [Content and tone](#8-content-and-tone)
9. [What Streamlit can and cannot do](#9-what-streamlit-can-and-cannot-do)
10. [Deliverables](#10-deliverables)

### Context in one minute

- A demo online shop that sells sauces: hot sauce, BBQ, pasta sauce, Asian sauces, dips.
- **No real payments and no real emails.** Checkout is simulated. Emails are saved to an "outbox" screen.
- Four roles: **guest**, **customer**, **staff**, **owner**.
- Built with **Streamlit** (a Python web framework) and **Supabase**. Streamlit has real layout limits. Please read [section 9](#9-what-streamlit-can-and-cannot-do) **before** starting high-fidelity designs.
- The app is used in training, including with **younger learners**. All imagery and copy must suit all ages.

---

## 1. The vision, word by word

> **"Exciting, yet comfortable, with a warm colour palette. It should make people curious to buy the sauce."**

| Word / phrase | What it means for the design | What it does NOT mean |
| --- | --- | --- |
| **Exciting** | Energy and appetite. Bold, close-up product photos with rich colour. Big, confident headings. Strong colour blocks in key places (hero, category tiles, "Find your heat"). Heat level shown as a fun flavour journey, from gentle to very hot. Short, lively copy ("Bright, fruity and full of kick"). Each screen has at least one moment that makes you look twice. | Not flashing, blinking, auto-playing or animated (it also can't be built in Streamlit, see [9.4](#94-interaction)). Not neon, "extreme" or danger imagery (flames, skulls, warning signs). Not loud on every part of the page. Not fake urgency ("Hurry! 2 people are viewing this!"). |
| **yet** | Excitement and comfort must be **balanced**. Excitement lives in **moments**: imagery, hero bands, headings, colour accents. Comfort lives in the **base**: layout, spacing, forms, checkout, behaviour. | Not a compromise where both are weak. Not exciting on one screen and dull on the next. |
| **Comfortable** | Easy on the eyes and easy to use. Calm cream backgrounds behind the bold moments. Generous spacing, soft rounded corners, readable text. Predictable buttons in predictable places. Prices, totals and stock always clear. Checkout feels calm and safe, with no surprises. Admin screens are calm and practical. | Not boring, beige or sleepy. Not "corporate dashboard". Not so quiet that products lose their personality. |
| **with a warm colour palette** | The whole palette comes from food and cooking: paprika, chilli orange, tomato, mustard, roasted brown, toasted cream (see [3.2](#32-suggested-colour-tokens)). Cool colours are used **only** for system messages (the "Demo only" banner) so they stand out. | Not "everything red and orange" at full strength. Warm colours are mainly for accents and bands, with cream as the base. Not using colour alone to show meaning (see [7.1](#71-accessibility-requirements)). |
| **Curious** | Each product hints at a story: heat level, flavour description, ingredients, "you might also like". Shoppers want to click and explore. Good product photos are the main tool. | Not clickbait. Not hiding the price or key facts to force a click. Not fake urgency ("Hurry! 2 people are viewing this!"). |
| **to buy** | Curiosity must lead somewhere. "Add to cart" is always easy to find, the cart count is always visible, and checkout has few steps. | Not pushy. No pop-ups, no pre-ticked upsells, no tricks that make leaving the cart hard. |
| **the sauce** | The **product is the hero**, not the brand or the UI. Big images, product names in a characterful heading font, and the heat level shown at a glance. | Not decoration that competes with the product photos. Not generic stock images of people eating. |

**One-line test for every screen:** *Is there one thing here that excites a first-time visitor to click one more product, and does the rest of the screen feel easy and relaxed to use?*

---

## 2. Design principles

1. **Product first.** Product image, name, price and heat level come before any other content on shopper screens.
2. **Exciting moments, comfortable base.** Save bold colour and big type for a few moments per screen (hero, category tiles, product names, heat). Keep everything around them calm: cream backgrounds, plenty of space, and facts (price, stock, totals, errors) in high-contrast, plain text.
3. **The busier the task, the calmer the screen.** Home and Shop can be the most exciting. Cart and checkout get calmer step by step. Admin screens are the calmest.
4. **One clear next step.** Each screen has one primary action (e.g. "Add to cart", "Continue to payment"). Secondary actions look secondary.
5. **Honest demo.** Wherever someone might enter real data (address, card, email), say clearly that this is a demo. Never pretend to send emails or take money.
6. **Never only colour.** Heat level, stock status and errors always use text or an icon as well as colour.
7. **Same pattern, same place.** Product cards, buttons, form errors and empty states look and behave the same everywhere.
8. **Shopper screens feel like a shop; admin screens feel like a tool.** Admin screens (staff/owner) are calmer and denser, but use the same tokens.
9. **Design for Streamlit.** Use the components listed in [section 9](#9-what-streamlit-can-and-cannot-do). A beautiful design that cannot be built is not done.

---

## 3. Visual direction

### 3.1 Mood

Keywords: *busy spice market, sizzling pan, bold jar labels, bright fresh chillies, then a cosy kitchen table in warm afternoon light.*

The first half of that list is the **exciting** part. The second half is the **comfortable** part. The mood board should show both and how they sit together.

Mood board should include:

- Bold, close-up photos of sauces: drips, swirls, glossy textures, vivid ingredients (chillies, tomatoes, mango, garlic, herbs, citrus).
- Strong warm colour blocks used behind product photos or headings (paprika, chilli orange, mustard, deep roasted brown).
- Calm, cosy scenes for contrast: jars on a wooden table, linen, soft side light.
- Confident label-style typography from food packaging and market signs.

Avoid: neon, black "extreme" hot sauce aesthetics, fire and explosions, alcohol, and anything not suitable for a young audience.

### 3.2 Suggested colour tokens

These are a starting point. The designer owns the final palette but must keep the **token names** (developers will map them to the Streamlit theme and CSS). Contrast ratios are approximate and must be checked.

| Token | Suggested value | Use | Contrast note |
| --- | --- | --- | --- |
| `color.bg` | `#FFF8F0` (warm cream) | Page background | – |
| `color.surface` | `#FFFFFF` | Cards, forms, dialogs | – |
| `color.surface-alt` | `#FBEFE3` (toasted cream) | Sidebar, section bands, table header | – |
| `color.border` | `#E8D9C8` | Card borders, dividers | Decorative only |
| `color.text` | `#2B2118` (dark roast brown) | Body text, headings | ~15:1 on bg |
| `color.text-muted` | `#6B5B4E` | Secondary text, labels | ~6:1 on bg (AA) |
| `color.primary` | `#B5451B` (paprika) | Primary buttons, links, active nav | ~5.5:1 with white text (AA) |
| `color.primary-hover` | `#8F3514` | Hover/pressed state | – |
| `color.accent` | `#E0A526` (mustard) | Badges, highlights, heat icons | **Not for text on light backgrounds.** Use dark text on it. |
| `color.highlight` | `#E8542A` (chilli orange) | **Exciting moments:** hero and category colour blocks, "Find your heat" strip, large display headings | ~3.7:1 with white. **Large text (24 px+, or 19 px+ bold) or decoration only**, never body text. |
| `color.surface-bold` | `#3A1F14` (deep roasted brown) | Bold bands, e.g. hero or footer, with cream text and mustard accents | Cream text ~14:1; mustard ~7:1 |
| `color.success` | `#2E7D32` | "In stock", success messages | ~5:1 on white |
| `color.warning` | `#8A5A00` | "Only N left", price-change notes | ~6:1 on white |
| `color.error` | `#B00020` (crimson) | Errors, "Out of stock" | ~6:1 on white. Must differ clearly from `primary`; always pair with an icon. |
| `color.demo` | `#1F5F7A` (calm teal) | "Demo only" banners | Deliberately **not** warm, so it stands out as a system message |

**Using the palette:** cream (`bg`, `surface`) is the comfortable base and covers most of each screen. Bold colours (`highlight`, `surface-bold`, `primary`) are the exciting moments; as a rough guide, keep them to about 10–20% of a shopper screen and less on checkout and admin screens.

**Heat scale (0–5):** shown as chilli icons, filled vs. outlined, plus a text label (e.g. "Heat 3 of 5 · Medium"). Colour may shift from mustard to paprika, but the count and label carry the meaning.

| Level | Label |
| --- | --- |
| 0 | No heat |
| 1 | Gentle |
| 2 | Mild |
| 3 | Medium |
| 4 | Hot |
| 5 | Very hot |

**Other tokens to define:** spacing scale (e.g. 4 / 8 / 12 / 16 / 24 / 32 / 48), corner radius (suggest 8–12 px for cards and buttons), and one soft shadow for cards.

**Dark mode:** not required for v1. Design for light mode. If you want to propose dark-mode values, add them as a separate token column.

### 3.3 Typography

| Role | Suggestion | Notes |
| --- | --- | --- |
| Headings & product names | A bold display font with energy, e.g. **Bricolage Grotesque** or **Fraunces** in a heavy weight (Google Fonts) | This is where the excitement comes from: big, confident, label-like. Use for H1–H3, hero text and product names only. |
| Body, UI, prices | **Source Sans 3** (Streamlit's default) or **Inter** | This is the comfortable part: plain, relaxed and easy to read. Prices use tabular numbers if the font supports them. |

Guidance:

- Body text minimum **16 px**. Labels and captions minimum **14 px**.
- A simple type scale is enough: H1, H2, H3, body, small, price.
- Custom fonts depend on the Streamlit version (see [9.3](#93-theming-and-styling)). Show how the design looks with the default font too, as a fallback.

### 3.4 Icons and imagery

- **Icons:** use **Google Material Symbols (Rounded)**. Streamlit supports them natively in buttons, navigation and alerts, so no custom icon work is needed for UI icons.
- **Custom icons needed:** chilli (filled and outline) for the heat scale. Deliver as SVG and PNG.
- **Product photos:** square (1:1), at least 800 × 800 px, product centred with even padding, consistent light and background across the range.
- **Placeholder image:** one branded placeholder for products with no image (seed product `DIP-001`), in square format.
- **Logo / wordmark:** a simple wordmark for the demo shop (name to be confirmed). Must work at small size in the navigation.
- **Emoji:** avoid as decoration in the UI. Material icons look more consistent. The chilli heat icons are the one place to have fun with custom illustration.

---

## 4. Sitemap and navigation

### 4.1 Sitemap

```
Shop (all users)
├── S1  Home
├── S2  Shop / Catalog & search
│   └── S3  Product detail
├── S4  Cart
├── Checkout (customers only)
│   ├── S7  Shipping details
│   ├── S8  Review order
│   ├── S9  Payment (demo)
│   └── S10 Order confirmation
├── Account
│   ├── S5  Sign in  (+ forgot password)
│   ├── S6  Register
│   ├── S11 My orders  (+ order detail)
│   └── S12 Profile
│
Admin (staff & owner only)
├── S13 Manage products  (tabs: All products · Low stock · Categories [owner])
├── S14 Product editor  (add / edit)
└── S15 Outbox
│
S16 System states (used on every screen: loading, empty, error, not found, no access, demo banner)
```

### 4.2 Navigation by role

| Item | Guest | Customer | Staff | Owner |
| --- | :-: | :-: | :-: | :-: |
| Home, Shop | ✓ | ✓ | ✓ | ✓ |
| Cart (with item count) | ✓ | ✓ | – | – |
| Sign in / Register | ✓ | – | – | – |
| Account menu: My orders, Profile, Sign out | – | ✓ | – | – |
| Admin: Manage products, Outbox | – | – | ✓ | ✓ |
| Categories tab | – | – | – | ✓ |
| Display name + role label, Sign out | – | ✓ | ✓ | ✓ |

Staff and owner cannot place orders (US-401 AC3), so the cart is hidden for them.

### 4.3 Navigation pattern

- **Desktop:** a top navigation bar with logo left, main links centre/left, and cart + account right. Admin links appear in a separate "Admin" group.
- **Mobile:** navigation collapses into a menu (Streamlit does this for us; see [9.2](#92-layout-and-navigation)).
- **Cart count** is visible on every shopper screen (US-302 AC1).
- **Product links** use a shareable URL, e.g. `/product?sku=HOT-001`, so a product can be opened directly.
- **No breadcrumbs** beyond a simple "← Back to shop" link on the product detail and checkout steps.

---

## 5. Key user flows

Please deliver each flow as a simple flow diagram plus the frames it uses.

### F1 – Discover and add to cart (guest)
S1 Home → S2 Shop (search "chip", filter by heat) → S3 Product detail → choose quantity → **Add to cart** → toast "Added 2 × Chipotle Smoky BBQ" → S4 Cart.
*Stories:* US-101–105, US-201–203, US-301, US-302

### F2 – Guest checks out (sign-in in the middle)
S4 Cart → **Checkout** → S5 Sign in (or S6 Register) → cart merged → back to S7 Shipping.
*Must show:* the shopper does not lose their cart, and lands back where they were.
*Stories:* US-401, US-305, US-501, US-502

### F3 – Checkout happy path (customer)
S4 Cart → S7 Shipping → S8 Review → S9 Payment (success card) → S10 Confirmation ("email saved to the demo outbox").
*Stories:* US-402–407

### F4 – Payment declined
S9 Payment (decline card) → error on the same screen "Payment declined (demo)" → cart and address kept → try again.
*Stories:* US-404 AC4

### F5 – Cart changed since last visit
Customer signs in → S4 Cart shows: one price changed (note), one item now unavailable (greyed, excluded), one quantity reduced to stock (note).
*Stories:* US-306, US-405 AC4

### F6 – Staff adds a new sauce
S13 Manage products → **Add product** → S14 Product editor → fill form → upload image → save → success message → back to list.
*Stories:* US-601, US-602, US-604

### F7 – Staff updates stock and hides a product
S13 → edit stock inline → set a product to Hidden → product disappears from S2.
*Stories:* US-605, US-606

### F8 – Owner archives a product
S13 → **Archive** → confirmation dialog → product moves to "Archived" status.
*Stories:* US-607

### F9 – Trainer shows the email flow
Place an order (F3) → sign in as staff → S15 Outbox → open newest email.
*Stories:* US-407, US-408

---

## 6. Screen-by-screen requirements

Each screen lists: **purpose**, **linked stories**, **who can see it**, **must show**, **states** to design, and **Streamlit notes**. Please design every screen at **desktop (1280 px)** and **mobile (375 px)** widths.

### S1 – Home

- **Purpose:** First impression. Make people curious and send them into the shop.
- **Stories:** No story in doc 01 covers a home page yet. It reuses catalog data from US-101. *Proposed new story:* **US-108 Home page (Should)**, to be added to doc 01.
- **Who:** Everyone.
- **Must show:**
  - Hero band: the most exciting moment in the app. A bold colour block (`surface-bold` or `highlight`), a big display headline, one supporting line, **Shop all sauces** button, and one large, vivid product or ingredient photo.
  - Category tiles (5 categories) that open S2 pre-filtered (US-103).
  - "Featured sauces" row of 4 product cards.
  - A "Find your heat" strip: heat levels 0–5 as chips that open S2 pre-filtered (US-104).
  - Small demo notice in the footer.
- **States:** loading, no featured products (hide the row).
- **Streamlit notes:** hero = `st.columns` (text + `st.image`). Category tiles and chips = buttons or `st.page_link`. No carousel or auto-play.

### S2 – Shop / Catalog and search

- **Purpose:** Browse, search, filter and sort all active products.
- **Stories:** US-101, US-102, US-103, US-104, US-105, US-106, US-107, US-301 (quick add)
- **Who:** Everyone.
- **Must show:**
  - Search box at the top (US-102 AC1).
  - Filters: category, heat range 0–5, "In stock only", sort (Name A–Z default).
  - Result count, e.g. "15 sauces".
  - **Product card** (key component): image (1:1), name, size, heat icons + label, price, stock badge when low/out, **View** and **Add to cart** buttons.
  - Pagination: 12 per page, Previous / Next, "Page 1 of 2".
- **States:** loading, results, no results ("No sauces found" + **Clear search**), out-of-stock card (Add disabled), low-stock card ("Only 3 left"), no-image card (placeholder).
- **Streamlit notes:** grid = `st.columns(3)` (desktop) in rows; columns stack to one per row on mobile. Filters sit in a sidebar or an expander above results (designer's choice, show both on mobile). **Whole cards cannot be clicked.** Use a **View** button or the product name as a link.

### S3 – Product detail

- **Purpose:** Give everything needed to decide, and make buying easy.
- **Stories:** US-201, US-202, US-203, US-204, US-301
- **Who:** Everyone.
- **Must show:**
  - Large image (left on desktop, top on mobile).
  - Name (heading font), category, SKU (small, muted), price, size, heat icons + label.
  - Stock status: "In stock" / "Only N left" / "Out of stock" (US-201 AC3).
  - Quantity selector (1 to min(stock, 10)) + **Add to cart** (primary).
  - Description, ingredients, and an **Allergens** section with "Demo data – not real allergen advice" (US-202).
  - "You might also like": up to 4 cards from the same category (US-204).
  - "← Back to shop".
- **States:** normal, low stock, out of stock (selector and button disabled), no allergens ("No common allergens listed"), product not found (S16), added-to-cart confirmation.
- **Curiosity idea (optional):** a "Great with…" line (e.g. "Great with tacos, grilled veg"). This needs a new product field. If you include it, mark it as a proposal so we can add it to doc 01.
- **Streamlit notes:** `st.columns([1,1])`, `st.number_input` for quantity, `st.toast` for confirmation, `st.expander` for ingredients/allergens is fine on mobile.

### S4 – Cart

- **Purpose:** Check and adjust the order before checkout.
- **Stories:** US-302, US-303, US-304, US-305, US-306, US-401
- **Who:** Guest, Customer.
- **Must show:**
  - Line items: thumbnail, name, unit price, quantity control, line total, **Remove**.
  - Summary panel: subtotal, shipping fee (or "Free"), total, a hint like "Add £8.50 more for free shipping", **Checkout** (primary).
  - "Continue shopping" link.
- **States:** empty ("Your cart is empty" + link to S2), normal, price changed (warning note on the line), item unavailable (greyed line, "No longer available", excluded from total), quantity reduced to stock (note), guest clicking Checkout (goes to S5).
- **Streamlit notes:** each line is a row of `st.columns`. Summary sits in a right column on desktop and below the items on mobile. Totals update after each change (a short reload is normal).

### S5 – Sign in (and forgot password)

- **Purpose:** Let existing users sign in quickly.
- **Stories:** US-502, US-504, US-506, US-401 AC1–2
- **Who:** Guest.
- **Must show:** email, password, **Sign in** (primary), "Forgot password?", "New here? Create an account" (to S6). If arriving from checkout: a short line "Sign in to finish your order. Your cart is saved."
- **States:** default, submitting, wrong details ("Email or password is incorrect"), forgot-password form, reset link saved ("A reset link was saved to the demo outbox").
- **Streamlit notes:** `st.form` with `st.text_input(type="password")`. Validation messages appear **after** pressing the button, not while typing.

### S6 – Register

- **Purpose:** Create a customer account.
- **Stories:** US-501
- **Who:** Guest.
- **Must show:** display name, email, password, confirm password, password rule ("At least 8 characters"), **Create account**, demo note "Please use a test email address", link to S5.
- **States:** default, field errors (invalid email, short password, passwords don't match), email already registered, success (signed in and returned to previous page).
- **Streamlit notes:** `st.form`. Errors are shown under each field or in one `st.error` list. Both are possible.

### S7 – Checkout: Shipping details

- **Purpose:** Collect the delivery address.
- **Stories:** US-402, US-505 AC2
- **Who:** Customer.
- **Must show:** progress indicator (**Shipping → Review → Payment**), fields from US-402 AC1, demo note "Please do not enter your real address", a small order summary, **Continue to review**.
- **States:** empty, pre-filled from profile, field errors.
- **Streamlit notes:** no native stepper. Design a simple step indicator using text or a progress bar (`st.progress` or styled text).

### S8 – Checkout: Review order

- **Purpose:** Final check before payment.
- **Stories:** US-403
- **Who:** Customer.
- **Must show:** items, quantities, prices, subtotal, shipping, total, shipping address, **Edit cart** and **Edit address** links, **Continue to payment**.
- **States:** normal, item became unavailable (send back to S4 with a message).

### S9 – Checkout: Payment (demo)

- **Purpose:** Simulate payment safely.
- **Stories:** US-404, US-405
- **Who:** Customer.
- **Must show:**
  - A prominent **demo banner** (`color.demo`): "Demo only. No real payment is taken. Do not enter real card details."
  - Visible list of demo card numbers (success and decline) with a **Copy** action.
  - Card number, expiry, CVC fields. Total to pay. **Place order** (primary).
- **States:** default, wrong card ("Please use a demo card number"), processing (button disabled, spinner; prevents double orders, US-405 AC7), declined ("Payment declined (demo)"), stock problem (US-405 AC4: list items to fix).
- **Streamlit notes:** no input masks (e.g. auto-spacing "4242 4242…"). `st.code` gives a built-in copy button for the demo numbers.

### S10 – Order confirmation

- **Purpose:** Reassure and close the loop.
- **Stories:** US-406, US-407
- **Who:** Customer.
- **Must show:** a warm thank-you heading, order number (large, copyable), items, total, shipping address, the line "A confirmation email has been saved to the demo outbox", and **Continue shopping** + **View my orders** buttons.
- **States:** normal only. Refreshing the page must not create a new order.

### S11 – My orders (+ order detail)

- **Purpose:** Let customers see past orders.
- **Stories:** US-409, US-407 AC5
- **Who:** Customer.
- **Must show:** list with order number, date, total, status badge (`placed`). Order detail shows items, totals, address and the related outbox email.
- **States:** no orders yet (friendly empty state + link to shop), list, detail.
- **Streamlit notes:** `st.dataframe` or a list of `st.container` rows. The detail can be an expander or `st.dialog`.

### S12 – Profile

- **Purpose:** Manage display name and default address.
- **Stories:** US-505
- **Who:** Customer.
- **Must show:** display name, email (read-only), default shipping address fields, **Save**, "Change password".
- **States:** default, saved (toast), field errors.

### S13 – Manage products (admin)

- **Purpose:** Staff/owner overview and quick edits.
- **Stories:** US-601, US-605, US-606, US-607, US-608, US-609, US-504
- **Who:** Staff, Owner.
- **Must show:**
  - Tabs: **All products** · **Low stock** · **Categories** (owner only).
  - All products: search by name/SKU, status filter (Active / Hidden / Archived [owner]), table with SKU, name, category, price, **editable stock**, status, actions (**Edit**, **Hide/Show**, **Archive** [owner]).
  - **Add product** (primary) top right.
  - Low stock: active products with stock ≤ 5, lowest first.
  - Categories: list, **Add**, **Rename**. Delete disabled when a category has products, with an explanation.
- **States:** loading, list, no results, stock save success/error, archive confirmation dialog ("Archive Ghost Pepper Inferno? It will be removed from sale for good."), customer opening the page (S16 no access).
- **Streamlit notes:** `st.data_editor` gives an editable table (good for stock). Per-row buttons inside the table are **not** possible. Use row selection + action buttons above the table, or a list of rows built with `st.columns`. `st.dialog` for confirmations.

### S14 – Product editor (add / edit)

- **Purpose:** Create or update a product.
- **Stories:** US-602, US-603, US-604
- **Who:** Staff, Owner.
- **Must show:** fields from US-602 AC1 grouped into sections (**Basics**, **Details**, **Pricing & stock**, **Image**), live preview of the product card, image upload with rules ("JPG, PNG or WEBP, max 2 MB"), current image with **Replace**, **Save** and **Cancel**. In edit mode the SKU is read-only; show "Last updated by Sam Staff, 9 Nov 2026".
- **States:** add (empty), edit (filled), validation errors (duplicate SKU, missing fields, bad price), upload error (wrong type/too big), saving, saved.
- **Streamlit notes:** `st.form`, `st.file_uploader` (its look can only be lightly styled), `st.multiselect` for allergens, `st.slider` or `st.select_slider` for heat. The preview updates after each field change, not per keystroke.

### S15 – Outbox (admin)

- **Purpose:** Show emails that *would* have been sent.
- **Stories:** US-407, US-408, US-506 AC1
- **Who:** Staff, Owner.
- **Must show:** a clear explanation at the top ("Emails are saved here instead of being sent. Nothing leaves this app."), list newest first (to, subject, related order, created time, status `not_sent`), and an email preview pane showing the plain-text body.
- **States:** empty ("No emails yet. Place an order to see one here."), list, selected email.
- **Streamlit notes:** list + preview in two columns on desktop, stacked on mobile. Plain text only, no HTML email designs needed.

### S16 – System states (shared)

Design these once as reusable patterns and show them in context on at least one screen each.

| State | Where | Content | Streamlit element |
| --- | --- | --- | --- |
| **Loading** | Lists, detail, checkout submit | Spinner or skeleton text | `st.spinner`, `st.status` |
| **Empty** | Search, cart, orders, outbox | Friendly line + one action | `st.info` or custom container |
| **Success** | Saves, add to cart | Short confirmation | `st.toast`, `st.success` |
| **Warning** | Price/stock changes | What changed + what we did | `st.warning` |
| **Error** | Forms, failed save, network | What went wrong + how to fix | `st.error` |
| **Not found** | Bad product link | "We couldn't find that sauce." + **Back to shop** | Page content |
| **No access** | Role-protected pages | "You do not have access to this page." + link home / sign in | Page content |
| **Demo banner** | Register, shipping, payment, confirmation, outbox | Calm teal banner, consistent wording | `st.info` with icon, or custom styled block |
| **Signed out** | After sign-out | Toast "You're signed out" on S2 | `st.toast` |

---

## 7. Accessibility and mobile

Target: **WCAG 2.2 level AA** where Streamlit allows it.

### 7.1 Accessibility requirements

1. **Contrast:** text at least 4.5:1, large text and UI parts at least 3:1. Check all tokens in [3.2](#32-suggested-colour-tokens).
2. **Not colour alone:** heat, stock, status and errors use text and/or icons as well.
3. **Labels:** every input has a visible label. Placeholders are not labels.
4. **Errors:** say what is wrong and how to fix it ("Password must be at least 8 characters"), next to or listing the field.
5. **Images:** every product image has alt text (use product name + short description). Decorative images have none.
6. **Keyboard:** every action can be reached and used with the keyboard. Focus must be visible (do not remove focus outlines in custom CSS).
7. **Touch targets:** at least 44 × 44 px on mobile.
8. **Headings:** one H1 per screen, then H2/H3 in order.
9. **Motion:** no auto-playing carousels or animations.
10. **Language:** plain English, short sentences (see [section 8](#8-content-and-tone)).

### 7.2 Mobile

- Design every screen at **375 px** wide, plus desktop at **1280 px**.
- Streamlit **stacks columns vertically** on narrow screens. Design the mobile order on purpose (e.g. on S3: image → name/price → Add to cart → description).
- The sidebar becomes a slide-out menu on mobile. Don't put the only path to an important action in the sidebar.
- Keep the primary action high on the page. Streamlit cannot fix (pin) a button to the bottom of the screen.
- Product grid: 3 columns desktop, 2 tablet (if feasible), 1 mobile.
- Tables (S13) can scroll sideways on mobile. Admin screens on mobile only need to be **usable**, not optimised.

---

## 8. Content and tone

### 8.1 Voice

**Lively, friendly and easy to follow.** Like an enthusiastic shop owner who loves their sauces: excited when describing flavours, calm and clear when helping you pay.

- **Product names, descriptions, hero and category text** can be lively and full of flavour words.
- **Buttons, forms, errors, checkout and admin text** stay calm, plain and short.

| Do | Don't |
| --- | --- |
| "Bright and fruity, with a slow habanero kick." | "THE HOTTEST SAUCE IN THE UNIVERSE!!!" |
| "Added to your cart." | "Item successfully appended to basket entity." |
| "Only 3 left" (when true) | "Hurry! Selling fast!" (fake urgency) |
| "Email or password is incorrect." | "Login failed. Error 401." |
| "Your cart is empty. Let's find you something tasty." | "No data." |

### 8.2 Rules

- Short sentences. Common words. Sentence case for buttons and headings ("Add to cart", not "Add To Cart").
- Buttons say what happens: **Add to cart**, **Continue to payment**, **Place order**.
- Prices always show the currency and two decimals: **£6.50**.
- Heat is described as flavour, never as danger or a dare.
- **Suitable for all ages:** no alcohol references, no violent, crude or "dare you" humour, and no mature product names. This also applies to placeholder copy in mock-ups.
- Demo wording is consistent everywhere: start with **"Demo only."** or **"Demo shop –"**.
- Use UK English spelling to match the GBP prices (colour, favourite), unless the team decides otherwise.

### 8.3 Copy the designer should draft

Hero headline and subline, category tile labels, empty-state lines, demo banner text, order confirmation message, and 15 product descriptions for the seed sauces (1–2 sentences each, see doc 01 §5.2).

---

## 9. What Streamlit can and cannot do

Streamlit builds pages from ready-made Python components. It is fast to build with, but it is **not** a free-form web canvas. Designs that follow this section can be built as drawn. Designs that ignore it will be simplified by developers.

> Exact features depend on the Streamlit version. If a design depends on something marked "version-dependent", flag it so a developer can confirm.

### 9.1 Building blocks you can design with

| Need | Streamlit element |
| --- | --- |
| Grid / side-by-side | `st.columns` (equal or ratio widths, e.g. 1:2) |
| Boxed section / card | `st.container(border=True)` |
| Tabs | `st.tabs` |
| Collapsible section | `st.expander` |
| Modal dialog | `st.dialog` |
| Small pop-out menu | `st.popover` |
| Side panel | `st.sidebar` |
| Forms | `st.form`, `text_input`, `number_input`, `selectbox`, `multiselect`, `slider`, `checkbox`, `radio`, `file_uploader` |
| Buttons / links | `st.button` (primary / secondary / tertiary), `st.page_link`, `st.link_button` |
| Messages | `st.success`, `st.info`, `st.warning`, `st.error`, `st.toast` |
| Loading | `st.spinner`, `st.progress`, `st.status` |
| Tables | `st.dataframe` (read-only, sortable), `st.data_editor` (editable cells) |
| Images | `st.image` |
| Badges / small labels | `st.badge` (version-dependent) or coloured text |
| Icons | Google Material Symbols in buttons, nav, alerts, toasts |

### 9.2 Layout and navigation

- **Can:** multipage apps with per-page URLs; navigation in the sidebar or as a **top bar** (top bar is version-dependent); grouping nav items into sections ("Shop", "Account", "Admin"); showing different nav items per role; wide or centred page width.
- **Cannot (easily):** a fully custom header; sticky headers or footers; a fixed bottom "Add to cart" bar; mega-menus; overlapping elements; precise pixel positioning.
- Columns **stack on mobile** automatically. You control the order only by the order in the design.

### 9.3 Theming and styling

- **Built-in theme** (no hacks): primary colour, background, secondary background, text colour, border colour, corner radius, and base font. Newer versions also support custom fonts loaded from a URL (version-dependent). Map your tokens to these first.
- **Custom CSS** is possible but **fragile**: Streamlit's internal class names can change between versions. Keep custom CSS small: e.g. the hero band, product name font, demo banner, card shadow.
- **Cannot:** style every native element (e.g. the inside of the file uploader or date picker); add custom hover animations to native buttons reliably.

### 9.4 Interaction

- Every interaction **re-runs the page** (a short reload, often with a brief spinner). Avoid designs that rely on instant, smooth micro-interactions.
- **No hover-only content.** Hover tooltips exist on inputs (`help=` text), but nothing important can be hidden behind hover. There is no hover on mobile anyway.
- **No clickable whole cards.** Use a button or link inside the card.
- **No drag-and-drop** (except dropping a file onto the uploader).
- **No input masks** or live formatting (e.g. card numbers, phone numbers).
- **Form validation shows after submit**, not while typing.
- **No infinite scroll or carousels.** Use pagination.
- **No custom JavaScript** in the plan for v1. (Custom components exist but add development cost. Avoid them.)

### 9.5 Quick check before handing over a screen

- [ ] Every element maps to a row in [9.1](#91-building-blocks-you-can-design-with), or is flagged as custom CSS.
- [ ] Nothing depends on hover, drag, sticky positioning or animation.
- [ ] Mobile frame shows the stacked order.
- [ ] Cards use a button/link to open, not the whole card.

---

## 10. Deliverables

Please hand over the following. Share a single Figma (or similar) file link plus exported assets.

### 10.1 Foundations

1. **Mood board** (1 page) with a short note on how it meets the vision.
2. **Design tokens table**: colours, type scale, spacing, radius, shadow, using the token names in [3.2](#32-suggested-colour-tokens), with **contrast results** for every text/background pair.
3. **Streamlit theme mapping**: which tokens go into the built-in theme, and which need custom CSS.
4. **Typography spec** with fallback (default-font) version.

### 10.2 Components

5. **Component sheet** with all states: product card (normal, low stock, out of stock, no image), heat indicator 0–5, stock badge, buttons (primary, secondary, tertiary, disabled), form fields (default, focus, error, disabled), cart line, order summary panel, checkout step indicator, demo banner, alert types, empty state, admin table row, confirmation dialog.

### 10.3 Screens and flows

6. **All screens S1–S16** at desktop (1280 px) **and** mobile (375 px), including every state listed in [section 6](#6-screen-by-screen-requirements).
7. **Role variants** of the navigation (guest, customer, staff, owner).
8. **Flow diagrams F1–F9** linking the frames.
9. **Clickable prototype** of F1 and F3 (at minimum) for training demos.
10. **Annotations** on each screen: linked story IDs, Streamlit element per component (from [9.1](#91-building-blocks-you-can-design-with)), and any custom CSS needed.

### 10.4 Assets

11. **Logo / wordmark** (SVG + PNG, light background).
12. **Chilli heat icons** (filled + outline, SVG + PNG).
13. **Product placeholder image** (square PNG/WEBP, ≥ 800 × 800).
14. **Hero image(s)** and **category tile images** (licensed for training use, with source and licence noted).
15. **Product photo guidelines** (1 page: framing, background, light, size). Optional: styled images for the 15 seed products.
16. A **favicon** (e.g. a simple chilli or jar mark).

Exported images go in `assets/images/`. A CSS snippet (if any) goes in `assets/styles/`.

### 10.5 Content

17. **Copy deck**: all UI text from [8.3](#83-copy-the-designer-should-draft), including the 15 product descriptions.
18. **Accessibility notes**: alt-text rules, focus order notes for complex screens (S2, S9, S13).

### 10.6 Proposals back to the product team

19. A short list of anything that needs a **change to doc 01**: for example, the proposed **US-108 Home page** and the optional **"Great with…"** product field.

---

*Questions about stories or scope: see doc 01 or ask the product owner. Questions about what Streamlit can build: ask a developer before finalising the design.*
