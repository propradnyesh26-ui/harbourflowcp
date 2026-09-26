# HarborFlow — Automated Port Logistics & Container Yard Management

Service-Oriented Architecture project for **HarborFlow Operations** —
a fictional port logistics company that manages maritime container movements,
yard slot allocation, and gate operations.

## Repository layout

```
.
├── backend/                Spring Boot microservices (Maven multi-module)
│   ├── pom.xml
│   ├── eureka-server/      Service discovery registry (port 8761)
│   ├── api-gateway/        Spring Cloud Gateway + JWT filter + CORS (port 8080)
│   ├── auth-service/       JWT issue + bcrypt password hashing (port 8081)
│   ├── carrier-service/    CRUD for shipping carriers (port 8082)
│   ├── container-service/  CRUD for containers (port 8083)
│   ├── yard-service/       Yard slot allocation workflow (port 8084)
│   └── gate-service/       Truck check-in / check-out log (port 8085)
└── frontend/               Next.js 14 dashboard with shadcn-style UI (port 3000)
```

## Tech stack

| Layer | Tech |
|---|---|
| Service discovery | Spring Cloud Netflix Eureka |
| Edge / routing | Spring Cloud Gateway + LoadBalancer |
| Auth | Spring Security + jjwt 0.12 (HS256) |
| Services | Spring Boot 3.2 + Spring Data JPA |
| Database | Neon Postgres, one DB / per-service schema |
| API docs | springdoc-openapi 2.5 (Swagger UI per service) |
| Frontend | Next.js 14 (App Router) + Tailwind + Radix UI primitives |
| Build | Maven 3.9 (Java 21) |

## Running

### Prerequisites

- Java 21 (Temurin recommended)
- Maven 3.9+
- Node.js 18+ & npm
- A Neon PostgreSQL database and its connection details

### Backend

```bash
cd backend
cp .env.example .env
# Fill in the Neon connection values and a generated HF_JWT_SECRET in .env.
set -a; . ./.env; set +a
mvn clean package -DskipTests
# Run the jars as described in backend/README.md, with the same environment loaded.
```

### Frontend

```bash
cd frontend
npm install
npm run dev
# open http://localhost:3000
```

Create `frontend/.env.local` (see `.env.local.example`) pointing at the gateway
once the backend is up.

The backend uses one Neon database with separate `auth`, `carrier`, `container`,
`yard`, and `gate` schemas. Database credentials and the shared JWT signing key
are supplied through `backend/.env`; no credentials are checked into the source.

## Default credentials

- Email: `admin@harborflow.com`
- Password: `admin123`

If the auth-service DB is empty, register a new account from the login page.

## API documentation

Each business service exposes Swagger UI:

| Service | URL |
|---|---|
| auth-service | http://localhost:8081/swagger-ui.html |
| carrier-service | http://localhost:8082/swagger-ui.html |
| container-service | http://localhost:8083/swagger-ui.html |
| yard-service | http://localhost:8084/swagger-ui.html |
| gate-service | http://localhost:8085/swagger-ui.html |
