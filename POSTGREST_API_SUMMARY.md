# PostgREST API Summary

PostgREST is a powerful tool for automatically generating a RESTful API from an existing PostgreSQL database. It provides a simple and efficient way to expose database resources as HTTP endpoints, allowing clients to interact with the database using standard HTTP methods.

## Features

- **Automatic API Generation**: PostgREST automatically generates RESTful API endpoints based on the structure of your PostgreSQL database schema.

- **SQL-based Filtering, Sorting, and Pagination**: Clients can filter, sort, and paginate through database resources using SQL-like query parameters in the URL.

- **Role-Based Security**: PostgREST integrates with PostgreSQL's role-based security system, allowing you to control access to API endpoints based on database roles and permissions.

- **JSON Web Token (JWT) Authentication**: Supports JWT authentication for secure access to API endpoints, allowing you to authenticate and authorize users based on tokens.

- **Customizable Responses**: PostgREST allows you to customize API responses using SQL views and functions, giving you fine-grained control over the data returned to clients.


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
    "id": 1,
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


## Backend Development Approach Comparison

| Functionality     | SpringBoot | SpringBoot (BFF) with Postgrest | Postgrest |
|-------------------|------------|-----------------------------------|-----------|
| Postgres DB Setup          | yes        | yes                               | yes       |
| Postgrest Setup   | no         | yes                               | yes       |
| BFF               | yes        | yes                               | no        |
| Entity            | yes        | no                                | no        |
| Dao               | yes        | no                                | no        |
| Service-Svc       | yes        | no                                | no        |
| Dto               | yes        | yes                               | no        |

This table provides a comparison of different backend development approaches, highlighting which functionalities are supported by each approach. 


## Advantages of PostgREST Compared to the Spring Boot Approach

| Aspect               | PostgREST                                       | Spring Boot                                      |
|----------------------|-------------------------------------------------|--------------------------------------------------|
| Rapid API Development| Simplifies API development by auto-generating   | Requires manual implementation of API endpoints  |
|                      | RESTful endpoints from PostgreSQL database      | and request handling logic                        |
| Lightweight Solution| Lightweight and minimalist approach            | Comprehensive framework with broader feature set |
| Scalability          | Scales well for simple CRUD operations         | Scales well for complex applications and        |
|                      |                                                 | enterprise-level deployments                       |
| Database Integration | Seamless integration with PostgreSQL database   | Requires additional configuration and setup for  |
|                      |                                                 | integrating with PostgreSQL                        |
| Reduced Boilerplate  | Reduces the need for boilerplate code          | Requires more code for setting up and            |
|                      |                                                 | configuring API endpoints                          |
| Prototyping          | Ideal for rapid prototyping and building simple| Suitable for building complex, scalable, and     |
|                      | CRUD-based applications                        | feature-rich applications                          |



## Drawbacks of PostgREST Compared to the Spring Boot Approach

| Aspect                     | Spring Boot                                      | PostgREST                                               |
|----------------------------|--------------------------------------------------|---------------------------------------------------------|
| Custom Business Logic      | Spring Boot allows for the implementation of complex custom business logic within the application code. | PostgREST provides limited support for implementing custom business logic, requiring additional layers or services for complex operations. |
| Flexibility                | Spring Boot offers greater flexibility in designing and scaling applications, allowing for customized application architectures tailored to specific requirements. | PostgREST may have limited flexibility, especially for applications with non-standard API requirements or complex data manipulation needs. |
| Integration with Ecosystem | Spring Boot seamlessly integrates with the broader Spring ecosystem, providing access to a wide range of libraries, tools, and community support. | PostgREST may have limitations in terms of ecosystem integration, potentially requiring workarounds or additional effort for certain functionalities not directly supported by PostgREST. |
| Control over API Behavior  | Spring Boot allows fine-grained control over API behavior and request processing, facilitating customization according to application needs. | PostgREST abstracts away some of the low-level API handling, potentially limiting control over certain aspects of API behavior and performance optimization. |
| Performance Optimization   | Spring Boot applications offer flexibility for performance optimization through various techniques such as caching, query optimization, and asynchronous processing. | PostgREST may have limitations in terms of performance optimization, especially for complex queries or high-volume workloads, requiring additional optimizations at the database or application layer. |
| JWT Security Roles         | Spring Boot supports multiple JWT security roles, allowing for more granular access control and authorization strategies. | PostgREST only supports a single JWT security role, which may limit the complexity of access control policies and authorization mechanisms in multi-role environments. |
| @Transactional Support     | Spring Boot provides built-in support for declarative transaction management using the `@Transactional` annotation, allowing for the management of database transactions within service methods. | PostgREST does not offer built-in support for transaction management, requiring developers to handle transactions at the database level or implement custom solutions for transaction management. |

### Implementing Custom Solutions for Transaction Management in PostgREST:

When using PostgREST, developers may need to implement custom solutions for transaction management. This typically involves handling transactions at the database level using PostgreSQL's transaction capabilities or implementing custom logic within the application layer to ensure data consistency and integrity. While this approach provides flexibility, it requires additional effort and may result in more complex code compared to

                                                                                

## Conclusion

PostgREST simplifies the process of building RESTful APIs by automatically generating endpoints from PostgreSQL databases. With its support for advanced features like filtering, authentication, and customizable responses, PostgREST is a valuable tool for building scalable and secure API backends.

Both PostgREST and Spring Boot with PostgreSQL offer distinct advantages and drawbacks, making them suitable for different use cases and project requirements.

- **PostgREST** simplifies API development by auto-generating RESTful endpoints directly from the database schema. It is suitable for applications with straightforward CRUD operations and where custom business logic can be handled at the database or higher layers.

- **Spring Boot with PostgreSQL** provides greater flexibility, allowing for the implementation of complex business logic, integration with a broader ecosystem of libraries and tools, and fine-grained control over API behavior and performance optimization. It is suitable for applications requiring customizations, scalability, and performance optimization.

When choosing between PostgREST and Spring Boot with PostgreSQL, we should consider the project's specific needs, including complexity, scalability, performance requirements, and the level of control and flexibility desired in API development.

### Additional Considerations:

- **Development Speed:** PostgREST can significantly accelerate the development process by reducing the need for manual endpoint configuration and boilerplate code. For projects prioritizing rapid prototyping or time-to-market, PostgREST may offer a quicker path to building functional APIs compared to Spring Boot.

- **Resource Efficiency:** PostgREST's lightweight architecture and direct database interaction can result in more efficient resource utilization, particularly in scenarios with high concurrency or limited infrastructure resources. Spring Boot, while powerful, may require more memory and processing power due to its larger runtime footprint and additional features.

- **Skillset and Familiarity:** Consideration should be given to the development team's existing skillset and familiarity with the chosen technologies. Developers experienced with SQL and PostgreSQL may find PostgREST intuitive and straightforward to work with, while those with Java and Spring Boot expertise may prefer the familiarity and flexibility of Spring Boot.

- **Community and Support:** Evaluate the level of community support, documentation, and ecosystem maturity for both PostgREST and Spring Boot. While Spring Boot benefits from a large and active community, extensive documentation, and a wide range of third-party libraries and tools, PostgREST's community and ecosystem may be smaller in comparison. Ensure that adequate support and resources are available for the chosen technology stack to address potential challenges and troubleshoot issues effectively.


