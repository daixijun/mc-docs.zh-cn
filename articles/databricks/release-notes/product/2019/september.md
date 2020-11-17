---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 06/03/2020
title: 2019 年 9 月 - Azure Databricks
description: 新 Azure Databricks 功能和改进的 2019 年 9 月发行说明。
ms.openlocfilehash: 4d85be2a3b34900b4376ae1cba74b80a9a75403f
ms.sourcegitcommit: 6b499ff4361491965d02bd8bf8dde9c87c54a9f5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2020
ms.locfileid: "94329101"
---
# <a name="september-2019"></a>2019 年 9 月

这些功能和 Azure Databricks 平台的改进已于 2019 年 9 月发布。

> [!NOTE]
>
> 发布分阶段进行。 在初始发布日期后，可能最长需要等待一周，你的 Azure Databricks 帐户才会更新。

## <a name="databricks-runtime-52-support-ends"></a>Databricks Runtime 5.2 支持已终结

2019 年 9 月 30 日

9 月 30 日结束对 Databricks Runtime 5.2 的支持。 请参阅 [Databricks 运行时支持生命周期](../../runtime/databricks-runtime-ver.md#runtime-support)。

## <a name="launch-pool-backed-automated-clusters-that-use-databricks-light-public-preview"></a>推出了使用 Databricks Light 的由池支持的自动化群集（公共预览版）

2019 年 9 月 26 日 - 10 月 1 日：版本 3.3

当我们在 7 月推出[池](../../../clusters/instance-pools/index.md)时，你为自动作业配置由池支持的群集时无法选择 [Databricks Light](../../../runtime/light.md#light) 作为运行时版本。 现在，你可以拥有快速启动的高性价比群集！

## <a name="azure-sql-database-gateway-ip-addresses-will-change-on-october-14-2019"></a>Azure SQL 数据库网关 IP 地址将在 2019 年 10 月 14 日发生更改

10 月 14 日，Microsoft 会将流量迁移到[这些区域](/sql-database/sql-database-gateway-migration)中的新网关。 如果你的工作区位于其中一个区域，并且你已通过[你自己的 Azure Databricks 虚拟网络](../../../administration-guide/cloud-configurations/azure/vnet-inject.md)（使用“VNet 注入”）为合并的元存储配置[用户定义的路由 (UDR)](../../../administration-guide/cloud-configurations/azure/udr.md)，则可能需要在元存储的 IP 地址更改时，更新这些 IP 地址。 有关你的区域的 IP 地址的最新列表，请参阅 [Azure SQL Database 网关 IP 地址](/sql-database/sql-database-connectivity-architecture#azure-sql-database-gateway-ip-addresses)表。

## <a name="azure-data-lake-storage-credential-passthrough-now-supported-on-standard-clusters-and-scala-public-preview"></a>标准群集和 Scala 现在支持 Azure Data Lake Storage 凭据直通身份验证（公共预览版）

2019 年 9 月 12-17 日：版本 3.2

[凭证传递身份验证](../../../security/credential-passthrough/adls-passthrough.md)现可用于运行 Databricks Runtime 5.5 及以上版本和 SparkR on Databricks Runtime 6.0 Beta 的标准群集上的 Python、SQL 和 Scala。 到目前为止，凭据传递身份验证需要高并发群集，这些群集不支持 Scala。

为 Azure Data Lake Storage 凭据传递身份验证启用群集时，在该群集上运行的命令可以在 Azure Data Lake Storage 中读取和写入数据，无需用户配置用于访问存储的服务主体凭据。 凭据从启动操作的用户自动设置。

出于安全考虑，只有一位用户可以在启用凭据传递身份验证的标准群集上运行命令。 [单用户](../../../security/credential-passthrough/adls-passthrough.md#single-user)是在创建时设置的，可由对群集具有管理权限的任何人进行编辑。 管理员需要确保单用户至少具有对群集的附加权限。

> [!div class="mx-imgBorder"]
> ![凭据传递身份验证单用户](../../../_static/images/clusters/credential-passthrough-single.png)

## <a name="pandas-dataframes-now-render-in-notebooks-without-scaling"></a>pandas 数据帧现在无需缩放即可在笔记本中呈现

2019 年 9 月 12-17 日：版本 3.2

在 Azure Databricks 笔记本中，`displayHTML` 以前会缩放一些成帧的 HTML 内容，以适应呈现的笔记本的可用宽度。 虽然此行为对于图像是可取的，但它使 pandas DataFrames 呈现效果不佳。 但不会再这样了！

## <a name="python-version-selector-display-now-dynamic"></a>现在会动态显示 Python 版本选择器

2019 年 9 月 12-17 日：版本 3.2

选择不支持 Python 2 的 Databricks Runtime（如 Databricks 6.0）时，群集创建页将隐藏 Python 版本选择器。

## <a name="databricks-runtime-60-beta"></a>Databricks Runtime 6.0 Beta

**2019 年 9 月 12 日**

Databricks Runtime 6.0 Beta 引入了许多库升级和新功能，其中包括：

* 用于 Delta Lake DML 命令的新 Scala 和 Java API，以及清空和历史记录实用工具命令。
* 增强的 DBFS FUSE v2 客户端，支持在模型训练过程中更快、更可靠地进行读取和写入。
* 对每个笔记本单元的多个 matplotlib 绘图的支持。
* 更新到 Python 3.7，以及更新的 numpy、pandas、matplotlib 和其他库。
* 停用 Python 2 支持。

有关详细信息，请参阅完整的 [Databricks Runtime 6.0（不受支持）](../../runtime/6.0.md)发行说明。