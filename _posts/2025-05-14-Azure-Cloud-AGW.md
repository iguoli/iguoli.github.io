---
title: Azure Cloud - Application Gateway
date: 2025-05-14
modify_date: 2025-05-14
tags: Azure Cloud
key: Azure-Cloud-AGW-2025-05-14
---

## Azure application gateway

Azure **Application Gateway** is a Layer 7 (application layer) **HTTP/HTTPS load balancer and reverse proxy** that provides **advanced traffic management** capabilities.

<!--more-->

### Functions

| Function                                       | Description                                                                               |
| ---------------------------------------------- | ----------------------------------------------------------------------------------------- |
| **Layer 7 Load Balancing**                     | Routes traffic based on HTTP(S) headers, URLs, cookies, etc.                              |
| **URL Path-Based Routing**                     | Routes requests to different backend pools based on URL paths (e.g., `/api/`, `/images/`) |
| **Host-Based Routing (Multi-site Hosting)**    | Routes based on domain name (e.g., `api.contoso.com`, `web.contoso.com`)                  |
| **Listener-level Certificate Binding** | Use different TLS certs per listener for multiple sites              |
| **TLS/SSL Termination**                | Decrypts SSL traffic at the gateway (offloads CPU from backends)     |
| **Web Application Firewall (WAF)**     | Protects against OWASP Top 10 vulnerabilities (e.g., SQLi, XSS)      |
| **Cookie-Based Affinity (Session Stickiness)** | Ensures a client stays on the same backend server using a gateway-managed cookie          |
| **Custom Probes**                              | Health checks with flexible configuration (e.g., path, status code, interval)             |

### 🚀 Application Gateway SKUs & Feature Differences

| SKU                    | Key Differences                                                 |
| ---------------------- | --------------------------------------------------------------- |
| **Standard v2**        | Modern, scalable, autoscaling, zone redundant                   |
| **WAF v2**             | Same as Standard v2 but includes Web Application Firewall       |
| **Standard (classic)** | Legacy SKU, lacks advanced features like autoscaling, rewriting |

### Overall diagram

```mermaid
graph TD
    subgraph AGW["Azure Application Gateway"]
        C1["🔓 SSL Termination"]
        C2["🛡️ WAF Inspection<br>(SQLi, XSS, DDoS)"]
        C3["↔️ Host-Based Routing<br>(e.g., api.contoso.com)"]
        C4["↔️ URL Path-Based Routing<br>(e.g., /images → VMSS, /api → AKS)"]
    end
    user[Internet User] -->|HTTPS Request| AGW
    AGW --> backendPool

    backendPool --> D1[Virtual Machines]
    backendPool --> D2[VM Scale Sets]
    backendPool --> D3[Azure Kubernetes Service]
    backendPool --> D4[Azure App Service]
```

### Multiple domains hosting (多域名托管)

```mermaid
graph TD
  subgraph DNS
    www["portal.contoso.com (CNAME)"]
    api["api.contoso.com (CNAME)"]
    kibana["kibana.contoso.com (CNAME)"]
    gateway["myappgw.eastus.cloudapp.azure.com<br>(Gateway canonical name)"]
  end

  www --> gateway
  api --> gateway
  kibana --> gateway
  gateway --> |A record| agw

  subgraph Application Gateway
    agw["Azure Application Gateway"]

    listener1["Listener<br>- Host: portal.contoso.com<br>- Port: 443<br>- Protocol: HTTPS<br>- SSL Certificate"]
    listener2["Listener<br>- Host: api.contoso.com<br>..."]
    listener3["Listener<br>- Host: kibana.contoso.com<br>..."]

    agw --> |"Host-based routing<br>(portal.contoso.com)"| listener1
    agw --> |"Host-based routing<br>(api.contoso.com)"| listener2
    agw --> |"Host-based routing<br>(kibana.contoso.com)"| listener3

    listener1 --> rule1["Rule<br>- Backend target<br>- Backend settings<br>- Path-based routing"]
    listener2 --> rule2["Rule"]
    listener3 --> rule3["Rule"]

    rule1 --> backendPool1["Backend Pool<br>(IP, FQDN, VM, VMSS)"]
    rule2 --> backendPool2["Backend Pool<br>(IP, FQDN, VM, VMSS)"]
    rule3 --> backendPool3["Backend Pool<br>(IP, FQDN, VM, VMSS)"]
  end

  lb["Load Balancer"]

  subgraph Virtual Machines
    vm["Kibana Server"]
  end

  backendPool1 -->|"IP address"| lb
  backendPool2 -->|"IP address"| lb
  backendPool3 -->|"VM"| vm

  lb --> ingressController

  subgraph AKS[Azure Kubernetes Service]
    ingressController["Ingress Controller<br>(Nginx, AGIC, etc.)"]
    ingress1["Ingress<br>portal.contoso.com"]
    ingress2["Ingress<br>api.contoso.com"]

    ingressController --> ingress1
    ingressController --> ingress2
    ingress1 --> frontend["Frontend Web App"]
    ingress2 --> apiBackend["API Service"]
  end
```