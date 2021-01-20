---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 01/12/2021
ms.author: v-junlch
ms.openlocfilehash: 813f4a3751aa2f6849e73ed9dc064680f911fe76
ms.sourcegitcommit: 88173d1dae28f89331de5f877c5b3777927d67e4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/14/2021
ms.locfileid: "98195117"
---
Azure 专用终结点是一种网络接口，你可通过该接口私密安全地连接到由 Azure 专用链接提供支持的服务。  专用终结点使用虚拟网络中的专用 IP 地址，从而将该服务有效地引入虚拟网络。

可以将专用终结点用于[高级](../articles/azure-functions/functions-premium-plan.md)和[应用服务](../articles/azure-functions/dedicated-plan.md)计划中托管的函数。

为函数创建入站专用终结点连接时，还需要一个 DNS 记录来解析专用地址。  默认情况下，使用 Azure 门户将在创建专用终结点时创建专用 DNS 记录。

