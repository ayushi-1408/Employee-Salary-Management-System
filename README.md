# Employee-Salary-Management-System
# ACME Employee Salary Management

A Spring Boot and Angular application for managing employee salary information.

## Technology

- Java 21
- Spring Boot
- Spring Data JPA
- H2 file database
- Angular
- Angular Material
- Docker Compose

## Expected Project Structure

The application source folders should eventually be placed like this:

```text
acme/
├── salary-api/       # Spring Boot backend
├── acme_frontend/    # Angular frontend
├── docker-compose.yml
├── docs/
└── README.md
```

This README is currently stored in `/Users/TYAGIAY/README.md`. Copy it into the root of your `acme` project before uploading the project to GitHub.

---

## Prerequisites

Install:

- Java 21
- Maven or the Maven wrapper included by Spring Initializr
- Node.js and npm
- Angular CLI
- Git
- Docker Desktop, if using Docker Compose
- IntelliJ IDEA for the backend
- Visual Studio Code for the frontend

Check installed versions:

```bash
java -version
mvn -version
node -v
npm -v
ng version
git --version
docker --version
```

---

# Local Development

Run the backend and frontend in separate terminal windows.

## Start the Spring Boot Backend

Open the backend folder in IntelliJ:

```text
salary-api
```

Run:

```text
src/main/java/com/acme/salary/SalaryApiApplication.java
```

The backend runs at:

```text
http://localhost:8080
```

The API base URL is:

```text
http://localhost:8080/api
```

Alternatively, run it from a terminal:

```bash
cd salary-api
./mvnw spring-boot:run
```

If macOS reports a permission error:

```bash
chmod +x mvnw
./mvnw spring-boot:run
```

## Start the Angular Frontend

Open `acme_frontend` in Visual Studio Code:

```bash
cd acme_frontend
npm install
npm start
```

The frontend runs at:

```text
http://localhost:4200
```

Open this URL in a browser:

```text
http://localhost:4200
```

---

# Angular Development Proxy

When Angular runs on port `4200` and Spring Boot runs on port `8080`, create this file:

```text
acme_frontend/proxy.conf.json
```

```json
{
  "/api": {
    "target": "http://localhost:8080",
    "secure": false,
    "changeOrigin": true,
    "logLevel": "debug"
  }
}
```

The `package.json` start script should be:

```json
{
  "scripts": {
    "start": "ng serve --proxy-config proxy.conf.json",
    "build": "ng build",
    "test": "ng test"
  }
}
```

The Angular environment file should contain:

```text
acme_frontend/src/environments/environment.ts
```

```typescript
export const environment = {
  production: false,
  apiUrl: '/api'
};
```

Angular sends this request:

```text
http://localhost:4200/api/employees
```

The proxy forwards it to:

```text
http://localhost:8080/api/employees
```

After changing the proxy configuration, restart Angular:

```bash
Ctrl + C
npm start
```

---

# H2 Database

The local H2 configuration should be in:

```text
salary-api/src/main/resources/application.properties
```

```properties
spring.application.name=salary-api

server.port=8080

spring.datasource.url=jdbc:h2:file:./data/salarydb
spring.datasource.driver-class-name=org.h2.Driver
spring.datasource.username=sa
spring.datasource.password=

spring.jpa.hibernate.ddl-auto=update
spring.jpa.show-sql=false
spring.jpa.properties.hibernate.format_sql=true

spring.h2.console.enabled=true
spring.h2.console.path=/h2-console

spring.data.web.pageable.default-page-size=25
spring.data.web.pageable.max-page-size=100

spring.jpa.properties.hibernate.jdbc.batch_size=500
spring.jpa.properties.hibernate.order_inserts=true
spring.jpa.properties.hibernate.order_updates=true

seed.enabled=false
seed.count=10000
```

The database files are stored in:

```text
salary-api/data/
```

This folder must be ignored by Git.

## H2 Console

Start the backend and open:

```text
http://localhost:8080/h2-console
```

Use:

```text
JDBC URL: jdbc:h2:file:./data/salarydb
User Name: sa
Password:
```

The password is empty for local development.

---

# Seed Employee Data

The seed runner creates deterministic synthetic employees directly in the database. CSV and Excel import are not required for this project.

The seed creates:

- Employee names
- Unique synthetic emails
- Departments
- Countries
- Job titles
- Hire dates
- Salaries
- Salary history

## Seed 10 Employees Locally

From the backend directory:

```bash
cd salary-api
./mvnw spring-boot:run \
  -Dspring-boot.run.arguments="--seed.enabled=true --seed.count=10"
```

After the seed finishes, stop the application with `Ctrl + C`, then start it normally:

```bash
./mvnw spring-boot:run
```

## Seed 10,000 Employees Locally

```bash
cd salary-api
./mvnw spring-boot:run \
  -Dspring-boot.run.arguments="--seed.enabled=true --seed.count=10000"
```

The seed runner should create only the missing records. For example, if one employee already exists and the target is 10, it creates nine more.

## Verify the Seed

```bash
curl "http://localhost:8080/api/employees?page=0&size=25&sort=lastName,asc"
```

The response should contain:

```json
"totalElements": 10
```

For 10,000 records, it should contain:

```json
"totalElements": 10000
```

Check analytics:

```bash
curl http://localhost:8080/api/analytics/summary
```

The response should contain a matching `headcount`.

---

# API Endpoints

## Employees

```text
GET    /api/employees
GET    /api/employees/{id}
POST   /api/employees
PUT    /api/employees/{id}
PUT    /api/employees/{id}/salary
DELETE /api/employees/{id}
```

Example list request:

```text
/api/employees?page=0&size=25&sort=lastName,asc
```

Supported filters:

```text
search
department
country
minSalary
maxSalary
```

Example:

```text
/api/employees?page=0&size=25&search=alex&department=Engineering
```

## Analytics

```text
GET /api/analytics/summary
GET /api/analytics/department
GET /api/analytics/country
GET /api/analytics/distribution?buckets=10
```

---

# Docker Compose

Docker Compose files:

```text
salary-api/Dockerfile
salary-api/.dockerignore
acme_frontend/Dockerfile
acme_frontend/.dockerignore
acme_frontend/nginx.conf
docker-compose.yml
```

## Start Docker Compose

Run from the root `acme` directory:

```bash
docker compose build
docker compose up -d
docker compose ps
```

The Docker frontend is available at:

```text
http://localhost
```

If the backend mapping is `8081:8080`, it is directly available at:

```text
http://localhost:8081
```

## View Logs

```bash
docker compose logs -f salary-api
docker compose logs -f acme-frontend
```

## Stop Docker Compose

```bash
docker compose down
```

This preserves the named database volume.

To remove the database and reset all data:

```bash
docker compose down -v
```

Use the `-v` command only when intentionally deleting the local database.

## Seed Docker Data

Stop the backend container:

```bash
docker compose stop salary-api
```

Run the seed inside Docker:

```bash
docker compose run --rm \
  -e SPRING_SEED_ENABLED=true \
  -e SPRING_SEED_COUNT=10000 \
  salary-api
```

Start the application:

```bash
docker compose up -d
```

Verify:

```bash
curl http://localhost/api/analytics/summary
```

---

# Tests

## Backend Tests

```bash
cd salary-api
./mvnw test
```

Build the backend:

```bash
./mvnw clean package
```

## Frontend Tests

```bash
cd acme_frontend
npm test -- --watch=false --browsers=ChromeHeadless
```

Build the frontend:

```bash
npm run build
```

---

# Common Problems

## Port 8080 Is Already in Use

Check the process:

```bash
lsof -nP -iTCP:8080 -sTCP:LISTEN
```

Stop the Spring Boot application in IntelliJ, or change the Docker host mapping:

```yaml
ports:
  - "8081:8080"
```

## Angular Is Blank

Check that `proxy.conf.json` exists and that `npm start` uses:

```text
ng serve --proxy-config proxy.conf.json
```

Restart Angular after changing proxy settings.

## Postman Works but Angular Does Not

For local development, use:

```text
Angular: http://localhost:4200
Backend: http://localhost:8080
```

The Angular environment should use:

```typescript
apiUrl: '/api'
```

## API Returns HTML Instead of JSON

If the response starts with `<!doctype html>`, Angular is not using the proxy. Restart Angular with:

```bash
npm start
```

## Docker Frontend Cannot Find Angular Build

Run:

```bash
cd acme_frontend
npm run build
ls dist
```

Update the `COPY` path in `acme_frontend/Dockerfile` to match the generated Angular output folder.

---

# GitHub Upload

Before committing, check the files:

```bash
git status
```

Do not commit:

```text
node_modules/
target/
data/
dist/
.env
*.mv.db
```

Commit the project:

```bash
git add .
git commit -m "feat: complete employee salary management application"
```

Add the GitHub repository:

```bash
git remote add origin YOUR_GITHUB_REPOSITORY_URL
git branch -M main
git push -u origin main
```

Never commit passwords, API keys, private keys, or real employee data.

---

# Demo Video

The demo should be approximately 3–5 minutes and show:

1. Dashboard KPI cards
2. Salary charts
3. Employee list
4. Search
5. Department and country filters
6. Pagination
7. Employee details
8. Salary update
9. Salary history
10. Soft deletion
11. Docker Compose startup
12. Explanation of the main design decisions

Add the video URL here:

```markdown
## Demo Video

[Watch the project demo](PASTE_VIDEO_LINK_HERE)
```

---

# Assessment Scope

Included:

- Employee CRUD
- Salary updates
- Salary history
- Soft delete
- Search
- Filtering
- Server-side pagination
- Analytics
- Deterministic seed data
- Support for 10,000 synthetic employees
- Docker Compose setup
- Backend and frontend tests

Deliberately excluded:

- Authentication
- Role-based access control
- Payroll processing
- Taxes
- Bonuses
- CSV import
- Excel import
- Live currency conversion

Bulk file import is not required. The application uses a clean database seed runner to populate synthetic employee records, which satisfies the assessment requirement.
