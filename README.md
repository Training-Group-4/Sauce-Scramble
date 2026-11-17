# Demo E-commerce App (Training Project)

A demo online shop used for training. It is built with:

- **[Streamlit](https://streamlit.io/)** for the user interface
- **[Supabase](https://supabase.com/)** for the database (Postgres), sign-in (Auth) and file storage (product images)
- **[Streamlit Community Cloud](https://streamlit.io/cloud)** as an example deployment target

> **Status:** project skeleton only. The folders and config templates are in place; there is no application code yet.

---

## Folder structure

```
.
├── app/                    # All Python application code
│   ├── pages/              # Streamlit multipage screens (catalog, cart, checkout, account...)
│   ├── components/         # Reusable UI pieces (product card, nav bar, cart summary...)
│   ├── services/           # Talks to Supabase: auth, database queries, storage uploads
│   ├── models/             # Data shapes for products, orders, users, cart items
│   ├── utils/              # Small helpers (formatting prices, validation...)
│   └── config/             # Loads settings and secrets in one place
│
├── assets/
│   ├── images/             # Static images (logo, placeholders)
│   └── styles/             # Custom CSS injected into Streamlit
│
├── docs/
│   ├── requirements/       # What the app must do (features, user stories)
│   ├── architecture/       # How the pieces fit together (diagrams, data model)
│   ├── deployment/         # How to deploy to Streamlit Community Cloud
│   └── planning/           # Roadmap, task lists, training session plans
│
├── supabase/
│   ├── migrations/         # SQL files that create tables, policies and buckets
│   └── seed/               # SQL/CSV demo data (sample products)
│
├── tests/
│   ├── unit/               # Fast tests with no network access
│   └── integration/        # Tests that talk to a real (test) Supabase project
│
├── scripts/                # Helper scripts (seed the database, reset demo data...)
│
├── .streamlit/
│   └── secrets.toml.example  # Template for Streamlit secrets
│
├── .env.example            # Template for local environment variables
├── .gitignore              # Keeps secrets and build files out of git
└── README.md
```

Empty folders contain a `.gitkeep` file so git keeps them. You can delete it once a folder has real files.

---

## Secrets and configuration

The app needs three Supabase values: the project URL, the **anon** (public) key, and the storage bucket name.

| Where you run the app        | Where the values go                                          |
| ---------------------------- | ------------------------------------------------------------ |
| Local machine (env vars)     | Copy `.env.example` → `.env`                                 |
| Local machine (Streamlit)    | Copy `.streamlit/secrets.toml.example` → `.streamlit/secrets.toml` |
| Streamlit Community Cloud    | App **Settings → Secrets**, paste the same TOML content      |

`.env` and `.streamlit/secrets.toml` are listed in `.gitignore` and must **never** be committed.
Only use the anon key in this app; the Supabase `service_role` key bypasses security rules and must never be placed in a Streamlit app.

---

## Deploying to Streamlit Community Cloud (overview)

1. Push this repository to GitHub (secrets are excluded by `.gitignore`).
2. In Streamlit Community Cloud, create a new app from the repository and choose the main app file (to be added under `app/`).
3. Paste your secrets into **Settings → Secrets**.
4. Add a `requirements.txt` at the repository root listing dependencies (to be added with the application code).

Full steps will be written in `docs/deployment/`.
