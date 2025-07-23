---
title: Azure Cloud - Storage Overview
date: 2025-05-12
modify_date: 2025-05-12
tags: Azure Cloud
key: Azure-Cloud-Storage-Overview-2025-05-12
---

## 🗄️ **Azure Storage Layer – Detailed Explanation**

### 1. **Azure Blob Storage (Object Storage)**

> **Purpose**: Storing unstructured data like images, videos, backups, and logs.

#### 🔹 Key Features:

* Stores data as **objects** in containers.
* Supports **tiered storage**: Hot, Cool, Archive (optimize for access frequency).
* REST API accessible; supports **SAS tokens** and **Azure AD** auth.
* Highly scalable and geo-redundant.

#### 🔹 Use Cases:

* Backup and disaster recovery
* Static website hosting
* Logging and telemetry
* Machine learning datasets
* Data lake (see ADLS Gen2)

### 2. **Azure Data Lake Storage Gen2 (ADLS Gen2)**

> **Purpose**: Big data analytics storage built on top of Blob Storage.

#### 🔹 Key Features:

* Combines **Hadoop-compatible** hierarchical namespace (folders) with Blob scalability.
* Optimized for **analytics** and **parallel access**.
* Supports **ACLs** and POSIX permissions.
* Integrated with **Azure Synapse**, **Databricks**, and **HDInsight**.

#### 🔹 Use Cases:

* Big data analytics
* Enterprise data lakes
* Storing raw/processed data for AI/ML pipelines

### 3. **Azure Files (File Shares)**

> **Purpose**: Fully managed file shares in the cloud using SMB/NFS protocols.

#### 🔹 Key Features:

* Mountable via **SMB** or **NFS** on Windows, Linux, and macOS.
* Supports **identity-based access** (Azure AD DS).
* **Premium** tier (SSD) for high IOPS; **Standard** for general use.
* Geo-redundancy options available.

#### 🔹 Use Cases:

* File server replacement (lift-and-shift)
* Shared configuration files across VMs/containers
* Application-level file storage
* Hosting FSLogix user profiles for Azure Virtual Desktop

### 4. **Azure Disks (Managed Disks)**

> **Purpose**: Persistent block-level storage used for Azure Virtual Machines.

#### 🔹 Key Features:

* Attached to VMs as OS/Data disks.
* Disk types:

  * **Standard HDD** – Low-cost dev/test
  * **Standard SSD** – Balanced performance
  * **Premium SSD** – High-performance workloads
  * **Ultra Disk** – High IOPS, low latency (mission-critical DBs)
* Snapshots and disk encryption supported.
* Zone-redundant disks available in some regions.

#### 🔹 Use Cases:

* VM boot and data volumes
* Databases (SQL Server, Oracle, etc.)
* High-performance applications
* Stateful workloads in AKS

### 5. **Azure Table Storage (NoSQL Key-Value Store)**

> **Purpose**: Schema-less NoSQL storage for structured, non-relational data.

#### 🔹 Key Features:

* Stores data as rows (entities) with key-value pairs.
* Partition key + row key = unique ID for fast lookup.
* Low-cost and massively scalable.
* Simple to use, accessed via REST or SDK.

#### 🔹 Use Cases:

* Audit logs
* IoT device metadata
* User profile data
* Shopping cart or session data

### 6. **Azure Queue Storage**

> **Purpose**: Messaging system for decoupling application components.

#### 🔹 Key Features:

* Simple **message queuing service** (first-in-first-out).
* Used for communication between app layers/services.
* Highly available and durable.
* Supports **Poison Message Handling**.

#### 🔹 Use Cases:

* Asynchronous processing
* Order/task/event handling pipelines
* Background jobs, queue-triggered functions
* Scaling microservices

### 7. **Archive and Backup Storage**

> **Purpose**: Long-term data storage and protection.

#### 🔹 Azure Backup:

* Centralized **backup-as-a-service** for VMs, SQL, file shares, etc.
* Managed through **Recovery Services Vault**.
* Supports retention, policy enforcement, and encryption.

#### 🔹 Archive Storage (Blob Tier):

* Very low-cost tier for rarely accessed data.
* High latency (hours) to retrieve.
* Ideal for regulatory or compliance data.

#### 🔹 Use Cases:

* Compliance data (7+ year retention)
* Legacy data archival
* DR strategy with Vaults

### Summary Comparison Table:

| Storage Type   | Data Model   | Access Protocol    | Best For                    | Performance Tiers        |
| -------------- | ------------ | ------------------ | --------------------------- | ------------------------ |
| Blob Storage   | Object       | REST/SAS/ADLS Gen2 | Media, backups, ML data     | Hot, Cool, Archive       |
| ADLS Gen2      | Hierarchical | HDFS API, REST     | Analytics, data lakes       | Hot, Cool                |
| Azure Files    | File         | SMB/NFS            | Shared storage, legacy apps | Standard, Premium        |
| Azure Disks    | Block        | Attached to VM     | VM data, databases          | HDD, SSD, Premium, Ultra |
| Table Storage  | Key-Value    | REST/SDK           | Logs, metadata, NoSQL apps  | N/A                      |
| Queue Storage  | Messaging    | REST/SDK           | Decoupling services         | N/A                      |
| Backup/Archive | All types    | Azure Portal/SDK   | Long-term storage           | Vault, Archive Blob      |

Great question. All the storage types mentioned in the Azure Storage Layer belong to one or more **Azure services**, either under **Azure Storage** or specialized services.

Here’s a clear mapping between **storage types** and the actual **Azure service names** you’ll find in the portal or when provisioning resources:

## 📦 **Mapping: Storage Types → Azure Service Names**

| **Storage Type**             | **Azure Service Name** (in Portal/API)              | **Remarks**                              |
| ---------------------------- | --------------------------------------------------- | ---------------------------------------- |
| ✅ **Blob Storage**           | **Storage Account** *(General-purpose v2)*          | Tiered object storage (Hot/Cool/Archive) |
| ✅ **Data Lake Storage Gen2** | **Storage Account** *(with hierarchical namespace)* | Same as Blob but with Data Lake features |
| ✅ **Azure Files**            | **Storage Account** *(General-purpose v2)*          | File share via SMB/NFS                   |
| ✅ **Azure Disks**            | **Managed Disks**                                   | Resource attached to VMs                 |
| ✅ **Table Storage**          | **Storage Account**                                 | NoSQL key-value storage                  |
| ✅ **Queue Storage**          | **Storage Account**                                 | Basic messaging for apps                 |
| ✅ **Backup Storage**         | **Recovery Services Vault**                         | Backups for VMs, SQL, files, etc.        |
| ✅ **Archive Blob**           | **Storage Account** *(Blob Tier = Archive)*         | Archive tier in Blob service             |

### 🔹 Service Categories in Azure Portal

| **Azure Portal Category**   | **Contains These Storage Types**                                        |
| --------------------------- | ----------------------------------------------------------------------- |
| **Storage Account**         | Blob, File, Table, Queue, Data Lake Gen2                                |
| **Managed Disks**           | Azure Disks                                                             |
| **Recovery Services Vault** | Backup & Recovery-related storage                                       |
| **Azure NetApp Files**      | (Optional) High-performance enterprise file storage (not covered above) |
| **Azure Backup**            | Uses **Recovery Services Vault** to store VM/File backups               |

### ✅ Real-World Examples:

* Provisioning **Blob, File, Table, Queue, or ADLS Gen2** storage? → You create a **Storage Account**.
* Want to store VM disks or snapshots? → Use **Managed Disks**.
* Need backup/DR services for VMs and databases? → Use **Recovery Services Vault** (under Azure Backup).
* Need low-cost long-term storage? → Use **Blob Storage** with **Archive** access tier.
