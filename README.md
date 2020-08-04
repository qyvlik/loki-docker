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

```yaml
schema_config:
  configs:
  - from: 0
    store: dynamo
    object_store: s3
    schema: v9
    index:
      prefix: dynamodb_table_name
      period: 0

storage_config:
  aws:
    s3: s3://access_key:secret_access_key@region/bucket_name
    dynamodbconfig:
      dynamodb: dynamodb://access_key:secret_access_key@region
```

```yaml
storage_config:
  boltdb:
    directory: /loki/index
#  filesystem:
#    directory: /tmp/loki/chunks
  aws: 
     s3: s3://AKI***PHA:0/W***WFN@eu-west-1/logger-loki-poc
```
