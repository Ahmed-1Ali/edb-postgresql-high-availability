(

echo # EnterpriseDB PostgreSQL High Availability \& Automatic Failover

echo.

echo ## Overview

echo.

echo Designed and implemented a High Availability architecture using EnterpriseDB Postgres Advanced Server with PostgreSQL Streaming Replication, WAL-based replication, EDB Failover Manager ^(EFM^), and a floating Virtual IP.

echo.

echo ## Architecture

echo.

echo - EDB1 - Primary Database Server - 192.168.1.168

echo - EDB2 - Standby Database Server - 192.168.1.166

echo - VIP - Floating Database Endpoint - 192.168.1.170

echo - Bitrix Application Server - 192.168.1.167

echo.

echo ## Components

echo.

echo - EnterpriseDB Postgres Advanced Server

echo - PostgreSQL Streaming Replication

echo - Write-Ahead Logging ^(WAL^)

echo - EDB Failover Manager ^(EFM^)

echo - Virtual IP ^(VIP^)

echo - pg\_basebackup

echo - Linux

echo - Bash

echo - systemd

echo - firewalld

echo.

echo ## High Availability

echo.

echo EDB1 operates as the Primary database server while EDB2 maintains a continuously synchronized Standby copy through PostgreSQL Streaming Replication.

echo.

echo WAL records database changes and is streamed from the Primary to the Standby over the network. EFM monitors the database cluster and supports automatic failover by promoting the Standby when the Primary becomes unavailable.

echo.

echo The application connects through the floating VIP instead of relying on a fixed database server address.

echo.

echo ## Failover Process

echo.

echo 1. EDB1 operates as Primary.

echo 2. EDB2 continuously receives WAL from EDB1.

echo 3. EFM monitors the database nodes.

echo 4. EDB1 failure is detected.

echo 5. EDB2 is promoted to Primary.

echo 6. The VIP is moved to the new Primary.

echo 7. Applications reconnect through the same VIP.

echo.

echo ## Testing

echo.

echo Failover and recovery scenarios were tested, including Primary failure simulation, Standby promotion, VIP migration, replication validation, and cluster status verification.

echo.

echo ## Troubleshooting

echo.

echo Troubleshooting included PostgreSQL configuration, replication, authentication, networking, firewall, systemd, EFM, and Linux service issues.

echo.

echo ## Technologies

echo.

echo EnterpriseDB Postgres Advanced Server, PostgreSQL Streaming Replication, EDB Failover Manager, WAL, pg\_basebackup, Linux, Bash, systemd, firewalld

) > README.md

