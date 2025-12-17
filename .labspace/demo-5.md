## Demo #5 - Secure

### Scan the container image

```bash terminal-id=build
docker scout quickview catalog-service --org $$org$$
```

We can see that Docker Scout is highlighting the CVEs that the container image has and is even recommending a version of the base image to fix them.

```none no-copy-button
  Target             │  catalog-service:latest  │    2C    26H    25M   122L     4?   
    digest           │  360db4f00cbd            │                                     
  Base image         │  node:18                 │    2C    26H    25M   122L     4?   
  Updated base image │  node:25-slim            │    0C     1H     2M    24L          
                     │                          │    -2    -25    -23    -98     -4 
```

We can also see the status of the policies evaluated.

```none no-copy-button
Policy status  FAILED  (4/7 policies met)

  Status │                     Policy                     │           Results            
─────────┼────────────────────────────────────────────────┼──────────────────────────────
  ✓      │ Default non-root user                          │                              
  !      │ AGPL v3 licenses found                         │    3 packages                
  !      │ Fixable critical or high vulnerabilities found │    2C    26H     0M     0L   
  ✓      │ No high-profile vulnerabilities                │    0C     0H     0M     0L   
  ✓      │ No outdated base images                        │                              
  !      │ Unapproved base images found                   │    1 deviation               
  ✓      │ Supply chain attestations                      │    0 deviations
```

### Use a smaller base image

Update the :fileLink[Dockerfile]{path="catalog-service-node/Dockerfile"} and save.

```yaml save-as=catalog-service-node/Dockerfile
###########################################################
# Stage: base
#
# This stage serves as the base for all of the other stages.
# By using this stage, it provides a consistent base for both
# the dev and prod versions of the image.
###########################################################
FROM node:25-slim AS base

# Setup a non-root user to run the app
WORKDIR /usr/local/app
RUN useradd -m appuser && chown -R appuser /usr/local/app
USER appuser
COPY --chown=appuser:appuser package.json package-lock.json ./


###########################################################
# Stage: dev
#
# This stage is used to run the application in a development
# environment. It installs all app dependencies and will
# start the app in a mode that will watch for file changes
# and automatically restart the app.
###########################################################
FROM base AS dev
ENV NODE_ENV=development
RUN npm install
CMD ["yarn", "dev-container"]


###########################################################
# Stage: final
#
# This stage serves as the final image for production. It
# installs only the production dependencies.
###########################################################
FROM base AS final
ENV NODE_ENV=production
RUN npm ci --production --ignore-scripts && npm cache clean --force
COPY ./src ./src

EXPOSE 3000

CMD [ "node", "src/index.js" ]
```

```diff no-copy-button
- FROM node:18 AS base
+ FROM node:25-slim AS base
```

Build a new container image version:

```bash terminal-id=build
docker build -t catalog-service:slim --sbom=true --provenance=mode=max .
```

See the differences between the two container images:

```bash terminal-id=build
docker images catalog-service
```

```none
IMAGE                    ID             DISK USAGE   CONTENT SIZE   EXTRA
catalog-service:latest   48806e62b871       1.62GB          413MB        
catalog-service:slim     8d03cef7a79f        368MB         84.1MB
```

```bash terminal-id=build
docker scout quickview catalog-service:slim --org $$org$$
```

Improvements have been made in terms of number of CVEs:

```none no-copy-button
  Target             │  catalog-service:slim  │    0C     2H     2M    24L   
    digest           │  23a9570bd754          │                              
  Base image         │  node:25-slim          │    0C     1H     2M    24L   
  Updated base image │  node:24-slim          │    0C     1H     2M    24L   
                     │                        │                              
```

```none no-copy-button
Policy status  FAILED  (6/7 policies met)
  Status │                     Policy                     │           Results            
─────────┼────────────────────────────────────────────────┼──────────────────────────────
  ✓      │ Default non-root user                          │                              
  ✓      │ No AGPL v3 licenses                            │    0 packages                
  !      │ Fixable critical or high vulnerabilities found │    0C     2H     0M     0L   
  ✓      │ No high-profile vulnerabilities                │    0C     0H     0M     0L   
  ✓      │ No outdated base images                        │                              
  ✓      │ No unapproved base images                      │    0 deviations              
  ✓      │ Supply chain attestations                      │    0 deviations
```

### Use Docker Hardened Image (DHI)

Update the :fileLink[Dockerfile]{path="Dockerfile"} and save.

```yaml save-as=catalog-service-node/Dockerfile
###########################################################
# Stage: base
#
# This stage serves as the base for all of the other stages.
# By using this stage, it provides a consistent base for both
# the dev and prod versions of the image.
###########################################################
FROM $$org$$/dhi-node:25-alpine3.22-dev AS base

# Setup a non-root user to run the app
WORKDIR /usr/local/app
COPY package.json package-lock.json ./


###########################################################
# Stage: dev
#
# This stage is used to run the application in a development
# environment. It installs all app dependencies and will
# start the app in a mode that will watch for file changes
# and automatically restart the app.
###########################################################
FROM base AS dev
ENV NODE_ENV=development
RUN npm install
CMD ["yarn", "dev-container"]


###########################################################
# Stage: final
#
# This stage serves as the final image for production. It
# installs only the production dependencies.
###########################################################
FROM base AS production-dependencies
ENV NODE_ENV=production
RUN npm ci --production --ignore-scripts && npm cache clean --force

FROM $$org$$/dhi-node:25-alpine3.22 AS final
ENV NODE_ENV=production
COPY --from=production-dependencies /usr/local/app/node_modules ./node_modules
COPY ./src ./src
EXPOSE 3000
CMD ["node", "src/index.js"]
```

Build a new container image version:

```bash terminal-id=build
docker build -t catalog-service:dhi --sbom=true --provenance=mode=max .
```

See the differences between the two container images:

```bash terminal-id=build
docker images catalog-service
```

```none
IMAGE                    ID             DISK USAGE   CONTENT SIZE   EXTRA
catalog-service:dhi      ac3a0d465de4        191MB         40.3MB        
catalog-service:latest   48806e62b871       1.62GB          413MB        
catalog-service:slim     8d03cef7a79f        368MB         84.1MB
```

```bash terminal-id=build
docker scout quickview catalog-service:dhi --org $$org$$
```

```none
Target     │  catalog-service:dhi                      │    0C     0H     1M     0L   
    digest   │  8e5de4cd5216                             │                              
  Base image │  demonstrationorg/dhi-node:25-alpine3.22  │                              

Policy status  SUCCEEDED  (7/7 policies met)

  Status │                              Policy                              │           Results            
─────────┼──────────────────────────────────────────────────────────────────┼──────────────────────────────
  ✓      │ Default non-root user                                            │                              
  ✓      │ No AGPL v3 licenses                                              │    0 packages                
  ✓      │ No fixable critical or high vulnerabilities                      │    0C     0H     0M     0L   
  ✓      │ No high-profile vulnerabilities                                  │    0C     0H     0M     0L   
  ✓      │ No unapproved base images                                        │    0 deviations              
  ✓      │ Supply chain attestations                                        │    0 deviations              
  ✓      │ No outdated base images                                          │
```

### Comparing the initial versus the DHI container images

```bash terminal-id=build
docker scout compare --ignore-unchanged --to catalog-service catalog-service:dhi --org $$org$$
```

```none
## Overview
                      │                         Analyzed Image                          │                        Comparison Image                          
  ────────────────────┼─────────────────────────────────────────────────────────────────┼──────────────────────────────────────────────────────────────────
    Target            │  catalog-service:dhi                                            │  catalog-service:latest                                          
      digest          │  8e5de4cd5216                                                   │  360db4f00cbd                                                    
      tag             │  latest                                                         │  latest                                                          
      platform        │ linux/amd64                                                     │ linux/amd64                                                      
      provenance      │ https://github.com/mathieu-benoit/labspace-all-101-docker-intro │ https://github.com/mathieu-benoit/labspace-all-101-docker-intro  
                      │  bb519ce0c7e825a475712ff36c50cb161902feb3                       │  4b73dd85cd90ec5b535cc5ad4b6193b5211cbcd2                        
      vulnerabilities │    0C     0H     0M     0L                                      │    2C    26H    25M   122L     4?                                
                      │    -2    -26    -25   -122     -4                               │                                                                  
      size            │ 40 MB (-358 MB)                                                 │ 398 MB                                                           
      packages        │ 211 (-595)                                                      │ 806                                                              
                      │                                                                 │                                                                  
  
  ## Policies
  
  3 improved, 0 worsened, 1 missing data
  
    Policy                                       Analyzed   Comparison  Change             
  
    Default non-root user                        ✓          ✓                   No Change  
    No AGPL v3 licenses                          ✓          ! 3         -3      Improved   
    No fixable critical or high vulnerabilities  ✓          ! 28        -28     Improved   
    No high-profile vulnerabilities              ✓          ✓                   No Change  
    No outdated base images                      ? No data  ✓                              
    No unapproved base images                    ✓          ! 1         -1      Improved   
    Supply chain attestations                    ✓          ✓                   No Change  
  
      View policy details → docker scout policy catalog-service:dhi
  
  ## Packages and Vulnerabilities
  
    +    9 packages added  
    -  587 packages removed  
    ⎌    4 packages changed (↑ 1 upgraded, ↓ 3 downgraded)  
       192 packages unchanged
  
    - 179 vulnerabilities removed
```