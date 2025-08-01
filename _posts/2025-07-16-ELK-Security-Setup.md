---
title: ELK Security Setup
date: 2025-07-16
modify_date: 2025-07-16
tags: ELK ElasticSearch Kibana
key: ELK-Security-Setup-2025-07-16
mermaid: true
---

# Minimal Security Setup for Elastic Stack

## Part 1: Elasticsearch Security Setup

### Step 1: Minimal Security Configuration (All Nodes)

- **Purpose**: Enables just enough security for Elasticsearch while minimizing impact
- **Impact**: Requires restart of all Elasticsearch nodes
- **Note**: This must be done on all nodes before proceeding

<!--more-->

**Configuration**:

- `/data/elasticsearch/config/elasticsearch.yml`

```yaml
# Enable security features
xpack.security.enabled: true

# Node-to-node transport security
xpack.security.transport.ssl:
  enabled: true
  verification_mode: certificate
  certificate: /data/elasticsearch/certs/es-node/es-node.crt
  key: /data/elasticsearch/certs/es-node/es-node.key
  certificate_authorities: [ "/data/elasticsearch/certs/ca/ca.crt" ]


# Keep HTTP unencrypted for internal network
xpack.security.http.ssl:
  enabled: false  # Disable HTTPS to avoid certificate management
```

### Step 2: Certificate Generation (First Master Node)

- **Purpose**: Creates PKI infrastructure for node authentication
- **Files Created**:
  - ca.crt: Certificate Authority public cert
  - ca.key: CA private key (keep secure!)
  - node certificates for each IP/DNS name

```bash
# Generate Certificate Authority
sudo /data/elasticsearch/bin/elasticsearch-certutil ca \
  --out /data/elasticsearch/certs/elastic-stack-ca.zip \
  --pem # output certificates and keys in PEM format instead of PKCS#12

sudo unzip /data/elasticsearch/certs/elastic-stack-ca.zip -d /data/elasticsearch/certs/

# Generate node certificates signed by CA
sudo /data/elasticsearch/bin/elasticsearch-certutil cert \
  --ca-cert /data/elasticsearch/certs/ca/ca.crt \
  --ca-key /data/elasticsearch/certs/ca/ca.key \
  --ip 10.153.36.68,10.153.36.69,10.153.36.70 \
  --dns elasticsearch,elasticsearch.elk.svc.cluster.local \
  --name es-node \
  --out /data/elasticsearch/certs/elastic-certificates.zip \
  --pem

sudo unzip /data/elasticsearch/certs/elastic-certificates.zip -d /data/elasticsearch/certs/

sudo chown -R elasticsearch:elasticsearch /data/elasticsearch/certs/
sudo chmod 640 /data/elasticsearch/certs/*/*.key
sudo chmod 644 /data/elasticsearch/certs/*/*.crt
```
**Note**: Distribute certs to all nodes in `/data/elasticsearch/certs/`

### Step 3: Password Setup (Any Master Node)

- **Purpose**: Sets passwords for built-in users (elastic, kibana_system, etc.)
- **Output**: Will display generated passwords - save these securely!
- **Recommendation**: Use 'auto' for initial setup, change to 'interactive' for production

```bash
sudo /data/elasticsearch/bin/elasticsearch-setup-passwords auto
```

```
Changed password for user apm_system
PASSWORD apm_system = G7CnVYvCztHE7NEpU3cU

Changed password for user kibana_system
PASSWORD kibana_system = GcJihp4NTeDSEqXEro3U

Changed password for user kibana
PASSWORD kibana = GcJihp4NTeDSEqXEro3U

Changed password for user logstash_system
PASSWORD logstash_system = DS7ynD3uiJ6OgDsSbc0o

Changed password for user beats_system
PASSWORD beats_system = TcTehkusBZsW1vLXoXMA

Changed password for user remote_monitoring_user
PASSWORD remote_monitoring_user = 72G0p6zvwBHH9uoDsXmd

Changed password for user elastic
PASSWORD elastic = fYpw7K0Wwbg2O4Nnf4SM
```

**Key Users Created**:

- elastic: Superuser account
- kibana_system: Kibana service account
- logstash_system: Logstash service account
- beats_system: Beats service account

### Step 4: Restart Elasticsearch

- **Purpose**: Applies security configuration changes
- **Verification**: Check logs for errors after restart
- **Troubleshooting**: If nodes won't join cluster, verify:
  - All nodes have identical CA cert
  - Certificates include correct IPs/DNS names
  - Security settings match across nodes

```bash
sudo systemctl restart elasticsearch

# Verify cluster health
# Use the elastic user created in Step 3
# Non-Production
export ES_USERNAME=elastic
export ES_PASSWORD=fYpw7K0Wwbg2O4Nnf4SM
curl -u $ES_USERNAME:$ES_PASSWORD http://10.94.144.71:9200/_cluster/health\?pretty
# Production
curl -u $ES_USERNAME:$ES_PASSWORD http://10.153.36.68:9200/_cluster/health\?pretty
```

## Part 2: Kibana Configuration

### Step 5: Update Kibana Configuration

- **Purpose**: Configures Kibana to connect to secured Elasticsearch
- **Location**: Update in your AKS ConfigMap or values.yaml if using Helm
- **Critical Values**:
  - Must use the kibana_system user credentials from Step 3
  - Each xpack.*.enabled flag activates specific connector capabilities

Create or update the ConfigMap for Kibana:

```bash
kubectl create configmap kibana-config --from-file=kibana.yml -n elk --dry-run=client -o yaml | kubectl apply -f -
```

```yaml
# kibana.yml
server.host: "0.0.0.0"
server.basePath: "/kibana"
server.rewriteBasePath: true
elasticsearch.hosts: ${ELASTICSEARCH_HOSTS}
elasticsearch.username: ${ELASTIC_USERNAME}
elasticsearch.password: ${ELASTIC_PASSWORD}
xpack.encryptedSavedObjects.encryptionKey: c798ac8e2712b3eb939e55337969c109
```

Create a Kubernetes Secret for Kibana credentials:

```bash
kubectl create secret generic kibana-es-credentials \
  --from-literal=ELASTIC_USERNAME=kibana_system \
  --from-literal=ELASTIC_PASSWORD='GcJihp4NTeDSEqXEro3U' \
  -n elk --dry-run=client -o yaml | kubectl apply -f -
```

Update the Kibana deployment to use the new ConfigMap and Secret:

```bash
kubectl patch deployment kibana -n elk --patch "$(cat <<EOF
spec:
  template:
    spec:
      containers:
      - name: kibana
        env:
        - name: ELASTICSEARCH_HOSTS
          value: "http://elasticsearch:9200"
        - name: ELASTIC_USERNAME
          valueFrom:
            secretKeyRef:
              name: kibana-es-credentials
              key: ELASTIC_USERNAME
        - name: ELASTIC_PASSWORD
          valueFrom:
            secretKeyRef:
              name: kibana-es-credentials
              key: ELASTIC_PASSWORD
        - name: SERVER_BASE_PATH
          value: "/kibana"
        - name: SERVER_REWRITE_BASE_PATH
          value: "true"
        - name: XPACK_ENCRYPTED_SAVED_OBJECTS_ENCRYPTION_KEY
          value: "c798ac8e2712b3eb939e55337969c109"
        volumeMounts:
        - name: kibana-config
          mountPath: /usr/share/kibana/config/kibana.yml
          subPath: kibana.yml
      volumes:
      - name: kibana-config
        configMap:
          name: kibana-config
EOF)"
```

### Step 6: Restart Kibana

- **Purpose**: Applies security configuration changes
- **Method**: Rolling restart ensures zero downtime
- **Verification**: Check Kibana logs after restart

```bash
kubectl rollout restart deployment kibana -n elk

# Monitor Kibana logs to ensure it starts correctly
kubectl stern deployment/kibana -n elk
```

## Part 3: Logstash Configuration

### Step 7: Create Logstash User and Role

Create a Logstash user and role in Elasticsearch:

```json
// Create logstash_writer role
POST /_security/role/logstash_writer
{
  "cluster": ["manage_index_templates", "monitor"],
  "indices": [
    {
      "names": ["*"],
      "privileges": ["write", "create", "create_index", "auto_configure"]
    }
  ]
}

// Create logstash user with password
POST /_security/user/logstash_writer
{
  "password": "DS7ynD3uiJ6OgDsSbc0o",
  "roles": ["logstash_writer"],
  "full_name": "Logstash Writer"
}
```

### Step 8: Update Logstash Configuration

Create a Kubernetes Secret for Logstash credentials:

```bash
kubectl create secret generic logstash-es-credentials \
  --from-literal=ELASTIC_USERNAME=logstash_writer \
  --from-literal=ELASTIC_PASSWORD='DS7ynD3uiJ6OgDsSbc0o' \
  --from-literal=MONITORING_USERNAME=logstash_system \
  --from-literal=MONITORING_PASSWORD='DS7ynD3uiJ6OgDsSbc0o' \
  -n elk --dry-run=client -o yaml | kubectl apply -f -
```

Update the Logstash configuration to use the new credentials:

```bash
kubectl create configmap logstash-config --from-file=logstash.yml -n elk --dry-run=client -o yaml | kubectl apply -f -
```

```yaml
http.host: "0.0.0.0"
path.config: /usr/share/logstash/config
xpack.monitoring.enabled: true
xpack.monitoring.elasticsearch.hosts: ${ELASTICSEARCH_HOSTS}
xpack.monitoring.elasticsearch.username: ${MONITORING_USERNAME}
xpack.monitoring.elasticsearch.password: ${MONITORING_PASSWORD}
```

Update the Logstash pipeline configuration to use the new credentials:

```ruby
output {
  elasticsearch {
    hosts => ["${ELASTICSEARCH_HOSTS}"]
    user => "${ELASTIC_USERNAME}"
    password => "${ELASTIC_PASSWORD}"
    index => "%{[@metadata][input]}-%{+YYYY.MM.dd}"
    action => "index"
  }
}
```

Update the Logstash deployment to use the new ConfigMap and Secret:

```bash
kubectl patch deployment logstash -n elk --patch "$(cat <<EOF
spec:
  template:
    spec:
      containers:
      - name: logstash
        env:
        - name: ELASTICSEARCH_HOSTS
          value: "http://elasticsearch.elk.svc.cluster.local:9200"
        - name: ELASTIC_USERNAME
          valueFrom:
            secretKeyRef:
              name: logstash-es-credentials
              key: ELASTIC_USERNAME
        - name: ELASTIC_PASSWORD
          valueFrom:
            secretKeyRef:
              name: logstash-es-credentials
              key: ELASTIC_PASSWORD
        - name: MONITORING_USERNAME
          valueFrom:
            secretKeyRef:
              name: logstash-es-credentials
              key: MONITORING_USERNAME
        - name: MONITORING_PASSWORD
          valueFrom:
            secretKeyRef:
              name: logstash-es-credentials
              key: MONITORING_PASSWORD
        volumeMounts:
        - name: logstash-config
          mountPath: /usr/share/logstash/config/logstash.yml
          subPath: logstash.yml
        - name: logstash-pipeline
          mountPath: /usr/share/logstash/config/pipeline.yml
          subPath: pipeline.yml
      volumes:
      - name: logstash-config
        configMap:
          name: logstash-config
      - name: logstash-pipeline
        configMap:
          name: logstash-pipeline
EOF)"
```

### Step 9: Restart Logstash

- **Purpose**: Applies configuration changes
- **Method**: Rolling restart ensures zero downtime
- **Verification**: Check Logstash logs after restart

```bash
kubectl rollout restart deployment logstash -n elk

# Monitor Logstash logs to ensure it starts correctly
kubectl stern deployment/logstash -n elk
```

## Part 4: Filebeat Configuration

### Step 10: Create Filebeat User and Role

Create a Filebeat user and role in Elasticsearch:

```json
// Create filebeat_writer role
POST /_security/role/filebeat_writer
{
  "cluster": ["monitor", "manage_index_templates", "manage_ilm", "manage_indices"],
  "indices": [
    {
      "names": ["*"],
      "privileges": ["write", "create", "create_index", "auto_configure"]
    }
  ]
}

// Create filebeat user with password
POST /_security/user/filebeat_writer
{
  "password": "TcTehkusBZsW1vLXoXMA",
  "roles": ["filebeat_writer"],
  "full_name": "Filebeat Writer"
}
```

### Step 11: Update Filebeat Configuration

Create a Kubernetes Secret for Filebeat credentials:

```bash
kubectl create secret generic filebeat-es-credentials \
  --from-literal=ELASTIC_USERNAME=filebeat_writer \
  --from-literal=ELASTIC_PASSWORD='TcTehkusBZsW1vLXoXMA' \
  -n elk --dry-run=client -o yaml | kubectl apply -f -
```

Update the Filebeat configuration to use the new credentials:

```yaml
# filebeat.yml
output.elasticsearch:
  hosts: [${ELASTICSEARCH_HOSTS}]
  username: ${ELASTIC_USERNAME}
  password: ${ELASTIC_PASSWORD}
  index: filebeat-%{+yyyy.MM.dd}
```

Update the Filebeat DaemonSet to use the new ConfigMap and Secret:

```bash
kubectl patch daemonset filebeat -n elk --patch "$(cat <<EOF
spec:
  template:
    spec:
      containers:
      - name: filebeat
        env:
        - name: ELASTICSEARCH_HOSTS
          value: "http://elasticsearch.elk.svc.cluster.local:9200"
        - name: ELASTIC_USERNAME
          valueFrom:
            secretKeyRef:
              name: filebeat-es-credentials
              key: ELASTIC_USERNAME
        - name: ELASTIC_PASSWORD
          valueFrom:
            secretKeyRef:
              name: filebeat-es-credentials
              key: ELASTIC_PASSWORD
        volumeMounts:
        - name: filebeat-config
          mountPath: /usr/share/filebeat/filebeat.yml
          subPath: filebeat.yml
      volumes:
      - name: filebeat-config
        configMap:
          name: filebeat-config
EOF)"
```

### Step 12: Restart Filebeat

```bash
# Restart Filebeat to apply configuration changes
kubectl rollout restart daemonset filebeat -n elk

# Monitor Filebeat logs to ensure it starts correctly
kubectl stern daemonset/filebeat -n elk
```