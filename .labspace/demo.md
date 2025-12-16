```bash
git clone https://github.com/dockersamples/catalog-service-node
```

```bash
mv catalog-service-node/* ./
rm -rf catalog-service-node
```

## Demo #1

```bash
docker run -e POSTGRES_PASSWORD=dev -p 5432:5432 -d postgres:17.2 
```

```bash
psql -h localhost -U postgres
```

```bash
select * from pg_stat_activity;
```

```bash
docker run -e POSTGRES_PASSWORD=dev -p 5433:5432 -d postgres:16.6
```

```bash
docker ps
```

```bash
psql -h localhost -p 5433 -U postgres
```

## Demo #2

TODO: FIXME: delete the 2 postgres container

```bash
docker compose up
```

Go to the application in the browser: :tabLink[http://localhost:5173]{href="http://localhost:5173" title="Web app"}.


New terminal:

```bash
npm run dev
```

Go to the application in the browser: :tabLink[http://localhost:3000]{href="http://localhost:3000" title="Web app"}.


