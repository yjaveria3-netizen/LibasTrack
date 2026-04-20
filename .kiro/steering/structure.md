# Project Structure

```
libastrack/
├── backend/                  # Express API server
│   ├── middleware/
│   │   └── auth.js           # JWT verification middleware
│   ├── models/               # Mongoose schemas
│   │   ├── User.js           # Brand identity + Google OAuth tokens
│   │   ├── Product.js        # Inventory items (PRD-XXXX IDs)
│   │   ├── Order.js          # Orders (ORD-XXXX IDs)
│   │   ├── Customer.js       # CRM records (CUS-XXXX IDs)
│   │   ├── Financial.js      # Transactions (TXN-XXXX IDs)
│   │   ├── Supplier.js       # Supplier records
│   │   ├── Collection.js     # Product collections
│   │   └── Return.js         # Returns & refunds
│   ├── routes/               # Express route handlers (one file per resource)
│   │   ├── auth.js           # Google OAuth + JWT endpoints
│   │   ├── products.js
│   │   ├── orders.js
│   │   ├── customers.js
│   │   ├── financial.js
│   │   ├── suppliers.js
│   │   ├── returns.js
│   │   ├── drive.js          # Google Drive/Sheets connection
│   │   └── checklist.js      # Launch checklist (schema defined inline)
│   ├── services/
│   │   └── googleSheets.js   # GoogleSheetsService class — all Sheets/Drive operations
│   ├── .env                  # Local env (gitignored)
│   ├── .env.example
│   ├── package.json
│   └── server.js             # App entry point, middleware setup, route mounting
│
├── frontend/                 # React SPA
│   ├── public/
│   │   └── index.html
│   └── src/
│       ├── components/
│       │   └── Layout.js     # App shell: sidebar nav + <Outlet />
│       ├── context/
│       │   ├── AuthContext.js   # user, loading, loginWithGoogle, logout, formatCurrency
│       │   └── ThemeContext.js  # theme ('dark'|'light'), toggle, isDark
│       ├── pages/            # One file per route
│       │   ├── Login.js
│       │   ├── AuthCallback.js
│       │   ├── BrandOnboarding.js
│       │   ├── Dashboard.js
│       │   ├── Products.js
│       │   ├── Orders.js
│       │   ├── Customers.js
│       │   ├── Financial.js
│       │   ├── Suppliers.js
│       │   ├── Returns.js
│       │   ├── Checklist.js
│       │   ├── Collection.js
│       │   ├── DriveSetup.js
│       │   └── BrandSettings.js
│       ├── utils/
│       │   └── api.js        # Axios instance with auth interceptors
│       ├── App.js            # Router, ProtectedRoute, PublicRoute, providers
│       ├── index.css         # All styles — design tokens + component classes
│       └── index.js          # React root
│
├── .kiro/
│   ├── specs/                # Feature specs
│   └── steering/             # AI steering rules (this directory)
├── package.json              # Root — concurrently scripts for running both apps
└── README.md
```

## Key Patterns

### Multi-tenancy
Every model has `userId: ObjectId` referencing `User`. All queries must filter by `req.user._id`. Never return data across users.

### Auto-increment IDs
Each model uses a `Counter` document (separate model per resource to avoid conflicts) with `findByIdAndUpdate + $inc` in a `pre('save')` hook to generate formatted IDs like `PRD-0001`.

### Google Sheets Sync
After every create/update/delete, call `syncToSheets()` (defined per route file) using `GoogleSheetsService`. Sync is best-effort — errors are caught and logged but don't fail the API response. Only syncs if `user.driveConnected && user.spreadsheetIds[resource]`.

### Frontend Data Fetching
Pages fetch data directly via `api.get/post/put/delete` (the Axios instance from `utils/api.js`). No global state manager — data lives in local `useState` per page. Use `Promise.all` for parallel fetches (see Dashboard).

### Styling
All styles live in `frontend/src/index.css`. Use CSS custom properties (`var(--token)`) for all colors, spacing, and shadows. Theme switching is done via `data-theme` attribute on `<html>`. Never use inline styles for colors — always reference a token.

### CSS Class Conventions
- Layout: `.app-layout`, `.sidebar`, `.main-content`, `.page-header`, `.page-body`
- Typography: `.page-title` (Syne), `.page-subtitle`, `.section-label`, `.card-title` (Instrument Serif italic)
- Components: `.card`, `.btn`, `.btn-primary/secondary/ghost/danger`, `.btn-sm/xs`, `.modal`, `.modal-overlay`
- Data: `.stats-grid`, `.stat-card`, `table`, `.toolbar`, `.search-input`, `.chip`, `.badge`, `.id-chip`
- States: `.badge-pending`, `.badge-delivered`, `.badge-cancelled`, etc.
