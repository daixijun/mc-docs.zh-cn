---
title: 使用 SSMS 进行连接
description: 使用 SQL Server Management Studio (SSMS) 在 Azure Synapse Analytics 中连接并查询专用 SQL 池。
services: synapse-analytics
author: WenJason
manager: digimobile
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
origin.date: 04/17/2018
ms.date: 11/30/2020
ms.author: v-jay
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 19390572dabc0947b182650afb5742b6126f7284
ms.sourcegitcommit: dabbf66e4507a4a771f149d9f66fbdec6044dfbf
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/26/2020
ms.locfileid: "96152950"
---
# <a name="connect-to-a-dedicated-sql-pool-in-azure-synapse-analytics-with-sql-server-management-studio-ssms"></a>使用 SQL Server Management Studio (SSMS) 连接到 Azure Synapse Analytics 中的专用 SQL 池

> [!div class="op_single_selector"]
> * [Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md)
> * [SSMS](sql-data-warehouse-query-ssms.md)

使用 SQL Server Management Studio (SSMS) 连接到 Azure Synapse 中的数据仓库并进行查询。

## <a name="prerequisites"></a>先决条件

要使用本教程，需要：

* 现有专用 SQL 池。 若要创建一个，请参阅[创建专用 SQL 池](create-data-warehouse-portal.md)。
* 安装了 SQL Server Management Studio (SSMS)。 免费[下载 SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?toc=/synapse-analytics/sql-data-warehouse/toc.json&bc=/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)（如果尚未安装）。
* 完全限定的 SQL Server 名称。 若要查找此信息，请参阅[连接到专用 SQL 池](sql-data-warehouse-connect-overview.md)。

## <a name="1-connect-to-your-dedicated-sql-pool"></a>1.连接到专用 SQL 池

1. 打开 SSMS。
2. 选择“文件”   > “连接对象资源管理器”  ，打开对象资源管理器。

    ![SQL Server 对象资源管理器](./media/sql-data-warehouse-query-ssms/connect-object-explorer.png)
3. 填写“连接到服务器”窗口中的字段。

   ![连接到服务器](./media/sql-data-warehouse-query-ssms/connect-object-explorer1.png)

   * **服务器名称**。 输入前面标识的 **服务器名称** 。
   * **身份验证**。 选择“SQL Server 身份验证”  或“Active Directory 集成身份验证”  。
   * “用户名”  和“密码”  。 如果上面选择了 SQL Server 身份验证，请输入用户名和密码。
   * 单击“连接”  。
4. 要浏览，请展开 Azure SQL 服务器。 可以查看与服务器关联的数据库。 展开 AdventureWorksDW 以查看示例数据库中的表。

   ![浏览 AdventureWorksDW](./media/sql-data-warehouse-query-ssms/explore-tables.png)

## <a name="2-run-a-sample-query"></a>2.运行示例查询

现在，已建立了与数据库的连接，接下来让我们编写查询。

1. 在 SQL Server 对象资源管理器中右键单击数据库。
2. 选择“新建查询”  。 此时将打开一个新的查询窗口。

   ![新建查询](./media/sql-data-warehouse-query-ssms/new-query.png)
3. 将以下 T-SQL 查询复制到查询窗口中：

   ```sql
   SELECT COUNT(*) FROM dbo.FactInternetSales;
   ```

4. 单击 `Execute` 或使用以下快捷方式即可运行查询：`F5`。

   ![运行查询](./media/sql-data-warehouse-query-ssms/execute-query.png)
5. 查看查询结果。 在此示例中，FactInternetSales 表包含 60398 行。

   ![查询结果](./media/sql-data-warehouse-query-ssms/results.png)

## <a name="next-steps"></a>后续步骤
若要为 Azure Active Directory 身份验证配置环境，请参阅[向 SQL 池进行身份验证](sql-data-warehouse-authentication.md)。
