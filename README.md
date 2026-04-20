<p align="center">
    <img src="https://capsule-render.vercel.app/api?type=waving&height=210&text=LibasTrack&fontAlign=50&fontAlignY=35&fontSize=64&animation=twinkling&desc=Fashion%20Brand%20Operations%20Platform&descAlignY=58&color=0:0f172a,35:1e293b,100:06b6d4" alt="LibasTrack banner" />
</p>

<p align="center">
    <img src="https://readme-typing-svg.herokuapp.com?font=Fira+Code&weight=600&size=20&pause=1200&center=true&vCenter=true&width=900&lines=Manage+Products%2C+Orders%2C+Customers%2C+Financials+and+Returns;Google+OAuth+%2B+Multi-Tenant+Architecture;Live+Sync+with+Google+Sheets+and+Local+Excel" alt="Typing animation" />
</p>

<p align="center">
    <img src="https://img.shields.io/badge/Stack-MERN-0ea5e9?style=for-the-badge" alt="MERN" />
    <img src="https://img.shields.io/badge/Auth-Google%20OAuth-22c55e?style=for-the-badge" alt="Google OAuth" />
    <img src="https://img.shields.io/badge/Sync-Google%20Sheets%20%2B%20Excel-f59e0b?style=for-the-badge" alt="Sync" />
    <img src="https://img.shields.io/badge/UI-React%20%2B%20Framer%20Motion-a855f7?style=for-the-badge" alt="UI" />
</p>

## Overview
LibasTrack is a full-stack platform for fashion brands to run end-to-end operations in one system.

It connects:
- product catalog and inventory
- order pipeline and fulfillment
- customer CRM and loyalty metrics
- transactions and payment records
- returns and refund workflows
- Google Sheets or local Excel sync

## Animated Highlights
<p align="center">
    <img src="https://github-profile-summary-cards.vercel.app/api/cards/profile-details?username=anuraghazra&theme=github_dark" alt="animated card" />
</p>

Core capabilities:
- Google OAuth sign-in
- multi-tenant data isolation
- auto-generated business IDs like PRD-0001, ORD-0001, CUS-0001, TXN-0001
- operational modules for Products, Orders, Customers, Financial, Suppliers, Returns, Checklist, Collections
- live sync to cloud sheets and local excel files

## Architecture
```text
LibasTrack/
|- backend/
|  |- models/
|  |- routes/
|  |- services/
|  |- middleware/
|  |- server.js
|  |- setup.js
|- frontend/
|  |- src/
|  |  |- pages/
|  |  |- components/
|  |  |- context/
|  |  |- hooks/
|  |  |- utils/
|  |- public/
|  |- package.json
|- README.md
```

## Tech
| Layer | Technology |
|---|---|
| Frontend | React 18, React Router v6, Framer Motion, SWR |
| Backend | Node.js, Express |
| Database | MongoDB, Mongoose |
| Auth | Google OAuth 2.0, JWT |
| Sync | Google Sheets API v4, Google Drive API v3, ExcelJS |
| Security | Helmet, rate limiting, payload sanitization |

## Quick Start (After Clone)

### 1. Clone
```bash
git clone <your-repo-url>
cd LibasTrack
```

### 2. Backend
```bash
cd backend
npm install
node setup.js
```

Then open .env and set real values:
- MONGODB_URI
- JWT_SECRET
- GOOGLE_CLIENT_ID
- GOOGLE_CLIENT_SECRET
- GOOGLE_REDIRECT_URI
- FRONTEND_URL

Run backend:
```bash
npm run dev
```

### 3. Frontend
```bash
cd ../frontend
npm install
```

Create .env (or copy from .env.example):
```env
REACT_APP_API_URL=http://localhost:5000/api
REACT_APP_NAME=LibasTrack
```

Run frontend:
```bash
npm start
```

### 4. Open App
- http://localhost:3000

## Google Cloud Setup
1. Create a Google Cloud project.
2. Enable Google Drive API and Google Sheets API.
3. Create OAuth web credentials.
4. Add redirect URI:
```text
http://localhost:5000/api/auth/google/callback
```

## Data Linking Across Modules
LibasTrack links records across sheets and modules through business IDs:
- Orders -> Customer ID
- Financial -> Order ID, Customer ID
- Returns -> Order ID, Customer ID, Product ID
- Customer stats recalculate from orders

This allows cross-sheet analysis like:
- which customer bought which products
- which order produced which transaction
- which return maps to which order and product

## Deployment
Backend:
- set production env vars in your host
- set FRONTEND_URL and GOOGLE_REDIRECT_URI to production URLs

Frontend:
- set REACT_APP_API_URL to backend URL
- run build command:
```bash
npm run build
```

## Security Notes
- never commit .env files
- commit only .env.example and template-safe setup files
- restrict OAuth credentials and MongoDB network access

<p align="center">
    <img src="https://capsule-render.vercel.app/api?type=waving&section=footer&height=120&color=0:06b6d4,100:0f172a" alt="footer" />
</p>
