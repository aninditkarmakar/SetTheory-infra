# SetTheory-infra
Docker containers for the supporting infrastructure for local full-stack development of the SetTheory WebApp and API.
Also contains db migration scripts.

## Postgres Database
To spin up the local instance of the PostgreSQL database along with pgAdmin for a web-based management portal, run

```bash
cd postgres-db
docker compose up --detach
```

This command will spin up two containers - `settheory_db` and `settheory_pgadmin`.

These containers are configured to automatically be attached to the `settheory_local_network` docker bridge to which all other SetTheory dev-related containers are attached. This allowed seamless networking via the hostnames.

The hostname for the sql db container is: `settheory-db-local`.

From your host machine, go to `http://localhost:8942` to access the web-based management portal for the postgres database (pgAdmin).

To log in, enter -
* Username: `<The PGADMIN_DEFAULT_EMAIL in the postgres-db/compose.yaml file>`
* Password: `<The PGADMIN_DEFAULT_PASSWORD in the postgres-db/compose.yaml file>`

## Liquibase
Run these commands from the `liquibase` directory.

```bash
# To apply changelog updates
docker run --rm --network settheory_local_network -v './pg-changelog:/liquibase/changelog' -v './liquibase.properties:/liquibase/liquibase.docker.properties' liquibase:4.32.0-alpine liquibase --defaults-file=/liquibase/liquibase.docker.properties update

# To rollback update up to a specific tag
docker run --rm --network settheory_local_network -v './pg-changelog:/liquibase/changelog' -v './liquibase.properties:/liquibase/liquibase.docker.properties' liquibase:4.32.0-alpine liquibase --defaults-file=/liquibase/liquibase.docker.properties rollback --tag=_epoch
