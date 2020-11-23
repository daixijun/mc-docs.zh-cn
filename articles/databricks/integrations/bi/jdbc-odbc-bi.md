---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 10/01/2020
title: JDBC 和 ODBC 驱动程序以及配置参数 - Azure Databricks
description: 了解如何获取 JDBC 和 ODBC 驱动程序和配置参数以连接到 Azure Databricks 群集。
ms.openlocfilehash: dfbc2e408fe352cd2fc99c82acea324f4fa94794
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589458"
---
# <a name="jdbc-and-odbc-drivers-and-configuration-parameters"></a>JDBC 和 ODBC 驱动器以及配置参数

可以将商业智能 (BI) 工具连接到 Azure Databricks 群集，以查询表中的数据。 每个 Azure Databricks 群集都在驱动程序节点上运行 JDBC 和 ODBC 服务器。 本文介绍了如何获取 JDBC 和 ODBC 驱动程序以及配置参数以连接到 Azure Databricks 群集。 有关特定于工具的连接说明，请参阅[商业智能工具](index.md)。

## <a name="cluster-requirements"></a>群集要求

若要使用 JDBC 或 ODBC 访问群集，必须具有“可附加到”[权限](../../security/access-control/cluster-acl.md)。

如果使用 JDBC 或 ODBC 连接到已终止的群集，并且具有“可重启”[权限](../../security/access-control/cluster-acl.md)，则将重启群集。

## <a name="step-1-download-and-install-a-jdbc-or-odbc-driver"></a><a id="jdbc-driver"> </a><a id="step-1-download-and-install-a-jdbc-or-odbc-driver"> </a>步骤 1：下载并安装 JDBC 或 ODBC 驱动程序

对于所有 BI 工具，都需要一个 JDBC 或 ODBC 驱动程序来连接到 Azure Databricks 群集。

1. 转到 [Databricks JDBC/ODBC 驱动程序下载页面](https://databricks.com/spark/odbc-driver-download/)。
2. 填写表格并提交。 页面将更新并包含指向多个下载选项的链接。
3. 选择一个驱动程序并下载它。
4. 安装驱动程序。 对于 JDBC，提供了不需要安装的 JAR。 对于 ODBC，将为所选平台提供一个安装包，该平台必须安装在系统上。

## <a name="step-2-gather-jdbc-or-odbc-connection-parameters"></a>步骤 2：收集 JDBC 或 ODBC 连接参数

若要配置 JDBC 或 ODBC 驱动程序，必须从 Azure Databricks 收集连接参数。 以下是 JDBC 或 ODBC 驱动程序可能需要的一些参数：

| 参数                            | 值                                                                    |
|---------------------------------------|--------------------------------------------------------------------------|
| 用户名和密码                 | 请参阅[用户名和密码](#username-password)。                         |
| 主机、端口、HTTP 路径、JDBC URL       | 请参阅[服务器主机名、端口、HTTP 路径和 JDBC URL](#jdbc-odbc-params)。 |

对于 ODBC 的 JDBC 和 DSN 配置，通常会在 `httpPath` 中指定以下内容：

| 参数                            | 值                                                                |
|---------------------------------------|----------------------------------------------------------------------|
| Spark 服务器类型                     | Spark Thrift 服务器                                                  |
| 架构/数据库                       | default                                                              |
| 身份验证机制 (AuthMech)   | 用户名和密码身份验证                                 |
| Thrift 传输                      | http                                                                 |
| SSL                                   | 1                                                                    |

### <a name="username-and-password"></a><a id="username-and-password"> </a><a id="username-password"> </a>用户名和密码

* **用户名**：`token`
* **密码**：`<personal-access-token>`，使用[生成个人访问令牌](../../dev-tools/api/latest/authentication.md#token-management)中的过程所创建。

JDBC/ODBC 驱动程序对 Azure Databricks 的凭据传递或 Azure Active Directory 用户名和密码身份验证都不支持。

### <a name="server-hostname-port-http-path-and-jdbc-url"></a><a id="jdbc-odbc-params"> </a><a id="server-hostname-port-http-path-and-jdbc-url"> </a>服务器主机名、端口、HTTP 路径和 JDBC URL

1. 在群集配置页面上，单击“高级选项”切换开关。
2. 单击“JDBC/ODBC”选项卡。它包含主机名、端口、协议、HTTP 路径和 JDBC URL。

   > [!div class="mx-imgBorder"]
   > ![JDBC-ODBC 选项卡](../../_static/images/third-party-integrations/jdbc-odbc-tab-azure.png)

3. 复制 BI 工具所需的参数。
4. 如果你的工具需要 JDBC URL，请将 `<personal-access-token>` 替换为你在[用户名和密码](#username-password)中创建的令牌。 例如：

   ```
   jdbc:spark://<server-hostname>:443/default;transportMode=http;ssl=1;httpPath=sql/protocolv1/o/0/xxxx-xxxxxx-xxxxxxxx;AuthMech=3;UID=token;PWD=dapideadbeefdeadbeefdeadbeefdeadbeef
   ```

5. JDBC 和 ODBC 驱动程序接受 ANSI SQL-92 方言中的 SQL 查询，并将查询转换为 Spark SQL。 如果应用程序直接生成 Spark SQL，或者应用程序使用特定于 Databricks Runtime 的任何非 ANSI SQL-92 标准 SQL 语法，Databricks 建议你将 `;UseNativeQuery=1` 添加到连接配置中。 通过该设置，驱动程序会将 SQL 查询逐字传递到 Databricks Runtime。

### <a name="odbc-data-source-name-configuration-for-the-simba-odbc-driver"></a>Simba ODBC 驱动程序的 ODBC 数据源名称配置

数据源名称 (DSN) 配置包含用于与特定数据库通信的参数。 BI 工具（如 Tableau）通常会提供一个用户界面，用于输入这些参数。 如果你必须自行安装和管理 Simba ODBC 驱动程序，则可能需要创建配置文件，并允许驱动程序管理器（Windows 上的 [ODBC 数据源管理器](https://docs.microsoft.com/sql/database-engine/configure-windows/open-the-odbc-data-source-administrator)和 Unix 上的 [unixODBC](http://www.unixodbc.org/)/[iODBC](http://www.iodbc.org/dataspace/doc/iodbc/wiki/iodbcWiki/WelcomeVisitors)）来访问这些配置文件。 创建两个文件：`/etc/odbc.ini` 和 `/etc/odbcinst.ini`。

#### `/etc/odbc.ini`

`/etc/odbc.ini` 的内容可以是：

```ini
[Databricks-Spark]
Driver=Simba
Server=<server-hostname>
HOST=<server-hostname>
PORT=<port>
SparkServerType=3
Schema=default
ThriftTransport=2
SSL=1
AuthMech=3
UID=token
PWD=<personal-access-token>
HTTPPath=<http-path>
```

将 `<personal-access-token>` 设置为在[用户名和密码](#username-password)中检索到的令牌，其他参数设置为在[服务器主机名、端口、HTTP 路径和 JDBC URL](#jdbc-odbc-params) 中检索到的令牌。

#### `/etc/odbcinst.ini`

`/etc/odbcinst.ini` 的内容可以是：

```ini
[ODBC Drivers]
Simba = Installed
[Simba Spark ODBC Driver 64-bit]
Driver = <driver-path>
```

根据在步骤 1 下载驱动程序时选择的操作系统设置 `<driver-path>`：

* MacOs `/Library/simba/spark/lib/libsparkodbc_sbu.dylib`
* Linux (64-bit) `/opt/simba/spark/lib/64/libsparkodbc_sb64.so`
* Linux (32-bit) `/opt/simba/spark/lib/32/libsparkodbc_sb32.so`

#### <a name="configure-paths"></a>配置路径

在环境变量中指定两个文件的路径，以便驱动程序管理器可以使用它们：

```ini
export ODBCINI=/etc/odbc.ini
export ODBCSYSINI=/etc/odbcinst.ini
export SIMBASPARKINI=<simba-ini-path>/simba.sparkodbc.ini # (Contains the configuration for debugging the Simba driver)
```

其中，`<simba-ini-path>` 为

* MacOS `/Library/simba/spark/lib`
* Linux (64-bit) `/opt/simba/sparkodbc/lib/64`
* Linux (32-bit) `/opt/simba/sparkodbc/lib/32`

## <a name="troubleshooting"></a>疑难解答

请参阅[排查 JDBC 和 ODBC 连接问题](/databricks/kb/bi/jdbc-odbc-troubleshooting)。