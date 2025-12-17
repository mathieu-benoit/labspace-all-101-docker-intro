## Demo #4 - Build

### Open the `Dockerfile`

Open the :fileLink[Dockerfile]{path="catalog-service-node/Dockerfile" line=8}.

We are using a Docker Official Image as the base (yes, it's an old version of Node, but we'll come back to that later).

```yaml no-copy-button
FROM node:18 AS base
```

### Build the container

```bash terminal-id=build
docker build -t catalog-service --sbom=true --provenance=mode=max .
```

In the output we can note the build time and number of `CACHED` layers.

```none no-copy-button
 => CACHED [base 2/4] WORKDIR /usr/local/app                                                                                              0.0s
 => CACHED [base 3/4] RUN useradd -m appuser && chown -R appuser /usr/local/app                                                           0.0s
 => CACHED [base 4/4] COPY --chown=appuser:appuser package.json package-lock.json ./                                                      0.0s
 => CACHED [final 1/2] RUN npm ci --production --ignore-scripts && npm cache clean --force                                                0.0s
 => CACHED [final 2/2] COPY ./src ./src                                                                                                   0.0s
 => CACHED [linux/amd64] generating sbom using docker.io/docker/buildkit-syft-scanner:stable-1
 ```

We can also see the lint output and guiding us towards a Dockerfile that is aligned with best practices.

```none no-copy-button
 1 warning found (use docker --debug to expand):
 - LegacyKeyValueFormat: "ENV key=value" should be used instead of legacy "ENV key value" format (line 38)
```

### Use Docker Build Cloud

![](.labspace/images/build-cloud.png)

For a more consistent experience and to speed up your build times, locally and in your CI pipelines, you can use Docker Build Cloud.

In the :fileLink[.github/workflows/pipeline-docker-cloud.yaml]{path="catalog-service-node/.github/workflows/pipeline-docker-cloud.yaml" line=143} workflow, we can see how to use Docker Build Cloud.

We can compare two CI pipeline using or not using Docker Build Cloud, and see the optimizations generated:
- [Pipeline run without DBC](https://github.com/dockersamples/catalog-service-node/actions/runs/19951688497) --> `Build and push image` --> `1m36s`
- [Pipeline run with DBC](https://github.com/dockersamples/catalog-service-node/actions/runs/19951662735) --> `Build and push image` --> `51s`