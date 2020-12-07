---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
origin.date: 11/20/2020
ms.date: 12/03/2020
ms.author: v-tawe
ms.custom: generated
ms.openlocfilehash: 4bc433fb700f5bc2aa1b984d9e077fddccac4d2f
ms.sourcegitcommit: c4ac22d1def90dd1e249bfce58b57ec4e86537db
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2020
ms.locfileid: "96544725"
---
|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure SignalR 服务应使用专用链接](https://portal.azure.cn/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F53503636-bcc9-4748-9663-5348217f160f) |审核没有达到“至少有一个已批准的专用终结点连接”标准的 Azure SignalR 服务资源。 虚拟网络中的客户端可以安全地访问通过专用链接获得专用终结点连接的资源。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SignalR/SignalR_PrivateEndpointEnabled_Audit.json) |
