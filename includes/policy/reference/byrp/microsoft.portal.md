---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
origin.date: 11/20/2020
ms.date: 12/03/2020
ms.author: v-tawe
ms.custom: generated
ms.openlocfilehash: e194b2193d3e0753f9a06b76addb53140ce6f2d3
ms.sourcegitcommit: 60e70acb6f9604aeef69d2027f7f96a1d7d5b248
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2020
ms.locfileid: "96541213"
---
|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[共享仪表板不应包含具有内联内容的 Markdown 磁贴](https://portal.azure.cn/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F04c655fe-0ac7-48ae-9a32-3a2e208c7624) |禁止创建 Markdown 磁贴中包含内联内容的共享仪表板，并强制将内容存储为在线托管的 Markdown 文件。 如果在 Markdown 磁贴中使用内联内容，你无法管理该内容的加密。 通过配置自己的存储，可以实现加密、双重加密甚至创建自己的密钥。 如果启用此策略，会将用户限制为使用 2020-09-01-preview 或更高版本的共享仪表板 REST API。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Portal/SharedDashboardInlineContent_Deny.json) |
