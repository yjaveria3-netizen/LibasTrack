# LibasTrack — Product Overview

LibasTrack is a full-stack fashion brand management platform (MERN stack) built for Pakistani fashion brands. It covers the full business lifecycle: inventory, orders, customers, financials, suppliers, returns, and a 10-phase launch checklist.

## Key Capabilities
- Google OAuth login (no passwords)
- Multi-tenant: each brand's data is fully isolated by `userId`
- Live sync to Google Sheets via Google Drive API — every CRUD operation mirrors to spreadsheets
- Dark/light theme with localStorage persistence
- Auto-generated IDs: `PRD-0001`, `ORD-0001`, `CUS-0001`, `TXN-0001`
- Multi-currency support (default: PKR)

## Modules
| Module | Route | Description |
|---|---|---|
| Dashboard | `/dashboard` | Stats overview, charts, quick actions |
| Products | `/products` | Inventory with variants, pricing |
| Orders | `/orders` | Order lifecycle management |
| Customers | `/customers` | CRM with segments, loyalty |
| Financial | `/financial` | Transactions, payment tracking |
| Suppliers | `/suppliers` | Supplier relationships |
| Returns | `/returns` | Returns & refund workflow |
| Checklist | `/checklist` | 10-phase pre-launch roadmap |
| Drive Setup | `/drive-setup` | Google Drive/Sheets connection |
| Brand Settings | `/brand-settings` | Brand identity configuration |

## Target Users
Fashion brand founders and operations teams managing a brand from launch through growth.
