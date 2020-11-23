---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 10/01/2020
title: Power BI - Azure Databricks
description: 了解如何将 Microsoft Power BI 与 Azure Databricks 配合使用。
ms.openlocfilehash: 307a720aee5bd90eeed7808023d9b64f45ee8476
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589948"
---
# <a name="power-bi"></a>Power BI

> [!IMPORTANT]
>
> 已集成到 Power BI Desktop 版本 2.85.681.0 及更高版本中的 Azure Databricks 连接器处于[公共预览](../../release-notes/release-types.md)阶段。

Microsoft Power BI 是一种业务分析服务，它使用自助式商业智能功能提供交互式可视化效果，使最终用户能够自行创建报表和仪表板，而无需依赖于信息技术人员或数据库管理员。

将 Azure Databricks 作为数据源与 Power BI 配合使用时，可以将 Azure Databricks 的性能和技术优势带给所有业务用户（而不是仅带给数据科学家和数据工程师）。

可以使用内置 Azure Databricks 连接器将 Power BI Desktop 连接到 Azure Databricks 群集。

## <a name="requirements"></a>要求

Power BI Desktop 2.85.681.0 或更高版本。 [下载最新版本](https://www.microsoft.com/en-us/download/details.aspx?id=58494)。

## <a name="connect-to-azure-databricks"></a>连接到 Azure DataBricks

1. 在 Azure Databricks 中，获取群集的服务器主机名和 HTTP 路径：

   单击左侧导航栏中的“群集”图标，然后从群集列表中选择你的群集。 在群集配置页面上，单击“高级选项”切换开关和“JDBC/ODBC”选项卡 。复制“服务器主机名”和“HTTP 路径”。

   > [!div class="mx-imgBorder"]
   > ![JDBC-ODBC 选项卡](../../_static/images/third-party-integrations/jdbc-odbc-tab-azure.png)

   有关详细信息，请参阅[服务器主机名、端口、HTTP 路径和 JDBC URL](jdbc-odbc-bi.md#jdbc-odbc-params)。

2. 在 PowerBI Desktop 中，转到“获取数据 > Azure”，并选择“Azure Databricks”连接器。

   > [!div class="mx-imgBorder"]
   > ![“获取数据”列表中的 Databricks 连接器](../../_static/images/third-party-integrations/power-bi/power-bi-connector-get-data.png)

3. 单击“连接”。
4. 粘贴从 Azure Databricks 群集配置页复制的“服务器主机名”和“HTTP 路径”。

   > [!div class="mx-imgBorder"]
   > ![Databricks JDBC/ODBC 配置](../../_static/images/third-party-integrations/power-bi/power-bi-connection-config.png)

5. 选择你的数据连接模式。

   有关导入和 DirectQuery 之间的差异的信息，请参阅[在 Power BI Desktop 中使用 DirectQuery](https://docs.microsoft.com/power-bi/connect-data/desktop-use-directquery)。

6. 单击“确定”。
7. 在身份验证提示下，选择要用于登录到 Azure Databricks 的方式：

   **Azure Active Directory**：使用 Azure 帐户凭据。 单击“登录”按钮。 在登录对话框中，输入 Azure 帐户用户名（电子邮件、电话号码或 Skype）。

   个人访问令牌：使用 Azure Databricks 生成的个人访问令牌。 若要获取一个这样的令牌，请参阅[使用 Azure Databricks 个人访问令牌进行身份验证](../../dev-tools/api/latest/authentication.md)。

   > [!NOTE]
   >
   > 对 Azure Databricks 的访问未启用用户名/密码身份验证。 建议使用 Azure Active Directory 身份验证。

8. 单击“连接”。
9. 从 Power BI 导航器中选择要查询的 Azure Databricks 数据

   > [!div class="mx-imgBorder"]
   > ![Power BI Navigator](../../_static/images/third-party-integrations/power-bi/power-bi-navigator.png)