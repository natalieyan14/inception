# Developer Documentation - Inception

## Overview

This repository contains a Docker Compose stack for WordPress, NGINX, and MariaDB. The project runs inside a VM and is exposed through HTTPS on the domain `nharutyu.42.fr`.

## Requirements

Before building the stack, make sure the VM has:

- Docker
- Docker Compose
- make
- a `/etc/hosts` entry for `127.0.0.1 nharutyu.42.fr`

The project is designed for a Debian 12 (Bookworm) VM running inside VirtualBox 7+.

## Repository Layout

- Makefile
- README.md
- USER_DOC.md
- DEV_DOC.md
- srcs/
  - .env
  - docker-compose.yml
  - requirements/
    - mariadb/
      - Dockerfile
      - tools/init.sh
    - nginx/
      - Dockerfile
      - conf/nginx.conf
    - wordpress/
      - Dockerfile
      - tools/init.sh
      - tools/make_dir.sh

## Build Flow

The `Makefile` uses Docker Compose with `srcs/docker-compose.yml` and `srcs/.env`.

Relevant targets:

- `make`: creates the data directories and starts the stack
- `make build`: creates the data directories and rebuilds the images
- `make down`: stops and removes the containers
- `make re`: stops the stack and rebuilds it
- `make clean`: removes containers and wipes the host data directories
- `make fclean`: performs a wider cleanup of containers, volumes, and networks

The startup script called by the Makefile creates these host directories if needed:

- /home/nharutyu/data/mariadb
- /home/nharutyu/data/wordpress

## Docker Compose Details

The stack defines three services:

- `nginx`
- `wordpress`
- `mariadb`

They communicate over a bridge network named `inception`.

The stack publishes only port 443 on the host for NGINX. WordPress and MariaDB remain internal.

The compose file uses bind-backed named volumes pointing to:

- /home/nharutyu/data/wordpress
- /home/nharutyu/data/mariadb

## Container Images

All three services are built from Alpine 3.16 base images.

NGINX uses the standard `nginx -g 'daemon off;'` foreground process.

WordPress runs PHP-FPM in the foreground after its initialization script creates `wp-config.php` if needed.

MariaDB initializes the database on first launch and then starts `mysqld` in the foreground.

## Configuration

The Compose file expects these values in `srcs/.env`:

```env
DB_NAME=wordpress
DB_USER=wpuser
DB_PASS=your_db_password
DB_ROOT=your_db_root_password
```

The NGINX configuration serves `nharutyu.42.fr` and `www.nharutyu.42.fr` over TLSv1.2 and TLSv1.3.

## Verification Notes

- The repository does not use `network: host` or `links:`.
- The Makefile does not use `--link`.
- The entrypoints are foreground processes and do not rely on infinite loops.
- Persistent state is kept on the host under `/home/nharutyu/data/`.

## Useful Commands

```bash
make
make down
make re
make clean
make fclean
docker ps
docker logs nginx
docker logs wordpress
docker logs mariadb
```