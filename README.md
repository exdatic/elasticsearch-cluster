# Elasticsearch cluster on Docker Swarm with Traefik
Configuration file to set up an Elasticsearch cluster on Docker Swarm, using Traefik reverse proxy to expose Elasticsearch service on ```example.com/es```.

Based on: 
- https://dockerswarm.rocks/traefik/ 
- https://marcofranssen.nl/building-a-elasticsearch-cluster-using-docker-compose-and-traefik
- https://github.com/deviantony/docker-elk/issues/410
- https://github.com/elastic/elasticsearch-docker/issues/91
## Setup cluster
```
git clone git@github.com:exdatic/elasticsearch-cluster.git
cd elasticsearch-cluster/
docker swarm init
export $(cat .env)
export USERNAME=admin
export PASSWORD=changeme
export HASHED_PASSWORD=$(openssl passwd -apr1 $PASSWORD)
export DOMAIN=example.com
export EMAIL=foo@example.com
docker stack deploy -c docker-compose.yml es
# run this command to get join tokens for worker nodes
docker swarm join-token worker
# execute the returned commands on worker nodes
```