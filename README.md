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
### Decrypt liquibase properties file
The following command was used to encrypt the liquibase properties file that points to the Neon sql endpoint:
```bash
openssl aes-256-cbc -pbkdf2 -salt -in liquibase.neon.dev.properties -out liquibase.neon.dev.properties.enc
```

To decrypt, run the command:
```bash
openssl aes-256-cbc -d -pbkdf2 -in liquibase.neon.dev.properties.enc -out liquibase.neon.dev.properties
```

Reach out to a team member for the encryption/decryption key.

### Executing liquibase migrations
Run these commands from the `liquibase` directory.

**Windows**

Check your PowerShell version by outputting the `$PSVersionTable` variable. If you are running PowerShell > 7.3, then you need to run the following command first in the Terminal session:
```PowerShell
$PSNativeCommandArgumentPassing = 'legacy'
```

And then run the necessary liquibase commands
```PowerShell
# To apply changelog updates
docker run --rm --network settheory_local_network --mount type=bind,source="$(pwd)"/pg-changelog,target=/liquibase/changelog --mount type=bind,source="$(pwd)"/liquibase.neon.dev.properties,target=/liquibase/liquibase.docker.properties liquibase:4.32.0-alpine liquibase --defaults-file=/liquibase/liquibase.docker.properties update

# To rollback update up to a specific tag
docker run --rm --network settheory_local_network --mount type=bind,source="$(pwd)"/pg-changelog,target=/liquibase/changelog --mount type=bind,source="$(pwd)"/liquibase.neon.dev.properties,target=/liquibase/liquibase.docker.properties liquibase:4.32.0-alpine liquibase --defaults-file=/liquibase/liquibase.docker.properties rollback --tag=_epoch
```

**Linux / MacOS**

```bash
# To apply changelog updates
docker run --rm --network settheory_local_network -v './pg-changelog:/liquibase/changelog' -v './liquibase.neon.dev.properties:/liquibase/liquibase.docker.properties' liquibase:4.32.0-alpine liquibase --defaults-file=/liquibase/liquibase.docker.properties update

# To rollback update up to a specific tag
docker run --rm --network settheory_local_network -v './pg-changelog:/liquibase/changelog' -v './liquibase.neon.dev.local.properties:/liquibase/liquibase.docker.properties' liquibase:4.32.0-alpine liquibase --defaults-file=/liquibase/liquibase.docker.properties rollback --tag=_epoch
```

## Troubleshooting
### `docker compose up` says "network could not be found"
You see the message below and the containers do not start up.
```
network settheory_local_network declared as external, but could not be found
```
You are probably trying to run the containers in this repo without ever having run the devcontainer for the SetTheory-webapp project which creates a docker network for all containers to communicate on for local development. To get past this error, run the command:

```bash
docker network create settheory_local_network --attachable
```

