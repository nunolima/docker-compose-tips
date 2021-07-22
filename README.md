# docker-compose-tips
Docker and docker-compose tips and samples

## Place the images on your own docker registry (like harbor or portus)

```bash
docker pull bitnami/prometheus:2.28.1@sha256:e8b4c403da323caccdf2aaa4aa0b4a01c5baacc9258c31a65e3b19cd20c006af
docker image tag bitnami/prometheus@sha256:e8b4c403da323caccdf2aaa4aa0b4a01c5baacc9258c31a65e3b19cd20c006af harbor.n2n.duckdns.org/devops/prometheus-server-bitnami:2.28.1
docker push harbor.n2n.duckdns.org/devops/prometheus-server-bitnami:2.28.1
```
