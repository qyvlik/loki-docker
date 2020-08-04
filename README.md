# loki-docker

https://grafana.com/docs/loki/latest/operations/authentication/
loki 本身没有 auth 层，需要使用 nginx 的 basic auth or OAuth2

Nginx配置Basic Auth登录认证
https://www.jianshu.com/p/b4a78af4e266

Grafana 日志聚合工具 Loki
https://www.qikqiak.com/post/grafana-log-tool-loki/

Grafana loki-log-docker-driver
https://github.com/grafana/loki/tree/master/docs/sources/clients/docker-driver


https://grafana.com/blog/2019/07/15/lokis-path-to-ga-docker-logging-driver-plugin-support-for-systemd/

```bash
docker plugin install  grafana/loki-docker-driver:latest --alias loki --grant-all-permissions
```

**Ignore the WARNING** : `WARNING: no logs are available with the 'loki' log driver`