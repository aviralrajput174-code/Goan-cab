# GaonCab — rural mobility platform starter

GaonCab is a mobile-first React + Tailwind PWA frontend with an Express REST API and PostgreSQL schema for village/small-town cab bookings.

## Important status
This is a runnable **MVP foundation**, not a fully launched production transport/payment system. OTP, maps/geocoding, push/SMS, Razorpay, KYC verification, live GPS, and payouts require real provider credentials, legal/compliance review, and device testing before launch. The app intentionally uses development OTP `123456` only when `NODE_ENV=development`; never use that behavior in production.

## Modules included
- Responsive React UI with Hindi/English toggle, User / Driver / Admin role views, vehicle selection, pickup/drop inputs, fare estimate, ride booking, demo driver list, booking history, driver online toggle, earnings summary, and admin controls.
- Express API with request validation, rate-limit/security middleware, health endpoint, ride creation/listing, driver availability, and admin ride status endpoint.
- PostgreSQL schema for users, driver profiles/documents, vehicles, villages/zones, fare rules, rides, payments, reviews, coupons, trusted contacts, notifications, support tickets, and audit logs.
- Seed data for five villages and ten demo drivers.
- PWA manifest and service worker for app-shell caching.
- Environment template and deployment notes.

## Requirements
- Node.js 20+
- PostgreSQL 15+
- npm

## Run locally

### 1. Database
Create a database, then run:

```bash
psql "$DATABASE_URL" -f backend/db/schema.sql
psql "$DATABASE_URL" -f backend/db/seed.sql
```

### 2. Backend
```bash
cd backend
cp .env.example .env
npm install
npm run dev
```

### 3. Frontend
In a second terminal:
```bash
cd frontend
cp .env.example .env
npm install
npm run dev
```
Vite runs on `http://localhost:5173`. The frontend expects API at `http://localhost:4000/api`.

## Production setup checklist
1. Set strong unique `JWT_SECRET`, `DATABASE_URL`, provider keys, `NODE_ENV=production`, and HTTPS.
2. Replace development OTP with Firebase Authentication or MSG91 Verify. Use provider-side verification and rate limits; never store OTPs in plaintext.
3. Add durable sessions/refresh-token rotation, passwordless auth abuse controls, phone-number verification, and account recovery.
4. Use a production map provider (Google Maps or OSM + a hosted geocoding/routing provider). Respect map tile/geocoding terms; do not rely on public OSM tiles for heavy production traffic.
5. Integrate Razorpay using server-created orders, signature verification, webhook idempotency, and reconciliation. Never trust a payment success flag from the browser.
6. Add object storage for KYC files; encrypt at rest, restrict access, scan uploads, and collect only necessary documents. Aadhaar handling requires careful legal/privacy review; prefer a compliant verification provider and avoid storing raw Aadhaar unless legally justified.
7. Use WebSockets or a managed realtime service for live driver location and ride state; current demo API is polling-friendly, not a realtime tracking service.
8. Add SMS/push providers, call masking if needed, WhatsApp Business API templates, SOS escalation process, support operations, and monitoring.
9. Add transactional boundaries, idempotency keys, DB migrations, backups, retention/deletion policies, audit logs, alerting, and automated tests.
10. Have local transport, insurance, tax, payment, privacy, consumer-protection, and child-safety requirements reviewed before launch.

## Deployment outline
- Frontend: Vercel, Netlify, or Cloudflare Pages; set `VITE_API_BASE_URL`.
- API: Render, Railway, Fly.io, or a managed container host; configure secrets and health checks.
- Database: managed PostgreSQL with SSL, backups, and private networking.
- Use separate staging/production projects. Run schema migrations before deployment.
- Configure CORS to the exact frontend domain, HTTPS, request limits, structured logs, uptime alerts, and error reporting.

## API overview
- `GET /api/health`
- `GET /api/villages`
- `GET /api/drivers/nearby?lat=&lng=&vehicleType=`
- `GET /api/rides?userId=`
- `POST /api/rides` — creates a pending ride after validation
- `PATCH /api/rides/:id/status` — admin/demo status update
- `PATCH /api/drivers/:id/availability`

The starter deliberately does not pretend to provide verified OTP/KYC, real maps, payment capture, payout processing, or emergency dispatch. Integrate and test those before public use.
