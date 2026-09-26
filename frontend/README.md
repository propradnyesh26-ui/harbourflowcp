# HarborFlow Frontend

Next.js 14 dashboard for HarborFlow port operations. Uses shadcn-style
components (Radix UI primitives + Tailwind) for a clean ops-console feel.

## Setup

```bash
npm install
cp .env.local.example .env.local   # point at the API gateway
npm run dev
```

Open http://localhost:3000.

## API client

`lib/api.ts` calls the real backend through the API Gateway configured by
`NEXT_PUBLIC_API_BASE`. It does not fall back to mock or static data. Start the
backend services and configure Neon credentials before using authenticated
dashboard workflows.

## Pages

- `/` — public landing with service registry table
- `/login` — operator sign-in / register
- `/docs` — public API reference with Swagger UI links per service
- `/dashboard` — authenticated overview
- `/dashboard/carriers` — CRUD
- `/dashboard/containers` — CRUD + status workflow
- `/dashboard/yard` — slot grid + place/release
- `/dashboard/gate` — transaction log
- `/dashboard/services` — service registry + Swagger links
