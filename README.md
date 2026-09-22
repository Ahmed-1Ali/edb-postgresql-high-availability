EnterpriseDB PostgreSQL High Availability \& Automatic Failover

Overview



Designed and implemented a High Availability architecture using EnterpriseDB Postgres Advanced Server with PostgreSQL Streaming Replication, WAL-based replication, EDB Failover Manager (EFM), and a floating Virtual IP.



The solution provides database redundancy, automatic failover, standby promotion, and application connectivity through a stable database endpoint.



Architecture

&#x20;                        ┌──────────────────────┐

&#x20;                        │      Bitrix App       │

&#x20;                        │   Application Server  │

&#x20;                        └──────────┬───────────┘

&#x20;                                   │

&#x20;                                   │ Database Connection

&#x20;                                   ▼

&#x20;                        ┌──────────────────────┐

&#x20;                        │     Floating VIP     │

&#x20;                        │      DB Endpoint     │

&#x20;                        └──────────┬───────────┘

&#x20;                                   │

&#x20;                        ┌──────────▼───────────┐

&#x20;                        │                      │

&#x20;                        │   EDB PostgreSQL HA  │

&#x20;                        │      Cluster         │

&#x20;                        │                      │

&#x20;                        └───────┬───────┬──────┘

&#x20;                                │       │

&#x20;                        Primary │       │ Standby

&#x20;                                │       │

&#x20;                   ┌────────────▼──┐ ┌──▼─────────────┐

&#x20;                   │     EDB1      │ │      EDB2      │

&#x20;                   │    PRIMARY    │ │    STANDBY     │

&#x20;                   │               │ │                │

&#x20;                   │ PostgreSQL    │ │ PostgreSQL     │

&#x20;                   │ EDB Advanced  │ │ EDB Advanced   │

&#x20;                   │ Server        │ │ Server         │

&#x20;                   └───────┬───────┘ └───────▲────────┘

&#x20;                           │                  │

&#x20;                           │   WAL Streaming  │

&#x20;                           └──────────────────┘



&#x20;                        EFM Cluster Monitoring

&#x20;                   Failover / Standby Promotion

Components

EDB1 - Primary Database Server

EDB2 - Standby Database Server

VIP - Floating Database Endpoint

Bitrix - Application Server

EDB Failover Manager (EFM)

High Availability



EDB1 operates as the Primary database server while EDB2 maintains a continuously synchronized Standby copy through PostgreSQL Streaming Replication.



WAL records database changes on the Primary and is continuously streamed to the Standby over the network.



EFM monitors the database cluster and supports failover by promoting the Standby when the Primary becomes unavailable.



The Bitrix application connects through the floating VIP instead of depending on a fixed database server address.



Replication Flow

Database Changes

&#x20;      │

&#x20;      ▼

&#x20;    EDB1

&#x20;  PRIMARY

&#x20;      │

&#x20;      │ WAL

&#x20;      ▼

PostgreSQL Streaming Replication

&#x20;      │

&#x20;      ▼

&#x20;    EDB2

&#x20;  STANDBY

Failover Process

EDB1 operates as Primary.

EDB2 continuously receives WAL from EDB1.

EFM monitors the database nodes.

Primary failure is detected.

EDB2 is promoted to Primary.

The VIP is moved to the new Primary.

Applications reconnect through the same VIP.

Implementation Commands

Check PostgreSQL Service

systemctl status edb-as-18

Check PostgreSQL Version

/usr/edb/as18/bin/psql --version

Check PostgreSQL Port

ss -lntp | grep 5444

Check Replication Status

sudo -u enterprisedb /usr/edb/as18/bin/psql \\

\-p 5444 -d postgres \\

\-c "SELECT client\_addr, state, sync\_state FROM pg\_stat\_replication;"

Check Standby Status

sudo -u enterprisedb /usr/edb/as18/bin/psql \\

\-p 5444 -d postgres \\

\-c "SELECT pg\_is\_in\_recovery();"

Create Standby Using pg\_basebackup

pg\_basebackup \\

\-h <PRIMARY\_IP> \\

\-p 5444 \\

\-D <STANDBY\_DATA\_DIRECTORY> \\

\-U <REPLICATION\_USER> \\

\-P -R

Check EFM Cluster Status

efm cluster-status <CLUSTER\_NAME>

Check EFM Service

systemctl status efm-5.4

Check VIP

ip addr show

Test Database Connectivity

/usr/edb/as18/bin/psql \\

\-h <VIP> \\

\-p 5444 \\

\-U enterprisedb \\

\-d postgres

Failover Testing



The HA environment was tested through controlled failure scenarios:



Primary database failure simulation

Standby promotion

EFM cluster monitoring

VIP migration

PostgreSQL replication validation

Application database reconnection

Cluster status verification

Recovery and re-synchronization testing

Troubleshooting



Troubleshooting activities included:



PostgreSQL configuration

Streaming replication

WAL replication

Authentication and pg\_hba.conf

Network connectivity

Firewall configuration

PostgreSQL service management

EFM configuration and monitoring

Standby promotion

VIP connectivity

Linux system services



Useful commands:



journalctl -u edb-as-18

journalctl -u efm-5.4

firewall-cmd --list-all

ss -lntp

ip addr

Technologies

EnterpriseDB Postgres Advanced Server

PostgreSQL Streaming Replication

Write-Ahead Logging (WAL)

EDB Failover Manager (EFM)

pg\_basebackup

Virtual IP

Linux

Bash

systemd

firewalld

Key Skills Demonstrated

PostgreSQL High Availability

Database Streaming Replication

Automatic Failover

Standby Promotion

WAL-based Replication

EFM Cluster Management

Linux Administration

Network Troubleshooting

Firewall Configuration

Database Recovery

Infrastructure Troubleshooting

Application Database Connectivity

