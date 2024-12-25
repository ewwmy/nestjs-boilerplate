# NestJS Boilerplate

🚀 Features:

- Dockerized
- PostgreSQL database
  - Test database included
  - Simple NestJS controller which communicates with the database
  - Prisma ORM
- Convenient development environment with watch mode through the Docker
- `dev` / `prod` Docker build modes
- Migrations
- Seeder 

## Run

```bash
cp .env.example .env.development
cp .env.example .env.production
cp ./backend/prisma/.env.example ./backend/prisma/.env
```

> Edit env-files for your purposes.

Install dependencies:

```bash
cd backend
npm i # or `npm ci` if you need exact versions of the packages locked in `package-lock.json`
cd ..
```

> Make sure the current dir is set to the root of the project before working with `docker compose`.

### Development

```bash
docker compose --env-file .env.development -f docker-compose.yml -f docker-compose.dev.yml up --build
```

### Production

```bash
docker compose --env-file .env.production up --build -d
```

### Shutdown

```bash
docker compose down
```

## Database connection

### Development

```bash
psql -h localhost -p 5432 -U nestjs-boilerplate -d nestjs-boilerplate
```

#### Migrations

##### Example of how to make migrations

Using the predefined npm scripts:

```bash
npm run migrate:dev -- --name migration_name
```

Inside the docker container:

```bash
docker compose exec nestjs-boilerplate-backend npx prisma migrate dev --name migration_name
```

Outside the docker container (make sure you have set the `DATABASE_URL` variable in your environment):

```bash
DATABASE_URL=postgresql://user:password@localhost:5432/nestjs-boilerplate npx prisma migrate dev --name migration_name
```

or (`prisma/.env` file with the `DATABASE_URL` variable must exist):

```bash
npx prisma migrate dev --name migration_name
```

#### Seeding

##### Inside the Docker container

```bash
docker compose exec nestjs-boilerplate-backend npx prisma db seed
```

##### Outside Docker

```bash
DATABASE_URL=postgresql://user:password@localhost:5432/nestjs-boilerplate npx prisma db seed
```

or just (if the `DATABASE_URL` environment variable is set properly):

```bash
npx prisma db seed
```

### Production

In this mode the database container is not accessible from outside, so we need to connect to the backend container first to access the database service from it:

```bash
docker exec -it nestjs-boilerplate-backend /bin/sh
apk update
apk add postgresql-client
psql -h nestjs-boilerplate -U nestjs-boilerplate -d nestjs-boilerplate # hostname (-h) can be either the service name or the container name from the docker compose config
```
