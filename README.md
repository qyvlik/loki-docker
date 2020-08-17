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

## loki storage

https://rtfm.co.ua/en/grafana-labs-loki-using-aws-s3-as-a-data-storage-and-aws-dynamodb-for-indexes/

使用 aws 保存日志，降低使用成本。

```yaml
schema_config:
  configs:
  - from: 2018-04-15
    store: aws
    object_store: s3
    schema: v11
    index:
      prefix: loki_index

storage_config:
  aws:
    s3: s3://AKI***:Qj***@ap-northeast-1/BUCKET_NAME
    dynamodb:
      dynamodb_url: dynamodb://AKI***:Qj***@ap-northeast-1

```

- dynamodb 的读写吞吐会在 loki 指定，每次指定 dynamodb 读写吞吐指标，aws 都会重新收费，请勿随意多次变更，参数如下：

```yaml
table_manager:
  index_tables_provisioning:
    provisioned_write_throughput: 10
    provisioned_read_throughput: 10
  chunk_tables_provisioning:
    provisioned_write_throughput: 10
    provisioned_read_throughput: 10
```

## grafana

https://community.grafana.com/t/solved-grafana-permission-denied-with-docker-on-centos7/12957

```bash
sudo chown -R 472:472 GRAFANA_VOLUMES
```

## loki

```bash
sudo chown -R 10001:10001 LOKI_VOLUMES
```

## loki docker-driver keep-file

loki 的 docker 插件，支持发送日志到 loki server 且保留日志到本地宿主机。

如下配置：
```yaml
version: '2'

services:
  ping:
    image: alpine:latest
    entrypoint: ping localhost
    logging:
      driver: loki
      options:
        loki-url: ${LOKI_URL}
        loki-external-labels: container_name={{.Name}},machine=qyvlik-mac,project=loki-test-app
        keep-file: "true"
        max-size: 512m

```

- `no-file`: 不创建日志文件
- `keep-file`: 容器停止时，是否保留日志文件
- `max-size`: 单个日志文件大小

loki docker-driver 的日志保留位置是 `/var/lib/docker/plugins/<id>/rootfs/var/log/docker/<container_id>`
其中 `id` 是 loki docker 插件的 id，`container_id` 是容器id

注意：**可以使用 `docker log <container_id>` 查看日志，但是无法使用 `docker-compose log` 查看日志。**

## loki 集群

https://grafana.com/blog/2018/12/12/loki-prometheus-inspired-open-source-logging-for-cloud-natives/

## loki 插件日志

docker的插件日志在 docker 守护进程的日志中

```bash
sudo journalctl -fu docker.service
```

- Ubuntu (old using upstart ) - /var/log/upstart/docker.log
- Ubuntu (new using systemd ) - sudo journalctl -fu docker.service
- Amazon Linux AMI - /var/log/docker
- Boot2Docker - /var/log/docker.log
- Debian GNU/Linux - /var/log/daemon.log
- CentOS - /var/log/daemon.log | grep docker
- CoreOS - journalctl -u docker.service
- Fedora - journalctl -u docker.service
- Red Hat Enterprise Linux Server - /var/log/messages | grep docker
- OpenSuSE - journalctl -u docker.service
- OSX - ~/Library/Containers/com.docker.docker/Data/com.docker.driver.amd64-linux/log/d‌​ocker.log
- Windows - Get-EventLog -LogName Application -Source Docker -After (Get-Date).AddMinutes(-5) | Sort-Object Time, as mentioned here.

https://stackoverflow.com/questions/30969435/where-is-the-docker-daemon-log
