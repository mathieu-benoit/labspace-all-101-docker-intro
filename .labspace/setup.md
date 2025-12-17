## Setup

TODOs:
- Test Container issue
- DCB
- DD
- Standalone VS Code?

::variableDefinition[org]{prompt="What is your Docker Organization?"}

```bash terminal-id=main
git clone https://github.com/dockersamples/catalog-service-node
cd catalog-service-node
./demo/sdlc-e2e/setup.sh
docker rm $(docker ps -a -q) -f
clear
```

```bash terminal-id=npm
cd catalog-service-node
npm install
clear
```

```bash terminal-id=build
cd catalog-service-node
docker build -t catalog-service --sbom=true --provenance=mode=max .
docker scout quickview catalog-service --org dockerdevrel
clear
```
