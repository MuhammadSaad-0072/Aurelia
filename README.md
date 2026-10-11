# Aurelia Document Studio

A Vercel-ready workspace for creating and managing preliminary confidentiality agreements and letters of intent. Accounts use Supabase Auth; drafts are stored in Supabase Postgres and protected by row-level security. The editor supports live previews, draft updates and deletion, and browser print-to-PDF.

Generated documents are starting drafts, not legal advice. Have qualified counsel review them for the relevant jurisdiction and transaction before signing.

## Stack

- Next.js App Router, React, TypeScript, Tailwind CSS
- Supabase Auth and Postgres with per-user RLS policies
- Vercel deployment or Docker Compose

## Supabase setup

1. Create a Supabase project.
2. In the SQL Editor, run [`supabase/schema.sql`](supabase/schema.sql).
3. In Authentication settings, enable email/password sign-up. For email confirmation, configure the site URL and allowed redirect URLs for your local and production domains.
4. Copy the project URL and anon/publishable key from Project Settings → API. Do not use the `service_role` key in this application.

## Run locally

```bash
npm ci
Copy-Item .env.example .env.local
```

Set `SUPABASE_URL` and `SUPABASE_ANON_KEY` in `.env.local`, then run:

```bash
npm run dev
```

Open http://localhost:3000. Create an account, confirm the email if confirmation is enabled, sign in, then start a document from the template library.

## Deploy to Vercel

1. Import this repository into Vercel. If the repository contains the parent folder, set Root Directory to `estatehub`.
2. Add `SUPABASE_URL` and `SUPABASE_ANON_KEY` under Project Settings → Environment Variables for Production (and Preview/Development as needed).
3. Configure the Supabase Auth Site URL and allowed redirect URLs to include the deployed domain.
4. Deploy with the default Next.js build command (`npm run build`).

No database runs inside Vercel and no local file storage is used for user documents. Supabase persists the documents across deployments and regions.

## Docker

Copy `.env.example` to `.env`, set the Supabase project values, and run:

```bash
docker compose up --build
```

The app is available at http://localhost:3000. Docker uses the same Supabase database as Vercel; no database volume is required.

## Main routes

- `/` — template overview
- `/login` — account creation and sign-in
- `/workspace` — private saved documents
- `/documents/new` — create a confidentiality agreement or letter of intent
- `/documents/:id` — edit, print, save as PDF, or delete a draft

Document API routes verify the Supabase user and pass the user JWT through to PostgREST. Database RLS ensures an account can only read or change rows it owns.
