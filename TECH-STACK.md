# BMS_WEB — Tech Stack & Architecture Summary

## Project Identity
- **Name**: BMS_WEB (web-planner)
- **Domain**: Logistics & Transport Management System (TMS)
- **Version**: 0.0.1

---

## Core Stack

| Layer | Technology | Version |
|---|---|---|
| Framework | Angular | 17.1.x |
| Language | TypeScript | ~5.2.2 |
| Styling | SCSS | — |
| Package Manager | npm | — |
| Build Tool | Angular CLI | 17.1.x |
| Container | Docker | — |

---

## UI Libraries

| Library | Role | Version |
|---|---|---|
| ng-zorro-antd | Primary UI component library (Ant Design) | 17.4.x |
| Angular Material + CDK | Secondary UI / form adapters | 17.x |
| PrimeNG | Supplementary components | 17.4.x |
| Font Awesome | Icon set (legacy) | 4.7 |
| @ant-design/icons-angular | Ant Design icon pack | 17.x |

---

## Charts & Visualization

| Library | Role |
|---|---|
| ngx-echarts + echarts | Primary charting (ECharts 6) |
| ng2-charts + chart.js | Secondary charting (Chart.js 4) |
| @swimlane/ngx-charts | Additional chart types |
| chartjs-plugin-datalabels | Chart label plugin |

---

## Key Libraries

| Library | Purpose |
|---|---|
| RxJS 7 | Reactive state & async |
| @ngx-translate | i18n (TH/EN) |
| @angular/fire + Firebase | Push notification / Firebase Auth |
| @microsoft/signalr | Real-time (SignalR WebSocket) |
| jwt-decode | JWT token parsing |
| crypto-js | Client-side encryption |
| pdf-lib + print-js | PDF generation & printing |
| xlsx | Excel export |
| angularx-qrcode | QR code generation |
| @ng-matero/extensions | Extended Material components |
| otpauth | 2FA / TOTP |
| ngx-color | Color picker |

---

## Project Architecture

```
src/
└── app/
    ├── app.module.ts              # Root module
    ├── app-routing.module.ts      # Root routing
    ├── app-routes.ts              # Route definitions
    ├── app-common.ts              # App-level constants
    ├── common-config.ts           # Global config (API URL, language)
    │
    ├── common/                    # Core utilities (shared across all modules)
    │   ├── api/                   # HTTP layer
    │   │   ├── app-api.service.ts           # Primary API service
    │   │   ├── app-api-interceptor.service.ts
    │   │   ├── app-one-api.service.ts
    │   │   ├── other-api.service.ts         # Secondary API service
    │   │   ├── other-api-interceptor.service.ts
    │   │   └── base/
    │   │       ├── api-builder.service.ts
    │   │       ├── api-error.ts
    │   │       └── api-interceptor-handler.ts
    │   ├── preference/            # App-level preferences (localStorage wrapper)
    │   ├── utils/                 # Helpers: JWT, security, download, locale
    │   └── base-page-component.ts # Base class for page components
    │
    ├── guards/                    # Route guards
    │   ├── auth-guard.service.ts  # Blocks unauthenticated users
    │   └── login-guard.service.ts # Redirects logged-in users from login page
    │
    ├── models/                    # TypeScript interfaces/models (~50+ files)
    │   └── app-*.model.ts         # Domain: billing, order, truck, driver, user, etc.
    │
    ├── modules/                   # Feature modules (lazy-loaded)
    │   ├── login/
    │   ├── forgot-password/
    │   ├── auth2fa/               # 2FA authentication
    │   ├── order/                 # Order management module
    │   ├── customer-barcode/      # Barcode print module
    │   ├── configtranslate/       # i18n config UI
    │   └── transport/             # MAIN module — Transport Management
    │       ├── guards/            # Role-based guard
    │       ├── menu/              # Menu config
    │       └── modules/           # Sub-modules (~50 features)
    │           ├── billing-*/     # Billing: expense, invoice, COD, revenue
    │           ├── businesspartner-*/ # Customer & vendor management
    │           ├── *-master/      # Master data: driver, truck, container, route
    │           ├── user-*/        # User, group, role management
    │           ├── config-*/      # Package, service charge config
    │           ├── dashboard/
    │           ├── status-report/
    │           ├── cost-report/
    │           └── capd-documentation/
    │
    └── shared/                    # Reusable UI
        ├── shared.module.ts
        ├── locator.service.ts
        ├── components/            # header, button, error pages
        ├── pipes/                 # type, scan-status, package, date-format, etc.
        └── widgets/               # m-table, date-picker, chart-progress, popup-pdf
```

---

## Build Environments

| Command | Target |
|---|---|
| `npm run start` | Local dev |
| `npm run start:dev` | Dev server |
| `npm run start:uat` | UAT |
| `npm run deploy:prod-on` | Production |

---

## Auth Flow
1. Login → JWT token issued
2. Optional 2FA (TOTP via `otpauth`)
3. `auth-guard` on protected routes
4. Token refresh via interceptor (`app-api-interceptor`)

## API Pattern
- Dual API services: `AppApiService` (main backend) + `OtherApiService` (external/third-party)
- Custom HTTP interceptors handle: auth header injection, token refresh, error handling
- Builder pattern: `ApiBuilderService` for constructing requests

## Naming Conventions
- Components: `kebab-case` folders, `PascalCase` class names
- Models: `app-[domain].model.ts`
- Services: `app-[feature].service.ts`
- Modules: lazy-loaded per feature folder
- Font: **Prompt** (Thai) + **Inter** (EN)
