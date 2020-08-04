# container send log to loki

1. install docker
2. install docker-compose
3. docker install loki-docker-driver
    `docker plugin install  grafana/loki-docker-driver:latest --alias loki --grant-all-permissions`
4. config the loki server
5. config the you app container logging driver, see [example](./docker-compose.yml)

https://github.com/grafana/loki/blob/master/docs/sources/clients/docker-driver/configuration.md