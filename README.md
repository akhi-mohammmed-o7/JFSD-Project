# Product Catalog System

A lightweight **RESTful microservice** for managing a product catalog, built with **Spring Boot 3.2.4** and **Java 17** as part of the *Java Full Stack Development (JFSD)* academic module — Project #4.

It exposes a clean CRUD REST API over an embedded **H2 in-memory database**, with request **bean validation** and a full **MockMvc integration test suite** covering happy paths, error paths, and validation rejections.

---

## Features

- Full **CRUD** REST API for products (`GET`, `POST`, `PUT`, `DELETE`)
- **Bean Validation** — product name is required (non-blank), price must be a positive number
- **Embedded H2 database** — zero external dependencies, starts instantly
- **H2 web console** for inspecting data in the browser
- **Pre-seeded demo data** loaded on startup via `data.sql`
- **13 MockMvc integration tests** covering every endpoint, 404 paths, and validation failures
- Correct HTTP semantics: `200`, `201 Created`, `204 No Content`, `400 Bad Request`, `404 Not Found`

## Technology Stack

| Component     | Technology                          |
|---------------|-------------------------------------|
| Language      | Java 17                             |
| Framework     | Spring Boot 3.2.4 (Spring MVC)      |
| Persistence   | Spring Data JPA (Hibernate 6.4)     |
| Database      | H2 (in-memory)                      |
| Validation    | Jakarta Bean Validation             |
| Build Tool    | Apache Maven                        |
| Testing       | JUnit 5 + MockMvc                   |
| API Client    | Postman                             |

## Project Structure

```
catalog-service/
├── pom.xml
└── src/
    ├── main/
    │   ├── java/com/example/catalog/
    │   │   ├── CatalogApplication.java        # Spring Boot entry point
    │   │   ├── controller/
    │   │   │   └── ProductController.java     # REST endpoint handlers
    │   │   ├── model/
    │   │   │   └── Product.java               # JPA entity with validation
    │   │   └── repository/
    │   │       └── ProductRepository.java     # Spring Data JPA interface
    │   └── resources/
    │       ├── application.properties         # Server & DB configuration
    │       └── data.sql                       # Seed data
    └── test/
        └── java/com/example/catalog/
            └── ProductApiIntegrationTest.java # MockMvc integration tests
```

## REST API

Base URL: `http://localhost:8080/api/products`

| Method | Endpoint             | Description               | Success Response           |
|--------|----------------------|---------------------------|----------------------------|
| GET    | `/api/products`      | List all products         | `200 OK` + product array   |
| GET    | `/api/products/{id}` | Get a single product      | `200 OK` / `404 Not Found` |
| POST   | `/api/products`      | Create a new product      | `201 Created`              |
| PUT    | `/api/products/{id}` | Update an existing product| `200 OK` / `404 Not Found` |
| DELETE | `/api/products/{id}` | Delete a product          | `204 No Content` / `404`   |

### Example: Create a product

```bash
curl -X POST http://localhost:8080/api/products \
  -H "Content-Type: application/json" \
  -d '{"name":"Keyboard","description":"Mechanical keyboard","price":79.99}'
```

Response — `201 Created`:

```json
{"id":4,"name":"Keyboard","description":"Mechanical keyboard","price":79.99}
```

### Example: Validation rejection

```bash
curl -X POST http://localhost:8080/api/products \
  -H "Content-Type: application/json" \
  -d '{"name":"Cheap","description":"d","price":-50}'
```

Response — `400 Bad Request` (price must be greater than 0):

```json
{"timestamp":"...","status":400,"error":"Bad Request","path":"/api/products"}
```

## Getting Started

### Prerequisites

- **Java 17** (JDK)
- **Apache Maven 3.x**

### Build & run

```bash
cd catalog-service
mvn spring-boot:run
```

The service starts on **port 8080**. Verify with:

```bash
curl http://localhost:8080/api/products
```

### Run the tests

```bash
cd catalog-service
mvn test
```

Runs all 13 integration tests against an isolated in-memory H2 database.

### H2 Console

While the app is running, open:

```
http://localhost:8080/h2-console
```

- JDBC URL: `jdbc:h2:mem:catalogdb`
- Username: `sa`
- Password: *(empty)*

## API Testing with Postman

The API can be exercised end-to-end using [Postman](https://www.postman.com/):

1. Start the service (`mvn spring-boot:run`).
2. Create a new request in Postman for each endpoint above, using base URL `http://localhost:8080/api/products`.
3. For `POST`/`PUT`, set the `Content-Type: application/json` header and provide a JSON body such as:

```json
{
  "name": "Smartphone",
  "description": "Latest model smartphone with great camera",
  "price": 599.50
}
```

## Product Model

| Field         | Type     | Constraints                              |
|---------------|----------|------------------------------------------|
| `id`          | `Long`   | Primary key, auto-generated              |
| `name`        | `String` | **Required** — must not be blank         |
| `description` | `String` | Optional                                 |
| `price`       | `Double` | **Required** — must be greater than 0    |

## License

This project is created for academic purposes as part of the JFSD curriculum.
