# EnterpriseDB PostgreSQL High Availability & Automatic Failover

## Overview

Designed and implemented a High Availability architecture using EnterpriseDB Postgres Advanced Server with PostgreSQL Streaming Replication, WAL-based replication, EDB Failover Manager (EFM), and a floating Virtual IP.

## Architecture

- EDB1 - Primary Database Server
- EDB2 - Standby Database Server
- VIP - Floating Database Endpoint
- Bitrix - Application Server

## Components

- EnterpriseDB Postgres Advanced Server
- PostgreSQL Streaming Replication
- Write-Ahead Logging (WAL)
- EDB Failover Manager (EFM)
- Virtual IP (VIP)
- pg_basebackup
- Linux
- Bash
- systemd
- firewalld

## High Availability

EDB1 operates as the Primary database server while EDB2 maintains a continuously synchronized Standby copy through PostgreSQL Streaming Replication.

WAL records database changes and is streamed from the Primary to the Standby over the network. EFM monitors the database cluster and supports failover by promoting the Standby when the Primary becomes unavailable.

The application connects through the floating VIP instead of relying on a fixed database server address.

## Failover Process

1. EDB1 operates as Primary.
2. EDB2 continuously receives WAL from EDB1.
3. EFM monitors the database nodes.
4. Primary failure is detected.
5. EDB2 is promoted to Primary.
6. The VIP is moved to the new Primary.
7. Applications reconnect through the same VIP.

## Testing

Failover and recovery scenarios were tested, including Primary failure simulation, Standby promotion, VIP migration, replication validation, and cluster-status verification.

## Troubleshooting

Troubleshooting included PostgreSQL configuration, replication, authentication, networking, firewall, systemd, EFM, and Linux service issues.

## Technologies

EnterpriseDB Postgres Advanced Server, PostgreSQL Streaming Replication, EDB Failover Manager, WAL, pg_basebackup, Linux, Bash, systemd, firewalld
