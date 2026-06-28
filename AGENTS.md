# ASNA for Chemicals

Full-stack chemical e-commerce running entirely locally. React 19 frontend → Express + SQLite (sql.js) backend.

## Location

```
C:\Users\Dryou\asna\
├── app/           ← React frontend (Vite, port 5173)
├── backend/       ← Express + SQLite (port 3001)
│   ├── index.js              ← Server (~1926 lines, 15 migrations, 50+ endpoints)
│   ├── products-data.json    ← 1,659 products (canonical source)
│   └── data.db               ← SQLite database (auto-created on first run)
├── node_modules/
├── package.json
├── pnpm-lock.yaml
├── pnpm-workspace.yaml
├── AGENTS.md
└── STOCK LIST 21-06-2026 (1).xlsx
```

## Quick Start

```bash
cd C:\Users\Dryou\asna
pnpm install          # Install frontend deps (one-time)
pnpm dev:all          # Start backend (3001) + frontend (5173) together
```

Then open **http://localhost:5173** in your browser.

### Alternative: start separately

```bash
pnpm dev:backend      # Express API on port 3001
pnpm dev:frontend     # Vite dev server on port 5173
```

## Every File in the Project (106 source files)

### Root (8 files)

| File | What it does |
|---|---|
| `package.json` | Workspace root — `pnpm dev:all` starts both frontend + backend |
| `pnpm-workspace.yaml` | Defines pnpm workspace: `app/` |
| `tsconfig.base.json` | Shared TypeScript config for workspace packages |
| `.gitignore` | Ignores node_modules, dist, .env, generated, logs |
| `.npmrc` | pnpm settings: `auto-install-peers=false` |
| `AGENTS.md` | This file |
| `STOCK LIST 21-06-2026 (1).xlsx` | Original Excel stock list from supplier (61 brands, 1,659 products) |
| `pnpm-lock.yaml` | Lockfile (auto-generated) |

### `backend/` — Express API Server (5 files)

| File | What it does |
|---|---|
| `index.js` | **The server.** ~2500 lines, 17 tables, 60+ endpoints: products, cart, auth, orders, admin, Dave AI, warehouse, automation, config, invoices, SDS, contact, terms, payments, quotes, samples, notifications, wishlist, reviews, shipping, barcode, bulk pricing, compliance, export. |
| `products-data.json` | 1,659 products from the Excel stock list (copied from mock/) |
| `data.db` | SQLite database (auto-created on first run, stores users, carts, orders) |
| `package.json` | Dependencies: express, sql.js, cors, jsonwebtoken, bcryptjs, uuid |
| `package-lock.json` | Lockfile (auto-generated) |

### `app/` — React Frontend

#### Config files (7)

| File | What it does |
|---|---|
| `package.json` | Frontend deps (react 19, wouter, tanstack query, vite, tailwind, shadcn/ui) |
| `vite.config.ts` | Proxies `/api` → `localhost:3001` when `VITE_FRONTEND_ONLY=false` |
| `.env.local` | `VITE_FRONTEND_ONLY=false` + `VITE_API_BASE_URL=http://localhost:3001` |
| `.env.example` | Template showing all available env vars (Supabase unrelated, kept as reference) |
| `tsconfig.json` | Frontend TypeScript config |
| `components.json` | shadcn/ui config |
| `index.html` | Vite entry HTML |

#### Public assets (10)

| File | Type |
|---|---|
| `asna-logo.png`, `asna-icon.png`, `favicon.svg` | Branding |
| `hero-bg.png`, `abstract-bg.png` | Backgrounds |
| `chemicals.png`, `glassware.png`, `microscope.png`, `safety.png` | Category images |
| `opengraph.jpg` | Social share image |

_(Mock API removed — backend is always required)_

#### `app/src/` — Source Code

##### Entry (3)

| File | What it does |
|---|---|
| `main.tsx` | React root + QueryClientProvider + AuthProvider |
| `App.tsx` | Router (wouter) — all 16 page routes |
| `index.css` | Tailwind v4 imports + custom animations |

##### Hooks (3)

| File | What it does |
|---|---|
| `use-auth.tsx` | Auth context consumer hook |
| `use-mobile.tsx` | Responsive breakpoint detection |
| `use-toast.ts` | Toast notification hook |

##### Lib (8)

| File | What it does |
|---|---|
| `auth-context.tsx` | **Auth provider.** Register/login via `/api/auth/*`, JWT in localStorage |
| `cart-context.tsx` | **Cart provider.** Add/remove/checkout via `/api/cart` with JWT |
| `supabase-api.ts` | All React Query hooks: `useGetProducts`, `useAddToCart`, `usePlaceOrder`, etc. — all call `/api/*` via fetch |
| `supabase-api.ts` | All React Query hooks: `useGetProducts`, `useAddToCart`, `usePlaceOrder`, etc. — all call `/api/*` via fetch |
| `invoice.ts` | Invoice generation utility |
| `navigate.ts` | Navigation helpers |
| `theme-context.tsx` | Light/dark theme toggle |
| `utils.ts` | `cn()` helper for Tailwind class merging |

##### Components — Custom (9)

| File | What it does |
|---|---|
| `Navbar.tsx` | Top navigation bar |
| `FloatingNavbar.tsx` | Sticky side/floating nav |
| `ProductCard.tsx` | Product listing card |
| `TiltCard.tsx` | 3D tilt-effect card (product detail) |
| `Molecule3D.tsx` | 3D molecule visualization |
| `ParticleField.tsx` | Animated particle background |
| `AnimatedSection.tsx` | Scroll-triggered fade/slide animation wrapper |
| `AdminModal.tsx` | Admin access modal (password: `admin123`) |
| `AIChatbot.tsx` | Chat bubble → calls `POST /api/chat` |

##### Components — UI (57 shadcn/ui components)

Buttons, cards, modals, tables, forms, toasts, tabs, dropdowns, etc. Full component library.
Key ones used directly: `button`, `card`, `badge`, `table`, `tabs`, `dialog`, `input`, `select`, `toast`, `sheet`, `dropdown-menu`, `skeleton`.

##### Pages (16)

| File | Route | What it does |
|---|---|---|
| `home.tsx` | `/` | Landing page with hero, categories, featured products |
| `shop.tsx` | `/shop` | Product catalog with filters, search, pagination |
| `product-detail.tsx` | `/product/:id` | Full product details, CAS, stock, add to cart |
| `cart.tsx` | `/cart` | Cart items, quantities, totals, checkout button |
| `checkout.tsx` | `/checkout` | Address, order summary, place order (deducts stock) |
| `login.tsx` | `/login` | Email/password login |
| `register.tsx` | `/register` | Create account |
| `forgot-password.tsx` | `/forgot-password` | Password reset request |
| `profile.tsx` | `/profile` | User profile, order history |
| `admin.tsx` | `/admin` | Full admin dashboard: orders, products, users, messages, stats, low-stock alerts, logs |
| `contact.tsx` | `/contact` | Contact form |
| `about.tsx` | `/about` | About ASNA |
| `terms.tsx` | `/terms` | Terms of service |
| `privacy.tsx` | `/privacy` | Privacy policy |
| `quote.tsx` | `/quote` | Request a quote |
| `not-found.tsx` | `*` | 404 page |

_(`packages/supabase/` removed — all React Query hooks now live in `app/src/lib/supabase-api.ts`)_

## How It All Connects

```
Browser ─→ Vite Dev Server (port 5173)
             │
             ├── .tsx files (React 19 + wouter)
             │      └── React Query hooks → fetch("/api/...")
             │
             └── Vite proxy (vite.config.ts) → http://localhost:3001
                    ▼
             Express + SQLite (port 3001)
                    │
                    ├── /api/auth/*       → register, login, profile
├── /api/products/*   → list, stats, search, categories, barcode/:code
├── /api/cart/*       → add, update, remove, merge
                    ├── /api/orders/*     → place, reorder, frequent, user
                    ├── /api/quotes/*     → request, admin approve/reject
                    ├── /api/payments/*   → methods, bank details, initiate
                    ├── /api/wishlist/*   → add, remove, check
                    ├── /api/samples/*    → request, admin manage
                    ├── /api/compliance/* → restricted products per country
                    ├── /api/shipping/*   → calculate rates
                    ├── /api/notifications/* → list, unread count
                    ├── /api/invoice/:ref    → PDF invoice
                    ├── /api/contact      → submit message
                    ├── /api/terms/active → get terms
                    ├── /api/chat         → AI chatbot
                     ├── /api/config       → public config (whatsapp number, bank, shipping, vat)
                     ├── /api/invoice/:ref    → PDF invoice
                     ├── /api/products/:id/sds → PDF safety data sheet
                     ├── /api/warehouse/*   → fulfillment dashboard (key: warehouse123)
                     ├── /api/admin/automation/rules → background automation CRUD
                     ├── /api/admin/dave    → AI admin assistant (natural language queries + actions)
                     └── /api/admin/*       → all admin endpoints (key: admin123)
```

## Key Facts

- **1,659 products** across 9 categories, 61 brands — from the Excel stock list
- **Stock is tracked** — deducted on order, validated on cart add
- **Admin password**: `admin123` (both the modal and the API key header)
- **Warehouse key**: `warehouse123` (sent as `x-warehouse-key` header)
- **JWT auth**: Register/login via `/api/auth/*`, token in `localStorage` as `asna_token`
- **Guest cart**: `session_id` based, merged on login via `POST /api/cart/merge`, persisted in localStorage
- **AI Chatbot / Dave**: `/api/admin/dave` accepts natural language in EN/AR — queries stats, orders, products, users, low-stock, search, health, revenue, forecast; actions: update order status, create/delete products. Also accessible from admin Dave tab.
- **Background automation**: `automation_rules` table with 3 default rules (low stock alert, auto-cancel stale orders, daily report). Rules checked every 60s. CRUD via `/api/admin/automation/rules`.
- **Bilingual**: Full Arabic/English with RTL support — 240+ translation keys, language toggle in navbar
- **Features**: PDF invoices, SDS sheets, bulk pricing engine, quotes, samples, compliance, shipping calculator, payment simulation, notifications, product comparison, wishlist, reviews, related products, forecasting, barcode scanning, email notifications, WhatsApp share
- **Barcode scanning**: Camera-based barcode reader in navbar + shop page, `GET /api/products/barcode/:code` lookup, all products seeded with GTIN-13 barcodes
- **Email notifications**: Nodemailer with SMTP config (`SMTP_HOST`, `SMTP_PORT`, `SMTP_USER`, `SMTP_PASS`, `SMTP_FROM`). Falls back to `mail.log` when SMTP not configured. Triggers: order confirmation, status change, contact auto-reply
- **WhatsApp**: Checkout success page has "Send via WhatsApp" button that opens `wa.me` with order details. Number from `GET /api/config` (env var `WHATSAPP_NUMBER`, default `966500000000`).
- **No cloud services required** — everything runs on localhost

## Admin Access

1. Go to `http://localhost:5173/admin`
2. Enter `admin123` in the modal
3. Enter `admin123` in the top-right key field (sends as `apikey` header)

## Notes

- Supabase was originally used, then completely removed in favor of local Express + SQLite. All Supabase remnants (`packages/supabase/`, mock API, `@supabase/ssr`) have been cleaned up.
- The original workspace root was `frontend/` on OneDrive Desktop. `last/` and `backend/` were separate folders. Now everything is consolidated at `C:\Users\Dryou\asna\`.
- Backend auto-creates `data.db` on first run with 16 migrations and 1,659 seeded products (all with barcodes).
- Email: Set `SMTP_HOST`, `SMTP_PORT`, `SMTP_USER`, `SMTP_PASS`, `SMTP_FROM` env vars for real email. Without them, emails are logged to `backend/mail.log`.
- WhatsApp: Set `WHATSAPP_NUMBER` env var on backend (default `966500000000`). If using Saudi number, omit leading `+` and `00`. Example: `966501234567`.
- All 60+ API endpoints verified working: auth, products, cart, orders, quotes, payments, wishlist, samples, shipping, notifications, invoice PDF, SDS PDF, admin, Dave AI, warehouse, automation, config.
