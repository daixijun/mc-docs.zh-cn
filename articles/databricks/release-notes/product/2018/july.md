---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 04/29/2020
title: 2018 年 7 月 - Azure Databricks
description: Azure Databricks 新增功能和改进的 2018 年 7 月发行说明。
ms.openlocfilehash: 4c2d600bff2d17f74cef8855a185e65e3e461132
ms.sourcegitcommit: 6b499ff4361491965d02bd8bf8dde9c87c54a9f5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2020
ms.locfileid: "94329238"
---
# <a name="july-2018"></a>2018 年 7 月

这些功能和 Azure Databricks 平台的改进已于 2018 年 7 月发布。

## <a name="libraries-api-supports-python-wheels"></a>库 API 支持 Python wheel

2018 年 7 月 31 日 - 8 月 7 日：版本 2.77

现在可以使用[库 API](../../../dev-tools/api/latest/libraries.md) 来安装 wheel 库。 在运行 Databricks Runtime 4.2 或更高版本的群集上安装 wheel 库时，会包括在库 `setup.py` 文件中指定的所有依赖项。 在运行 Databricks Runtime 4.1 或更低版本的群集上安装 wheel 库时，该文件会添加到 `PYTHONPATH` 变量，而不会安装依赖项。

## <a name="ipython-notebook-export"></a>IPython 笔记本导出

2018 年 7 月 31 日 - 8 月 7 日：版本 2.77

现在，在将 Azure Databricks 笔记本导出到 IPython 笔记本格式时，结果会包含在导出中。

## <a name="azure-key-vault-backed-secret-scopes"></a>Azure Key Vault 支持的机密范围

2018 年 7 月 19 日 - 24 日：版本 2.76

现在，机密支持由 [Azure Key Vault](/key-vault/key-vault-overview) 支持的范围。 在创建范围后，可以从该范围访问相应 Key Vault 中的所有机密。 有关详细信息，请参阅[创建 Azure Key Vault 支持的机密范围](../../../security/secrets/secret-scopes.md#akv-ss)。

> [!NOTE]
>
> Azure Key Vault 支持的机密范围是 Key Vault 的只读接口。 若要在 Azure Key Vault 中管理机密，必须使用 Azure [SetSecret](https://docs.microsoft.com/rest/api/keyvault/setsecret) REST API 或 Azure 门户 UI。

## <a name="trial-premium-workspaces"></a>高级试用版工作区

2018 年 7 月 20 日 - 24 日：版本 2.76

Azure Databricks 现在提供了试用版高级工作区。 在 14 天的试用期中，你可以访问免费的 Azure Databricks DBU。 有关详细信息，请参阅[创建工作区](/azure-databricks/quickstart-create-databricks-workspace-portal)。

## <a name="cluster-mode-and-high-concurrency-clusters"></a>群集模式和高并发性群集

2018 年 7 月 19 日 - 24 日：版本 2.76

在创建群集时，“群集类型”选项已重命名为“群集模式” 。 “无服务器池”选项已由“高并发性”群集模式替代 。 高并发性群集已经过优化，在由多个并发活动用户共享时可以提供高效的资源利用率、隔离、安全性和最佳性能。 高并发性群集只支持 SQL、Python 和 R 语言。 高并发性群集提供了无服务器池的所有优点，同时还允许灵活地进行 Spark 和资源配置。 有关详细信息，请参阅[高并发群集](../../../clusters/configure.md#high-concurrency)。

> [!div class="mx-imgBorder"]
> ![群集模式](../../../_static/images/clusters/high-concurrency-azure.png)

## <a name="table-access-control"></a>表访问控制

2018 年 7 月 19 日 - 24 日：版本 2.76

“表访问控制”复选框只适用于高并发群集。

> [!div class="mx-imgBorder"]
> ![表 ACL 复选框](../../../_static/images/clusters/table-acl-checkbox.png)

## <a name="unavailable-cluster-node-types-grayed-out"></a>不可用的群集节点类型已灰显

2018 年 7 月 3 日 - 10 日：版本 2.75

现在，不适用于你的订阅和区域的群集节点类型已灰显，你在创建群集时无法选择它们。

## <a name="r-markdown-support"></a>R Markdown 支持

2018 年 7 月 3 日 - 10 日：版本 2.75

Azure Databricks R 笔记本可以导出到 R Markdown 格式，R Markdown 文档可以作为 Azure Databricks 笔记本导入。 请参阅[笔记本外部格式](../../../notebooks/notebooks-manage.md#notebook-formats)。

## <a name="home-page-redesign-with-ability-to-drop-files-to-import-data"></a>重新设计了主页，可以通过拖放文件来导入数据

2018 年 7 月 3 日 - 10 日：版本 2.75

新主页添加了更加清晰简洁的界面，其中有指向改进后的入门教程的链接，并且提供了通过拖放文件来导入数据的功能。 请参阅[数据概述](../../../data/data.md#access-data)。

## <a name="widget-default-behavior"></a>小组件默认行为

2018 年 7 月 3 日 - 10 日：版本 2.75

现在，在为小组件选择新值时的默认执行行为是“不执行任何操作”。 在更改小组件值时，如果需要重新运行完整的笔记本，或者只重新运行与值相关的命令，则必须更新小组件设置。 请参阅[配置小组件设置](../../../notebooks/widgets.md#configure-widget-settings)。

## <a name="table-creation-ui"></a>表创建 UI

2018 年 7 月 3 日 - 10 日：版本 2.75

现在，在该 UI 中创建表时，请从“数据”页选择“添加数据”。

> [!div class="mx-imgBorder"]
> ![添加数据](../../../_static/images/tables/add-table-icon.png)

请参阅[使用 UI 创建表](../../../data/tables.md#create-table-ui)。

## <a name="multi-line-json-data-import"></a>多行 JSON 数据导入

2018 年 7 月 3 日 - 10 日：版本 2.75

现在，在创建表时可以导入多行 JSON 数据文件。 以前，JSON 数据文件必须平展到一行。 请参阅[使用 UI 创建表](../../../data/tables.md#create-table-ui)。