## Demo #1 - Run PostgreSQL

Browse the catalog of Docker images on [hub.docker.com](https://hub.docker.com).

Search for and select the `postgres` page.

See the various versions/tags available for the `postgres` image.

Run and test a `postgres` container.

```bash terminal-id=pg1
docker run -e POSTGRES_PASSWORD=dev -p 5432:5432 postgres:17.2 
```

```bash terminal-id=psql1
psql -h localhost -U postgres
```

```bash terminal-id=psql1
select * from pg_stat_activity;
```

Run and test a second `postgres` container.

```bash terminal-id=pg2
docker run -e POSTGRES_PASSWORD=dev -p 5433:5432 postgres:16.6
```

```bash terminal-id=psql2
docker ps
```

```bash terminal-id=psql2
psql -h localhost -p 5433 -U postgres
```

```bash terminal-id=psql2
select * from pg_stat_activity;
```