---
title: include 文件
description: include 文件
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 12/28/2020
ms.topic: include
origin.date: 05/05/2020
ms.author: v-johya
ms.openlocfilehash: 7b2585ce8e084345eef76fd1eb7f7d7883d29212
ms.sourcegitcommit: b4fd26098461cb779b973c7592f951aad77351f2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/04/2021
ms.locfileid: "97856768"
---
## <a name="sign-in-to-luis-portal"></a>登录到 LUIS 门户

<!--Not available in MC: luis-portal-note.md-->
LUIS 的新用户需要执行此过程：

1. 登录 [LUIS 门户](https://luis.azure.cn)，选择你所在的国家/地区，然后同意使用条款。 如果看到“我的应用”，则 LUIS 资源已存在，你应该跳过此过程，开始创建应用。 有两种注册方式：

    * 使用 Azure 资源（推荐）- 可将 LUIS 帐户与新的或现有的 Azure 创作资源进行关联。 这与已迁移注册的效果一样。 稍后无需再执行[迁移过程](../luis-migration-authoring.md#what-is-migration)。

    * 使用试用密钥。 这使你能够使用无需设置的试用资源登录到 LUIS。 如果选择此方式，则最终需要[迁移帐户](../luis-migration-authoring.md#migration-steps)并将应用程序与创作资源关联。

1. 在出现的“选择创作”窗口中，找到你的 Azure 订阅和 LUIS 创作资源。 如果没有资源，可新建一个。

    :::image type="content" source="../media/luis-how-to-azure-subscription/choose-authoring-resource.png" alt-text="选择语言理解创作资源的类型。":::
    
    创建新的创作资源时，请提供以下信息：
    * **租户名称** - 与 Azure 订阅关联的租户。
    * **Azure 订阅名称** - 将对资源计费的订阅。
    * **Azure 资源组名称** - 你选择或创建的自定义资源组名称。 使用资源组可将 Azure 资源分组，以便进行访问和管理。
    * **Azure 资源名称** - 你选择的自定义名称，用作创作和预测终结点查询的 URL 的一部分。
    * **定价层** - 定价层确定每秒和每月的最大事务数。



