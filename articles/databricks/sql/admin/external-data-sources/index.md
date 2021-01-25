---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 11/17/2020
title: 外部数据源 - Azure Databricks
description: 了解 Azure Databricks SQL Analytics 外部数据源。
ms.openlocfilehash: a84930a7c2af683dd558f4e92a208236b3968154
ms.sourcegitcommit: bb7497d5a11e8fb506907221ff65a18e6c523372
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/22/2021
ms.locfileid: "98692213"
---
# <a name="external-data-sources"></a>外部数据源

> [!IMPORTANT]
>
> 此功能目前以[公共预览版](../../../release-notes/release-types.md)提供。 请联系 Azure Databricks 代表，以申请访问权限。

外部数据源是一种计算资源，支持对 Azure Databricks 环境外部的一组数据对象运行 SQL 命令。

外部数据源上的 SQL 命令通过单个帐户，因此无法确定运行查询的人员。 无法在 Azure Databricks SQL Analytics 中查看外部数据源的状态，并且配置外部数据源时必须配置对数据源内对象的访问权限。

外部数据源支持外部数据源固有的 SQL 命令。

必须是 Azure Databricks [管理员](../../../administration-guide/users-groups/users.md)才能管理外部数据源。

另一类型的数据源是 [SQL 终结点](../sql-endpoints.md)。

> [!NOTE]
>
> 对外部数据源执行 SQL Analytics 查询不会产生 DBU 费用。 Azure Databricks 保留对此免费使用进行限制的权利，后续也可能会开始对此使用收费。

## <a name="create-an-external-data-source"></a>创建外部数据源

1. 单击边栏底部的![用户设置图标](../../../_static/images/icons/user-settings-icon.png)图标，然后选择“设置”。
2. 单击“外部数据源”选项卡。
3. 单击“+ 新建数据源”。
4. 选择数据源类型，然后按照配置说明进行操作：

   * [Axibase 时序数据库](axibase-time-series-database.md)
   * [Azure Synapse Analytics](azure-synapse-analytics.md)
   * [ElasticSearch](elasticsearch.md)
   * [Google Analytics](google-analytics.md)
   * [Jira](jira.md)
   * [JSON API](json-api.md)
   * [MongoDB](mongodb.md)
5. 单击“创建”。

## <a name="supported-external-data-source-types"></a>支持的外部数据源类型

SQL Analytics 支持以下外部数据源类型：

* Axibase 时序数据库
* Cassandra
* ClickHouse
* CockroachDB
* IBM 支持的 DB2
* Druid
* ElasticSearch
* Google Analytics
* Graphite
* Hive
* Impala
* InfluxDB
* JIRA
* JSON
* Apache Kylin
* MemSQL
* Microsoft Azure Synapse Analytics
* Microsoft Azure SQL 数据库
* Microsoft SQL Server
* MongoDB
* MySQL
* Oracle
* PostgreSQL
* Presto
* Prometheus
* Qubole
* Rockset
* Salesforce
* ScyllaDB
* Snowflake
* TreasureData
* Vertica
* Yandex AppMetrica
* Yandex Metrica