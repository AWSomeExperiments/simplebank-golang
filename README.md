# go-lang-masterclass

You can find the course on Udemey at [this link](https://www.udemy.com/course/backend-master-class-golang-postgresql-kubernetes/)

Through the course I learned how to design, develop and deploy a backend web service from scratch for a simple bank.  It provides APIs for the fronts to do the following things:

1. Create and manage bank accounts, which are composed of owner’s name, balance, and currency.
2. Record all balance changes to each of the account. So every time some money is added to or subtracted from the account, an account entry record will be created.
3. Perform a money transfer between 2 accounts. This should happen within a transaction, so that either both accounts’ balance are updated successfully or none of them are.


The programming language used to develop the service is Golang. and the other backend web development topics covered in the course are:

1. Working with database using Postgres and SQlC.
2. Building RESTful HTTP APIs using GIN and securing the APIs using JW and PASETO access token.
3. Building the app with Docker and deploying the application to a production Kubernetes cluster on AWS.
4. Managing user sessions, building and using gRPC APIs, and embedding Swagger documentation as part of the backend service.
5. Asynchronous processing in Golang using background workers and Redis
6. Improving the stability and security of the server.

## Setup local development

### Install tools

- [Docker desktop](https://www.docker.com/products/docker-desktop)
- [TablePlus](https://tableplus.com/)
- [Golang](https://golang.org/)
- [Homebrew](https://brew.sh/)
- [Migrate](https://github.com/golang-migrate/migrate/tree/master/cmd/migrate)

    ```bash
    brew install golang-migrate
    ```
- [Upgarde Migrate](https://github.com/golang-migrate/migrate/tree/master/cmd/migrate)

      ```bash
      brew upgrade golang-migrate
      ```

- [DB Docs](https://dbdocs.io/docs)

    ```bash
    npm install -g dbdocs
    dbdocs login
    ```

- [DBML CLI](https://www.dbml.org/cli/#installation)

    ```bash
    npm install -g @dbml/cli
    dbml2sql --version
    ```

- [Sqlc](https://github.com/kyleconroy/sqlc#installation)

    ```bash
    brew install sqlc
    ```

- [Gomock](https://github.com/golang/mock)

    ``` bash
    go install github.com/golang/mock/mockgen@v1.6.0
    ```

### Setup infrastructure

- Create the bank-network

    ``` bash
    make network
    ```

- Start postgres container:

    ```bash
    make postgres
    ```

- Create simple_bank database:

    ```bash
    make createdb
    ```

- Run db migration up all versions:

    ```bash
    make migrateup
    ```

- Run db migration up 1 version:

    ```bash
    make migrateup1
    ```

- Run db migration down all versions:

    ```bash
    make migratedown
    ```

- Run db migration down 1 version:

    ```bash
    make migratedown1
    ```

### Documentation

- Generate DB documentation:

    ```bash
    make db_docs
    ```

- Access the DB documentation at [this address](https://dbdocs.io/techschool.guru/simple_bank). Password: `secret`

### How to generate code

- Generate schema SQL file with DBML:

    ```bash
    make db_schema
    ```

- Generate SQL CRUD with sqlc:

    ```bash
    make sqlc
    ```

- Generate DB mock with gomock:

    ```bash
    make mock
    ```

- Create a new db migration:

    ```bash
    make new_migration name=<migration_name>
    ```

### How to run

- Run server:

    ```bash
    make server
    ```

- Run test:

    ```bash
    make test
    ```

## Deploy to kubernetes cluster

- [Install nginx ingress controller](https://kubernetes.github.io/ingress-nginx/deploy/#aws):

    ```bash
    kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v0.48.1/deploy/static/provider/aws/deploy.yaml
    ```

- [Install cert-manager](https://cert-manager.io/docs/installation/kubernetes/):

    ```bash
    kubectl apply -f https://github.com/jetstack/cert-manager/releases/download/v1.4.0/cert-manager.yaml
    ```

## Docker commands

- Run psql console in terminal:

    ```bash
    docker exec -it postgres12 psql -U POSTGRES_USER -d simple_bank
    ```
- List images:

    ```bash
    docker images 
    ```
- Delete a image
      
    ```bash
    docker rmi <IMAGE ID>
    ```
- List containers available locally
      
    ```bash
    docker ps -a
    ```

- Delete container
      
    ```bash
    docker rm <image name>
    ``````

- Build a docker image with latest tag
    ```bash
    docker build -t simplebank:latest .
    ```

- To see network setting of image
    ```bash
    docker container inspect <image name>
    ```

- List docker network
    
    ```bash
    docker network ls
    ```

- Create network
    
    ```bash
    docker network create bank-network
    docker network connect bank-network postgres12
    docker network inspect bank-network
    ```

- Run a docker image with custom network and env variables
    ```bash
    docker run --name simplebank --network bank-network -p 8080:8080 -e GIN_MODE=release -e DB_SOURCE="postgresql://POSTGRES_USER:POSTGRES_PASSWORD@postgres12:5432/simple_bank?sslmode=disable" simplebank:latest
    ```


## Useful links

- [Postgres Lock Monitoring - To look for blocked queries and what's blocking them](https://wiki.postgresql.org/wiki/Lock_Monitoring) <br/>
  ```
  SELECT
        a.application_name,
        l.relation::regclass,
        l.transactionid,
        l.mode,
        l.locktype,
        l.GRANTED,
        a.usename,
        a.query,
        a.pid
  FROM pg_stat_activity a
  JOIN pg_locks l ON l.pid = a.pid
  WHERE a.application_name = 'psql'
  ORDER BY a.pid;
  ```
