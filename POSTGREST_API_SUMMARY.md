# PostgREST API Summary

PostgREST is a powerful tool for automatically generating a RESTful API from an existing PostgreSQL database. It provides a simple and efficient way to expose database resources as HTTP endpoints, allowing clients to interact with the database using standard HTTP methods.

## Features

- **Automatic API Generation**: PostgREST automatically generates a RESTful API from your PostgreSQL database schema, reducing the need for manual API development.
  
- **SQL-based Filtering, Sorting, and Pagination**: Clients can filter, sort, and paginate through database resources using SQL-like query parameters in the URL.

- **Role-Based Security**: PostgREST integrates with PostgreSQL's role-based security system, allowing you to control access to API endpoints based on database roles and permissions.

- **JWT Authentication**: Supports JSON Web Token (JWT) authentication for secure access to API endpoints.

- **Customizable Responses**: PostgREST allows you to customize API responses using SQL views and functions, giving you fine-grained control over the data returned to clients.

## Usage

1. **Installation**: Install PostgREST using your preferred method (e.g., binary download, Docker image, package manager).

2. **Configuration**: Configure PostgREST by specifying the database connection details, authentication settings, and other options in a configuration file.

3. **Start the Server**: Start the PostgREST server, specifying the configuration file and the port on which the server should listen for incoming requests.

4. **Access the API**: Once the server is running, clients can access the API endpoints using HTTP requests, such as GET, POST, PUT, DELETE, etc.

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

This will generate API endpoints for accessing the products table, allowing clients to perform CRUD operations on the data.

## Conclusion

PostgREST simplifies the process of building RESTful APIs by automatically generating endpoints from PostgreSQL databases. With its support for advanced features like filtering, authentication, and customizable responses, PostgREST is a valuable tool for building scalable and secure API backends.
