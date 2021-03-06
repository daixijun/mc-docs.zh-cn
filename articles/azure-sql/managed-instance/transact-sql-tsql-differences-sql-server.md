---
title: SQL Server 与 Azure SQL 托管实例之间的 T-SQL 差异
description: 本文讨论 Azure SQL 托管实例与 SQL Server 之间的 Transact-SQL (T-SQL) 差异。
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.devlang: ''
ms.topic: reference
author: WenJason
ms.author: v-jay
ms.reviewer: sstein, bonova, danil
origin.date: 11/10/2020
ms.date: 01/25/2021
ms.custom: seoapril2019, sqldbrb=1
ms.openlocfilehash: 28f114acb29366b1742219ed074c6a57f754ed7b
ms.sourcegitcommit: e1edc6ef84dbbda1da4e0a42efa3fd62eee033d1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/18/2021
ms.locfileid: "98541854"
---
# <a name="t-sql-differences-between-sql-server--azure-sql-managed-instance"></a>SQL Server 与 Azure SQL 托管实例之间的 T-SQL 差异
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

本文汇总并解释了 Azure SQL 托管实例与 SQL Server 之间的语法和行为差异。 


SQL 托管实例可与 SQL Server 数据库引擎高度兼容，且 SQL 托管实例支持大多数功能。

![从 SQL Server 轻松迁移](./media/transact-sql-tsql-differences-sql-server/migration.png)

与 SQL Server 相比，SQL 托管实例中引入了一些 PaaS 限制，并且在行为方面有一些变化。 这些差异划分为以下几个类别：<a name="Differences"></a>

- [可用性](#availability)包括 [Always-On 可用性组](#always-on-availability-groups)和[备份](#backup)方面的差异。
- [安全性](#security)包括[审核](#auditing)、[证书](#certificates)、[凭据](#credential)、[加密提供程序](#cryptographic-providers)、[登录名和用户](#logins-and-users)以及[服务密钥和服务主密钥](#service-key-and-service-master-key)方面的差异。
- [配置](#configuration)包括[缓冲池扩展](#buffer-pool-extension)、[排序规则](#collation)、[兼容性级别](#compatibility-levels)、[数据库镜像](#database-mirroring)、[数据库选项](#database-options)、[SQL Server 代理](#sql-server-agent)以及[表选项](#tables)方面的差异。
- [功能](#functionalities)包括 [BULK INSERT/OPENROWSET](#bulk-insert--openrowset)、[CLR](#clr)、[DBCC](#dbcc)、[分布式事务](#distributed-transactions)、[已扩展事件](#extended-events)、[文件流和文件表](#filestream-and-filetable)、[全文语义搜索](#full-text-semantic-search)、[链接服务器](#linked-servers)、[Polybase](#polybase)、[复制](#replication)、[还原](#restore-statement)、[Service Broker](#service-broker)、[存储过程、函数和触发器](#stored-procedures-functions-and-triggers)。
- [环境设置](#Environment)，例如 VNet 和子网配置。

其中的大多数功能都是体系结构约束，代表服务功能。

在 SQL 托管实例中发现并将在将来解决的临时已知问题在[“发行说明”页](../database/doc-changes-updates-release-notes.md)中进行了说明。

## <a name="availability"></a>可用性

### <a name="always-on-availability-groups"></a><a name="always-on-availability-groups"></a>Always On 可用性组

[高可用性](../database/high-availability-sla.md)内置在 SQL 托管实例中，用户无法控制。 不支持以下语句：

- [CREATE ENDPOINT … FOR DATABASE_MIRRORING](https://docs.microsoft.com/sql/t-sql/statements/create-endpoint-transact-sql)
- [CREATE AVAILABILITY GROUP](https://docs.microsoft.com/sql/t-sql/statements/create-availability-group-transact-sql)
- [ALTER AVAILABILITY GROUP](https://docs.microsoft.com/sql/t-sql/statements/alter-availability-group-transact-sql)
- [DROP AVAILABILITY GROUP](https://docs.microsoft.com/sql/t-sql/statements/drop-availability-group-transact-sql)
- [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql) 语句的 [SET HADR](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-hadr) 子句

### <a name="backup"></a>Backup

SQL 托管实例包含自动备份，因此用户可以创建完整数据库 `COPY_ONLY` 备份。 不支持差异、日志和文件快照备份。

- 使用 SQL 托管实例，可以只将实例数据库备份到 Azure Blob 存储帐户：
  - 仅支持 `BACKUP TO URL`。
  - 不支持 `FILE`、`TAPE` 和备份设备。
- 支持大多数常规 `WITH` 选项。
  - `COPY_ONLY` 是必需的。
  - 不支持 `FILE_SNAPSHOT`。
  - 不支持磁带选项 `REWIND`、`NOREWIND`、`UNLOAD` 和 `NOUNLOAD`。
  - 不支持日志特定的选项 `NORECOVERY`、`STANDBY` 和 `NO_TRUNCATE`。

的限制： 

- 使用 SQL 托管实例可将实例数据库备份到最多包含 32 个条带的备份，如果使用备份压缩，则这种方法对于不超过 4 TB 的数据库而言已足够。
- 不能在使用服务托管透明数据加密 (TDE) 加密的数据库上执行 `BACKUP DATABASE ... WITH COPY_ONLY`。 服务托管的 TDE 强制使用内部 TDE 密钥对备份进行加密。 无法导出该密钥，因此无法还原备份。 使用自动备份和时间点还原，或者改用[客户管理的 (BYOK) TDE](../database/transparent-data-encryption-tde-overview.md#customer-managed-transparent-data-encryption---bring-your-own-key)。 也可以在数据库上禁用加密。
- 无法将托管实例上进行的本机备份恢复到 SQL Server。 这是因为与任何版本的 SQL Server 相比，托管实例具有更高的内部数据库版本。
- 在 SQL 托管实例中使用 `BACKUP` 命令最大可以设置 195 GB 的备份条带大小（即最大 Blob 大小）。 增加备份命令中的带状线数量以缩小单个带状线大小，将其保持在限制范围内。

    > [!TIP]
    > 从本地环境或虚拟机中的 SQL Server 备份数据库时，若要解决此限制，可以：
    >
    > - 备份到 `DISK` 而不是 `URL`。
    > - 将备份文件上传到 Blob 存储。
    > - 还原到 SQL 托管实例。
    >
    > SQL 托管实例中的 `Restore` 命令支持备份文件中的更大 Blob 大小，因为将使用不同的 Blob 类型来存储上传的备份文件。

有关使用 T-SQL 进行备份的信息，请参阅 [BACKUP](https://docs.microsoft.com/sql/t-sql/statements/backup-transact-sql)。

## <a name="security"></a>安全性

### <a name="auditing"></a>审核

在审核方面，Azure SQL 和 SQL Server 的主要差异是：

- 在 SQL 托管实例中，审核在服务器级别执行。 在 Azure Blob 存储中存储 `.xel` 日志文件。
- 在 Azure SQL 数据库中，审核在数据库级别执行。 在 Azure Blob 存储中存储 `.xel` 日志文件。
- 在本地 SQL Server 或虚拟机中，审核在服务器级别执行。 在文件系统或 Windows 事件日志中存储事件。
 
SQL 托管实例中的 XEvent 审核支持 Azure Blob 存储目标。 不支持文件和 Windows 日志。

Azure Blob 存储审核的主要 `CREATE AUDIT` 语法差异为：

- 提供了新语法 `TO URL`，用于指定 `.xel` 文件要放到的 Azure Blob 存储容器的 URL。
- 不支持语法 `TO FILE`，因为 SQL 托管实例无法访问 Windows 文件共享。

有关详细信息，请参阅： 

- [CREATE SERVER AUDIT](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-transact-sql) 
- [ALTER SERVER AUDIT](https://docs.microsoft.com/sql/t-sql/statements/alter-server-audit-transact-sql)
- [审核](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine)

### <a name="certificates"></a>证书

由于 SQL 托管实例无法访问文件共享和 Windows 文件夹，因此存在以下约束：

- 不支持将 `CREATE FROM`/`BACKUP TO` 文件用于证书。
- 不支持 `FILE`/`ASSEMBLY` 中的 `CREATE`/`BACKUP` 证书。 无法使用私钥文件。 

请参阅 [CREATE CERTIFICATE](https://docs.microsoft.com/sql/t-sql/statements/create-certificate-transact-sql) 和 [BACKUP CERTIFICATE](https://docs.microsoft.com/sql/t-sql/statements/backup-certificate-transact-sql)。 
 
**解决方法**：请勿在创建证书备份后再还原该备份，而应 [先获取证书二进制文件内容和私钥，将其存储为 .sql 文件，然后从二进制文件创建证书](https://docs.microsoft.com/sql/t-sql/functions/certencoded-transact-sql#b-copying-a-certificate-to-another-database)：

```sql
CREATE CERTIFICATE  
   FROM BINARY = asn_encoded_certificate
WITH PRIVATE KEY (<private_key_options>)
```

### <a name="credential"></a>凭据

仅支持 Azure Key Vault 和 `SHARED ACCESS SIGNATURE` 标识。 不支持 Windows 用户。

请参阅 [CREATE CREDENTIAL](https://docs.microsoft.com/sql/t-sql/statements/create-credential-transact-sql) 和 [ALTER CREDENTIAL](https://docs.microsoft.com/sql/t-sql/statements/alter-credential-transact-sql)。

### <a name="cryptographic-providers"></a>加密提供程序

由于 SQL 托管实例无法访问文件，因此无法创建加密提供程序：

- 不支持 `CREATE CRYPTOGRAPHIC PROVIDER`。 请参阅 [CREATE CRYPTOGRAPHIC PROVIDER](https://docs.microsoft.com/sql/t-sql/statements/create-cryptographic-provider-transact-sql)。
- 不支持 `ALTER CRYPTOGRAPHIC PROVIDER`。 请参阅 [ALTER CRYPTOGRAPHIC PROVIDER](https://docs.microsoft.com/sql/t-sql/statements/alter-cryptographic-provider-transact-sql)。

### <a name="logins-and-users"></a>登录名和用户

- 支持使用 `FROM CERTIFICATE`、`FROM ASYMMETRIC KEY` 和 `FROM SID` 创建的 SQL 登录名。 请参阅 [CREATE LOGIN](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql)。
- 支持使用 [CREATE LOGIN](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current) 语法或 [CREATE USER FROM LOGIN [Azure AD 登录名]](https://docs.microsoft.com/sql/t-sql/statements/create-user-transact-sql?view=azuresqldb-mi-current) 语法创建的 Azure Active Directory (Azure AD) 服务器主体（登录名）。 这些登录名是在服务器级别创建的。

    SQL 托管实例支持使用语法 `CREATE USER [AADUser/AAD group] FROM EXTERNAL PROVIDER` 的 Azure AD 数据库主体。 此功能也称为 Azure AD 包含的数据库用户。

- 不支持使用 `CREATE LOGIN ... FROM WINDOWS` 语法创建的 Windows 登录名。 使用 Azure Active Directory 登录名和用户。
- 创建实例的 Azure AD 用户具有[不受限制的管理特权](../database/logins-create-manage.md)。
- 可以使用 `CREATE USER ... FROM EXTERNAL PROVIDER` 语法创建非管理员 Azure AD 数据库级用户。 请参阅 [CREATE USER ...FROM EXTERNAL PROVIDER](../database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities)。
- Azure AD 服务器主体（登录名）仅支持一个 SQL 托管实例中的 SQL 功能。 无论是在相同还是不同的 Azure AD 租户中，需要跨实例交互的功能都不支持 Azure AD 用户。 此类功能的示例包括：

  - SQL 事务复制。
  - 链接服务器。

- 不支持设置映射到作为数据库所有者的 Azure AD 组的 Azure AD 登录名。
- 支持使用其他 Azure AD 主体模拟 Azure AD 服务器级主体，例如 [EXECUTE AS](https://docs.microsoft.com/sql/t-sql/statements/execute-as-transact-sql) 子句。 EXECUTE AS 限制包括：

  - 当名称不同于登录名时，EXECUTE AS USER 不支持 Azure AD 用户。 例如，如果用户是通过语法 CREATE USER [myAadUser] FROM LOGIN [john@contoso.com] 创建的，则会尝试通过 EXEC AS USER = _myAadUser_ 进行模拟。 基于 Azure AD 服务器主体（登录名）创建 **USER** 时，请指定与 **LOGIN** 中的 login_name 相同的 user_name。
  - 只有属于 `sysadmin` 角色的 SQL 服务器级主体（登录名）可以针对 Azure AD 主体执行以下操作：

    - EXECUTE AS USER
    - EXECUTE AS LOGIN

  - 若要使用 EXECUTE AS 语句模拟用户，用户需要直接映射到 Azure AD 服务器主体（登录名）。 即使调用方对指定用户名具有模拟权限，也无法有效地使用 EXECUTE AS 语句模拟映射到 Azure AD 服务器主体中的 Azure AD 组中的用户。

- SQL 托管实例中的 Azure AD 用户在使用 [SSMS V18.4 或更高版本](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)或 [SQLPackage.exe](https://docs.microsoft.com/sql/tools/sqlpackage-download) 时，可以使用 bacpac 文件进行数据库导出/导入。
  - 使用数据库 bacpac 文件时，可以使用以下配置： 
    - 在同一 Azure AD 域的不同托管实例之间导出/导入数据库。
    - 在同一 Azure AD 域中将数据库从 SQL 托管实例导出以及将其导入 SQL 数据库。 
    - 在同一 Azure AD 域中从 SQL 数据库导出数据库以及将其导入 SQL 托管实例。
    - 将数据库从 SQL 托管实例导出以及将其导入 SQL Server（2012 或更高版本）。
      - 在此配置中，所有 Azure AD 用户都创建为没有登录名的 SQL Server 数据库主体（用户）。 用户类型列为 `SQL`，在 sys.database_principals 中以 `SQL_USER` 的形式呈现。 其权限和角色保留在 SQL Server 数据库元数据中，可以用于模拟。 但是，它们不能用来通过其凭据访问和登录 SQL Server。

- 只有服务器级主体登录名（由 SQL 托管实例预配进程创建）、服务器角色的成员（例如 `securityadmin` 或 `sysadmin`）或者在服务器级别拥有 ALTER ANY LOGIN 权限的其他登录名可以在 SQL 托管实例的 master 数据库中创建 Azure AD 服务器主体（登录名）。
- 如果登录名是 SQL 主体，则只有属于 `sysadmin` 角色的登录名才能使用 create 命令来为 Azure AD 帐户创建登录名。
- Azure AD 登录名必须是用于 Azure SQL 托管实例的同一目录中的 Azure AD 成员。
- 从 SQL Server Management Studio 18.0 预览版 5 开始，Azure AD 服务器主体（登录名）将显示在对象资源管理器中。
- 允许 Azure AD 服务器主体（登录名）与 Azure AD 管理员帐户重叠。 解析主体以及将权限应用到 SQL 托管实例时，Azure AD 服务器主体（登录名）优先于 Azure AD 管理员。
- 在身份验证期间，将应用以下顺序来解析身份验证主体：

    1. 如果 Azure AD 帐户存在并直接映射到 Azure AD 服务器主体（登录名）（以类型“E”的形式存在于 sys.server_principals 中），则授予访问权限并应用 Azure AD 服务器主体（登录名）的权限。
    2. 如果 Azure AD 帐户是映射到 Azure AD 服务器主体（登录名）的 Azure AD 组的成员（以类型“X”的形式存在于 sys.server_principals 中），则授予访问权限并应用 Azure AD 组登录名的权限。
    3. 如果 Azure AD 帐户是在门户中配置的、SQL 托管实例的特殊 Azure AD 管理员（不存在于 SQL 托管实例系统视图中），则应用 SQL 托管实例的 Azure AD 管理员的特殊固定权限（传统模式）。
    4. 如果 Azure AD 帐户存在并直接映射到数据库中的 Azure AD 用户（以类型“E”的形式存在于 sys.database_principals 中），则授予访问权限并应用 Azure AD 数据库用户的权限。
    5. 如果 Azure AD 帐户是映射到数据库中 Azure AD 用户的 Azure AD 组的成员（以类型“X”的形式存在于 sys.database_principals 中），则授予访问权限并应用 Azure AD 组登录名的权限。
    6. 如果某个 Azure AD 登录映射到 Azure AD 用户帐户或 Azure AD 组帐户并解析为用户身份验证，则应用此 Azure AD 登录名中的所有权限。

### <a name="service-key-and-service-master-key"></a>服务密钥和服务主密钥

- 不支持[主密钥备份](https://docs.microsoft.com/sql/t-sql/statements/backup-master-key-transact-sql)（由 SQL 数据库服务管理）。
- 不支持[主密钥还原](https://docs.microsoft.com/sql/t-sql/statements/restore-master-key-transact-sql)（由 SQL 数据库服务管理）。
- 不支持[服务主密钥备份](https://docs.microsoft.com/sql/t-sql/statements/backup-service-master-key-transact-sql)（由 SQL 数据库服务管理）。
- 不支持[服务主密钥还原](https://docs.microsoft.com/sql/t-sql/statements/restore-service-master-key-transact-sql)（由 SQL 数据库服务管理）。

## <a name="configuration"></a>配置

### <a name="buffer-pool-extension"></a>缓冲池扩展

- 不支持[缓冲池扩展](https://docs.microsoft.com/sql/database-engine/configure-windows/buffer-pool-extension)。
- 不支持 `ALTER SERVER CONFIGURATION SET BUFFER POOL EXTENSION`。 请参阅 [ALTER SERVER CONFIGURATION](https://docs.microsoft.com/sql/t-sql/statements/alter-server-configuration-transact-sql)。

### <a name="collation"></a>排序规则

默认实例排序规则为 `SQL_Latin1_General_CP1_CI_AS` 并可以被指定为创建参数。 请参阅[排序规则](https://docs.microsoft.com/sql/t-sql/statements/collations)。

### <a name="compatibility-levels"></a>兼容级别

- 支持的兼容级别：100、110、120、130、140 和 150。
- 不支持低于 100 的兼容级别。
- 新数据库的默认兼容级别为 140。 对于已还原的数据库，如果其兼容级别在还原之前为 100 或更高，则还原后保持不变。

请参阅 [ALTER DATABASE 兼容级别](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level)。

### <a name="database-mirroring"></a>数据库镜像

不支持数据库镜像。

- 不支持 `ALTER DATABASE SET PARTNER` 和 `SET WITNESS` 选项。
- 不支持 `CREATE ENDPOINT … FOR DATABASE_MIRRORING`。

有关详细信息，请参阅 [ALTER DATABASE SET PARTNER 和 SET WITNESS](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-database-mirroring) 以及 [CREATE ENDPOINT … FOR DATABASE_MIRRORING](https://docs.microsoft.com/sql/t-sql/statements/create-endpoint-transact-sql)。

### <a name="database-options"></a>数据库选项

- 不支持多个日志文件。
- “常规用途”服务层级不支持内存中对象。 
- 每个“常规用途”实例限制为 280 个文件，这意味着，每个数据库最多只能有 280 个文件。 “常规用途”层级中的数据文件和日志文件都会计入此限制。 [“业务关键”层级支持每个数据库 32,767 个文件](./resource-limits.md#service-tier-characteristics)。
- 数据库中不能有包含文件流数据的文件组。 如果 .bak 包含 `FILESTREAM` 数据，还原将会失败。 
- 每个文件都被放置在 Azure Blob 存储中。 每个文件的 IO 和吞吐量取决于每个单独文件的大小。

#### <a name="create-database-statement"></a>CREATE DATABASE 语句

以下限制适用于 `CREATE DATABASE`：

- 无法定义文件和文件组。 
- 不支持 `CONTAINMENT` 选项。 
- 不支持 `WITH` 选项。 
   > [!TIP]
   > 解决方法是在 `CREATE DATABASE` 后面使用 `ALTER DATABASE` 来设置数据库选项，以添加文件或设置包含。 

- 不支持 `FOR ATTACH` 选项。
- 不支持 `AS SNAPSHOT OF` 选项。

有关详细信息，请参阅 [CREATE DATABASE](https://docs.microsoft.com/sql/t-sql/statements/create-database-sql-server-transact-sql)。

#### <a name="alter-database-statement"></a>ALTER DATABASE 语句

无法设置或更改某些文件属性：

- 无法在 `ALTER DATABASE ADD FILE (FILENAME='path')` T-SQL 语句中指定文件路径。 请从脚本中删除 `FILENAME`，因为 SQL 托管实例自动放置文件。 
- 无法使用 `ALTER DATABASE` 语句更改文件名。

默认会设置以下选项，无法更改这些选项：

- `MULTI_USER`
- `ENABLE_BROKER ON`
- `AUTO_CLOSE OFF`

无法修改以下选项：

- `AUTO_CLOSE`
- `AUTOMATIC_TUNING(CREATE_INDEX=ON|OFF)`
- `AUTOMATIC_TUNING(DROP_INDEX=ON|OFF)`
- `DISABLE_BROKER`
- `EMERGENCY`
- `ENABLE_BROKER`
- `FILESTREAM`
- `HADR`
- `NEW_BROKER`
- `OFFLINE`
- `PAGE_VERIFY`
- `PARTNER`
- `READ_ONLY`
- `RECOVERY BULK_LOGGED`
- `RECOVERY_SIMPLE`
- `REMOTE_DATA_ARCHIVE` 
- `RESTRICTED_USER`
- `SINGLE_USER`
- `WITNESS`

有关详细信息，请参阅 [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-file-and-filegroup-options)。

### <a name="sql-server-agent"></a>SQL Server 代理

- 目前，SQL 托管实例不支持启用和禁用 SQL Server 代理。 SQL 代理始终运行。
- SQL Server 代理设置为只读。 SQL 托管实例不支持过程 `sp_set_agent_properties`。 
- 作业
  - 支持 T-SQL 作业步骤。
  - 支持以下复制作业：
    - 事务日志读取器
    - 快照
    - 分发服务器
  - 支持 SSIS 作业步骤。
  - 目前不支持其他类型的作业步骤：
    - 不支持合并复制作业步骤。 
    - 不支持队列读取器。 
    - 尚不支持命令外壳。
  - SQL 托管实例无法访问外部资源（例如，通过 robocopy 访问网络共享）。 
  - 不支持 SQL Server Analysis Services。
- 部分支持通知。
- 支持电子邮件通知，不过需要配置数据库邮件配置文件。 SQL Server 代理只能使用一个数据库邮件配置文件，并且该配置文件必须命名为 `AzureManagedInstance_dbmail_profile`。 
  - 不支持寻呼机。
  - 不支持 NetSend。
  - 尚不支持警报。
  - 不支持代理。
- 不支持 EventLog。
- 用户必须直接映射到 Azure AD 服务器主体（登录名），才能创建、修改或执行 SQL 代理作业。 未直接映射的用户（例如，具有创建、修改或执行 SQL 代理作业权限的 Azure AD 组中的用户）将无法有效地执行这些操作。 这是由于托管实例模拟和 [EXECUTE AS 限制](#logins-and-users)的缘故。

目前不支持以下 SQL 代理功能：

- 代理
- 针对空闲 CPU 计划作业
- 启用或禁用代理
- 警报

有关 SQL Server 代理的信息，请参阅 [SQL Server 代理](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent)。

### <a name="tables"></a>表

不支持以下表类型：

- [FILESTREAM](https://docs.microsoft.com/sql/relational-databases/blob/filestream-sql-server)
- [FILETABLE](https://docs.microsoft.com/sql/relational-databases/blob/filetables-sql-server)
- [EXTERNAL TABLE](https://docs.microsoft.com/sql/t-sql/statements/create-external-table-transact-sql) (Polybase)
- [MEMORY_OPTIMIZED](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/introduction-to-memory-optimized-tables)（仅在“常规用途”层级中不受支持）

若要了解如何创建和更改表，请参阅 [CREATE TABLE](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql) 和 [ALTER TABLE](https://docs.microsoft.com/sql/t-sql/statements/alter-table-transact-sql)。

## <a name="functionalities"></a>功能

### <a name="bulk-insert--openrowset"></a>BULK INSERT/OPENROWSET

由于 SQL 托管实例无法访问文件共享和 Windows 文件夹，必须从 Azure Blob 存储导入文件：

- 从 Azure Blob 存储导入文件时，必须在 `BULK INSERT` 命令中指定 `DATASOURCE`。 请参阅 [BULK INSERT](https://docs.microsoft.com/sql/t-sql/statements/bulk-insert-transact-sql)。
- 从 Azure Blob 存储中读取文件内容时，必须在 `OPENROWSET` 函数中指定 `DATASOURCE`。 请参阅 [OPENROWSET](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql)。
- `OPENROWSET` 可以用来从 Azure SQL 数据库、Azure SQL 托管实例或 SQL Server 实例读取数据。 其他资源（例如 Oracle 数据库或 Excel 文件）不受支持。

### <a name="clr"></a>CLR

由于 SQL 托管实例无法访问文件共享和 Windows 文件夹，因此存在以下约束：

- 仅支持 `CREATE ASSEMBLY FROM BINARY`。 请参阅 [CREATE ASSEMBLY FROM BINARY](https://docs.microsoft.com/sql/t-sql/statements/create-assembly-transact-sql)。 
- 不支持 `CREATE ASSEMBLY FROM FILE`。 请参阅 [CREATE ASSEMBLY FROM FILE](https://docs.microsoft.com/sql/t-sql/statements/create-assembly-transact-sql)。
- `ALTER ASSEMBLY` 不能引用文件。 请参阅 [ALTER ASSEMBLY](https://docs.microsoft.com/sql/t-sql/statements/alter-assembly-transact-sql)。

### <a name="database-mail-db_mail"></a>数据库邮件 (db_mail)
 - `sp_send_dbmail` 无法使用 @file_attachments 参数发送附件。 在此过程中无法访问本地文件系统和外部共享或 Azure Blob 存储。
 - 请参阅与 `@query` 参数和身份验证相关的已知问题。
 
### <a name="dbcc"></a>DBCC

SQL 托管实例不支持 SQL Server 中启用的未记录 DBCC 语句。

- 仅支持有限数量的全局跟踪标志。 不支持会话级 `Trace flags`。 请参阅[跟踪标志](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql)。
- [DBCC TRACEOFF](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceoff-transact-sql) 和 [DBCC TRACEON](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceon-transact-sql) 使用有限数量的全局跟踪标志。
- 无法使用带有 REPAIR_ALLOW_DATA_LOSS、REPAIR_FAST 和 REPAIR_REBUILD 选项的 [DBCC CHECKDB](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-checkdb-transact-sql)，因为无法在 `SINGLE_USER` 模式中设置数据库 - 请参阅 [ALTER DATABASE 的差异](#alter-database-statement)。 潜在的数据库损坏将由 Azure 支持团队负责处理。 如果发生数据库损坏，请联系 Azure 支持人员。

### <a name="distributed-transactions"></a>分布式事务

对[分布式事务](../database/elastic-transactions-overview.md)的部分支持目前为公共预览版。 支持的应用场景有：
* 参与者只包含属于[服务器信任组](./server-trust-group-overview.md)的 Azure SQL 托管实例的事务。
* 从 .NET（TransactionScope 类）和 Transact-SQL 启动的事务。

Azure SQL 托管实例当前不支持本地或 Azure 虚拟机中的 MSDTC 通常支持的其他应用场景。

### <a name="extended-events"></a>扩展事件

不支持对扩展事件 (XEvent) 使用某些特定于 Windows 的目标：

- 不支持 `etw_classic_sync` 目标。 在 Azure Blob 存储中存储 `.xel` 文件。 请参阅 [etw_classic_sync 目标](https://docs.microsoft.com/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#etw_classic_sync_target-target)。
- 不支持 `event_file` 目标。 在 Azure Blob 存储中存储 `.xel` 文件。 请参阅 [event_file 目标](https://docs.microsoft.com/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#event_file-target)。

### <a name="filestream-and-filetable"></a>文件流和文件表

- 不支持文件流数据。
- 数据库中不能有包含 `FILESTREAM` 数据的文件组。
- 不支持 `FILETABLE`。
- 表不能采用 `FILESTREAM` 类型。
- 不支持以下函数：
  - `GetPathLocator()`
  - `GET_FILESTREAM_TRANSACTION_CONTEXT()`
  - `PathName()`
  - `GetFileNamespacePat)`
  - `FileTableRootPath()`

有关详细信息，请参阅 [FILESTREAM](https://docs.microsoft.com/sql/relational-databases/blob/filestream-sql-server) 和[文件表](https://docs.microsoft.com/sql/relational-databases/blob/filetables-sql-server)。

### <a name="full-text-semantic-search"></a>全文语义搜索

不支持[语义搜索](https://docs.microsoft.com/sql/relational-databases/search/semantic-search-sql-server)。

### <a name="linked-servers"></a>链接的服务器

SQL 托管实例中的链接服务器支持有限数量的目标：

- 支持的目标有 SQL 托管实例、SQL 数据库、Azure Synapse SQL 和 SQL Server 实例。 
- 链接服务器不支持分布式可写事务 (MS DTC)。
- 不支持的目标为文件、Analysis Services 和其他 RDBMS。 请尝试使用从 Azure Blob 存储进行本机 CSV 导入（使用 `BULK INSERT` 或 `OPENROWSET`）来代替文件导入操作。

操作： 

- 不支持跨实例写入事务。
- 支持使用 `sp_dropserver` 删除链接服务器。 请参阅 [sp_dropserver](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-dropserver-transact-sql)。
- `OPENROWSET` 函数只能用于在 SQL Server 实例上执行查询。 它们可以是托管的、位于本地或位于虚拟机中。 请参阅 [OPENROWSET](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql)。
- `OPENDATASOURCE` 函数只能用于在 SQL Server 实例上执行查询。 它们可以是托管的、位于本地或位于虚拟机中。 仅支持将 `SQLNCLI`、`SQLNCLI11` 和 `SQLOLEDB` 值用作提供程序。 例如 `SELECT * FROM OPENDATASOURCE('SQLNCLI', '...').AdventureWorks2012.HumanResources.Employee`。 请参阅 [OPENDATASOURCE](https://docs.microsoft.com/sql/t-sql/functions/opendatasource-transact-sql)。
- 不能使用链接服务器从网络共享读取文件（Excel、CSV）。 请尝试使用从 Azure Blob 存储读取 CSV 文件的 [BULK INSERT](https://docs.microsoft.com/sql/t-sql/statements/bulk-insert-transact-sql#e-importing-data-from-a-csv-file)/[OPENROWSET](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql#g-accessing-data-from-a-csv-file-with-a-format-file)。

### <a name="polybase"></a>PolyBase

唯一可用的外部资源类型是 Azure SQL 数据库、Azure SQL 托管实例和 Azure Synapse 池的 RDBMS（公共预览版）。 有关 PolyBase 的信息，请参阅 [PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide)。

### <a name="replication"></a>复制

- 支持快照和双向复制类型。 不支持合并复制、对等复制和可更新订阅。
- [事务复制](replication-transactional-overview.md)在 SQL 托管实例上为公共预览版，但存在一些约束：
    - 所有类型的复制参与者（发布服务器、分发服务器、拉取订阅服务器和推送订阅服务器）都可以放置在 SQL 托管实例上，但发布服务器和分发服务器必须同时在云中或同时在本地。
    - SQL 托管实例可以与最新版 SQL Server 通信。 有关详细信息，请参阅[支持的版本矩阵](replication-transactional-overview.md#supportability-matrix)。
    - 事务复制存在一些[其他的网络要求](replication-transactional-overview.md#requirements)。

有关配置事务复制的详细信息，请参阅以下教程：
- [SQL MI 发布服务器与 SQL MI 订阅服务器之间的复制](replication-between-two-instances-configure-tutorial.md)
- [SQL MI 发布服务器、SQL MI 分发服务器和 SQL Server 订阅服务器之间的复制](replication-two-instances-and-sql-server-configure-tutorial.md)

### <a name="restore-statement"></a>RESTORE 语句 

- 支持的语法：
  - `RESTORE DATABASE`
  - `RESTORE FILELISTONLY ONLY`
  - `RESTORE HEADER ONLY`
  - `RESTORE LABELONLY ONLY`
  - `RESTORE VERIFYONLY ONLY`
- 不支持的语法：
  - `RESTORE LOG ONLY`
  - `RESTORE REWINDONLY ONLY`
- 来源： 
  - `FROM URL`（Azure Blob 存储）是唯一受支持的选项。
  - 不支持 `FROM DISK`/`TAPE`/备份设备。
  - 不支持备份集。
- 不支持 `WITH` 选项。 `WITH`（如 `DIFFERENTIAL`、`STATS`、`REPLACE` 等）等还原尝试将失败。
- `ASYNC RESTORE`：即使客户端连接断开，还原也会继续。 如果删除了连接，可以在 `sys.dm_operation_status` 视图中检查还原操作的状态，以及 CREATE DATABASE 和 DROP DATABASE 的状态。 请参阅 [sys.dm_operation_status](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database)。 

将设置或重写以下数据库选项，以后无法更改： 

- `NEW_BROKER`（如果未在 .bak 文件中启用代理）。 
- `ENABLE_BROKER`（如果未在 .bak 文件中启用代理）。 
- `AUTO_CLOSE=OFF`（如果 .bak 文件中的数据库采用 `AUTO_CLOSE=ON`）。 
- `RECOVERY FULL`（如果 .bak 文件中的数据库采用 `SIMPLE` 或 `BULK_LOGGED` 恢复模式）。
- 添加源 .bak 文件中不包含内存优化文件组，则会添加名为 XTP 的内存优化文件组。 
- 任何现有的内存优化文件组将重命名为 XTP。 
- `SINGLE_USER` 和 `RESTRICTED_USER` 选项将转换为 `MULTI_USER`。

的限制： 

- 根据损坏类型，有时可以还原已损坏的数据库的备份，但在修复损坏之前，不会创建自动备份。 确保在源 SQL 托管实例上运行 `DBCC CHECKDB`，并使用备份 `WITH CHECKSUM` 来避免此问题。
- 无法在 SQL 托管实例上还原包含本文档所述的任何限制的数据库的 `.BAK` 文件（例如 `FILESTREAM` 或 `FILETABLE` 对象）。
- 无法还原包含多个备份集的 `.BAK` 文件。 
- 无法还原包含多个日志文件的 `.BAK` 文件。
- 在“常规用途”实例上，无法还原包含 8 TB 以上的数据库、活动的内存中 OLTP 对象或每个实例有 280 个以上的文件的备份。 
- 在“业务关键”实例上，无法还原包含 4 TB 以上的数据库或内存中 OLTP 对象，且总大小超过[资源限制](resource-limits.md)中所述大小的备份。
有关 restore 语句的信息，请参阅 [RESTORE 语句](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql)。

 > [!IMPORTANT]
 > 这些限制同样适用于内置的时间点还原操作。 例如，在“业务关键”实例上，无法还原大于 4 TB 的“常规用途”数据库。 在“常规用途”实例上，无法还原包含内存中 OLTP 文件或 280 个以上的文件的“业务关键”数据库。

### <a name="service-broker"></a>服务代理

不支持跨实例 Service Broker：

- `sys.routes`：作为先决条件，必须从 sys.routes 中选择地址。 该地址必须在每个路由的本地。 请参阅 [sys.routes](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-routes-transact-sql)。
- `CREATE ROUTE`：不能将 `CREATE ROUTE` 与除 `LOCAL` 以外的 `ADDRESS` 配合使用。 请参阅 [CREATE ROUTE](https://docs.microsoft.com/sql/t-sql/statements/create-route-transact-sql)。
- `ALTER ROUTE`：不能将 `ALTER ROUTE` 与除 `LOCAL` 以外的 `ADDRESS` 配合使用。 请参阅 [ALTER ROUTE](https://docs.microsoft.com/sql/t-sql/statements/alter-route-transact-sql)。 

### <a name="stored-procedures-functions-and-triggers"></a>存储过程、函数和触发器

- “常规用途”层级不支持 `NATIVE_COMPILATION`。
- 不支持以下 [sp_configure](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-configure-transact-sql) 选项： 
  - `allow polybase export`
  - `allow updates`
  - `filestream_access_level`
  - `remote access`
  - `remote data archive`
  - `remote proc trans`
- 不支持 `sp_execute_external_scripts`。 请参阅 [sp_execute_external_scripts](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql#examples)。
- 不支持 `xp_cmdshell`。 请参阅 [xp_cmdshell](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/xp-cmdshell-transact-sql)。
- 不支持 `Extended stored procedures`，其中包括 `sp_addextendedproc` 和 `sp_dropextendedproc`。 请参阅[扩展存储过程](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/general-extended-stored-procedures-transact-sql)。
- 不支持 `sp_attach_db`、`sp_attach_single_file_db` 和 `sp_detach_db`。 请参阅 [sp_attach_db](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-attach-db-transact-sql)、[sp_attach_single_file_db](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-attach-single-file-db-transact-sql) 和 [sp_detach_db](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-detach-db-transact-sql)。

### <a name="system-functions-and-variables"></a>系统函数和变量

以下变量、函数和视图返回不同的结果：

- `SERVERPROPERTY('EngineEdition')` 返回值 8。 此属性唯一标识 SQL 托管实例。 请参阅 [SERVERPROPERTY](https://docs.microsoft.com/sql/t-sql/functions/serverproperty-transact-sql)。
- `SERVERPROPERTY('InstanceName')` 返回 NULL，因为 SQL Server 存在的实例概念并不适用于 SQL 托管实例。 请参阅 [SERVERPROPERTY('InstanceName')](https://docs.microsoft.com/sql/t-sql/functions/serverproperty-transact-sql)。
- `@@SERVERNAME` 返回完整的 DNS“可连接”名称，例如 my-managed-instance.wcus17662feb9ce98.database.chinacloudapi.cn。 请参阅 [@@SERVERNAME](https://docs.microsoft.com/sql/t-sql/functions/servername-transact-sql)。 
- `SYS.SERVERS` 返回完整的 DNS“可连接”名称，例如，为属性“name”和“data_source”返回 `myinstance.domain.database.chinacloudapi.cn`。 请参阅 [SYS.SERVERS](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-servers-transact-sql)。
- `@@SERVICENAME` 返回 NULL，因为 SQL Server 存在的服务概念并不适用于 SQL 托管实例。 请参阅 [@@SERVICENAME](https://docs.microsoft.com/sql/t-sql/functions/servicename-transact-sql)。
- 支持 `SUSER_ID`。 如果 Azure AD 登录名不在 sys.syslogins 中，则返回 NULL。 请参阅 [SUSER_ID](https://docs.microsoft.com/sql/t-sql/functions/suser-id-transact-sql)。 
- 不支持 `SUSER_SID`。 将返回错误数据，这是暂时性的已知问题。 请参阅 [SUSER_SID](https://docs.microsoft.com/sql/t-sql/functions/suser-sid-transact-sql)。 

## <a name="environment-constraints"></a><a name="Environment"></a>环境约束

### <a name="subnet"></a>子网
-  在部署 SQL 托管实例的子网中，无法放置其他任何资源（例如虚拟机）。 请使用其他子网部署这些资源。
- 子网必须有足够数量的可用 [IP 地址](connectivity-architecture-overview.md#network-requirements)。 至少为 16 个，但建议在子网中至少有 32 个 IP 地址。
- [不能将服务终结点与 SQL 托管实例的子网相关联](connectivity-architecture-overview.md#network-requirements)。 创建虚拟网络时，请务必禁用“服务终结点”选项。
- 可以在某个区域部署的 vCore 数和实例类型存在一些[约束和限制](resource-limits.md#regional-resource-limitations)。
- 存在一些[必须在子网上应用的安全规则](connectivity-architecture-overview.md#network-requirements)。

### <a name="vnet"></a>VNET
- VNet 可以使用资源模型进行部署 - 不支持适用于 VNet 的经典模型。
- 创建 SQL 托管实例后，不支持将 SQL 托管实例或 VNet 移到另一个资源组或订阅。
- 应用服务环境、逻辑应用和 SQL 托管实例之类的某些服务（用于异地复制、事务复制，或者通过链接服务器来使用）在其 VNet 是通过[全局对等互连](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers)进行连接的情况下不能访问不同区域中的 SQL 托管实例。 可以通过 VPN 网关经由 ExpressRoute 或 VNet-to-VNet 连接到这些资源。

### <a name="failover-groups"></a>故障转移组
系统数据库不会复制到故障转移组中的辅助实例。 因此，除非在辅助实例上手动创建系统数据库中的对象，否则依赖于该对象的方案将不可能在辅助实例上出现。

### <a name="tempdb"></a>TEMPDB
- 在“常规用途”层级上，`tempdb` 的最大文件大小不能超过 24 GB 每核心。 在“业务关键”层级上，最大 `tempdb` 大小根据 SQL 托管实例存储大小受到限制。 在“常规用途”层级上，`Tempdb` 日志文件大小限制为 120 GB。 如果某些查询需要在 `tempdb` 中为每个核心提供 24 GB 以上的空间，或者生成 120 GB 以上的日志数据，则这些查询可能会返回错误。
- `Tempdb` 始终拆分为 12 个数据文件：1 个主要数据文件（也称为主文件）和 11 个非主要数据文件。 无法更改文件结构，并且无法将新文件添加到 `tempdb`。 
- 不支持[内存优化的 `tempdb` 元数据](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database?view=sql-server-ver15#memory-optimized-tempdb-metadata)（一种新的 SQL Server 2019 内存中数据库功能）。
- 重启或故障转移后，无法在 `tempdb` 中自动创建在模型数据库中已创建的对象，因为 `tempdb` 不能从模型数据库中获取其初始对象列表。 每次重启或故障转移后，必须在 `tempdb` 中手动创建对象。

### <a name="msdb"></a>MSDB

SQL 托管实例中的以下 MSDB 架构必须由其相应的预定义角色拥有：

- 常规角色
  - TargetServersRole
- [固定数据库角色](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent-fixed-database-roles?view=sql-server-ver15)
  - SQLAgentUserRole
  - SQLAgentReaderRole
  - SQLAgentOperatorRole
- [DatabaseMail 角色](https://docs.microsoft.com/sql/relational-databases/database-mail/database-mail-configuration-objects?view=sql-server-ver15#DBProfile)：
  - DatabaseMailUserRole
- [集成服务角色](https://docs.microsoft.com/sql/integration-services/security/integration-services-roles-ssis-service?view=sql-server-ver15)：
  - msdb
  - db_ssisltduser
  - db_ssisoperator
  
> [!IMPORTANT]
> 客户更改预定义的角色名称、架构名称和架构所有者将会影响服务的正常运行。 如果对这些属性进行任何更改，在检测到此类更改后会立即将其还原到预定义值，或者最迟在下次更新服务时还原，以确保服务正常运行。

### <a name="error-logs"></a>错误日志

SQL 托管实例将详细信息放在错误日志中。 有很多内部系统事件记录在错误日志中。 使用自定义过程读取已筛选出某些不相关条目的错误日志。 有关详细信息，请参阅 [SQL 托管实例 - sp_readmierrorlog](https://docs.microsoft.com/archive/blogs/sqlcat/azure-sql-db-managed-instance-sp_readmierrorlog) 或用于 Azure Data Studio 的 [SQL 托管实例扩展（预览版）](https://docs.microsoft.com/sql/azure-data-studio/azure-sql-managed-instance-extension#logs)。

## <a name="next-steps"></a>后续步骤

- 有关 SQL 托管实例的详细信息，请参阅[什么是 SQL 托管实例？](sql-managed-instance-paas-overview.md)
- 有关功能和比较列表，请参阅 [Azure SQL 托管实例功能比较](../database/features-comparison.md)。
- 有关版本更新和已知问题的状态，请参阅 [SQL 托管实例发行说明](../database/doc-changes-updates-release-notes.md)
- 有关如何新建 SQL 托管实例的快速入门，请参阅[创建 SQL 托管实例](instance-create-quickstart.md)。
