## Demo #1 - Run PosgreSQL

```bash terminal-id=main
docker run -e POSTGRES_PASSWORD=dev -p 5432:5432 -d postgres:17.2 
```

```bash terminal-id=main
psql -h localhost -U postgres
```

```bash terminal-id=main
select * from pg_stat_activity;
```

```bash terminal-id=main
docker run -e POSTGRES_PASSWORD=dev -p 5433:5432 -d postgres:16.6
```

```bash terminal-id=main
docker ps
```

```bash terminal-id=main
psql -h localhost -p 5433 -U postgres
```
