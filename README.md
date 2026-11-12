# 90 Degree – QR Menu & Order Panel

Customers scan a table QR code, order from the menu, and track their order.
Staff manage orders, the menu (with photos) and reset tables from `/#admin`.

## Deploy on Vercel (about 5 minutes)

1. Push this folder to a GitHub repo (or run `npx vercel` in this folder).
2. In Vercel: **Add New → Project → import the repo** (no build settings needed).
3. Project → **Storage → Marketplace → Upstash Redis → Create** and connect it to the project.
   This adds the database environment variables automatically (free plan is enough).
4. Project → **Settings → Environment Variables**, add:
   - `ADMIN_PASSWORD` – your admin password (8+ characters, make it long)
   - `SESSION_SECRET` – random string, create with `openssl rand -base64 32`
   - `TABLES` – number of tables (default 12)
5. **Redeploy** (Deployments → ⋯ → Redeploy) so the variables take effect.
6. Open `https://YOUR-DOMAIN/#admin`, log in, go to **Orders** and **print the table QR codes**.
   Print them *after* your final domain is set, because the QR codes contain the site address.

## Using it
- **Orders tab:** move orders pending → cooking → ready → served. "Open tables" shows each table's bill so far.
- **Reset table:** clears that table's orders from the live list and from customers' phones, ready for the next guests. Sales totals still count them for 24 hours.
- **Menu tab:** add, edit, delete items, change photos, mark sold out.

## Security built in
- Admin password check on the server, signed `HttpOnly` + `Secure` + `SameSite=Strict` cookie (12 h), login rate limit (6 tries / 15 min).
- All admin actions require the session; customers can only create orders and read their own (by unguessable order ID).
- Prices and totals are calculated on the server from the menu, so customers can't change prices. Quantities, table numbers, notes and photos are validated.
- Order rate limit per IP, CSRF protection (origin check + JSON-only), no secrets in the browser.
- Security headers in `vercel.json`: CSP, HSTS, no framing, nosniff, referrer and permissions policies.

## Notes
- Local test: `npx vercel dev` with the variables in a `.env` file (copy `.env.example`).
- Customers need no account. Their "My orders" list is remembered on their own phone.
- The QR library loads from cdnjs (allowed in the CSP). To remove that outside dependency, host `qrcode.min.js` in `/public`.
