---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 03/26/2020
ms.author: larryfr
ms.openlocfilehash: 2f82725c3748f444ec17665ba7adbed153cbab05
ms.sourcegitcommit: b6fead1466f486289333952e6fa0c6f9c82a804a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/27/2020
ms.locfileid: "96301047"
---
> [!TIP]
> 登录后，你将看到与你的 Azure 帐户关联的订阅列表。 在 `isDefault: true` 的情况下显示的订阅信息是 Azure CLI 命令的当前已激活的订阅。 此订阅必须与包含 Azure 机器学习工作区的订阅相同。 可以通过访问工作区的概述页从 [Azure 门户](https://portal.azure.cn)找到订阅 ID。 还可以使用 SDK 从工作区对象获取订阅 ID。 例如，`Workspace.from_config().subscription_id`。
> 
> 若要选择另一个订阅，请使用 `az account set -s <subscription name or ID>` 命令并指定要切换到的订阅名称或 ID。 有关订阅选择的详细信息，请参阅[使用多个 Azure 订阅](/cli/manage-azure-subscriptions-azure-cli)。