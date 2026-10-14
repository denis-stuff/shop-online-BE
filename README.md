# Store Online - Backend

REST API for the Store Online, built with Spring Boot and MariaDB. This is the backend of the project; the frontend lives in a separate repository and consumes this API.

## Technologies

- Java 21
- Spring Boot 3.5.0 (Web, Data JPA, Validation)
- MariaDB
- Maven
- Lombok
- JWT (jjwt) for login tokens
- BCrypt (spring-security-crypto) for comparing encrypted passwords

## Requirements

- JDK 21
- Maven (or the version bundled with IntelliJ)
- MariaDB running on port 3306

## Database

The database is named `storeonlinedb` and contains four tables:

| Table | Description |
|---|---|
| usuarios | Customer and administrator accounts |
| productos | Store catalog |
| ordenes | Purchase header for each order |
| orden_detalle | Products and quantities for each purchase |

To create it, run the script `storeonlinedb.sql` in MariaDB. The application does not create or modify tables on its own.

## Configuration

Edit `src/main/resources/application.properties` with your own values:

```properties
spring.application.name=carritogt
spring.datasource.url=jdbc:mariadb://localhost:3306/storeonlinedb
spring.datasource.username=YOUR_USERNAME
spring.datasource.password=YOUR_PASSWORD
spring.datasource.driver-class-name=org.mariadb.jdbc.Driver
spring.jpa.hibernate.ddl-auto=none

jwt.secret=A_LONG_SECRET_KEY_WITH_AT_LEAST_32_CHARACTERS
jwt.expiracion-minutos=120
server.error.include-message=always
```

Do not commit real credentials or secrets to the repository.

## Running the project

From IntelliJ, run the `StoreOnlineApplication` class. It can also be launched from the terminal:

```bash
mvn spring-boot:run
```

The API is available at `http://localhost:8080`.

## Endpoints

### Products

| Method | Route | Description | Response |
|---|---|---|---|
| GET | /api/productos | Lists active products | 200 |
| GET | /api/productos/{id} | Retrieves a product | 200 / 404 |
| POST | /api/productos | Creates a product | 201 / 400 |
| PUT | /api/productos/{id} | Updates all product fields | 200 / 400 / 404 |
| DELETE | /api/productos/{id} | Deactivates a product (activo = 0) | 204 / 404 |

The DELETE operation does not remove the row; it only marks the product as inactive to preserve order history.

Example body for POST and PUT:

```json
{
  "nombre": "Fast Charger 20W",
  "descripcion": "Wall charger with USB-C input",
  "precio": 95.50,
  "stock": 35,
  "imagenUrl": "https://picsum.photos/200",
  "activo": true
}
```

### Authentication

| Method | Route | Description | Response |
|---|---|---|---|
| POST | /api/auth/login | Logs in and returns a token | 200 / 401 |

Request body:

```json
{
  "email": "user@email.com",
  "password": "your_password"
}
```

Successful response:

```json
{
  "token": "******",
  "user": {
    "id": 2,
    "nombre": "Ana Lopez",
    "email": "ana.lopez@correo.com",
    "rol": "cliente"
  }
}
```

Passwords are stored encrypted with BCrypt in the `contrasena_hash` column and are never returned in API responses.

## CORS

The API accepts requests from `http://localhost:3000`, where the frontend runs in development. To change it, edit the `WebConfig` class.

## Structure

```text
src/main/java/org/storeonline/
    StoreOnlineApplication.java
    AuthController.java
    JwtService.java
    WebConfig.java
    Producto.java
    ProductoController.java
    ProductoRepository.java
    Usuario.java
    UsuarioRepository.java
```

Each table is represented by an entity (data model), a repository (database access), and a controller (API endpoints).
