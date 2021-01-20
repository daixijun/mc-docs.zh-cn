---
ms.openlocfilehash: 1b77d50be5af2bf4a59553088f1a35038334fce0
ms.sourcegitcommit: 88173d1dae28f89331de5f877c5b3777927d67e4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/14/2021
ms.locfileid: "98195091"
---


| 功能 |[消耗计划](../articles/azure-functions/consumption-plan.md)|[高级计划](../articles/azure-functions/functions-premium-plan.md)|[专用计划](../articles/azure-functions/dedicated-plan.md)|[ASE](../articles/app-service/environment/intro.md)| Kubernetes |
|----------------|-----------|----------------|---------|-----------------------| ---|
|[入站 IP 限制和专用站点访问](../articles/azure-functions/functions-networking-options.md#inbound-access-restrictions)|✅是|✅是|✅是|✅是|✅是|
|[虚拟网络集成](../articles/azure-functions/functions-networking-options.md#virtual-network-integration)|❌否|✅是（区域）|✅是（区域和网关）|✅是| ✅是|
|[虚拟网络触发器（非 HTTP）](../articles/azure-functions/functions-networking-options.md#virtual-network-triggers-non-http)|❌否| ✅是 |✅是|✅是|✅是|
|[混合连接](../articles/azure-functions/functions-networking-options.md#hybrid-connections)（仅限 Windows）|❌否|✅是|✅是|✅是|✅是|
|[出站 IP 限制](../articles/azure-functions/functions-networking-options.md#outbound-ip-restrictions)|❌否| ✅是|✅是|✅是|✅是|

