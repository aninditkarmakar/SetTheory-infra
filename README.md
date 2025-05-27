# SetTheory-containers-dev
Docker containers for the supporting infrastructure for local full-stack development of the SetTheory WebApp and API

## Postgres Database
To spin up the local instance of the PostgreSQL database along with pgAdmin for a web-based management portal, run

```bash
cd postgres-db
docker compose up
```

This command will spin up two containers - `settheory_db` and `settheory_pgadmin`.

These containers are configured to automatically be attached to the `settheory_local_network` docker bridge to which all other SetTheory dev-related containers are attached. This allowed seamless networking via the hostnames.

The hostname for the sql db container is: `settheory-db-local`.

From your host machine, go to `http://localhost:8942` to access the web-based management portal for the postgres database (pgAdmin).

To log in, enter -
* Username: `<The PGADMIN_DEFAULT_EMAIL in the postgres-db/compose.yaml file>`
* Password: `<The PGADMIN_DEFAULT_PASSWORD in the postgres-db/compose.yaml file>`
