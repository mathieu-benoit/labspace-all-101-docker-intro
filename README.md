# [All 101] Docker Intro and Overview Labspace

👋 [All 101] Docker Intro and Overview Labspace!

During this lab, you will learn to do the following:
- Demo #1 - Run PosgreSQL
- Demo #2 - Develop
- Demo #3 - Test
- Demo #4 - Build
- Demo #5 - Secure

## Run this Labspace

You can run this Labspace in its latest version from anywhere (if you have Docker and Docker Compose installed):

```bash
docker compose -f oci://ghcr.io/mathieu-benoit/labspace-all-101-docker-intro:latest up
```

## Contribute to this Labspace

After you forked this GitHub repository, you can run this Labspace locally by running these commands:

On Mac/Linux:

```bash
CONTENT_PATH=$PWD docker compose -f oci://dockersamples/labspace-content-dev -f .labspace/compose.override.yaml up
```

On Windows with PowerShell:

```bash
$Env:CONTENT_PATH = (Get-Location).Path; docker compose -f oci://dockersamples/labspace-content-dev -f .labspace/compose.override.yaml up
```