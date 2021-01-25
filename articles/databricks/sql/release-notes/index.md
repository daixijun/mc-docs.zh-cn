---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 01/12/2021
title: SQL Analytics 发行说明 - Azure Databricks
description: 了解 Azure Databricks SQL Analytics 版本。
ms.openlocfilehash: 5138e492c93a7c47ce356c7cd201e6e58118bc93
ms.sourcegitcommit: bb7497d5a11e8fb506907221ff65a18e6c523372
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/22/2021
ms.locfileid: "98692172"
---
# <a name="sql-analytics-release-notes"></a>SQL Analytics 发行说明

> [!IMPORTANT]
>
> 此功能目前以[公共预览版](../../release-notes/release-types.md)提供。 请联系 Azure Databricks 代表，以申请访问权限。

## <a name="sql-analytics-public-preview"></a>SQL Analytics（公共预览版）

**2021 年 1 月 7 日**

* SQL 终结点上的“自动停止”设置现在已“启用”，默认值为 120 分钟。
* 除了 ``TEXT`` 类型的查询参数，将不再向查询参数添加引号。 如果已使用 ``Dropdown List``、``Query Based Dropdown List`` 或任意 ``Date`` 类型的查询参数，则必须添加引号才能运行查询。 例如，如果查询是 ``SELECT {{ d }}``，则现在此查询必须为 ``SELECT '{{ d }}'``。

**2020 年 11 月 18 日**

Databricks 很高兴地推出 SQL Analytics 公共预览版（一个直观的环境，可用于运行临时查询和基于数据湖中存储的数据创建仪表板）。  SQL Analytics 可助力组织运行多云 [lakehouse 体系结构](https://databricks.com/glossary/data-lakehouse)，该体系结构可为数据仓库性能提供数据湖经济性，同时提供良好的 SQL Analytics 用户体验。 SQL Analytics：

* 与当前使用的 BI 工具（例如 Tableau 和 Microsoft Power BI）集成，查询数据湖中最完整和最新的数据。
* 使用 SQL 原生接口对现有 BI 工具进行补充，该接口支持数据分析师和数据科学家直接在 Azure Databricks 中查询数据湖数据。
* 支持通过丰富的可视化效果和拖放式仪表板共享查询见解，以及自动在重要数据发生更改时发出警报。
* 使用 [SQL 终结点](../admin/sql-endpoints.md)为数据湖引入可靠性、质量、缩放、安全性和性能，以便用户使用最新和最完整的数据来运行常规的分析工作负载。

有关详细信息，请参阅 [Azure Databricks SQL Analytics 指南](../index.md)。 请联系 Databricks 代表，以申请访问权限。

## <a name="usage-privilege"></a>USAGE 特权

**2020 年 11 月 18 日**

SQL Analytics 引入了 ``USAGE`` 特权，以简化数据访问管理。 为了使用数据库中的对象，除了执行该操作所需的所有特权以外，还必须获得该数据库的 USAGE 特权。

可以将 ``USAGE`` 特权授予数据库或目录。

对于已使用表访问控制的工作区，``USAGE`` 特权会自动授予给根 ``CATALOG`` 上的用户组。

有关详细信息，请参阅[数据访问控制](../user/security/access-control/data-acl.md)。

## <a name="known-issues"></a>已知问题

**2020 年 12 月 1 日**

* 类似于时间戳的表达式（如 ``now()``、``current_date()`` 和 ``current_timestamp()``）可能会导致返回过期的缓存结果。
* Tableau Online 用户无法连接到 SQL 端点。

**2020 年 11 月 18 日**

* 从多群集负载均衡 SQL 终结点中的 Delta Lake 以外的数据源中读取的内容可能不一致。
* [Photon](../admin/sql-endpoints.md#create-a-sql-endpoint) 禁止用于写入（例如 ``CREATE TABLE AS SELECT``）。
* 用户具有针对查询的“可运行”权限并运行该查询时，如果该查询是由另一个用户创建的，则查询历史记录会将该查询的创建者（而不是该查询的运行者）显示为用户。
* 在 SQL Analytics 中访问的 Delta 表将其架构和表属性上传到配置的元存储。 如果使用的是外部元存储，则可以在元存储中查看 Delta Lake 信息。 Delta Lake 会尽最大努力使这些信息保持最新状态。 也可使用 ``DESCRIBE <table>`` 命令来确保元存储中的信息已更新。