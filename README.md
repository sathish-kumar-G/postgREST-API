# Using PostgREST for Creating RESTful APIs with PostgreSQL

PostgREST is a standalone web server that turns the PostgreSQL database directly into a RESTful API. The structural constraints and permissions in the database determine the API endpoints and operations. Using PostgREST is an alternative to manual CRUD programming.

For detailed documentation and information, visit [PostgREST website](https://postgrest.org/en/v12/index.html).

## 1. Get it Running

### a) Create PostgreSQL Container

```bash
version: "3.8"

services:
    api-server-db-demo:
        image: postgres:14-alpine
        container_name: postgrest-db-demo
        ports:
            - "5432:5432"
        healthcheck:
            test: [ "CMD", "pg_isready" ]
            interval: 10s
            timeout: 7s
            start_period: 2s
            retries: 3
        environment:
            POSTGRES_USER: user
            POSTGRES_PASSWORD: password
            POSTGRES_DB: postgrest_db_demo
        volumes:
            - restful-server-data:/var/lib/postgresql/data
        restart: "no"
        networks:
            - postgrest_demo

volumes:
    restful-server-data:

networks:
    postgrest_demo:
```

### b) Install PostgREST
Download the latest release from PostgREST GitHub Releases.
[Download](https://github.com/PostgREST/postgrest/releases/latest)

Install PostgREST from the downloaded location:

```bash
tar xJf postgrest-<version>-<platform>.tar.xz
```

Check whether it is installed successfully:
```bash
./postgrest -h
```

## 2. Create Database Schema and Table
```bash
sudo docker exec -it <container-id> /bin/bash
```
Inside the container shell:

Create Schema
```bash
CREATE SCHEMA transit_management;
```

Create Sequence
```bash
CREATE SEQUENCE transit_management.bus_stops_seq
  INCREMENT 1
  MINVALUE 1
  MAXVALUE 9223372036854775807
  START 1
  CACHE 1;
```

Create Table
```bash
-- Create table
CREATE TABLE transit_management.bus_stops (
    id INT8 NOT NULL UNIQUE DEFAULT NEXTVAL('transit_management.bus_stops_seq'),
    stop_abbr VARCHAR(255),
    stop_name VARCHAR(255),
    node_abbr VARCHAR(255),
    site_name VARCHAR(255),
    street_num VARCHAR(255),
    street_no VARCHAR(255),
    on_street VARCHAR(255),
    at_street VARCHAR(255),
    unit VARCHAR(50),
    city VARCHAR(100),
    state VARCHAR(50),
    zipcode VARCHAR(20),
    address_summary TEXT,
    zone_code VARCHAR(50),
    phone VARCHAR(20),
    lon FLOAT,
    lat FLOAT,
    geo_status INT,
    cell_area INT,
    shape_id INT,
    percent_dist_from_a INT,
    segment_side_flag VARCHAR(5),
    comments TEXT,
    picture_file VARCHAR(255),
    ivr_num VARCHAR(20),
    bay_num VARCHAR(20),
    stop_position VARCHAR(50),
    facing_dir VARCHAR(20),
    fare_zone_id INT,
    curb_type VARCHAR(50),
    in_service VARCHAR(5),
    last_service_date VARCHAR(255),
    accessibility_mask INT,
    preferred VARCHAR(5),
    bench VARCHAR(5),
    shelter VARCHAR(5),
    lighting VARCHAR(5),
    garbage VARCHAR(5),
    telephone VARCHAR(5),
    sign_post VARCHAR(5),
    bicycle VARCHAR(5),
    transfer VARCHAR(5),
    gps_lon FLOAT,
    gps_lat FLOAT,
    gps_height FLOAT,
    gis_x FLOAT,
    gis_y FLOAT,
    user_d3 VARCHAR(50),
    user_d4 VARCHAR(50),
    user_d5 VARCHAR(50),
    xcrd INT,
    ycrd INT,
    ipos INT,
    dist_from_at_st INT,
    grid INT,
    parc INT,
    user_s7 VARCHAR(50),
    node VARCHAR(50),
    gtf_wheelchair_boarding VARCHAR(5),
    user_s10 VARCHAR(50),
    short_desc VARCHAR(255),
    crnr VARCHAR(255),
    addr VARCHAR(255),
    intr VARCHAR(255),
    lmrk VARCHAR(255),
    imps VARCHAR(255),
    user_a7 VARCHAR(255),
    stalf VARCHAR(50),
    stacf VARCHAR(50),
    stbef VARCHAR(50),
    staln VARCHAR(50),
    stacn VARCHAR(50),
    stben VARCHAR(50),
    sch_display VARCHAR(255),
    lighting2 VARCHAR(255),
    dir_of_travel VARCHAR(255),
    ivr_sign VARCHAR(255),
    garbage2 VARCHAR(255),
    ada_access VARCHAR(255),
    sidewalk VARCHAR(255),
    lift VARCHAR(255),
    curb2 VARCHAR(255),
    curb_cut VARCHAR(255),
    telephone2 VARCHAR(255),
    park_and_ride VARCHAR(255),
    bus_stop_type VARCHAR(255),
    neighborhood_typ VARCHAR(255),
    base VARCHAR(255),
    lighting3 VARCHAR(255),
    collect_date VARCHAR(255),
    map_page VARCHAR(255),
    county_code VARCHAR(255),
    station_name VARCHAR(255),
    gps_radius FLOAT,
    stop_class VARCHAR(255),
    stop_cluster_id VARCHAR(255),
    heading INT,
    CONSTRAINT stops_pk PRIMARY KEY (id)
);
```

Add Csv File in the Database container and Insert the data
```bash
COPY transit_management.bus_stops (
   stop_abbr, 
     stop_name, 
   node_abbr, 
    site_name, 
  street_num, 
   street_no, 
on_street, 
   at_street, 
     unit, 
    city, 
  state, 
 zipcode, 
    address_summary, 
 zone_code, 
 phone, 
 lon, 
lat, 
  geo_status, 
    cell_area, 
    shape_id, 
percent_dist_from_a, 
   segment_side_flag, 
  comments, 
  picture_file, 
    ivr_num, 
   bay_num,stop_position, 
  facing_dir, 
   fare_zone_id, 
  curb_type, 
   in_service, 
  last_service_date, 
  accessibility_mask, 
 preferred, 
    bench, 
   shelter, 
    lighting, 
     garbage, 
    telephone, 
     sign_post, 
    bicycle, 
 transfer, 
    gps_lon, 
    gps_lat, 
    gps_height, 
     gis_x, 
     gis_y, 
    user_d3, 
    user_d4, 
    user_d5, 
  xcrd, 
    ycrd, 
 ipos, 
    dist_from_at_st, 
  grid, 
    parc, 
    user_s7, 
  node, 
     gtf_wheelchair_boarding, 
 user_s10, 
    short_desc, 
    crnr, 
     addr, 
    intr, 
    lmrk, 
    imps, 
     user_a7, 
     stalf, 
    stacf, 
  stbef, 
  staln, 
    stacn, 
 stben, 
    sch_display, 
   lighting2, 
   dir_of_travel, 
   ivr_sign, 
   garbage2, 
    ada_access, 
  sidewalk, 
     lift, 
    curb2, 
     curb_cut, 
   telephone2, 
  park_and_ride, 
     bus_stop_type, 
     neighborhood_typ, 
    base, 
lighting3, 
   collect_date, 
  map_page, 
 county_code, 
   station_name, 
     gps_radius, 
     stop_class, 
   stop_cluster_id, 
    heading
)
FROM '/tmp/demo/Bus_Stops.csv'
DELIMITER ',' CSV HEADER;

```

Create an anonymous role to use for anonymous web requests. When a request comes in, PostgREST will switch into this role in the database to run queries.
```bash
create role web_anon nologin;
```

Grant privilege to the anonymous web_anon role for schema and table
```bash
grant usage on schema transit_management to web_anon;
grant select on transit_management.bus_stops to web_anon;
```
Create a dedicated role for connecting to the database, instead of using the highly privileged postgres role. So we’ll do that, name the role authenticator 
and also grant it the ability to switch to the web_anon role
```bash
create role authenticator noinherit login password 'password';
grant web_anon to authenticator;
```
Create role called web_user for users who authenticate with the API. This role will have the authority to do anything.
```bash
create role web_user nologin;
grant web_user to authenticator;

grant usage on schema transit_management to web_user;
grant all on transit_management.bus_stops to web_user;
grant usage, select on sequence transit_management.bus_stops_seq to web_user;
```

## 3. Run PostgREST
Create a configuration file named postgrest.conf with the following content:

### PostgREST Configuration

Here's an example configuration for PostgREST:

```conf
# Database connection settings
db-uri = "postgres://authenticator:password@localhost:5432/postgrest_db_demo"
db-schemas = "transit_management"
db-anon-role = "web_anon"

# OIDC settings
jwt-secret = "{
  \"alg\":\"RS256\",
  \"e\":\"AQAB\",
  \"kid\":\"4Vt3bsGUZcObyqXsjzBFMS37zVyzBm2Woog+mftLMmE=\",
  \"kty\":\"RSA\",
  \"n\":\"_PVlC0j1k0Zc6ovuQHb1c61Ita0FUGQ_-0wfQiG-73kwk_fuae8IAmFTgDmojZnM4ZpFFFeSpMhsJAM6eYklswhzKm5YygYdabRagKgyoD4_sUIkkclaX4u1fw7YGB-6fyYUxAKlfz7nt2G3HqUL29zmnXIwj4l2NLIclpHJjTOKTV3ZkG-BlSs5BOw9NRSQh56ixm_-bsx8DIh7UGlymwD-msldN6rK3u5FEbTxSOmy3U16vAdX_3G_D6KcOSU_zBpNUYc9bjlPhTp61KCgZR3DW8QxnZ1fs-u_19C_8U3FdonH5lLLlVPq51sjxJEEfR1lzUATgcIlvI5IvzyrKw\",
  \"use\":\"sig\"
}"
jwt-secret-is-base64 = false
jwt-audience = "75lir73kccmkded4v2avoqnvmo"
jwt-issuer = "https://cognito-idp.us-east-1.amazonaws.com/us-east-1_3g5LAO5c9"

# JWT role claim key for AWS Cognito groups
jwt-role-claim-key = ".\"cognito:groups\"[0]"

# CORS settings
server-cors-allowed-origins = "http://localhost:3000"
```

### Running PostgREST with Configuration File

To run PostgREST using the provided configuration file (`postgrest.conf`), follow these steps:

1. Navigate to the directory where PostgREST executable and configuration file are located.

2. Run PostgREST using the configuration file:

```bash
./postgrest postgrest.conf
```

### Oauth2.0 Provider(Aws Cognito)
The configuration above is set up for integration with AWS Cognito as an OAuth provider. Adjust the OIDC settings (jwt-secret, jwt-audience, jwt-issuer, jwt-role-claim-key) according to your AWS Cognito configuration.
Make sure to replace placeholders such as authenticator, password, localhost, 5432, postgrest_db_demo, and other placeholders with your actual database connection details and AWS Cognito settings.
Ensure that the PostgreSQL database and PostgREST server are properly configured and accessible.


## 4. Refer to Other Documents
- [PostgREST Configuration Reference](https://postgrest.org/en/v12/references/configuration.html#configuration)
- [PostgREST Errors Reference](https://postgrest.org/en/v12/references/errors.html)
- [PostgREST API Schemas Reference](https://postgrest.org/en/v12/references/api/schemas.html#schemas)
- [PostgreSQL Documentation for SQL CREATE ROLE](https://www.postgresql.org/docs/current/sql-createrole.html)
- [PostgREST Resource Embedding Reference](https://postgrest.org/en/v12/references/api/resource_embedding.html)
- [PostgREST Tables and Views Reference for Insert](https://postgrest.org/en/v12/references/api/tables_views.html#insert)

