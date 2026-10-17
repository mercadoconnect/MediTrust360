# MediTrust360 Staff Workspace

The MediTrust360 Staff Workspace is a role-aware web application for facility teams. It connects reception, nursing, clinical, pharmacy, laboratory, and finance workflows to the MediTrust API.

## What it supports

- Staff sign-in, password recovery, password reset, password change, and secure sign-out.
- Reception workflows for patient search, consent-aware check-in, walk-in registration, check-in requests, and visit-queue management.
- Nursing workflows for reviewing the live queue and recording patient vital signs.
- Doctor workflows for appointments, patient search, longitudinal records, consultations, prescriptions, and visit-queue management.
- Pharmacy, laboratory, and finance workspaces for their respective operational workflows.
- Facility-scoped access, role-specific navigation, responsive layouts, and a secure server-side API proxy.

## Prerequisites

- Node.js 20 or later
- npm
- A running MediTrust API instance (the default URL is `http://localhost:8000/api`)

## Getting started

Install the dependencies:

```bash
npm install
```

Create a `.env.local` file in the project root:

```env
MEDITRUST_API_BASE_URL=http://localhost:8000/api
```

Update the value if the MediTrust API is hosted elsewhere. Do not expose backend credentials or tokens in this file; authentication tokens are managed in an HTTP-only cookie by the application.

Start the development server:

```bash
npm run dev
```

Open [http://localhost:3000](http://localhost:3000). Unauthenticated visitors are sent to `/login`.

## Available scripts

| Command | Description |
| --- | --- |
| `npm run dev` | Start the local development server. |
| `npm run lint` | Run ESLint. |
| `npm run build` | Create a production build. |
| `npm run start` | Serve the production build. |

## API integration

The browser does not call the MediTrust API directly. Server routes under `app/api` forward requests to `MEDITRUST_API_BASE_URL`:

- `app/api/auth/*` handles staff authentication and password-management requests.
- `app/api/backend/[...path]` forwards authenticated application requests with the server-managed bearer token.

On a successful sign-in, the backend token is stored as the `meditrust_token` HTTP-only cookie. The UI stores only the staff-session details required to render the appropriate workspace.

## Project structure

```text
app/
├── api/                 # Authentication handlers and authenticated API proxy
├── components/          # Role workspaces, patient views, and shared UI
├── lib/meditrust.ts     # API client, domain types, and session helpers
├── login/               # Staff sign-in and password recovery experience
├── globals.css          # Application styles
├── layout.tsx           # Root layout, metadata, and local font setup
└── page.tsx             # Staff workspace entry point
```

## Roles

The interface recognizes `reception`, `nurse`, `doctor`, `pharmacy`, `pharmacist`, `lab`, `lab_tech`, and `finance` staff roles. The API must return a supported `facility_staff.role` and an associated facility for a staff member to access the workspace.

## Production

Before deployment, set `MEDITRUST_API_BASE_URL` to the production API URL in the hosting environment, then run:

```bash
npm run build
npm run start
```

Use HTTPS in production so the authentication cookie is transmitted securely.
