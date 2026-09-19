# ACME Employee Salary Management

Employee salary management application built with Spring Boot, Angular, and H2.



https://github.com/user-attachments/assets/96d28ddf-7cd0-421f-9039-baab7baa5017



## Technology

- Java 21
- Spring Boot
- Angular
- Angular Material
- H2 database
- Docker Compose

## Project Structure

```text
acme/
├── salary-api/       # Spring Boot backend
├── acme_frontend/    # Angular frontend
├── docker-compose.yml
├── docs/
└── README.md
```

## Quick Start Without Docker

### 1. Download the Project

Download the ZIP from GitHub and extract it, or clone it:

```bash
git clone YOUR_GITHUB_REPOSITORY_URL
cd acme
```

### 2. Start the Backend

Open `salary-api` in IntelliJ IDEA and run:

```text
SalaryApiApplication.java
```

Or use the terminal:

```bash
cd salary-api
./mvnw spring-boot:run
```

The backend runs at:

```text
http://localhost:8080
```

### 3. Install Frontend Dependencies

Open another terminal:

```bash
cd acme_frontend
npm install
```

### 4. Start the Frontend

```bash
npm start
```

The frontend runs at:

```text
http://localhost:4200
```

Open that URL in a browser.

## Seed Employee Data

To create 10,000 synthetic employees, run this from `salary-api`:

```bash
./mvnw spring-boot:run \
  -Dspring-boot.run.arguments="--seed.enabled=true --seed.count=10000"
```

To create only 10 employees for testing:

```bash
./mvnw spring-boot:run \
  -Dspring-boot.run.arguments="--seed.enabled=true --seed.count=10"
```

The seed data is synthetic and deterministic. CSV and Excel imports are not required for this MVP.

## Run with Docker Compose

Make sure Docker Desktop is running, then execute from the project root:

```bash
docker compose build
docker compose up -d
```

Open the frontend:

```text
http://localhost
```

View logs:

```bash
docker compose logs -f
```

Stop the application:

```bash
docker compose down
```

The Docker setup uses a persistent volume for the H2 database.

## API Examples

```text
GET    /api/employees
GET    /api/employees/{id}
POST   /api/employees
PUT    /api/employees/{id}
PUT    /api/employees/{id}/salary
DELETE /api/employees/{id}
```

Analytics endpoints:

```text
GET /api/analytics/summary
GET /api/analytics/department
GET /api/analytics/country
GET /api/analytics/distribution
```

## Testing

Backend:

```bash
cd salary-api
./mvnw test
```

Frontend:

```bash
cd acme_frontend
npm test -- --watch=false --browsers=ChromeHeadless
```

## Scope

Included:

- Employee CRUD
- Salary updates and history
- Search and filters
- Server-side pagination
- Soft delete
- Salary analytics
- Synthetic seed data
- Docker Compose setup

Not included in this MVP:

- Authentication
- RBAC
- Payroll processing
- Taxes
- Bonuses
- CSV or Excel import
- Live currency conversion
