---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 03/26/2020
title: 合作伙伴数据集成 - Azure Databricks
description: 了解如何设置与合作伙伴产品集成的 Azure Databricks。
ms.openlocfilehash: dbafeb5c6934bf1fee53fadee28279a5b6d4afa5
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589574"
---
# <a name="partner-data-integrations"></a>合作伙伴数据集成

> [!IMPORTANT]
>
> 此功能目前以[公共预览版](../../release-notes/release-types.md)提供。

可以通过合作伙伴数据集成将数据加载到合作伙伴产品 UI 的 Azure Databricks 中。 这样就可以将数据从各种源引入 Azure Databricks，其特点是低代码、易于实现且可缩放。

若要允许从合作伙伴产品集成，必须创建并启动 Azure Databricks 群集。 合作伙伴产品会连接到此群集，并将作业向下推送到它所在的位置，以便与 Azure Databricks 交互。

若要查看所有集成的列表，请执行以下操作：

1. 转到工作区 UI 的右上方，单击 ![“帐户”图标](../../_static/images/account-settings/account-icon.png) 用户帐户图标，然后选择“合作伙伴集成”。

   > [!div class="mx-imgBorder"]
   > ![合作伙伴集成](../../_static/images/admin-settings/partner-integrations.png)

   “合作伙伴集成”页显示一系列合作伙伴产品磁贴：

   > [!div class="mx-imgBorder"]
   > ![合作伙伴库](../../_static/images/third-party-integrations/partner-gallery-azure.png)

2. 在一个合作伙伴磁贴中，单击“设置指南”或参阅：

   * [Qlik 集成](qlik.md)
   * [Fivetran 集成](fivetran.md)
   * [Infoworks 集成](infoworks.md)
   * [StreamSets 集成](streamsets.md)
   * [Syncsort 集成](syncsort.md)

> [!NOTE]
>
> 除非另有说明，否则合作伙伴集成由第三方提供。你的帐户必须有相应的提供程序，以便使用第三方的产品或服务。  尽管 Databricks 尽量使此内容保持最新状态，但我们不会就合作伙伴集成页上的集成或内容准确性做出任何陈述。 有关集成的问题，请咨询相应的提供商。