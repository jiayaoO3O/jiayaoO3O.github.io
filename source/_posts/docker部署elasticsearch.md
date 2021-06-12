---
title: docker部署elasticsearch
date: 2021-05-27 10:05:00
tags: [docker,elasticsearch]
categories: [技术]
---

## 部署elasticsearch

### 使用单节点模式

在本地或者测试环境可以使用单节点模式, 简单方便

```shell
docker run --name elasticsearch --net elastic -v /path/to/data:/usr/share/elasticsearch/data -p 9200:9200 -p 9300:9300 -d -e discovery.type=single-node -e ELASTIC_PASSWORD=xxxxx -e xpack.security.enabled=true -e xpack.security.authc.api_key.enabled=true docker.elastic.co/elasticsearch/elasticsearch:7.13.0
```

成功之后ES会以单节点模式启动.

<!-- more -->

### 使用多节点模式

如果想在生产环境使用多节点模式, 可以使用以下命令 :
```shell
docker run --name es01 \
 --network=elastic \
 -v /path/to/elastic/elasticsearch/es01:/usr/share/elasticsearch/data \
 -p 9200:9200 -p 9300:9300 \
 -d \
 -e node.name=es01 \
 -e cluster.name=es-cluster \
 -e discovery.seed_hosts=es02 \
 -e cluster.initial_master_nodes=es01,es02 \
 -e ELASTIC_PASSWORD=xxxx \
 -e xpack.security.enabled=true \
 docker.elastic.co/elasticsearch/elasticsearch:7.13.0
```

```shell
docker run --name es02 \
 --network=elastic \
 -v /path/to/jiayao/elastic/elasticsearch/es02:/usr/share/elasticsearch/data \
 -d \
 -e node.name=es02 \
 -e cluster.name=es-cluster \
 -e discovery.seed_hosts=es01 \
 -e cluster.initial_master_nodes=es01,es02 \
 docker.elastic.co/elasticsearch/elasticsearch:7.13.0
```

也可以使用docker-compose :

```yaml
version: '2.2'
services:
  es01:
    image: docker.elastic.co/elasticsearch/elasticsearch:7.13.0
    container_name: es01
    environment:
      - node.name=es01
      - cluster.name=es-docker-cluster
      - discovery.seed_hosts=es02,es03
      - cluster.initial_master_nodes=es01,es02,es03
      - bootstrap.memory_lock=true
      - ELASTIC_PASSWORD=xxxx
      - xpack.security.enabled=true
      - "ES_JAVA_OPTS=-Xms512m -Xmx512m"
    ulimits:
      memlock:
        soft: -1
        hard: -1
    volumes:
      - data01:/usr/share/elasticsearch/data
    ports:
      - 9200:9200
    networks:
      - elastic
  es02:
    image: docker.elastic.co/elasticsearch/elasticsearch:7.13.0
    container_name: es02
    environment:
      - node.name=es02
      - cluster.name=es-docker-cluster
      - discovery.seed_hosts=es01,es03
      - cluster.initial_master_nodes=es01,es02,es03
      - bootstrap.memory_lock=true
      - ELASTIC_PASSWORD=xxxx
      - xpack.security.enabled=true
      - "ES_JAVA_OPTS=-Xms512m -Xmx512m"
    ulimits:
      memlock:
        soft: -1
        hard: -1
    volumes:
      - data02:/usr/share/elasticsearch/data
    networks:
      - elastic
  es03:
    image: docker.elastic.co/elasticsearch/elasticsearch:7.13.0
    container_name: es03
    environment:
      - node.name=es03
      - cluster.name=es-docker-cluster
      - discovery.seed_hosts=es01,es02
      - cluster.initial_master_nodes=es01,es02,es03
      - bootstrap.memory_lock=true
      - ELASTIC_PASSWORD=xxxx
      - xpack.security.enabled=true
      - "ES_JAVA_OPTS=-Xms512m -Xmx512m"
    ulimits:
      memlock:
        soft: -1
        hard: -1
    volumes:
      - data03:/usr/share/elasticsearch/data
    networks:
      - elastic

volumes:
  data01:
    driver: local
  data02:
    driver: local
  data03:
    driver: local

networks:
  elastic:
    driver: bridge
```

volumes数据卷也可以直接绑定本地目录.

### 生产模式配置服务器

```shell
sysctl -w vm.max_map_count=262144
```

```shell
sudo swapoff -a
```

## 部署kibana

注意, 在docker中如果想用-e参数将配置作为环境变量传入kibana中, 则所有的-e参数都是全大写且用下划线分割的：
```shell
docker run --name kibana \
  --net elastic \
  -p 5601:5601 \
  -e ELASTICSEARCH_HOSTS=http://elasticsearch:9200 \
  -e ELASTICSEARCH_PASSWORD=xxxxxxxx \
  -e ELASTICSEARCH_USERNAME=elastic \
  -e XPACK_SECURITY_ENABLED=true \
  -e XPACK_SECURITY_ENCRYPTIONKEY=something_at_least_32_characters \
  -e XPACK_REPORTING_ENCRYPTIONKEY=something_at_least_32_characters \
  -e XPACK_ENCRYPTEDSAVEDOBJECTS_ENCRYPTIONKEY=something_at_least_32_characters \
  -e I18N_LOCALE=zh-CN \
  -d docker.elastic.co/kibana/kibana:7.13.1
```

## 部署filebeat收集日志

按照kibana中的指示安装filebeat之后, 配置filebeat.yml文件收集docker日志 :

```yaml
filebeat.inputs:
# 类型选择docker
- type: docker
  # 容器ID使用'*'代替，表示获取所有容器
  containers.ids:
    - '*'
  # 配置处理器
  processors:
   # 添加docker元数据
    - add_docker_metadata: ~

# 直接输出到es
output.elasticsearch:
  hosts: ["localhost:9200"]
  username: "elastic"
  password: "xxxx"
setup.kibana:
  host: "http://localhost:5601"
setup.ilm.rollover_alias: "自定义的索引命名"
```

