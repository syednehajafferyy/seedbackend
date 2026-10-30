# Zaheer Seed Corporation — Backend & Admin API

The official backend web service, REST API, and administrative management portal for **Zaheer Seed Corporation** (Kasur, Pakistan). Built with **Next.js 14 (App Router)**, **PostgreSQL on Neon**, and **Prisma ORM**.

---

## 🌾 Overview & Features

- **Decoupled Architecture**: Exposes public REST endpoints with full Cross-Origin Resource Sharing (CORS) support for the [seedfrontend](https://github.com/syednehajafferyy/seedfrontend) application.
- **Neon PostgreSQL**: Production-ready connection pooling with PgBouncer.
- **Prisma ORM 5.22**: Strongly typed data models for:
  - **Admin**: Staff accounts with bcryptjs salt hashing.
  - **Category**: Crop classifications (Cereals, Fibre Crops, etc.).
  - **Product**: Seed varieties with descriptions, images, and publication status.
  - **Inquiry**: Customer inquiries with honeypot and throttle validation.
  - **Order & OrderItem**: Seed orders with item quantities, customer delivery info, and status progression (`NEW`, `CONFIRMED`, `FULFILLED`, `CANCELLED`).
  - **SiteImage**: Dynamic site banner and promotional image references.
- **Admin Control Panel (`/admin`)**:
  - Secure authentication via Jose HS256 JWT tokens in httpOnly cookies.
  - Multi-layer route protection (Next.js Edge Middleware + Server Component session validation).
  - Dashboard overview tracking product counts, inquiry volume, and new orders.
  - Product catalog editor with Vercel Blob public asset upload support.
  - Inquiries manager to review, resolve, and delete customer messages.
  - Orders manager to monitor and advance order statuses.

---

## 📁 Repository Structure

```text
seedbackend/
├── prisma/
│   ├── schema.prisma       # Data models, enums & relational schema
│   └── seed.ts             # First admin initialization script
├── src/
│   ├── app/
│   │   ├── actions/        # Server actions (admin, auth, contact, order)
│   │   ├── admin/          # Admin portal routes (/login, /dashboard, etc.)
│   │   ├── api/            # Public CORS-enabled REST endpoints
│   │   │   ├── categories/ # GET /api/categories
│   │   │   ├── health/     # GET /api/health
│   │   │   ├── inquiries/  # POST /api/inquiries
│   │   │   ├── orders/     # POST /api/orders
│   │   │   └── products/   # GET /api/products
│   │   ├── layout.tsx      # Admin root layout
│   │   ├── page.tsx        # Backend service index
│   │   └── globals.css     # Admin styles & design tokens
│   ├── components/         # Admin components (ProductForm)
│   ├── lib/
│   │   ├── auth.ts         # JWT cookie session & bcrypt helpers
│   │   └── db.ts           # Prisma client singleton
│   └── middleware.ts       # Route guard for /admin/*
├── .env.example            # Environment variables template
├── .gitignore              # Git ignore rules
├── next.config.mjs         # Next.js config & CORS headers
├── package.json            # Node dependencies and scripts
├── postcss.config.js       # PostCSS config
├── tailwind.config.ts      # Tailwind CSS configuration
├── tsconfig.json           # TypeScript configuration
├── vercel.json             # Vercel deployment & CORS headers
└── README.md               # Backend documentation
```

---

## 📡 REST API Reference

All API routes support CORS and return JSON:

### 1. Health Check
- **Endpoint**: `GET /api/health`
- **Response**: `{ "status": "ok", "service": "...", "time": "..." }`

### 2. Products Catalog
- **Endpoint**: `GET /api/products`
- **Query Params**: `?category=<slug>` (optional filter)
- **Response**: `{ "ok": true, "products": [...] }`

### 3. Categories List
- **Endpoint**: `GET /api/categories`
- **Response**: `{ "ok": true, "categories": [...] }`

### 4. Submit Inquiry
- **Endpoint**: `POST /api/inquiries`
- **Body**: `{ "name": "...", "email": "...", "phone": "...", "subject": "...", "message": "..." }`
- **Response**: `{ "ok": true, "message": "Inquiry received successfully", "id": "..." }`

### 5. Submit Order Request
- **Endpoint**: `POST /api/orders`
- **Body**: `{ "name": "...", "phone": "...", "address": "...", "notes": "...", "items": [{ "name": "Wheat Seed", "quantity": 2 }] }`
- **Response**: `{ "ok": true, "message": "Order placed successfully", "orderId": "..." }`

---

## 🚀 Setup & Local Development

### 1. Prerequisites
- Node.js >= 18.x
- PostgreSQL database (or free Neon account)

### 2. Installation
```bash
npm install
```

### 3. Environment Variables
Copy `.env.example` to `.env`:
```env
DATABASE_URL="postgresql://USER:PASSWORD@HOST-pooler.neon.tech/DB?sslmode=require&pgbouncer=true"
DIRECT_URL="postgresql://USER:PASSWORD@HOST.neon.tech/DB?sslmode=require"
AUTH_SECRET="your-32-byte-base64-random-secret"
ADMIN_NAME="Administrator"
ADMIN_EMAIL="admin@mail.com"
ADMIN_PASSWORD="admin123"
BLOB_READ_WRITE_TOKEN=""
```

### 4. Database Setup & Seed
```bash
npx prisma generate
npx prisma migrate dev --name init
npm run db:seed
```

### 5. Run Server
```bash
npm run dev
```
Open [http://localhost:3000/admin](http://localhost:3000/admin) to log in.

---

## 🌐 Deploy to Vercel

1. Push or import this repository into [Vercel](https://vercel.com).
2. Framework Preset: **Next.js**.
3. Configure Environment Variables:
   - `DATABASE_URL`
   - `DIRECT_URL`
   - `AUTH_SECRET`
   - `BLOB_READ_WRITE_TOKEN`
4. Run `npx prisma migrate deploy` to sync your production database schema.
