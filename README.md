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
b) Install PostgREST
Download the latest release from PostgREST GitHub Releases.
Install PostgREST from the downloaded location:

tar xJf postgrest-<version>-<platform>.tar.xz
Check whether it is installed successfully:

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
Additional Notes
Make sure to replace placeholders such as <port>, <password>, <container-id>, <user-name(role-name)>, <database-name>, <secret-key>, <cors-origins>, etc. with your actual values.
Take necessary security precautions when setting up roles and authentication.
Ensure that proper CORS settings are configured for your application's needs.
Monitor and maintain your PostgreSQL database and PostgREST server for optimal performance and security.

