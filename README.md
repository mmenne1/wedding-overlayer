# Gather — wedding gallery service

This folder now contains a deployable full-service MVP:

- `server.js` — Express API and Google Drive importer
- `public/` — gallery studio and customer gallery UI
- `render.yaml` — Render deployment blueprint
- PostgreSQL-backed gallery records
- Google OAuth admin authentication
- Google Drive folder import, including recursive subfolders as categories
- Password-protected customer links with server-side image proxying and downloads
- Owner/subscriber accounts with a one-gallery/50-photo free trial and Stripe subscription gating

## Run locally

```bash
npm install
DATABASE_URL="postgres://..." \
ADMIN_SECRET="long-random-secret" \
GOOGLE_CLIENT_ID="..." \
GOOGLE_CLIENT_SECRET="..." \
GOOGLE_REDIRECT_URI="https://your-service.onrender.com/auth/google/callback" \
ADMIN_EMAILS="your-google-email@example.com" \
GOOGLE_DRIVE_API_KEY="your-key" \
npm start
```

Open `http://localhost:3000`. The Drive API key must be allowed to use the Drive API. The folder and its files must be shared appropriately for the importer to read them.

## Deploy with GitHub + Render

1. Create a GitHub repository and push this `wedding-gallery` folder.
2. Create a PostgreSQL database. Render Postgres availability and free-tier terms can change; Neon or Supabase are also suitable free PostgreSQL providers.
3. In Render, choose **New → Blueprint** and connect the repository. Render will read `render.yaml`.
4. Add the environment variables described in `project.md` to the web service:
   - `DATABASE_URL`
   - `GOOGLE_DRIVE_API_KEY`
   - Google OAuth variables
   - `ADMIN_SECRET` (Render generates this from the blueprint)
   - PayPal: `PAYPAL_CLIENT_ID`, `PAYPAL_CLIENT_SECRET`, `PAYPAL_PLAN_ID`, `PAYPAL_MODE`, `PAYPAL_WEBHOOK_ID`
   - Optional Stripe fallback: `STRIPE_SECRET_KEY`, `STRIPE_PRICE_ID`, `STRIPE_WEBHOOK_SECRET`
   - `PUBLIC_APP_URL`
5. Deploy and open the service URL.

## Notes on access control

Customer pages use random, unguessable IDs and never display the admin workspace or a gallery index. The admin API uses Google OAuth. For a production launch, add rate limiting, audit logs, and a database backup policy. A public Google Drive folder remains accessible to anyone who has its original Drive URL; the gallery link cannot revoke that Drive access.
