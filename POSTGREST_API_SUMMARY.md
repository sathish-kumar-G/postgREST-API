# PostgREST API Summary

PostgREST is a powerful tool for automatically generating a RESTful API from an existing PostgreSQL database. It provides a simple and efficient way to expose database resources as HTTP endpoints, allowing clients to interact with the database using standard HTTP methods.

## Features

- **Automatic API Generation**: PostgREST automatically generates RESTful API endpoints based on the structure of your PostgreSQL database schema.

- **SQL-based Filtering, Sorting, and Pagination**: Clients can filter, sort, and paginate through database resources using SQL-like query parameters in the URL.

- **Role-Based Security**: PostgREST integrates with PostgreSQL's role-based security system, allowing you to control access to API endpoints based on database roles and permissions.

- **JSON Web Token (JWT) Authentication**: Supports JWT authentication for secure access to API endpoints, allowing you to authenticate and authorize users based on tokens.

- **Customizable Responses**: PostgREST allows you to customize API responses using SQL views and functions, giving you fine-grained control over the data returned to clients.

- **WebSocket Support**: Supports WebSocket connections for real-time data updates and notifications from the database.

- **PostgreSQL Integration**: Leverages PostgreSQL's powerful features such as JSONB data types, stored procedures, and triggers to provide efficient data access and manipulation.


## Installation

PostgREST can be installed using various methods, including binary downloads, Docker images, and package managers. Detailed installation instructions for different platforms can be found in the official documentation.

## Configuration

### 1) PostgREST is configured using a configuration file typically named `postgrest.conf`.

 This file contains settings such as database connection details, authentication options, server settings, and more. You can customize the configuration file to suit your specific requirements.

```conf
db-uri = "postgres://role-name:password@localhost:port/db_name"
db-schemas = "schema-name"
db-anon-role = "anon-role-name"


# OIDC settings
jwt-secret = "{\r\n\"alg\":\"RS256\",\r\n\"e\":\"AQAB\",\r\n\"kid\":\"4Vt3bsGUZcObyqXsjzBFMS37zVyzBm2Woog+mftLMmE=\",\r\n\"kty\":\"RSA\",\r\n\"n\":\"_PVlC0j1k0Zc6ovuQHb1c61Ita0FUGQ_-0wfQiG-73kwk_fuae8IAmFTgDmojZnM4ZpFFFeSpMhsJAM6eYklswhzKm5YygYdabRagKgyoD4_sUIkkclaX4u1fw7YGB-6fyYUxAKlfz7nt2G3HqUL29zmnXIwj4l2NLIclpHJjTOKTV3ZkG-BlSs5BOw9NRSQh56ixm_-bsx8DIh7UGlymwD-msldN6rK3u5FEbTxSOmy3U16vAdX_3G_D6KcOSU_zBpNUYc9bjlPhTp61KCgZR3DW8QxnZ1fs-u_19C_8U3FdonH5lLLlVPq51sjxJEEfR1lzUATgcIlvI5IvzyrKw\",\r\n\"use\":\"sig\"\r\n}"


jwt-secret-is-base64 = false
jwt-audience = "75lir73kccmkded4v2avoqnvmo"
jwt-issuer = "https://cognito-idp.us-east-1.amazonaws.com/us-east-1_3g5LAO5c9"

# JWT role claim key for AWS Cognito groups
jwt-role-claim-key = ".\"cognito:groups\"[0]"

# Cors
server-cors-allowed-origins = "http://localhost:3000"

```
```bash
-- Start PostgREST server
./postgrest postgrest.conf
```

### 2) Docker Configuration

To deploy PostgREST using Docker, you can utilize a docker-compose.yml file as shown below:

```bash
version: "3.8"

services: 
    api-server-db:
        image: service-db
        container_name: service-db
        ports:
          - "5432:5432"
        healthcheck:
            test: [ "CMD", "pg_isready" ]
            interval: 10s
            timeout: 7s
            start_period: 2s
            retries: 3
        volumes:
            - postgrest-restful-server-data:/var/lib/postgresql/data
        restart: "no"
        networks:
            - postgrest

      
    api:
      image: postgrest/postgrest:v12.0.0
      container_name: postgrest
      ports:
          - "8000:8000"
      environment:
          PGRST_DB_URI: postgres://service_usr:service123@api-server-db:5432/service_db
          PGRST_DB_SCHEMAS: "schema-name"
          PGRST_DB_ANON_ROLE: service_usr
          PGRST_SERVER_PORT: 8000
          PGRST_JWT_SECRET: "{\"alg\":\"RS256\",\"e\":\"AQAB\",\"kid\":\"4Vt3bsGUZcObyqXsjzBFMS37zVyzBm2Woog+mftLMmE=\",\"kty\":\"RSA\",\"n\": \"_PVlC0j1k0Zc6ovuQHb1c61Ita0FUGQ_-0wfQiG-73kwk_fuae8IAmFTgDmojZnM4ZpFFFeSpMhsJAM6eYklswhzKm5YygYdabRagKgyoD4_sUIkkclaX4u1fw7YGB-6fyYUxAKlfz7nt2G3HqUL29zmnXIwj4l2NLIclpHJjTOKTV3ZkG-BlSs5BOw9NRSQh56ixm_-bsx8DIh7UGlymwD-msldN6rK3u5FEbTxSOmy3U16vAdX_3G_D6KcOSU_zBpNUYc9bjlPhTp61KCgZR3DW8QxnZ1fs-u_19C_8U3FdonH5lLLlVPq51sjxJEEfR1lzUATgcIlvI5IvzyrKw\",\"use\":\"sig\"}"
          PGRST_JWT_SECRET_IS_BASE64: "false"
          PGRST_JWT_AUDIENCE: "75lir73kccmkded4v2avoqnvmo"
          PGRST_JWT_ISSUER: "https://cognito-idp.us-east-1.amazonaws.com/us-east-1_3g5LAO5c9"
          PGRST_JWT_ROLE_CLAIM_KEY: ".\"cognito:groups\"[0]"
          PGRST_SERVER_CORS_ALLOWED_ORIGINS: "http://localhost:8080"
          PGRST_OPENAPI_SECURITY_ACTIVE: true
      volumes:
          - postgrest-restful-server-data:/var/lib/postgresql/data
      restart: "no"
      networks:
          - postgrest

volumes:
    postgrest-restful-server-data:

networks:
    postgrest:

```

This docker-compose.yml file defines two services: api-server-db for the PostgreSQL database and api for the PostgREST server. Adjust the configuration according to your specific setup and requirements.


## Usage

1. **Define Database Schema**: Design your database schema in PostgreSQL, including tables, views, functions, and triggers as needed.

2. **Install PostgREST**: Install PostgREST on your server or local machine according to the installation instructions provided in the documentation.

3. **Configure PostgREST**: Create a configuration file (`postgrest.conf`) and specify the required settings such as database connection details, authentication options, and server settings.

4. **Start PostgREST Server**: Start the PostgREST server by providing the path to the configuration file and optionally specifying the port on which the server should listen for incoming requests.

5. **Access the API**: Once the server is running, clients can access the generated API endpoints using standard HTTP methods (GET, POST, PUT, DELETE, etc.) and interact with the database resources.


## Example

Suppose we have a PostgreSQL database with a table named `products` containing information about various products. We can use PostgREST to generate a RESTful API for this table as follows:

```sql
-- products table schema
CREATE TABLE products (
    id SERIAL PRIMARY KEY,
    name TEXT NOT NULL,
    price NUMERIC NOT NULL,
    category TEXT NOT NULL
);

-- Insert sample data
INSERT INTO products (name, price, category) VALUES
    ('Laptop', 999.99, 'Electronics'),
    ('Smartphone', 599.99, 'Electronics'),
    ('Headphones', 99.99, 'Electronics');
```


### PostgREST CRUD Operations Example

This document provides examples of CRUD (Create, Read, Update, Delete) operations using API endpoints generated by PostgREST for a hypothetical `products` table in a PostgreSQL database.

### 1. Page and Sort Products

To retrieve a paginated and sorted list of products, you can send a GET request to the `/products` endpoint with query parameters for pagination (`limit` and `offset`) and sorting (`order`).

Example request to retrieve the first 10 products sorted by name in ascending order:

```http
GET /products?limit=10&offset=0&order=name.asc
Prefer: count=exact
```

Sample Response:

```json
[
    {
        "id": 1,
        "name": "Product A",
        "price": 19.99,
        "category": "Electronics"
    },
    {
        "id": 2,
        "name": "Product B",
        "price": 24.99,
        "category": "Clothing"
    },
    ...
]
```

Response Headers:

```http
Content-Range: 0-5/6
```

### 2. Get All Products
To retrieve all products, you can send a GET request to the /products endpoint without any query parameters.

Example request:

```http
GET /products
```

Sample Response:

```json
[
    {
        "id": 1,
        "name": "Product A",
        "price": 19.99,
        "category": "Electronics"
    },
    {
        "id": 2,
        "name": "Product B",
        "price": 24.99,
        "category": "Clothing"
    },
    ...
]
```

### 3. Get Product by ID
To retrieve a specific product by its ID, you can send a GET request to the /products endpoint with a filter for the product ID.

Example request to retrieve the product with ID 1:

```http
GET /products?id=eq.1
```

Sample Response:

```json
[
{
    "id": 1,
    "name": "Product A",
    "price": 19.99,
    "category": "Electronics"
}
]
```

### 4. Create (POST) a Product
To create a new product, you can send a POST request to the /products endpoint with JSON data representing the new product.

Example request:

```http
POST /products
Content-Type: application/json
Prefer: return=representation

{
    "name": "New Product",
    "price": 19.99,
    "category": "Electronics"
}
```

Sample Response:

```json
[
{
    "id": 3,
    "name": "New Product",
    "price": 19.99,
    "category": "Electronics"
}
]
```

### 5. Update (PUT) a Product by ID
To update an existing product, you can send a PUT request to the /products endpoint with the ID of the product to be updated and JSON data containing the updated product information.

Example request to update the product with ID 1:

```http
PUT /products?id=eq.1
Content-Type: application/json
Prefer: return=representation

{
    "name": "Updated Product",
    "price": 24.99,
    "category": "Electronics"
}
```

Sample Response:

```json
[
{
    "id": 1,
    "name": "Updated Product",
    "price": 24.99,
    "category": "Electronics"
}
]
```

### 6. Delete All Products
To delete all products, you can send a DELETE request to the /products endpoint without any query parameters.

Example request:

```http
DELETE /products
```

Sample Response:
204 No Content

### 7. Delete Single Products
To delete single product, you can send a DELETE request to the /products endpoint with query parameters.

Example request:

```http
DELETE /products?id=eq.1
```

Sample Response:
204 No Content

These examples demonstrate how to perform various CRUD operations using the API endpoints generated by PostgREST for the products table. Adjust the endpoint URLs and request payloads according to your specific requirements and configuration.


## Conclusion

PostgREST simplifies the process of building RESTful APIs by automatically generating endpoints from PostgreSQL databases. With its support for advanced features like filtering, authentication, and customizable responses, PostgREST is a valuable tool for building scalable and secure API backends.
