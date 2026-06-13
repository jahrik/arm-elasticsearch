# AGENTS.md

Multi-arch Elasticsearch image: pinned `FROM` over the official `elasticsearch` image, part of the `elk` swarm stack.

## Commands

```bash
make build                                        # build jahrik/arm-elasticsearch:latest
curl -fsS http://localhost:9200/_cluster/health   # smoke test a running container
make deploy                                       # swarm stack deploy (stack: elk)
```

## CI

`build.yml`: Test (build + cluster health, with `vm.max_map_count` bump and single-node env) on PR; Release (buildx amd64+arm64 push to Docker Hub) on merge to main. Needs `DOCKERHUB_USERNAME`/`DOCKERHUB_TOKEN` secrets.

## Quirks

- Bump ES via the `FROM` tag (Docker Hub `library/elasticsearch`).
- ES needs `discovery.type=single-node`, `xpack.security.enabled=false`, and a heap cap to run standalone in tests; production config belongs in the compose file, not the image.
- `playbook.yml`/`inventory.ini`/`group_vars/`/`templates/`/`config/` are legacy ES 5.x cluster artifacts — reference only, don't modernize them with the image.
- `docker-compose.yml` is a swarm fragment: external `elk` overlay network — keep that wiring.
