---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 05/22/2020
title: SQL Workbench/J - Azure Databricks
description: 了解如何将 SQL Workbench/J 与 Azure Databricks 配合使用。
ms.openlocfilehash: d127c6f6b1de3d1598d1d9308f3ccfa49696c017
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589946"
---
# <a name="sql-workbenchj"></a><a id="sql-workbenchj"> </a><a id="workbenchj"> </a>SQL Workbench/J

本文介绍如何将 SQL Workbench/J 与 Azure Databricks 配合使用。

## <a name="step-1-download-and-install-software"></a>步骤 1：下载并安装软件

下载并安装以下项：

* [SQL Workbench/J](http://www.sql-workbench.net/downloads.html)
* [Simba Spark JDBC 驱动程序](jdbc-odbc-bi.md#jdbc-driver)

## <a name="step-2-get-azure-databricks-connection-information"></a>步骤 2：获取 Azure Databricks 连接信息

1. 获取[个人访问令牌](../../dev-tools/api/latest/authentication.md#token-management)。
2. 按照[服务器主机名、端口、HTTP 路径和 JDBC URL](jdbc-odbc-bi.md#jdbc-odbc-params) 中的说明获取群集的 JDBC URL。

## <a name="step-3-configure-connection-in-workbenchj-to-an-azure-databricks-cluster"></a>步骤 3：在 Workbench/J 中配置到 Azure Databricks 群集的连接

1. 启动 SQL Workbench/J。
2. 选择“文件”>“连接”窗口。
3. 在“选择连接配置文件”对话框中，单击“管理驱动程序”。
   1. 在“名称”字段中，键入 `Spark JDBC`。
   1. 在“库”字段中，单击“选择 JAR 文件”图标。 浏览到下载了 Simba Spark JDBC 驱动程序 JAR 的目录。
   1. 验证“Classname”字段是否已填充。

      > [!div class="mx-imgBorder"]
      > ![Spark JDBC 驱动程序](../../_static/images/third-party-integrations/workbenchj/workbenchj-driver.png)

   1. 单击“确定”。
4. 单击“新建连接配置文件”![“连接配置文件”图标](../../_static/images/third-party-integrations/workbenchj/workbenchj-new-connection-profile.png) 图标。
   1. 键入配置文件的名称。
   1. 在“驱动程序”字段中，选择“com.simba.spark.jdbc.Driver”。
   1. 在“URL”字段中，输入在步骤 2 中构造的 URL。
   1. 在“用户名”字段中，输入 `token`。
   1. 在“密码”字段中，输入步骤 2 中的个人访问令牌。

      > [!div class="mx-imgBorder"]
      > ![连接配置文件](../../_static/images/third-party-integrations/workbenchj/workbenchj-profile.png)

   1. 单击“测试”。

      > [!div class="mx-imgBorder"]
      > ![连接测试](../../_static/images/third-party-integrations/workbenchj/workbenchj-test.png)

   1. 单击“确定”两次。