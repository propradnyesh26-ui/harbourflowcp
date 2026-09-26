# HarborFlow Backend

Spring Boot multi-module microservices backend for HarborFlow Operations.

## Modules

| Module | Port | Responsibility |
|---|---|---|
| `eureka-server` | 8761 | Service discovery registry |
| `api-gateway` | 8080 | Spring Cloud Gateway, JWT validation, CORS, load balancer |
| `auth-service` | 8081 | JWT issuance, bcrypt password hashing |
| `carrier-service` | 8082 | CRUD for shipping carriers (Postgres schema `carrier`) |
| `container-service` | 8083 | CRUD for containers (Postgres schema `container`) |
| `yard-service` | 8084 | Yard slot allocation (Postgres schema `yard`) |
| `gate-service` | 8085 | Truck check-in / check-out log (Postgres schema `gate`) |

## Build

```bash
mvn clean package -DskipTests
```

Each module produces a Spring Boot fat jar in `target/`.

## Run order

Copy `.env.example` to `.env`, set the Neon JDBC URL, username, password, and a
random `HF_JWT_SECRET` of at least 32 bytes. Generate a key with
`openssl rand -base64 48`. Do not commit `.env`; it is ignored by Git. Load the
same variables in each terminal before starting a jar:

```bash
set -a; . ./.env; set +a
```

Build the modules:

```bash
mvn clean package -DskipTests
```

Start Eureka first, then the services in this order. Run each command in a
separate terminal after loading `.env` there:

```bash
java -jar eureka-server/target/eureka-server-1.0.0.jar
java -jar auth-service/target/auth-service-1.0.0.jar
java -jar carrier-service/target/carrier-service-1.0.0.jar
java -jar container-service/target/container-service-1.0.0.jar
java -jar yard-service/target/yard-service-1.0.0.jar
java -jar gate-service/target/gate-service-1.0.0.jar
java -jar api-gateway/target/api-gateway-1.0.0.jar
```

Run the frontend in another terminal:

```bash
cd ../frontend
cp .env.local.example .env.local
npm install
npm run dev
```

## Database

All five persistence services connect to the same Neon database using
`NEON_JDBC_URL`, `NEON_DB_USERNAME`, and `NEON_DB_PASSWORD`. Each service owns
its schema (`auth`, `carrier`, `container`, `yard`, `gate`) through
`hibernate.default_schema`. Hibernate uses `ddl-auto: update` and creates a
missing schema namespace; it does not drop existing tables or data.

## Security

- `HF_JWT_SECRET` shared by auth-service and api-gateway; generate a private
  value locally and use the same value for both.
- Tokens are HS256, 24h expiry.
- All `/api/**` routes except `/api/auth/login` and `/api/auth/register`
  require `Authorization: Bearer <token>` — verified at the gateway.
- The gateway uses explicit Eureka-backed routes; automatic discovery routes
  are disabled so they cannot bypass JWT filters.
- `HF_CORS_ALLOWED_ORIGINS` is a comma-separated list. It defaults to
  `http://localhost:3000,http://127.0.0.1:3000`.

Required backend environment variables are documented in `.env.example`.
Configure `NEXT_PUBLIC_API_BASE=http://localhost:8080` in
`frontend/.env.local.example` for the frontend.

## API docs

Per-service Swagger UI at `http://localhost:<port>/swagger-ui.html`. OpenAPI
spec at `/v3/api-docs`.
