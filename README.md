# arm-elasticsearch

[![Build](https://github.com/jahrik/arm-elasticsearch/actions/workflows/build.yml/badge.svg)](https://github.com/jahrik/arm-elasticsearch/actions/workflows/build.yml)

Multi-arch [Elasticsearch](https://www.elastic.co/elasticsearch) image for the `elk` swarm stack. Built for a 2018 Pi swarm cluster (ES 5.6 on openjdk:8); now a pinned layer over the official multi-arch `elasticsearch` image.

## Run

```bash
docker run -d -p 9200:9200 \
  -e discovery.type=single-node \
  -e xpack.security.enabled=false \
  -e "ES_JAVA_OPTS=-Xms512m -Xmx512m" \
  jahrik/arm-elasticsearch:latest
curl http://localhost:9200/_cluster/health
```

Needs `vm.max_map_count >= 262144` on the host: `sudo sysctl -w vm.max_map_count=262144`.

## Deploy (swarm)

```bash
docker network create -d overlay elk   # once
just deploy                            # data persists to /data on the node
```

## Build

```bash
just build
just push
```

CI: PR builds + cluster-health check; merge to main pushes multi-arch (amd64/arm64) to Docker Hub. No armv7: modern Elasticsearch is 64-bit only (and needs more RAM than a Pi 3 has).

## Legacy

`playbook.yml`, `inventory.ini`, `group_vars/`, `templates/`, and `config/` are the original ES 5.x host-prep ansible bits and configs from the Pi cluster, kept for reference.
