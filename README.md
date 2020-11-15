# Postgres DB with web administration interface

This docker compose file uses these containers
- Postgres v13.1 as PostgreSQL DB https://hub.docker.com/_/postgres
- PgAdmin4 v4.28 as web based administration tool for the PostgreSQL database https://hub.docker.com/r/dpage/pgadmin4
- Caddy v2.2.1 as reverse proxy server https://hub.docker.com/_/caddy

To deploy, create an ```.env``` file in the same directory with ```docker-compose.yml``` file at server with your environment variables in it.

```
POSTGRES_PASSWORD=your-secure-db-password
POSTGRES_SERVER_PORT=5432

PGADMIN_DEFAULT_EMAIL=your-web-admin-email
PGADMIN_DEFAULT_PASSWORD=your-secure-admin-password
PGADMIN_LISTEN_PORT=8080
```

## Caddy Config

Caddy config is named ```Caddyfile``` and it is pretty straightforward.

**Default configuration is**
```
:80 {
  reverse_proxy pgadmin4:8080
}
```
which publishes Pgadmin web interface on the server's port 80.

Replace ```:80``` with your domain name to get automatic https via LetsEncrypt. Add your domain to serve only https (recommended)

Don't forget to set your DNS first as Caddy will automatically validate and create certificates for you; 

```
db.myawesomedomain.com {
  reverse_proxy pgadmin4:8080
}
```

Open up your server's 443 port to outside access before setting Caddyfile and rebuilding your docker-compose via your server's or hosting provider's firewall. By default, docker modifies iptables on linux directly https://docs.docker.com/network/iptables/

If you don't want to open your web interface to the public, you could always create a tunnel for it

```
sudo ssh -N -L 443:127.0.0.1:443 root@server-public-ip -i ~/.ssh/id_rsa
```

and visit https://127.0.0.1
