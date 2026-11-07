# Resto Backend

REST API for managing a restaurant, built with **Spring Boot 3.4** and **Java 21**.

The backend covers the full restaurant workflow: authentication, clients, orders, tables, menu items, servers, delivery drivers, deliveries, invoices, stock management, notifications, and manager reporting.

---

## Features

- **JWT authentication** with access and refresh tokens
- **Role-based authorization** (`CLIENT`, `SERVEUR`, `MANAGER`, `LIVREUR`, `RESPONSABLE_STOCK`, `RESPONSABLE_CUISINE`, `ADMIN`)
- **Client management** including loyalty points and account status
- **Order management** (dine-in, takeaway, delivery) with status tracking and amount calculation
- **Table management** (availability, occupancy, reservation, statistics)
- **Menu management** with categories, images, ingredients, availability and stock checks
- **Stock management** with ingredients, movements, alerts, expiry tracking and CSV exports
- **Delivery management** with driver assignment, reassignment and status tracking
- **Invoicing** with payment status, payment methods, revenue and VAT reports
- **Manager dashboard** with statistics, reports (daily/weekly/monthly/yearly), PDF/Excel/CSV exports
- **Notifications** and email support
- **OpenAPI / Swagger UI** documentation

---

## Tech Stack

| Layer | Technology |
|---|---|
| Language | Java 21 |
| Framework | Spring Boot 3.4.3 |
| Web | Spring MVC (`spring-boot-starter-web`) |
| Persistence | Spring Data JPA / Hibernate |
| Database | MySQL |
| Security | Spring Security + JWT (`jjwt` 0.12.5) |
| Validation | Jakarta Validation |
| Mapping / Boilerplate | Lombok |
| API Docs | Springdoc OpenAPI 2.7.0 + Swagger UI |
| Email | Spring Boot Mail |
| Build | Maven (Maven Wrapper included) |

---

## Prerequisites

- **JDK 21**
- **MySQL** (running and accessible)
- No local Maven installation required — use the included `mvnw` wrapper

---

## Getting Started

### 1. Clone the repository

```bash
git clone https://github.com/youssefelkassimi/resto-backend.git
cd resto-backend
```

### 2. Create the database

The application expects a MySQL database named `restoo` (Hibernate `ddl-auto: update` creates/updates tables automatically):

```sql
CREATE DATABASE restoo;
```

### 3. Configure the application

Edit `src/main/resources/application.yaml`:

```yaml
server:
  port: 8081

spring:
  datasource:
    url: jdbc:mysql://localhost:3333/restoo
    username: root
    password: 1111
```

> The default configuration points to MySQL on port **3333**. Adjust the URL, username and password to match your local setup.

Mail credentials and the JWT secret are also configured in this file and should be set for your environment before deploying.

### 4. Run the application

Linux / macOS:

```bash
./mvnw spring-boot:run
```

Windows:

```cmd
mvnw.cmd spring-boot:run
```

The API starts on **http://localhost:8081**.

### 5. Build a JAR

```bash
./mvnw clean package
java -jar target/rsi-0.0.1-SNAPSHOT.jar
```

---

## API Documentation

Once the application is running, the interactive API documentation is available at:

- **Swagger UI:** http://localhost:8081/swagger-ui.html
- **OpenAPI JSON:** http://localhost:8081/v3/api-docs

Authentication and Swagger endpoints are publicly accessible. All other endpoints require a valid JWT.

---

## Authentication

Authentication is stateless and uses JWT bearer tokens.

| Method | Endpoint | Description |
|---|---|---|
| `POST` | `/api/auth/register` | Register a new client account and receive tokens |
| `POST` | `/api/auth/login` | Authenticate and receive access + refresh tokens |
| `POST` | `/api/auth/refresh` | Obtain a new access token from a refresh token |

Send the access token on protected requests:

```http
Authorization: Bearer <access-token>
```

Configured defaults (`application.yaml`):

- Access token expiration: **24 hours**
- Refresh token expiration: **7 days**
- Failed login attempts lock the account temporarily after 5 attempts

---

## Main API Modules

All routes are prefixed with `/api`.

| Module | Base path | Highlights |
|---|---|---|
| Authentication | `/api/auth` | Register, login, refresh token |
| Clients | `/api/clients` | CRUD, search, VIP, loyalty points, order history, statistics |
| Orders | `/api/commandes` | CRUD, status transitions, availability check, amount calculation, CSV export |
| Tables | `/api/tables` | CRUD, occupy/release/reserve, availability, statistics |
| Menu items | `/api/plats` | CRUD, images, categories, ingredients, stock availability, recommendations |
| Servers | `/api/serveurs` | CRUD, assignment to orders, performance statistics |
| Delivery drivers | `/api/livreurs` | CRUD, availability, leave/deactivation, delivery history, statistics |
| Deliveries | `/api/livraisons` | Create, assign/reassign driver, start/complete/cancel, delays |
| Invoices | `/api/factures` | Create, pay/cancel, payment method, revenue, VAT report, download |
| Stock | `/api/stock` | Ingredients, movements, alerts, inventory, reports, CSV export |
| Managers | `/api/managers` | CRUD, dashboard stats, reports, PDF/Excel/CSV exports, alerts |
| Files | `/api/file` | Upload and download files |

---

## Project Structure

```
src/main/java/com/fst/rsi/resto
├── config/       # Security, JWT, CORS, OpenAPI and async configuration
├── controller/   # REST endpoints
├── dto/          # Request/response data transfer objects
├── entity/       # JPA entities and enums
├── exception/    # Custom exceptions and error handling
├── repository/   # Spring Data JPA repositories
├── service/      # Business logic
└── RestoApplication.java

src/main/resources
└── application.yaml
```

### Domain model

Core entities include `User`, `Client`, `Serveur`, `Manager`, `Livreur`, `Commande`, `LigneCommande`, `Plat`, `Ingredient`, `CompositionPlat`, `TableRestaurant`, `Livraison`, `Facture`, `Fournisseur`, `GestionStock`, `AlerteStock`, `Notification`, `PerformanceServeur` and `RapportVente`.

---

## Testing

```bash
./mvnw test
```

---

## Notes

- CORS is configured to allow requests from any origin pattern with credentials, and supports `GET`, `POST`, `PUT`, `PATCH`, `DELETE` and `OPTIONS`.
- The default JWT secret and database credentials in `application.yaml` are development defaults. Replace them with secure values for any non-local environment.
- Hibernate is configured with `ddl-auto: update` and SQL logging enabled.
