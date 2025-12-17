## Setup

TODOs:
- Test Container issue
- DCB
- DD
- Standalone VS Code?

::variableDefinition[org]{prompt="What is your Docker Organization?"}

```bash terminal-id=main
git clone https://github.com/dockersamples/catalog-service-node
```

```bash terminal-id=main
cd catalog-service-node
```

```bash terminal-id=main
./demo/sdlc-e2e/setup.sh
```

```bash terminal-id=main
docker rm $(docker ps -a -q) -f
```

```bash terminal-id=npm
cd catalog-service-node
npm install
```

```bash terminal-id=build
cd catalog-service-node
docker build --sbom=true --provenance=mode=max .
```
