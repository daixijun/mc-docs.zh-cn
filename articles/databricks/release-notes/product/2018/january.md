---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 04/07/2020
title: 2018 年 1 月 - Azure Databricks
description: Azure Databricks 新增功能和改进功能的 2018 年 1 月发行说明。
ms.openlocfilehash: 7539827ad6494df53503910d810a747c88cf679c
ms.sourcegitcommit: 6b499ff4361491965d02bd8bf8dde9c87c54a9f5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2020
ms.locfileid: "94329440"
---
# <a name="january-2018"></a>2018 年 1 月

发布分阶段进行。 Databricks 帐户可能会在初始发布日期一周后才会更新。

## <a name="mount-points-for-azure-blob-storage-containers-and-data-lake-stores"></a>Azure Blob 存储容器和 Data Lake Store 的装入点

**2018 年 1 月 16 日至 23 日：版本 2.63**

我们提供了有关通过 Databricks 文件系统 (DBFS) 装载 Azure Blob 存储容器和 Data Lake Store 的说明。 这样，同一工作区中的所有用户都可以通过装入点访问 Blob 存储容器或 Data Lake Store（或者容器或存储中的文件夹）。 DBFS 管理用于访问已装载的 Blob 存储容器或 Data Lake Store 的凭据，并在后台自动处理通过 Azure Blob 存储或 Data Lake Store 进行的身份验证。

装载 Blob 存储容器和 Data Lake Store 需要 Databricks Runtime 4.0 及更高版本。 装载容器或存储后，可以使用 Runtime 3.4 或更高版本来访问装入点。

有关详细信息，请参阅 [Azure Blob 存储](../../../data/data-sources/azure/azure-storage.md)和 [Azure Data Lake Storage Gen1](../../../data/data-sources/azure/azure-datalake.md)。

## <a name="cluster-tags"></a>群集标记

**2018 年 1 月 4 日至 11 日：版本 2.62**

现在可以指定将传播到与群集关联的所有 Azure 资源（VM、磁盘、NIC 等）的群集标记。 除了用户提供的标记外，资源还将自动标记有群集名称、群集 ID 和群集创建者用户名。

有关详细信息，请参阅[群集标记](../../../clusters/configure.md#cluster-tags)。

## <a name="table-access-control-for-sql-and-python-private-preview"></a>SQL 和 Python 的表访问控制（个人预览版）

**2018 年 1 月 4 日至 11 日：版本 2.62**

> [!NOTE]
>
> 此功能在个人预览版中提供。 请联系你的帐户管理员以请求访问权限。 此功能还需要 Databricks Runtime 3.5+。

去年，我们为 SQL 用户引入了数据对象访问控制。 今天，我们很高兴地宣布为 SQL 和 Python 用户提供表访问控制 (ACL) 的专用预览版。 使用表访问控制，可以限制对安全对象（如表、数据库、视图或函数）的访问。 还可以通过对包含任意查询的派生视图设置权限来提供细粒度的访问控制（例如，对符合特定条件的行和列进行访问控制）。

有关详细信息，请参阅[数据对象特权](../../../security/access-control/table-acls/object-privileges.md)。

## <a name="exporting-notebook-job-run-results-via-api"></a>通过 API 导出笔记本作业运行结果

**2018 年 1 月 4 日至 11 日：版本 2.62**

为了提高你在作业结果上进行共享和协作的能力，我们现在提供一个新的作业 API 终结点 `jobs/runs/export`，它允许你在代码和仪表板视图中检索笔记本作业运行结果的静态 HTML 表示形式。

有关详细信息，请参阅[运行导出](../../../dev-tools/api/latest/jobs.md#jobsjobsserviceexportrun)。

## <a name="apache-airflow-190-includes-databricks-integration"></a>Apache Airflow 1.9.0 包括 Databricks 集成

**2018 年 1 月 2 日**

去年，我们发布了 Airflow（一种常用的管理 ETL 计划的解决方案）的预览版功能，使客户能够以原生方式创建在 Airflow DAG 中触发 Databricks 运行的任务。 我们很高兴地宣布，这些集成已在 Airflow 1.9.0 版中公开发布。

有关详细信息，请参阅 [Apache Airflow](../../../dev-tools/data-pipelines.md#airflow)。