## Setup

::variableDefinition[org]{prompt="What is your Docker Organization?"}

```bash terminal-id=main
git clone https://github.com/dockersamples/catalog-service-node
```

```bash terminal-id=main
shopt -s dotglob
mv catalog-service-node/* ./
rm -rf catalog-service-node
shopt -s dotglob
```

```bash terminal-id=main
./demo/sdlc-e2e/setup.sh
```

```bash terminal-id=main
docker build --sbom=true --provenance=mode=max .
```
