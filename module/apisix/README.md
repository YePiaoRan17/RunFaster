## install

https://apisix.apache.org/docs/apisix/installation-guide/

### Docker

https://github.com/apache/apisix-docker

```shell
cd apisix-docker/example
docker-compose -p docker-apisix up -d
```

#### dashboard

http://192.168.19.128:9000/

admin admin

#### prometheus

http://192.168.19.128:9090/

#### grafana

http://192.168.19.128:3000/

#### web

http://192.168.19.128:9081/

http://192.168.19.128:9082/



### 路由注册

https://apisix.apache.org/docs/apisix/router-radixtree/

```shell
curl http://192.168.19.128:9180/apisix/admin/routes/1 -H 'X-API-KEY: edd1c9f034335f136f87ad84b625c8f1' -X PUT -d '
{
    "upstream": {
       "nodes": {
           "192.168.19.128:9081": 1,
           "192.168.19.128:9082": 1
       },
       "type": "roundrobin"
    },
    "priority": 2,
    "uri": "/hello"
}'

curl http://192.168.19.128:9080/hello
```





#### Prometheus

https://apisix.apache.org/zh/docs/apisix/plugins/prometheus/

```yaml
_meta:
  disable: false
plugin_attr:
  prometheus:
    export_addr:
      ip: 192.168.19.128
      port: 9091
    export_uri: /apisix/metrics
```

curl -i http://192.168.19.128:9091/apisix/prometheus/metrics

todo：无法更换路由



#### OpenTelemetry



```shell
curl http://192.168.19.128:9180/apisix/admin/routes/1 -H 'X-API-KEY: edd1c9f034335f136f87ad84b625c8f1' -X PUT -d '
{
    "upstream": {
       "nodes": {
           "192.168.19.128:9081": 1,
           "192.168.19.128:9082": 1
       },
       "type": "roundrobin"
    },
    "plugins": {
        "opentelemetry": {
            "sampler": {
                "name": "always_on"
            }
        }
    },
    "priority": 2,
    "uri": "/hello"
}'

curl http://192.168.19.128:9080/hello
```



config.yaml

```yaml
plugins:
  - opentelemetry
  - prometheus

plugin_attr:
  prometheus:
    export_addr:
      ip: "0.0.0.0"
      port: 9091
  opentelemetry:
    resource:
      service.name: APISIX
      tenant.id: business_id
    collector:
      address: 192.168.19.128:4318
      request_timeout: 3
      request_headers:
        foo: bar
    batch_span_processor:
      drop_on_queue_full: false
      max_queue_size: 6
      batch_timeout: 2
      inactive_timeout: 1
      max_export_batch_size: 2
```



sudo ufw allow 4318



# Todo

如何开启插件

插件重启：https://apisix.apache.org/docs/dashboard/FAQ/

