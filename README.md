# EnterpriseDB PostgreSQL High Availability & Automatic Failover

## Overview

Designed and implemented a High Availability architecture using **EnterpriseDB Postgres Advanced Server**, **PostgreSQL Streaming Replication**, **WAL-based replication**, **EDB Failover Manager (EFM)**, and a **floating Virtual IP**.

The solution provides database redundancy, automatic failover, standby promotion, and application connectivity through a stable database endpoint.

---

## Architecture

```text
                         +----------------------+
                         |      Bitrix App      |
                         |  Application Server  |
                         +----------+-----------+
                                    |
                                    | Database Connection
                                    v
                         +----------------------+
                         |     Floating VIP     |
                         |     DB Endpoint      |
                         +----------+-----------+
                                    |
                         +----------v-----------+
                         |  EDB PostgreSQL HA   |
                         |       Cluster        |
                         +----------+-----------+
                                    |
                   +----------------+----------------+
                   |                                 |
                PRIMARY                           STANDBY
                   |                                 |
            +------v------+                   +------v------+
            |    EDB1     |                   |    EDB2     |
            |   PRIMARY   |                   |   STANDBY   |
            |             |                   |             |
            | EDB Postgres|                   | EDB Postgres|
            |  Advanced   |                   |  Advanced   |
            |   Server    |                   |   Server    |
            +------+------+
                   |
                   |        WAL Streaming
                   +-------------------------------->

                   EFM Cluster Monitoring
                 Failover / Standby Promotion
``` 

---

## Components

| Component | Role |
|---|---|
| **EDB1** | Primary Database Server |
| **EDB2** | Standby Database Server |
| **VIP** | Floating Database Endpoint |
| **Bitrix** | Application Server |
| **EFM** | Cluster Monitoring & Failover Management |

---

## High Availability

EDB1 operates as the **Primary** database server while EDB2 maintains a continuously synchronized **Standby** copy through PostgreSQL Streaming Replication.

**WAL (Write-Ahead Logging)** records database changes on the Primary and is continuously streamed to the Standby over the network.

**EFM (EDB Failover Manager)** monitors the database cluster and supports failover by promoting the Standby when the Primary becomes unavailable.

The Bitrix application connects through the **floating VIP** instead of depending on a fixed database server address.

---

## Replication Flow

```text
Database Changes
       |
       v
     EDB1
   PRIMARY
       |
       | WAL
       v
PostgreSQL Streaming Replication
       |
       v
     EDB2
   STANDBY
```

