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


---

## Failover Process

1. EDB1 operates as Primary.
2. EDB2 continuously receives WAL from EDB1.
3. EFM monitors the database nodes.
4. Primary failure is detected.
5. EDB2 is promoted to Primary.
6. The VIP is moved to the new Primary.
7. Applications reconnect through the same VIP.

---

## Implementation Commands

### Check PostgreSQL Service

```bash
systemctl status edb-as-18
```

### Check PostgreSQL Version

```bash
/usr/edb/as18/bin/psql --version
```

### Check PostgreSQL Port

```bash
ss -lntp | grep 5444
```

### Check Replication Status

```bash
sudo -u enterprisedb /usr/edb/as18/bin/psql -p 5444 -d postgres -c "SELECT client_addr, state, sync_state FROM pg_stat_replication;"
```

### Check Standby Status

```bash
sudo -u enterprisedb /usr/edb/as18/bin/psql -p 5444 -d postgres -c "SELECT pg_is_in_recovery();"
```

### Create Standby Using pg_basebackup

```bash
pg_basebackup -h <PRIMARY_IP> -p 5444 -D <STANDBY_DATA_DIRECTORY> -U <REPLICATION_USER> -P -R
```

### Check EFM Cluster Status

```bash
efm cluster-status <CLUSTER_NAME>
```

### Check EFM Service

```bash
systemctl status efm-5.4
```

### Check VIP

```bash
ip addr show
```

### Test Database Connectivity

```bash
/usr/edb/as18/bin/psql -h <VIP> -p 5444 -U enterprisedb -d postgres
```

---

## Failover Testing

The HA environment was tested through controlled failure scenarios:

- Primary database failure simulation
- Standby promotion
- EFM cluster monitoring
- VIP migration
- PostgreSQL replication validation
- Application database reconnection
- Cluster status verification
- Recovery and re-synchronization testing

---

## Troubleshooting

Troubleshooting activities included:

- PostgreSQL configuration
- Streaming replication
- WAL replication
- Authentication and pg_hba.conf
- Network connectivity
- Firewall configuration
- PostgreSQL service management
- EFM configuration and monitoring
- Standby promotion
- VIP connectivity
- Linux system services

### Useful Commands

```bash
journalctl -u edb-as-18
```

```bash
journalctl -u efm-5.4
```

```bash
firewall-cmd --list-all
```

```bash
ss -lntp
```

```bash
ip addr
```

---

## Technologies

- EnterpriseDB Postgres Advanced Server
- PostgreSQL Streaming Replication
- Write-Ahead Logging (WAL)
- EDB Failover Manager (EFM)
- pg_basebackup
- Virtual IP
- Linux
- Bash
- systemd
- firewalld

---

## Key Skills Demonstrated

- PostgreSQL High Availability
- Database Streaming Replication
- Automatic Failover
- Standby Promotion
- WAL-based Replication
- EFM Cluster Management
- Linux Administration
- Network Troubleshooting
- Firewall Configuration
- Database Recovery
- Infrastructure Troubleshooting
- Application Database Connectivity
