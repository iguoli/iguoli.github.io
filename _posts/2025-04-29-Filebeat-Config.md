---
title: Filebeat configuration on k8s
date: 2025-04-29
modify_date: 2025-04-29
tags: Kubernetes DevOps
key: Filebeat-config-2025-04-29
---
## 1. Background

The pods' logs on AKS are collected by filebeat which is deployed as a DaemonSet on each node. The logs will be classified into different indices (business and non-business) based on the container name, and then rolled over to a new index when the index size exceeds 10GB or 1 day. The business logs are stored in Elasticsearch for 30 days, while the non-business logs are stored for 7 days.

<!--more-->

## 2. Implementation

### 2.1. Filebeat Config

filebeat-config.yaml

```yaml
filebeat.inputs:
- type: filestream
  id: aks-container-logs
  enabled: true
  paths:
  - /var/log/containers/*.log
  parsers.container.stream: all
  prospector.scanner.symlinks: true
  processors:
  - add_kubernetes_metadata:
      in_cluster: true
      host: ${NODE_NAME}
      matchers:
      - logs_path:
          logs_path: "/var/log/containers/"
  - include_fields:
      fields:
      - host.name
      - kubernetes.container.name
      - kubernetes.namespace
      - kubernetes.node.name
      - kubernetes.pod.name
      - kubernetes.pod.uid
      - log.file.path
      - log.offset
      - message
      - stream

processors:
- drop_fields:
    fields:
    - agent.ephemeral_id
    - agent.hostname
    - agent.id
    - agent.type
    - agent.version
    - ecs.version
    - agent
    - ecs
    ignore_missing: true

logging.level: info
http.enabled: true
http.port: 5066

output.elasticsearch:
  hosts: ["http://elasticsearch.elk.svc.cluster.local:9200"]

  indices:
  - index: "filebeat_biz"
    when.or:
    - equals.kubernetes.container.name: "my-gateway"
    - equals.kubernetes.container.name: "my-apm-server"
    - equals.kubernetes.container.name: "my-web-console-v2"
  - index: "filebeat_nonbiz"
```

### 2.2. ILM Policy

#### 2.2.1. Create business ILM policy

`PUT _ilm/policy/filebeat-biz-30d-ilm-policy`

```json
{
  "policy": {
    "phases": {
      "hot": {
        "actions": {
          "rollover": {
            "max_primary_shard_size": "10gb",
            "max_age": "1d"
          }
        }
      },
      "delete": {
        "min_age": "30d",
        "actions": {
          "delete": {}
        }
      }
    }
  }
}
```

#### 2.2.2. Create non-business ILM policy

`PUT _ilm/policy/filebeat-nonbiz-7d-ilm-policy`

```json
{
  "policy": {
    "phases": {
      "hot": {
        "actions": {
          "rollover": {
            "max_primary_shard_size": "10gb",
            "max_age": "1d"
          }
        }
      },
      "delete": {
        "min_age": "7d",
        "actions": {
          "delete": {}
        }
      }
    }
  }
}
```

### 2.3. Index template

#### 2.3.1. Create business index template

`PUT _index_template/filebeat-biz-index-template`

```json
{
  "index_patterns": ["filebeat_biz-*"],
  "template": {
    "settings": {
      "index.lifecycle.name": "filebeat-biz-30d-ilm-policy",
      "index.lifecycle.rollover_alias": "filebeat_biz"
    },
    "aliases": {}
  },
  "priority": 500
}
```

#### 2.3.2. Create non-business index template

`PUT _index_template/filebeat-nonbiz-index-template`

```json
{
  "index_patterns": ["filebeat_nonbiz-*"],
  "template": {
    "settings": {
      "index.lifecycle.name": "filebeat-nonbiz-7d-ilm-policy",
      "index.lifecycle.rollover_alias": "filebeat_nonbiz"
    },
    "aliases": {}
  },
  "priority": 500
}
```

### 2.4. Rollover alias and initial index

#### 2.4.1. Create business initial index and rollover alias

- `PUT <filebeat_biz-{now/d{yyyy.MM.dd|+08:00}}-000001>`

- URL Encoded: `PUT %3Cfilebeat_biz-%7Bnow%2Fd%7Byyyy.MM.dd%7C%2B08%3A00%7D%7D-000001%3E`

```json
{
  "aliases": {
    "filebeat_biz": {
      "is_write_index": true
    }
  }
}
```

此命令是在同一个操作里同时完成了两件事：

1. **创建了初始索引**

   索引名是动态的，比如今天执行的话，就是：filebeat_biz-2025.04.28-000001

2. **创建并绑定了 alias** `filebeat-biz-nonprod`

   并且加了："is_write_index": true，表示这个索引是 alias 的当前写入索引。

#### 2.4.2. Create non-business initial index and rollover alias

- `PUT <filebeat_nonbiz-{now/d{yyyy.MM.dd|+08:00}}-000001>`

- URL Encoded: `PUT %3Cfilebeat_nonbiz-%7Bnow%2Fd%7Byyyy.MM.dd%7C%2B08%3A00%7D%7D-000001%3E`

```json
{
  "aliases": {
    "filebeat_nonbiz": {
      "is_write_index": true
    }
  }
}
```

- PUT _ilm/policy：新建生命周期策略（控制 rollover 和保留天数）。

- PUT _index_template：设置索引模板，任何新建的匹配 pattern（比如 filebeat_biz-*）的索引，自动套用 ILM策略、shard数、副本数、alias。

- PUT `<dynamic-index-name>`：用 Elasticsearch 动态表达式 `<...>` 生成带日期的索引名，PUT 时要包含尖括号 `<...>`，这是动态索引的语法。

- `"is_write_index": true`：告诉 alias，当前索引是写入的地方。

#### 2.4.3 Rollover the alias manually if needed

- `POST /filebeat_biz/_rollover`

- `POST /filebeat_nonbiz/_rollover`