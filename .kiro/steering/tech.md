# Tech Stack

## Backend (`/backend`)
- **Runtime**: Node.js with Express
- **Database**: MongoDB + Mongoose (ODM)
- **Auth**: Google OAuth 2.0 + JWT (7-day expiry, stored in localStorage)
- **Google APIs**: googleapis v126 — Drive API v3, Sheets API v4
- **Middleware**: helmet, morgan, express-rate-limit (300 req/15min), cors
- **Dev server**: nodemon

## Frontend (`/frontend`)
- **Framework**: React 18 (Create React App)
- **Routing**: React Router v6
- **HTTP client**: Axios with request/response interceptors (auto-attaches Bearer token, redirects to `/login` on 401)
- **Animations**: Framer Motion
- **Charts**: Recharts
- **Toasts**: react-hot-toast
- **Icons**: react-icons
- **Date utils**: date-fns
- **Styling**: Custom CSS only — no Tailwind, no CSS-in-JS, no component libraries

## Fonts
- `Syne` — headings, titles, brand name
- `Instrument Sans` — body, UI, buttons
- `Instrument Serif` — card titles (italic)

## Environment Variables

### Backend (`backend/.env`)
```
PORT=5000
MONGODB_URI=mongodb://localhost:27017/libastrack
JWT_SECRET=
GOOGLE_CLIENT_ID=
GOOGLE_CLIENT_SECRET=
GOOGLE_REDIRECT_URI=http://localhost:5000/api/auth/google/callback
FRONTEND_URL=http://localhost:3000
```

### Frontend (`frontend/.env`)
```
REACT_APP_API_URL=http://localhost:5000/api
```

## Common Commands

```bash
# Install all dependencies
npm run install:all

# Run both backend + frontend concurrently (from root)
npm run dev

# Backend only
npm run dev:backend   # nodemon
npm start             # node (production)

# Frontend only
npm run dev:frontend  # react-scripts start (port 3000)
npm run build         # production build → frontend/build/

# Individual (from their directories)
cd backend && npm run dev
cd frontend && npm start
cd frontend && npm run build
```

## API Conventions
- All routes prefixed with `/api/`
- All responses: `{ success: boolean, ...data }` or `{ success: false, message: string }`
- Auth: `Authorization: Bearer <jwt>` header on all protected routes
- Rate limit: 300 requests per 15 minutes on `/api/`
- Frontend proxy: `frontend/package.json` proxies to `http://localhost:5000`
