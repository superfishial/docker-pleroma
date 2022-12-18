# Pleroma Docker

# Supported tags

- `stable` - latest stable release
- `develop` - latest develop branch
- `v2.4.5` - specific release (only the latest one is supported)

# How to use this image

## Start a Pleroma instance

**Docker Compose**

```yaml
version: "3.8"

services:
  db:
    image: postgres:12.1-alpine
    container_name: pleroma_db
    restart: always
    healthcheck:
      test: ["CMD", "pg_isready", "-U", "pleroma"]
    environment:
      POSTGRES_USER: pleroma
      POSTGRES_PASSWORD: ChangeMe!
      POSTGRES_DB: pleroma
    volumes:
      - ./data/postgres:/var/lib/postgresql/data
  web:
    image: ghcr.io/explodingcamera/pleroma:stable
    container_name: pleroma_web
    healthcheck:
      test:
        ["CMD-SHELL", "wget -q --spider --proxy=off localhost:4000 || exit 1"]
    restart: always
    ports:
      - "4000:4000"
    volumes:
      - ./data/uploads:/data/uploads
      - ./data/static:/data/static
      - ./custom-config.exs:/data/config.exs:ro # optional
    environment:
      PUID: 1000
      PGID: 1000
      DOMAIN: example.com
      INSTANCE_NAME: Pleroma
      ADMIN_EMAIL: admin@example.com
      NOTIFY_EMAIL: notify@example.com
      DB_USER: pleroma
      DB_PASS: ChangeMe!
      DB_NAME: pleroma
    depends_on:
      - db
```

**Docker CLI**

```bash
docker run -d \
  --name=pleroma \
  -e PUID=1000 \
  -e PGID=1000 \
  -e DOMAIN="example.com"
  -e INSTANCE_NAME="Pleroma"
  -e ADMIN_EMAIL="admin@example.com"
  -e NOTIFY_EMAIL="notify@example.com"
  -e DB_USER="pleroma"
  -e DB_PASS="ChangeMe!"
  -e DB_NAME="pleroma"
  -p 4000:4000 \
  -v /path/to/static:/data/static \
  -v /path/to/uploads:/data/uploads \
  -v /path/to/customconfig:/data/config.exs \ # optional
  --restart unless-stopped \
  ghcr.io/explodingcamera/pleroma:stable
```

# Build-time variables

- `PLEROMA_VERSION` - Pleroma version to build (default: `stable`)
- `PLEROMA_REPO` - Pleroma repository to clone (default: `https://git.pleroma.social/pleroma/pleroma.git`)
- `EXTRA_PKGS` - Extra packages to install (default: `imagemagick libmagic ffmpeg`)

# Other Docker images

- [angristan/docker-pleroma](https://github.com/angristan/docker-pleroma)
- [potproject/docker-pleroma](https://github.com/potproject/docker-pleroma)
- [rysiek/docker-pleroma](https://git.pleroma.social/rysiek/docker-pleroma)
- [RX14/iscute.moe](https://github.com/RX14/kurisu.rx14.co.uk/blob/master/services/iscute.moe/pleroma/Dockerfile)
