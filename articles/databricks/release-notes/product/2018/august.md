---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 04/29/2020
title: 2018 年 8 月 - Azure Databricks
description: 新 Azure Databricks 功能和改进的 2018 年 8 月发行说明。
ms.openlocfilehash: 1c2967a167292f9cd66c095c8eaf1ac416194638
ms.sourcegitcommit: 6b499ff4361491965d02bd8bf8dde9c87c54a9f5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2020
ms.locfileid: "94329241"
---
# <a name="august-2018"></a>2018 年 8 月

这些功能和 Azure Databricks 平台的改进已于 2018 年 8 月发布。

## <a name="new-regions"></a>新区域

**2018 年 8 月 31 日**

目前以下其他区域提供 Azure Databricks：

* 加拿大东部
* 加拿大中部
* 印度中部
* 印度西部
* 印度南部
* 澳大利亚中部
* 澳大利亚中部 2
* 日本东部
* 日本西部

## <a name="conditional-access"></a>条件性访问

**2018 年 8 月 28日**

Azure Databricks 当前支持 Azure Active Directory 条件访问，以便管理员能够控制用户何时何地能登录到 Azure Databricks。 例如，条件访问策略可能会限制企业网络的登录，也可能会要求多重身份验证。

条件访问仅在 Azure AD Premium 中可用。

请参阅[条件访问](../../../administration-guide/access-control/conditional-access.md)。

## <a name="workspace-sidebar-redesign"></a>重新设计了工作区边栏

2018 年 8 月 28 日 - 9 月 4 日：版本 2.79

用于导航工作区文件的边栏已经过简化，一次只显示两个泳道，在需要查看两个以上的泳道时提供可用于水平滚动的向左和向右键，当单击左侧导航栏中的“工作区”或“主页”按钮时，默认显示用户文件视图，在使用主窗格时，可将边栏固定为打开状态，便于保持流畅操作。 希望你能喜欢这些更改。

> [!div class="mx-imgBorder"]
> ![工作区泳道](../../../_static/images/workspace/sidebar.png)

## <a name="new-environment-variables-in-init-scripts"></a>初始化脚本中的新环境变量

2018 年 8 月 28 日 - 9 月 4 日：版本 2.79

群集范围初始化脚本当前支持以下[环境变量](../../../clusters/init-scripts.md#env-var)：

* `DB_DRIVER_IP` - 驱动程序节点的 IP 地址
* `DB_INSTANCE_TYPE`- Spark 节点的类型
* `DB_PYTHON_VERSION` - 在群集上使用的 Python 版本
* `DB_IS_JOB_CLUSTER` - 指示是否已创建群集来运行作业

请参阅[诊断日志](../../../clusters/init-scripts.md#cluster-scoped-init-script)。

## <a name="cluster-scoped-init-scripts"></a>群集范围的初始化脚本

2018 年 8 月 14 日至 21 日：版本 2.78

群集范围 init 脚本是在群集配置中定义的 init 脚本。 与其他类型的脚本相比，群集范围初始化脚本具有以下优势：

* 同时适用于你创建的群集和为运行作业而创建的群集。
* 受群集 ACL 保护。
* 在[群集事件日志](../../../clusters/clusters-manage.md#event-log)中记录开始和结束事件。
* 具有可配置的日志位置。
* 支持预定义的支持条件处理的环境变量。

有关详细信息，请参阅[诊断日志](../../../clusters/init-scripts.md#cluster-scoped-init-script)。

## <a name="collapsible-headings"></a>可折叠的标题

2018 年 8 月 14 日至 21 日：版本 2.78

在包含 Markdown 标题的单元格后显示的单元格现在可以折叠到标题单元格中。 下图显示了“标题 1”，后面的两个单元格已折叠到其中。

> [!div class="mx-imgBorder"]
> ![Markdown 标题下已折叠的单元格](../../../_static/images/notebooks/headings.png)

若要展开和折叠标题，请单击 + 和 - 。