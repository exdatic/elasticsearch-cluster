# Elasticsearch cluster on Docker Swarm with Traefik

This repository contains the configuration file to set up an Elasticsearch cluster on Docker Swarm. It uses Traefik reverse proxy to expose the Elasticsearch REST API on `/es` and the Traefik Dasboard on `/traefik`.

👉 We have done our best to avoid cargo culting 📦📦📦!

Credits:

- https://dockerswarm.rocks/traefik/
- https://marcofranssen.nl/building-a-elasticsearch-cluster-using-docker-compose-and-traefik
- https://github.com/deviantony/docker-elk/issues/410
- https://github.com/elastic/elasticsearch-docker/issues/91

## Setup docker swarm

### Init docker swarm

```bash
docker swarm init
```

### Get docker swarm join tokens

```bash
docker swarm join-token worker
```

## Deploy Elasticsearch cluster

### Clone this repository

```bash
git clone git@github.com:exdatic/elasticsearch-cluster.git
cd elasticsearch-cluster/
```

### Generate the admin password

The admin password is used to protect access to the Traefik Dashboard and the exposed Elasticsearch REST API.

```bash
$ openssl passwd -apr1 secret
$apr1$d8m.ROJH$.8G4W/giLtYFbC9x2dC671
```

### Setup environment variables

The `docker-compose.yml` contains references to environment variables that are defined in the following `.env` file:

```plain
DOMAIN=example.com
EMAIL=admin@example.com
ADMIN_AUTH=admin:$apr1$d8m.ROJH$.8G4W/giLtYFbC9x2dC671
```

### Deploy elasticsearch stack

```bash
export $(cat .env)
docker stack deploy -c docker-compose.yml es
```

👉 `docker stack deploy` doesn't load `.env` file as `docker compose up` does (see <https://github.com/moby/moby/issues/29133>)

## Troubleshooting

### Elasticsearch master not discovered or elected yet

The most common reason for this is that you previously ran Elasticsearch as a single-node instance or with `docker-compose up`. Another reason is that you have changed `node.name` or the service name. The solution to this is simple:

👉 Remove all data volumes used by Elasticsearch and redeploy your cluster!
