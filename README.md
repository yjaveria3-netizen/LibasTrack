# Ayesha Ahmad Atelier — Business Management Suite

A full-stack MERN application that replaces Google Drive chaos with a beautiful, user-friendly dashboard. All data enters through your dashboard and syncs **live** to Google Sheets in your Google Drive.

---

## 🌟 Features

- **Google OAuth Login** — Sign in with Gmail, no passwords needed
- **Google Drive Integration** — Paste your Drive folder link once; spreadsheets are created automatically
- **Live Sync** — Every entry (product, order, customer, transaction) syncs instantly to Google Sheets
- **Product Management** — Full CRUD with auto-generated IDs (PRD-0001)
- **Order Management** — Track orders with status (Pending → Delivered)
- **Customer Management** — Customer registry with date joined
- **Financial Records** — Track payments, methods, and statuses
- **Launch Checklist** — All 10 phases pre-loaded (Brand → Post-Launch Growth)
- **Beautiful UI** — Luxury editorial dark theme with gold accents

---

## 🗂️ Project Structure

```
ayesha-atelier/
├── backend/          ← Express + MongoDB + Google APIs
│   ├── models/       ← Mongoose schemas (User, Product, Order, Customer, Financial)
│   ├── routes/       ← API routes (auth, products, orders, customers, financial, drive, checklist)
│   ├── services/     ← Google Sheets service
│   ├── middleware/   ← JWT auth middleware
│   └── server.js
└── frontend/         ← React app
    └── src/
        ├── pages/    ← Login, Dashboard, Products, Orders, Customers, Financial, Checklist, DriveSetup
        ├── components/  ← Layout, Sidebar
        ├── context/  ← AuthContext
        └── utils/    ← Axios API client
```

---

## 🚀 Setup Instructions

### Step 1 — Google Cloud Console

1. Go to [console.cloud.google.com](https://console.cloud.google.com)
2. Create a new project (e.g. "Ayesha Atelier")
3. Enable these APIs:
   - **Google Drive API**
   - **Google Sheets API**
   - **Google+ API / People API**
4. Go to **Credentials → Create Credentials → OAuth 2.0 Client ID**
5. Application type: **Web application**
6. Add authorized redirect URI: `http://localhost:5000/api/auth/google/callback`
7. Copy your **Client ID** and **Client Secret**

### Step 2 — Backend Setup

```bash
cd backend
npm install
cp .env.example .env
```

Edit `.env`:
```env
PORT=5000
MONGODB_URI=mongodb://localhost:27017/ayesha_atelier
JWT_SECRET=your_random_secret_key_here
GOOGLE_CLIENT_ID=your_google_client_id
GOOGLE_CLIENT_SECRET=your_google_client_secret
GOOGLE_REDIRECT_URI=http://localhost:5000/api/auth/google/callback
FRONTEND_URL=http://localhost:3000
```

```bash
npm run dev
```

### Step 3 — Frontend Setup

```bash
cd frontend
npm install
cp .env.example .env
```

Edit `.env`:
```env
REACT_APP_API_URL=http://localhost:5000/api
```

```bash
npm start
```

### Step 4 — First Time Use

1. Open `http://localhost:3000`
2. Click **"Continue with Google"** — sign in with the Gmail that owns your Drive
3. You'll be redirected to **Drive Setup** page
4. Enter your Drive name and paste the link to your **"Ayesha Ahmad Atelier"** folder
5. Click **Connect Drive & Create Sheets** — spreadsheets are auto-created in your Drive
6. Done! Start entering data — it syncs live ✓

---

## 📊 Google Drive Folder Structure Expected

```
Ayesha Ahmad Atelier/    ← Paste link to this folder
└── Database/            ← Spreadsheets auto-created here
    ├── Products.xlsx
    ├── Orders.xlsx
    ├── Customer.xlsx
    └── Financial.xlsx
```

---

## 🔧 Tech Stack

| Layer | Tech |
|-------|------|
| Frontend | React 18, React Router v6, Framer Motion |
| Backend | Node.js, Express |
| Database | MongoDB + Mongoose |
| Auth | Google OAuth 2.0 + JWT |
| Sheets | Google Sheets API v4 |
| Drive | Google Drive API v3 |
| Styling | Custom CSS (luxury dark theme) |

---

## 📦 Production Deployment

**Backend (Railway / Render):**
- Set all `.env` variables in dashboard
- Update `GOOGLE_REDIRECT_URI` to your production URL
- Update `FRONTEND_URL` to your frontend URL

**Frontend (Vercel / Netlify):**
- Set `REACT_APP_API_URL` to your backend URL
- Build: `npm run build`

---

## 💡 Notes

- MongoDB must be running locally (`mongod`) or use MongoDB Atlas
- Google API credentials must have Drive + Sheets scopes
- The `Database` subfolder is searched for automatically; if not found, sheets go to the root folder
- All IDs are auto-generated: `PRD-0001`, `ORD-0001`, `CUS-0001`, `TXN-0001`
