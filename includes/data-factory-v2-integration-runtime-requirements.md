---
author: WenJason
ms.service: data-factory
ms.topic: include
origin.date: 07/13/2019
ms.date: 11/23/2020
ms.author: v-jay
ms.openlocfilehash: 2b22d099787bcaf2000f6504884ea949d04c5f79
ms.sourcegitcommit: 054636c134cc0f53c194a6b76668644e18d1c4fe
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2020
ms.locfileid: "95970727"
---
<!--
    Separate the generic requirement on Self-hosted Integration Runtime set-up from connector articles.
-->
如果数据存储位于本地网络、Azure 虚拟网络或 Amazon Virtual Private Cloud 内部，则需要配置[自承载集成运行时](../articles/data-factory/create-self-hosted-integration-runtime.md)才能连接到该数据存储。

另外，如果数据存储是托管的云数据服务，可以使用 Azure 集成运行时。 如果访问范围限制为防火墙规则中允许的 IP，你可以选择将 [Azure Integration Runtime IP](../articles/data-factory/azure-integration-runtime-ip-addresses.md) 添加到允许列表。 

要详细了解网络安全机制和数据工厂支持的选项，请参阅[数据访问策略](../articles/data-factory/data-access-strategies.md)。
