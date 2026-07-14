# User Documentation - Inception

## What this stack provides

This infrastructure runs a WordPress website accessible at https://nharutyu.42.fr over HTTPS. It consists of three services:

- NGINX - web server, handles all incoming traffic on port 443
- WordPress - the website and admin panel
- MariaDB - the database storing all WordPress content

## Starting and stopping the project

### Start

```bash
cd ~/inception
make
```

Wait about 30 seconds for all services to initialize.

### Stop and remove containers

```bash
make down
```

### Rebuild from scratch

```bash
make re
```

### Clean the host data directories

```bash
make clean
```

### Full cleanup

```bash
make fclean
```

## Accessing the website

Open a browser and go to:

- https://nharutyu.42.fr/

Accept the browser's security warning about the self-signed certificate.

## Managing the configuration

The project uses `srcs/.env` for the database settings required by Docker Compose.

Example content:

```env
DB_NAME=wordpress
DB_USER=wpuser
DB_PASS=your_db_password
DB_ROOT=your_db_root_password
```

## Checking services are running

Use:

```bash
docker ps
```

All three containers (`nginx`, `wordpress`, `mariadb`) should show status `Up`.

## Viewing logs

For a specific service:

```bash
docker logs nginx
docker logs wordpress
docker logs mariadb
```

## Where data is stored

Persistent data is stored on the host in:

- /home/nharutyu/data/mariadb
- /home/nharutyu/data/wordpress

These directories are preserved across container restarts and rebuilds unless you run a cleanup target.
