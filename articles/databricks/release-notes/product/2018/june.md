---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 04/29/2020
title: 2018 年 6 月 - Azure Databricks
description: Azure Databricks 新增功能和改进的 2018 年 6 月发行说明。
ms.openlocfilehash: 23b3bba8c3922a3aa66054f9cd452efeb23af458
ms.sourcegitcommit: 6b499ff4361491965d02bd8bf8dde9c87c54a9f5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2020
ms.locfileid: "94329439"
---
# <a name="june-2018"></a>2018 年 6 月

这些功能和 Databricks 平台改进已于 2018 年 6 月发布。

## <a name="rstudio-integration"></a>RStudio 集成

**2018 年 6 月 19 日：版本 2.74**

Azure Databricks 现在与 RStudio Server（适用于 R 的常用 IDE）集成。利用此功能强大的新集成，你可以：

* 直接从 Azure Databricks 启动 RStudio UI。
* 在 RStudio IDE 中导入 SparkR 和 sparklyr 包。
* 使用 Apache Spark 从 RStudio IDE 访问、浏览和转换大型数据集。
* 在 Azure Databricks 群集上执行和监视 Spark 作业。
* 使用版本控制来管理代码。
* 使用 Azure Databricks 上的 RStudio Server 的开源版或专业版。

RStudio 集成需要 [Azure Databricks 高级计划](https://databricks.com/product/azure-pricing)。 必须在高并发群集上安装集成。 有关详细信息，请参阅 [Azure Databricks 上的 RStudio](../../../spark/latest/sparkr/rstudio.md)。

## <a name="cluster-log-purge"></a>群集日志清除

**2018 年 6 月 19 日：版本 2.74**

默认情况下，群集日志保留 30 天。 现在，可以通过转到管理控制台上的“工作区存储”选项卡来立即永久地删除它们。 请参阅[管理工作区存储](../../../administration-guide/workspace/storage.md)。

## <a name="new-regions"></a>新区域

**2018 年 6 月 7 日**

目前，Azure Databricks 在以下区域中可用：

* 澳大利亚东部
* 澳大利亚东南部
* 英国南部
* 英国西部

## <a name="trash-folder"></a>回收站文件夹

**2018 年 6 月 7 日：版本 2.73**

新的 ![回收站](../../../_static/images/workspace/trash-icon.png)“回收站”文件夹包含已删除的所有笔记本、库和文件夹。 30 天后，“回收站”文件夹会被自动清除。 可以通过将已删除的对象从“回收站”文件夹拖放到另一个文件夹中来还原该对象。

有关详细信息，请参阅[删除对象](../../../workspace/workspace-objects.md#delete-object)。

## <a name="reduced-log-retention-period"></a>缩短了日志保留期

**2018 年 6 月 7 日：版本 2.73**

群集日志现在会保留 30 天。 这些日志过去会无限期保留。

## <a name="gzipped-api-responses"></a>Gzip 压缩的 API 响应

**2018 年 6 月 7 日：版本 2.73**

使用 `Accept-Encoding: gzip` 标头发送的请求会返回 gzip 压缩响应。 有关示例，请参阅[获取群集的 gzip 压缩列表](../../../dev-tools/api/latest/examples.md#gzip)。

## <a name="table-import-ui"></a>表导入 UI

**2018 年 6 月 7 日：版本 2.73**

[创建表 UI](../../../data/tables.md#create-table-ui) 现在支持推断 CSV 文件架构的选项：

> [!div class="mx-imgBorder"]
> ![推断表架构选项](../../../_static/images/tables/infer-schema.png)