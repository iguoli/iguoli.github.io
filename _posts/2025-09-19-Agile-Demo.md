---
title: Network Security - OAuth 2.0
date: 2025-08-01
modify_date: 2025-08-01
tags: Network Security
key: Network-Security-OAuth-2025-08-01
mermaid: true
---

## 一、项目准备阶段（Scrum Master + PO）

### 1. 定义愿景 & 范围

* **愿景**：为车企构建一个高效、安全、可扩展的采购系统，覆盖 **采购申请 → 审批 → 下单 → 供应商对接 → 支付 → 报表**。
* **最小可行产品 (MVP)**：先上线采购申请、审批、下单。

📌 **产出**：在 **Confluence** 写下《产品愿景与范围文档》。

### 2. 搭建工具体系

* **Jira**：新建一个 **Scrum Software 项目**，配置好 **Epic/Story/Task** 模板。
* **Confluence**：作为文档库，存放愿景、需求规格、架构设计、会议纪要。
* **GitHub/GitLab**：源代码仓库，按 **microservice 模块** 或 **单体仓库+模块目录** 管理。
* **GitLab CI/CD**：流水线配置，负责 **构建 → 测试 → 制品（Docker 镜像） → 部署到 K8s**。
* **K8s + Helm**：部署环境，提供 **测试环境 / UAT / 生产环境**。

## 二、敏捷实践（Scrum 流程）

### 1. Backlog 建立（PO 负责）

在 Jira 里创建以下 **Epic**：

* **Epic-1: 用户与权限管理**
* **Epic-2: 采购申请流程**
* **Epic-3: 审批与下单**
* **Epic-4: 供应商对接**
* **Epic-5: 支付与报表**
* **Epic-6: 系统运维与监控**

每个 Epic 下拆分 **Story（用户故事）**，例如：

* **Story**: 作为采购人员，我希望能提交采购申请，以便走审批流程。

再将 Story 拆成 **Task（开发/测试/运维任务）**：

* Task: 设计采购申请表结构（数据库）
* Task: 实现采购申请 API
* Task: 前端页面开发
* Task: 单元测试与集成测试

📌 **最佳实践**：Story 用 **用户语言**，Task 用 **开发语言**。

### 2. Sprint 计划（Scrum Master 主持）

* Sprint 周期：**2 周**
* Sprint-1 目标：实现用户注册/登录 + 提交采购申请
* Sprint-2 目标：实现审批流程 + 下单
* Sprint-3 目标：实现供应商接口对接
* Sprint-4 目标：实现支付 + 报表
* Sprint-5 目标：运维与性能优化

在 Sprint Planning 中：

* PO 提供 **优先级排序后的 Backlog**
* 团队估算 **Story Points（工作量）**
* Scrum Master 监督 **可交付性**

### 3. 日常敏捷实践

* **Daily Scrum（站会）**：每天 15 分钟，回答 3 个问题：

  1. 昨天完成了什么？
  2. 今天计划做什么？
  3. 有什么阻碍？
* **Sprint Review（评审）**：演示已完成的功能给 PO & 业务方。
* **Sprint Retrospective（回顾）**：团队总结改进点。

## 三、技术落地 Demo

### 1. Git 流程

采用 **GitLab Flow**：

* `main` → 稳定分支
* `develop` → 集成分支
* `feature/*` → 功能分支
* `release/*` → 预发布
* `hotfix/*` → 紧急修复

### 2. GitLab CI/CD Pipeline (示例 `.gitlab-ci.yml`)

```yaml
stages:
  - build
  - test
  - docker
  - deploy

build:
  stage: build
  script:
    - mvn clean package -DskipTests
  artifacts:
    paths:
      - target/*.jar

test:
  stage: test
  script:
    - mvn test

docker-build:
  stage: docker
  script:
    - docker build -t registry.example.com/purchase-service:$CI_COMMIT_SHA .
    - docker push registry.example.com/purchase-service:$CI_COMMIT_SHA

deploy:
  stage: deploy
  script:
    - helm upgrade --install purchase-service ./helm/purchase-service \
        --set image.tag=$CI_COMMIT_SHA
  environment:
    name: dev
    url: http://purchase.dev.company.com
```

### 3. Helm Chart (示例 `values.yaml`)

```yaml
replicaCount: 2

image:
  repository: registry.example.com/purchase-service
  tag: "latest"

service:
  type: ClusterIP
  port: 8080

resources:
  limits:
    cpu: "500m"
    memory: "512Mi"
```

### 4. 监控与日志

* 在 **K8s 部署 Prometheus + Grafana** → 监控 API 延迟、错误率。
* 部署 **EFK（Elasticsearch + Fluentd + Kibana）** → 日志收集与可视化。

## 四、Scrum Master & PO 的日常角色

* **作为 PO**：

  * 维护 Product Backlog
  * 确保优先级和需求清晰
  * 和业务方沟通需求

* **作为 Scrum Master**：

  * 确保团队遵守敏捷原则
  * 主持 Sprint Planning / Daily / Review / Retrospective
  * 解决阻碍（技术、资源、跨部门）

## 五、Confluence 知识库结构（推荐）

* **产品愿景与范围**
* **需求规格文档（分 Epic/Story）**
* **架构设计（微服务图、数据库设计、K8s 拓扑）**
* **CI/CD 流程文档**
* **运维手册（监控告警、日志分析）**
* **Sprint 回顾总结**
