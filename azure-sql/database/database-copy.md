---
title: Copy a database
description: Create a transactionally consistent copy of an existing database in Azure SQL Database on either the same server or a different server.
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: mathoma, randolphwest, hudequei
ms.date: 09/27/2024
ms.service: azure-sql-database
ms.subservice: data-movement
ms.topic: how-to
ms.custom:
  - sqldbrb=1
  - devx-track-azurepowershell
  - devx-track-azurecli
---
# Copy a transactionally consistent copy of a database in Azure SQL Database
[!INCLUDE [appliesto-sqldb](../includes/appliesto-sqldb.md)]

Azure SQL Database provides several methods for creating a copy of an existing [database](single-database-overview.md) on either the same server or a different server. You can copy a database by using Azure portal, PowerShell, Azure CLI, or Transact-SQL.

[!INCLUDE [entra-id](../includes/entra-id.md)]

## Overview

A database copy is a transactionally consistent snapshot of the source database at the point in time when the copy request is initiated. You can select the same server or a different server for the copy. Also you can choose to keep the backup redundancy and compute size of the source database, or use a different backup storage redundancy and/or compute size within the same service tier. It's also possible to copy a database in the Standard service tier to either the Standard or General Purpose tier and a database in the Premium service tier to either the Premium or Business Critical tier. 

After the copy is complete, the new database is a fully functional and independent database to the source database. The logins, users, and permissions in the copied database are managed independently from the source database. The copy is created using geo-replication technology. Once replica seeding is complete, the geo-replication link is automatically terminated. All the requirements for using geo-replication apply to the database copy operation. See [Active geo-replication overview](active-geo-replication-overview.md) for details.

> [!NOTE]  
> The [Azure portal](https://portal.azure.com), PowerShell, and the Azure CLI don't support database copy to a different subscription.

## Database copy for Hyperscale databases

For databases in the [Hyperscale service tier](service-tier-hyperscale.md), the target database determines whether the copy is a fast copy, or a size-of-data copy: 

- **Fast copy**: When the copy is done in the same region as the source, the copy is created from the snapshots of blobs, this copy is a fast operation regardless of the database size.

- **Size-of-data copy**: When the target database is in a different region than the source or if the database backup storage redundancy (Local, Zonal, Geo) from the target differs from the source database, the copy operation is a size-of-data operation. Copy time isn't directly proportional to size, as page server blobs are copied in parallel.

## Logins in the database copy

When you copy a database to the same server, the same logins can be used on both databases. The security principal you use to copy the database becomes the database owner on the new database.

When you copy a database to a different server, the security principal that initiated the copy operation on the target server becomes the owner of the new database.

Regardless of the target server, all database users, permissions, and security identifiers (SIDs) are copied to the database copy. Using [contained database users](logins-create-manage.md) for data access ensures that the copied database has the same user credentials, so that after the copy is complete you can immediately access it with the same credentials.

If you use server level logins for data access and copy the database to a different server, the login-based access might not work. This can happen because the logins don't exist on the target server, or because those passwords and security identifiers (SIDs) are different. For more information about managing logins when you copy a database to a different server, see [How to manage Azure SQL Database security after disaster recovery](active-geo-replication-security-configure.md). After the copy operation to a different server succeeds, and before other users are remapped, only the login associated with the database owner, or the server administrator can log in to the copied database. To resolve logins and establish data access after the copying operation is complete, see [Resolve logins](#resolve-logins).

## Copy using the Azure portal

To copy a database by using the Azure portal, open the page for your database, and then choose **Copy** to open the **Create SQL Database - Copy database** page. Fill in the values for the target server where you want to copy your database to.

:::image type="content" source="media/database-copy/database-copy.png" alt-text="Screenshot of Azure portal, showing Database copy option highlighted on the database overview page." lightbox="media/database-copy/database-copy.png":::

## Copy a database 

You can copy a database by using PowerShell, the Azure CLI, and Transact-SQL (T-SQL). 

### [PowerShell](#tab/azure-powershell)

For PowerShell, use the [New-AzSqlDatabaseCopy](/powershell/module/az.sql/new-azsqldatabasecopy) cmdlet.

> [!IMPORTANT]  
> The PowerShell Azure Resource Manager (RM) module is still supported by Azure SQL Database, but all future development is for the Az.Sql module. The AzureRM module will continue to receive bug fixes until at least December 2020. The arguments for the commands in the Az module and in the AzureRm modules are substantially identical. For more about their compatibility, see [Introducing the new Azure PowerShell Az module](/powershell/azure/new-azureps-module-az).

```powershell
New-AzSqlDatabaseCopy -ResourceGroupName "<resourceGroup>" -ServerName $sourceserver -DatabaseName "<databaseName>" `
    -CopyResourceGroupName "myResourceGroup" -CopyServerName $targetserver -CopyDatabaseName "CopyOfMySampleDatabase"
```

The database copy is an asynchronous operation but the target database is created immediately after the request is accepted. If you need to cancel the copy operation while still in progress, drop the target database using the [Remove-AzSqlDatabase](/powershell/module/az.sql/remove-azsqldatabase) cmdlet.

For a complete sample PowerShell script, see [Copy a database to a new server](scripts/copy-database-to-new-server-powershell.md).

### [Azure CLI](#tab/azure-cli)

```azurecli
az sql db copy --dest-name "CopyOfMySampleDatabase" --dest-resource-group "myResourceGroup" --dest-server $targetserver `
    --name "<databaseName>" --resource-group "<resourceGroup>" --server $sourceserver
```

The database copy is an asynchronous operation but the target database is created immediately after the request is accepted. If you need to cancel the copy operation while still in progress, drop the target database using the [az sql db delete](/cli/azure/sql/db#az-sql-db-delete) command.

### [Transact-SQL](#tab/tsql)

Log in to the `master` database with the server administrator login or the login that created the database you want to copy. For database copy to succeed, logins that aren't the server administrator must be members of the **dbmanager** role. For more information about logins and connecting to the server, see [Manage logins](logins-create-manage.md).

Start copying the source database with the [CREATE DATABASE ... AS COPY OF](/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current&preserve-view=true#copy-a-database) statement. The T-SQL statement continues running until the database copy operation is complete.

> [!NOTE]  
> Terminating the T-SQL statement doesn't terminate the database copy operation. To terminate the operation, drop the target database.

### Copy to the same server

Log in to the `master` database with the server administrator login or the login that created the database you want to copy. For database copying to succeed, logins that aren't the server administrator must be members of the **dbmanager** role.

This command copies `Database1` to a new database named `Database2` on the same server. Depending on the size of your database, the copying operation might take some time to complete.

```sql
-- Execute on the master database to start copying
CREATE DATABASE Database2 AS COPY OF Database1;
```

### Copy to an elastic pool

Log in to the `master` database with the server administrator login or the login that created the database you want to copy. For database copying to succeed, logins that aren't the server administrator must be members of the **dbmanager** role.

This command copies `Database1` to a new database named `Database2` in an elastic pool named pool1. Depending on the size of your database, the copying operation might take some time to complete.

`Database1` can be a single or pooled database. Copying between different tier pools is supported, but some cross-tier copies fail. For example, you can copy a single or elastic standard db into a General Purpose pool, but you can't copy a standard elastic db into a premium pool.

```sql
-- Execute on the master database to start copying
CREATE DATABASE Database2
AS COPY OF Database1
(SERVICE_OBJECTIVE = ELASTIC_POOL( name = pool1 ));
```

### Copy to a different server

Connect to the `master` database of the target server where the new database is to be created. Use a login that has the same name and password as the database owner of the source database on the source server. The login on the target server must also be a member of the **dbmanager** role, or be the server administrator login.

This command copies `Database1` on server1 to a new database named `Database2` on server2. Depending on the size of your database, the copying operation might take some time to complete.

```sql
-- Execute on the master database of the target server (server2) to start copying from Server1 to Server2
CREATE DATABASE Database2 AS COPY OF server1.Database1;
```

> [!IMPORTANT]  
> Both servers' firewalls must be configured to allow inbound connection from the IP of the client issuing the T-SQL CREATE DATABASE ... AS COPY OF command. To determine the source IP address of current connection, execute `SELECT client_net_address FROM `sys.dm_exec_connections` WHERE session_id = @@SPID;`

> [!NOTE]  
> Database copy using T-SQL isn't supported when connecting to the destination server over a [private endpoint](private-endpoint-overview.md). If a private endpoint is configured but public network access is allowed, database copy is supported when connected to the destination server from a public IP address using SQL authentication. Once the copy operation completes, public access can be [denied](connectivity-settings.md#deny-public-network-access).

Similarly, the below command copies `Database1` on server1 to a new database named `Database2` within an elastic pool called pool2, on server2.

```sql
-- Execute on the master database of the target server (server2) to start copying from Server1 to Server2
CREATE DATABASE Database2 AS COPY OF server1.Database1 (SERVICE_OBJECTIVE = ELASTIC_POOL( name = pool2 ) );
```

### Copy to a different subscription

You can use the steps in the [Copy a SQL Database to a different server](#copy-to-a-different-server) section to copy your database to a server in a different subscription using T-SQL. Make sure you use a login that has the same name and password as the database owner of the source database. Additionally, the login must be a member of the **dbmanager** role or a server administrator, on both source and target servers.

> [!TIP]  
> When copying databases in the same Microsoft Entra ID tenant, authorization on the source and destination servers is simplified if you initiate the copy command using an authentication login with sufficient access on both servers. The minimum necessary level of access is membership in the **dbmanager** role in the `master` database on both servers. For example, you can use a Microsoft Entra ID login that is a member of a group designated as the server administrator on both servers.

```sql
--Step# 1
--Create login and user in the master database of the source server.

CREATE LOGIN loginname WITH PASSWORD = 'xxxxxxxxx'
GO
CREATE USER [loginname] FOR LOGIN [loginname] WITH DEFAULT_SCHEMA=[dbo];
GO
ALTER ROLE dbmanager ADD MEMBER loginname;
GO

--Step# 2
--Create the user in the source database and grant dbowner permission to the database.

CREATE USER [loginname] FOR LOGIN [loginname] WITH DEFAULT_SCHEMA=[dbo];
GO
ALTER ROLE db_owner ADD MEMBER loginname;
GO

--Step# 3
--Capture the SID of the user "loginname" from master database

SELECT [sid] FROM sysusers WHERE [name] = 'loginname';

--Step# 4
--Connect to Destination server.
--Create login and user in the master database, same as of the source server.

CREATE LOGIN loginname WITH PASSWORD = 'xxxxxxxxx', SID = [SID of loginname login on source server];
GO
CREATE USER [loginname] FOR LOGIN [loginname] WITH DEFAULT_SCHEMA=[dbo];
GO
ALTER ROLE dbmanager ADD MEMBER loginname;
GO

--Step# 5
--Execute the copy of database script from the destination server using the credentials created

CREATE DATABASE new_database_name
AS COPY OF source_server_name.source_database_name;
```

> [!TIP]  
> Database copy using T-SQL supports copying a database from a subscription in a different Azure tenant. This is only supported when using a SQL authentication login to log in to the target server.
> Creating a database copy on a logical server in a different Azure tenant is not supported when [Microsoft Entra](https://techcommunity.microsoft.com/t5/azure-sql-blog/support-for-azure-ad-user-creation-on-behalf-of-azure-ad/ba-p/2346849) authentication is active (enabled) on either source or target logical server.

---


## Monitor progress of the copy operation

Monitor the copying process by querying the [sys.databases](/sql/relational-databases/system-catalog-views/sys-databases-transact-sql), [sys.dm_database_copies](/sql/relational-databases/system-dynamic-management-views/sys-dm-database-copies-azure-sql-database), and [sys.dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) views. While the copying is in progress, the `state_desc` column of the `sys.databases` view for the new database is set to `COPYING`.

- If the copying fails, the `state_desc` column of the `sys.databases` view for the new database is set to `SUSPECT`. Execute the DROP statement on the new database, and try again later.
- If the copying succeeds, the `state_desc` column of the `sys.databases` view for the new database is set to `ONLINE`. The copying is complete, and the new database is a regular database that can be changed independent of the source database.

> [!NOTE]  
> If you decide to cancel the copying while it's in progress, execute the [DROP DATABASE](/sql/t-sql/statements/drop-database-transact-sql) statement on the new database.

> [!IMPORTANT]  
> If you need to create a copy with a substantially smaller service objective than the source, the target database might not have sufficient resources to complete the seeding process and it can cause the copy operation to fail. In this scenario use a geo-restore request to create a copy in a different server and/or a different region. For more information, see [Recover an Azure SQL Database using database backups](recovery-using-backups.md#geo-restore).

## Permissions

To create a database copy, you need to be in the following roles:

- Subscription Owner or
- SQL Server Contributor role or
- Custom role on the source server with following permissions:
  - Microsoft.Sql/servers/databases/read
  - Microsoft.Sql/servers/databases/write and
- Custom role on the target server with following permissions:
  - Microsoft.Sql/servers/read
  - Microsoft.Sql/servers/databases/read
  - Microsoft.Sql/servers/databases/write

To cancel a database copy, you need to be in the following roles:

- Subscription Owner or
- SQL Server Contributor role or
- Custom role on the target database with following permission:
  - Microsoft.Sql/servers/databases/delete

To manage database copy using the Azure portal, you also need the following permissions:

- Microsoft.Resources/subscriptions/resources/read
- Microsoft.Resources/deployments/read
- Microsoft.Resources/deployments/write
- Microsoft.Resources/deployments/operationstatuses/read

If you want to see the operations under deployments in the resource group on the portal, operations across multiple resource providers including SQL operations, you need these additional permissions:

- Microsoft.Resources/subscriptions/resourcegroups/deployments/operations/read
- Microsoft.Resources/subscriptions/resourcegroups/deployments/operationstatuses/read

## Resolve logins

After the new database is online on the target server, use the [ALTER USER](/sql/t-sql/statements/alter-user-transact-sql?view=azuresqldb-current&preserve-view=true) statement to remap the users from the new database to logins on the target server. To resolve orphaned users, see [Troubleshoot Orphaned Users](/sql/sql-server/failover-clusters/troubleshoot-orphaned-users-sql-server). See also [How to manage Azure SQL Database security after disaster recovery](active-geo-replication-security-configure.md).

All users in the new database retain the permissions that they had in the source database. The user who initiated the database copy becomes the database owner of the new database. After the copying succeeds and before other users are remapped, only the database owner can sign in to the new database.

To learn about managing users and logins when you copy a database to a different server, see [How to manage Azure SQL Database security after disaster recovery](active-geo-replication-security-configure.md).

## Database copy errors

The following errors can be encountered while copying a database in Azure SQL Database. For more information, see [Copy an Azure SQL Database](database-copy.md).

| Error code | Severity | Description |
| ---: | ---: | :--- |
| 40635 | 16 | Client with IP address '%.&#x2a;ls' is temporarily disabled. |
| 40637 | 16 | Create database copy is currently disabled. |
| 40561 | 16 | Database copy failed. Either the source or target database does not exist. |
| 40562 | 16 | Database copy failed. The source database has been dropped. |
| 40563 | 16 | Database copy failed. The target database has been dropped. |
| 40564 | 16 | Database copy failed due to an internal error. Please drop target database and try again. |
| 40565 | 16 | Database copy failed. No more than 1 concurrent database copy from the same source is allowed. Please drop target database and try again later. |
| 40566 | 16 | Database copy failed due to an internal error. Please drop target database and try again. |
| 40567 | 16 | Database copy failed due to an internal error. Please drop target database and try again. |
| 40568 | 16 | Database copy failed. Source database has become unavailable. Please drop target database and try again. |
| 40569 | 16 | Database copy failed. Target database has become unavailable. Please drop target database and try again. |
| 40570 | 16 | Database copy failed due to an internal error. Please drop target database and try again later. |
| 40571 | 16 | Database copy failed due to an internal error. Please drop target database and try again later. |

## Related content

- [Authorize database access to SQL Database, SQL Managed Instance, and Azure Synapse Analytics](logins-create-manage.md)
- [Configure and manage Azure SQL Database security for geo-restore or failover](active-geo-replication-security-configure.md)
- [Export to a BACPAC file - Azure SQL Database and Azure SQL Managed Instance](database-export.md)
