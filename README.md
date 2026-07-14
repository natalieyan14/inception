*This project has been created as part of the 42 curriculum by nharutyu.*

# Inception

## Description

This project sets up a small Docker Compose infrastructure for a WordPress site running behind NGINX and backed by MariaDB. The stack is meant to run inside a Debian VM and exposes the application through HTTPS only.

The services are:

- NGINX: reverse proxy and TLS termination on port 443
- WordPress: PHP-FPM application server on port 9000, internal only
- MariaDB: database server on port 3306, internal only

The current configuration uses the domain `nharutyu.42.fr`.

## Instructions
### Prerequisites

Before starting the project, make sure the following are installed inside the VM:

- Docker
- Docker Compose
- make

You also need a VirtualBox 7+ VM running Debian 12 (Bookworm).

### Setup

1. Clone the repository.

```bash
git clone <your-repo-url>
cd inception
```

2. Create the host data directories if they do not already exist.

```bash
mkdir -p /home/nharutyu/data/mariadb /home/nharutyu/data/wordpress
```

3. Create `srcs/.env` with the database configuration used by Compose.

```env
DB_NAME=wordpress
DB_USER=wpuser
DB_PASS=your_db_password
DB_ROOT=your_db_root_password
```

4. Add the domain to `/etc/hosts`.

```bash
echo "127.0.0.1 nharutyu.42.fr" | sudo tee -a /etc/hosts
```

### Build and Launch

Run:

```bash
make
```

The Makefile creates the host data directories if needed and starts all containers in detached mode.

### Makefile Targets

- `make`: build and start the stack
- `make build`: build images and start the stack
- `make down`: stop and remove containers
- `make re`: stop, rebuild, and start again
- `make clean`: stop containers and wipe the data directories
- `make fclean`: full cleanup of containers, networks, volumes, and data directories

### Access

Open the site at:

- https://nharutyu.42.fr

Accept the browser warning for the self-signed certificate.

### Data Persistence

Persistent data is stored on the host in:

- /home/nharutyu/data/mariadb
- /home/nharutyu/data/wordpress

This data remains available after container restarts and rebuilds unless you run a cleanup target.

### Project Structure

- Makefile
- README.md
- srcs/
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

### Notes

- NGINX listens on 443 with TLSv1.2 and TLSv1.3.
- WordPress connects to MariaDB through the internal Docker bridge network.
- The containers are built from Alpine 3.16 base images.

## Resources

- Docker official documentation: https://docs.docker.com
- MariaDB Docker best practices: https://mariadb.com/kb/en/docker-and-mariadb/
- NGINX SSL configuration: https://nginx.org/en/docs/http/configuring_https_servers.html
- Docker secrets: https://docs.docker.com/engine/swarm/secrets/

AI was used to help reorganize the documentation, align it with the repository layout, and draft concise explanations. The technical details were checked against the actual Makefile, Compose file, Dockerfiles, and scripts in this repository.
