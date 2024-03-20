# Using PostgREST for Creating RESTful APIs with PostgreSQL

PostgREST is a standalone web server that turns the PostgreSQL database directly into a RESTful API. The structural constraints and permissions in the database determine the API endpoints and operations. Using PostgREST is an alternative to manual CRUD programming.

For detailed documentation and information, visit [PostgREST website](https://postgrest.org/en/v12/index.html).

## 1. Get it Running

### a) Create PostgreSQL Container

```bash
docker pull postgres:14.11
sudo docker run --name tutorial -p {port}:5432 \
                           -e POSTGRES_PASSWORD={password} \
                           -d postgres
```

### b) Install PostgREST
Download the latest release from PostgREST GitHub Releases.
Install PostgREST from the downloaded location:
bash
Copy code
tar xJf postgrest-<version>-<platform>.tar.xz
Check whether it is installed successfully:
bash
Copy code
./postgrest -h

## 2. Create Database Schema and Table

sudo docker exec -it <container-id> /bin/bash
Inside the container shell:


create schema api;
create table api.todos (
           id serial primary key,
           done boolean not null default false,
           task text not null,
           due timestamptz
          );
insert into api.todos (task) values ('task1'), ('task2');

create role web_anon nologin;
grant usage on schema api to web_anon;
grant select on api.todos to web_anon;

create role authenticator noinherit login password '<password>';
grant web_anon to authenticator;

## 3. Run PostgREST
Create a configuration file named postgrest.conf with the following content:


db-uri = "postgres://<user-name(role-name)>:<password>@localhost:<port>/<database-name>"
db-schemas = "api"
db-anon-role = "web_anon"

jwt-secret = "<secret-key>"
jwt-secret-is-base64 = false
jwt-audience = "75lir73kccmkded4v2avoqnvmo"
jwt-issuer = "https://cognito-idp.us-east-1.amazonaws.com/us-east-1_3g5LAO5c9"
jwt-role-claim-key = ".\"cognito:groups\"[0]"

server-cors-allowed-origins = "<cors-origins>"
Run PostgREST using the configuration file:

./postgrest postgrest.conf

## 4. Authentication
PostgREST is designed to keep the database at the center of API security. All authorization happens in the database. There are three types of roles used by PostgREST, the authenticator, anonymous, and user roles.


CREATE ROLE authenticator LOGIN NOINHERIT NOCREATEDB NOCREATEROLE NOSUPERUSER;
CREATE ROLE anonymous NOLOGIN;
CREATE ROLE webuser NOLOGIN;
We use JSON Web Tokens (JWT) to authenticate API requests. JWT allows us to be stateless and not require database lookups for verification. As you’ll recall, a JWT contains a list of cryptographically signed claims. All claims are allowed, but PostgREST cares specifically about a claim called role.


{
    "role": "todo_user",
    "exp": 123456789
}
Role: The database role under which to execute SQL for API requests.
Exp: Expiration timestamp for token, expressed in “Unix epoch time”.

## 5. Refer to Other Documents
- [PostgREST Configuration Reference](https://postgrest.org/en/v12/references/configuration.html#configuration)
- [PostgREST Errors Reference](https://postgrest.org/en/v12/references/errors.html)
- [PostgREST API Schemas Reference](https://postgrest.org/en/v12/references/api/schemas.html#schemas)
- [PostgreSQL Documentation for SQL CREATE ROLE](https://www.postgresql.org/docs/current/sql-createrole.html)
- [PostgREST Resource Embedding Reference](https://postgrest.org/en/v12/references/api/resource_embedding.html)
- [PostgREST Tables and Views Reference for Insert](https://postgrest.org/en/v12/references/api/tables_views.html#insert)

