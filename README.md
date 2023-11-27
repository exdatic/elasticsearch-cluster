# Elasticsearch cluster on Docker Swarm with Traefik

This repository contains the configuration file to set up an Elasticsearch cluster on Docker Swarm. It uses Traefik reverse proxy to expose the Elasticsearch REST API on `/es` and the Traefik Dasboard on `/.

👉 We have done our best to avoid [cargo culting](https://en.wikipedia.org/wiki/Cargo_cult_programming) 📦📦📦!

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

### Increase max_map_count kernel parameter on every node

See <https://www.elastic.co/guide/en/elasticsearch/reference/current/vm-max-map-count.html>.

```bash
sysctl -w vm.max_map_count=262144
```

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

The most common reason for this is that a previous run of Elasticsearch had a different configuration (e.g. configured as a single node, running under a different `node.name` or service name) or failed (e.g. missing system settings). The solution is simple:

👉 Remove all volumes from all nodes used by Elasticsearch and redeploy your cluster!
