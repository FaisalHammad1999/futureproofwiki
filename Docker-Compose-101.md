Docker Compose is a free, open source tool to more easily work with multiple Docker containers.

## Installation
On most Windows and Mac Docker installations, Compose comes included. For Linux, look for docker-compose in your distribution's package manager. More details on alternative installation methods are available on the docker-compose [GitHub repo](https://github.com/docker/compose).

***

## YAML Essentials
We can define the 'rules' for docker-compose to follow with either YAML ([**Y**AML **A**in't **M**arkup **L**anguage](https://en.wikipedia.org/wiki/YAML#:~:text=YAML%20(a%20recursive%20acronym%20for,is%20being%20stored%20or%20transmitted.)) _or_ JSON. It is much more common to use YAML for this purpose though so that's what we be using here too.

**YAML maps** are a bit like JavaScript Objects or Python dictionaries, using key value pairs:
```yaml
key1: val1
key2: val2
key3:
    nestedkey: nestedval
```
You may also see the inline syntax for mappings: `key 3: { nestedkey: nestedval }`

**YAML sequences** are like JavaScript Arrays or Python lists. Each new element is indicated with a `-`:
```yaml
- element1
- element2
    - nestedSequenceEl1
    - nestedSequenceEl2
- element3
```
You may also see the inline syntax for sequences: `[ element1, element2 ]`

You might see these combined eg. a sequence as the value of a map key:
```yaml
key1:
    - k1SequenceEl2
    - k1SequenceEl2
key2: val2
```
Or a map stored in a sequence:
```yaml
- element1
- 
 element2MapK1: val1
 element2MapK1: val1
- element3
```

Note that YAML relies on whitespace to understand what is going on so be precise with it!

Comments in YAML start with `#`
```yaml
key1: val1 # This is a comment in YAML about this line
```

***

## Anatomy of a `docker-compose.yaml`
Your docker-compose file is going to be a YAML map with certain keys at the top level and your configuration defined in their values.
The four primary keys are:
```yaml
version: # your docker compose file type version here (string)
services: # define your separate docker services here
volumes: # optional: define any shared storage here (can be any of the 3 types: volume, bind mount, tmpfs)
networks: # optional: your config here
```

```yaml
# in docker-compose.yaml / docker-compose.yml
version: '3' # This walkthrough is for version 3 files
services: # here I refer to 2 services and give some instructions similar to when using `docker run` command
    web-app:
        image: my-web-app # point to the docker image
        ports: # map ports as needed
            - '3000:3000' # use local port 3000 to access container port 3000
        environment: # set any environment variables
            - MY_SECRET_KEY=shh987654321
        command: [ "npm", "start" ] # inline sequence syntax is optional
        depends_on: # define any service(s) it relies on (this helps compose decide what order to prep and start up services in)
            - the-database
    the-database:
        image: my-database
        ports:
            - 3679:3679
        volumes:
            - my-datastore:/data
volumes:
    my-datastore: # If given no value (or `null` / `~`), this will create a new volume for you with the name of the key
```

There's a good chance you won't need the `networks` key when starting out. If left out, a standard bridge network is created by default and all services added. The services can access each other with the syntax of `servicename:port` eg. `the-database:3679` / `web-app:3000`. Local host can access on `localhost:port`. If no `ports` are defined for a service, other services can reach it at `servicename:80` but localhost will not be able to access as no ports were exposed.

If you think you might need more specific `networks` settings in your project, you can check out [the documentation](https://docs.docker.com/compose/networking/)

_Note that by default, `docker-compose up` will look for a file called `docker-compose` in the same folder but if you want, you can override this with the `-f` flag: \
`docker-compose up -f my-compose-config.yaml`

***

## `docker-compose` CLI Cheatsheet
Many of the `docker` commands have `docker-compose` equivalents such as `build`, `run`, `start`, `stop` and more. When used with `docker-compose`, these commands will affect all the services used.

There are only two new commands specific to `docker-compose`:
- `up`: creates any networks & volumes then creates, starts and attaches containers
- `down`: removes containers and any networks (does not remove images or volumes by default)

You can see all the available flags for these commands with `docker-compose up --help` and `docker-compose down --help`.

If you wanted to completely tear down including images and volumes, the following will do a full clean-up: \
`docker-compose down --rmi all --volumes --remove-orphans` & `docker image prune -a`

***

## Example web application dev setup
This is assuming an express server using [`pg`](https://node-postgres.com/features/connecting) package to connect to a postgres database and a file tree of:
```
/webapp
    |- docker-compose.yaml
    |- /db
        |- seed.sql
    |- /server
        |- package.json
        |- package-lock.json
        |- server.js
```

### Server files notes
- package.json to include an `npm start` script that starts the server
- server.js to include `pg` connection
```js
const { Pool } = require("pg");
const pool = new Pool()
```

### docker-compose file
**Notes:**
- We are mapping the db container port 5432 since that is the default postgres port and there's no good reason to change it!
- We are mapping it to 35432 for convention's sake but as with any port map, feel free to change this.
- To get away with the minimal `const pool = new Pool()` above, we must make sure it has access to the env vars set below.
- This example uses official Docker images but you could use any built image, including custom ones.
- The volume rule of `- "./db/:/docker-entrypoint-initdb.d/:ro"` will run all the files in local `./db` folder in alphabetical order once the database is setup. Both the `postgres` and `mongo` official images support this. 

```yaml
version: '3'
services:
  server:
    image: node:14
    working_dir: /server
    ports: 
      - 3000:3000
    environment: 
      - PGUSER=my-app
      - PGHOST=db
      - PGPASSWORD=my-app-pass
      - PGDATABASE=my-app-db
      - PGPORT=5432
    depends_on:
      - db
    volumes:
      - type: bind
        source: ./server
        target: /server
    command: bash -c "npm install && npm start"

  db:
    image: postgres:latest
    ports:
      - 35432:5432
    volumes:
      - "dbdata:/var/lib/postgresql/data"
      - "./db/:/docker-entrypoint-initdb.d/:ro"
    environment: 
      - POSTGRES_DB=my-app-db
      - POSTGRES_USER=my-app
      - POSTGRES_PASSWORD=my-app-pass
volumes:
  dbdata:
```

### Start it up!
To get it going, run `docker-compose up` from within the same folder

### Interacting directly with the DB
If you have `psql` installed locally, to access the database, you can run: \
`psql postgres://<user>:<password>@localhost:<mapped-port>/<db-name>` \
eg. `psql postgres://my-app:my-app-pass@localhost:35432/my-app-db`

If you don't have `psql` installed locally, you can still connect! Attach to the container and run psql directly in it with: \
`docker exec -it <container_name> psql -U <pg-user> <db-name>` \
eg. `docker exec -it webapp_db_1 psql -U my-app my-app-db` \
(If you don't know your container name, just run `docker ps` to find it!)

## Make it stop!
See above for more details on a thorough teardown but if you're looking for a basic stop: \
`docker-compose down`: NB. run from within the same folder as the docker-compose file or use the `-f` flag to point to the file